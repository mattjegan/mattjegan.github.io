---
layout: post
title: Describing Descriptors - A follow up
---

Over the past weekend I gave [a talk at PyConAU entitled "Describing Descriptors"](https://youtube.com/watch?v=lmcgtUw5djw). In this talk I sought to provide the audience an introduction to Pythons descriptor features and some basic use cases they might have for them. Following the talk there were some questions that dig into areas that I had not experimented with with regards to descriptors. This post seeks to answer those questions and any other interesting questions I find while investigating.

## How do you delete the actual descriptor instance from a class?

So if we take a very simple descriptor, `Desc`, which prints when it is accessed:

```python
from weakref import WeakKeyDictionary

class Desc:
    def __init__(self):
        self.data = WeakKeyDictionary()

    def __get__(self, instance, owner):
        print('Called: __get__')
        if instance is None:
            return self
        return self.data[instance]

    def __set__(self, instance, value):
        print('Called: __set__')
        self.data[instance] = value
```

and use it on a class, note that we are declaring it as a class attribute:

```python
class Person:
    name = Desc()

    def __init__(self, name):
        self.name = name
```

We can then create instances of this class

```python
>>> p1 = Person('matt')
Called: __set__
```

noting that `Desc.__set__` was called. We can see that this descriptor exists on the `Person` class itself:

```python
>>> 'name' in dir(Person)
True
```

and then delete it from the class directly:

```python
>>> del Person.name
```

Finally, when we create a new instance, we can see that `Desc.__set__` was no longer called and the descriptor doesn't exist on the class anymore:

```python
>>> p2 = Person('sam')
>>> 'name' in dir(Person)
False
```

Do note however, that our `p2.name` was still set, however it is now just a regular instance attribute.

## How do descriptors work with slots?

`__slots__` are an interesting feature of Python and allow the programmer to assign fixed memory for their instance attributes rather than having their class use `__dict__`. For example, we can create a `Person` class with a `name` attribute like so:

```python
class Person:
    __slots__ = ('name', )


p = Person()
p.name = 'matt'
```

In CPython, slots are [implemented as descriptors](https://github.com/python/cpython/blob/master/Objects/descrobject.c). I have tried to assign my own descriptor to a slot by declaring a descriptor as usual which doesn't work and I also tried to subclass the slots type to create my own descriptor but that didn't work either. [This stack overflow answer](https://stackoverflow.com/a/47995309/1277637) suggests that we can add extra validations to members of `__slots__` by hiding renamed variables on the class however this adds more descriptors on to the class/instance so you now have 2 attributes for your initial 1.

## Can you chain descriptors?

When clarifying this question, the question is was better phrased as "Can we combine descriptors for validations?". For this we will be reimplementing the `NonNegativeInteger` descriptor from the original talk however this time we want to implement the integer validation and the non-negative validation as separate descriptors for whatever reason. Note that this is a very simple usecase and probably doesn't require descriptors but I'm using it to demonstrate the concept. We are going to implement a `_validate` hook that any subclass can implement to allow us to add extra validations to the descriptor, the reason we do this is because the superclass will call `self.data[instance] = value` before the end of the method and so we would a dirty dictionary if we just called `super().__set__` from the subclass.

```python
from weakref import WeakKeyDictionary

class IntDesc:
    def __init__(self):
        self.data = WeakKeyDictionary()

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return self.data[instance]

    def __set__(self, instance, value):
        if not isinstance(value, int):
            raise TypeError('Value is not an int')

        if hasattr(self, '_validate'):
            value = self._validate(instance, value)

        self.data[instance] = value


class NonNegativeInteger(IntDesc):
    def _validate(self, instance, value):
        if value < 0:
            raise ValueError('Value must be non-negative')

        return value


class Person:
    age = NonNegativeIntDesc()

    def __init__(self, age):
        self.age = age
```

As you can see, our `IntDesc` checks whether or not the value being set is an integer and before it sets the value it calls any extra validations implemented in `_validate`. We can then add those validations in `NonNegativeInteger._validate`. This isn't a particularly pretty example, please let me know if you have a nicer way of explaining descriptor subclassing. The primary issue with this approach is that we needed to add the `_validate` hook where our ideal solution would use a call to `super()` however this doesn't work with features that need to go after the initial call rather than before.

## Is there a performance penalty for using descriptors?

I was unsure as to whether there was a performance penalty for using descriptors and so I've tested it. The feature I wanted to test performance on was a simple setting of an attribute. For this test we are going to try 5 different approaches:

1. Using a descriptor that stores the value on the instance.
2. Using a descriptor that stores the value in a `WeakKeyDictionary`.
3. Storing the value on the instance as normal.
4. Storing the value using `@property`.
5. Storing the value using `__setattr__`.

The 5 implementations can be [found on Github](https://github.com/mattjegan/describing-descriptors/tree/master/performance)

My method was to time the implementations while they set a value 1 million times. I repeated this 100 times and averaged the results to try smooth out any outliers. The results are as follows:

* Descriptor storing the value on the instance: 0.144 seconds
* Descriptor storing the value in a `WeakKeyDictionary`:  0.142 seconds
* Storing the value on the instance as normal: 0.139 seconds
* Storing the value using `@property`: 0.382 seconds
* Storing the value using `__setattr__`: 0.678 seconds

As you can see, both implementations of descriptors were only slightly slower than storing the value directly on the instance whereas `@property` and `__setattr__` were significantly slower on average. All of these benchmarks were ran on my 2017 Macbook Pro.

## By using descriptors do we lose spatial relativity?

## Can you have inter-attribute validation?

You sure can, building on some points from the talk, please don't add too much magic to your code as you will confuse beginners and keeping the code simple will lead to greater maintainability. Anyway, we can make two attributes rely on one another by accessing them through the instance argument:

```python
from datetime import datetime, timedelta
from weakref import WeakKeyDictionary

class AgeDesc:
    def __init__(self):
        self.data = WeakKeyDictionary()

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return self.data[instance]

    def __set__(self, instance, value):
        if not isinstance(value, int):
            raise TypeError('Age must be an integer')

        if value < 0:
            raise ValueError('Age must be a positive integer')

        self.data[instance] = value


class DobDesc:
    def __init__(self):
        self.data = WeakKeyDictionary()

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return self.data[instance]

    def __set__(self, instance, value):
        # Check that this date of birth is valid for the instances age
        if not isinstance(value, datetime):
            raise TypeError('DoB must be a datetime')

        if (datetime.now() - value).days - (365 * instance.age) > 365:
            raise ValueError(f"If the age is {instance.age}, the date of birth cannot be {value}")

        self.data[instance] = value


class Person:
    age = AgeDesc()
    date_of_birth = DobDesc()
```

Here you can see that we are creating two data descriptors, one for the age and one for the date of birth. The `AgeDesc` simply checks that the age is a number and is positive however the `DobDesc` initial checks the value is a datetime but also verifies that it is a possible date by comparing it to the `age` attribute on the `Person` in its `__set__` method. If it isn't a valid date then it raises an error. For example:

```python
p = Person()
p.age = 10
p.date_of_birth = datetime.now() - timedelta(days=365*10) # Sets the DoB fine
p.date_of_birth = datetime.now() - timedelta(days=365*20) # Raises a ValueError
```
