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

## By using descriptors do we lose spatial relativity?

## Can you have inter-attribute validation?

You sure can, building on some points from the talk, please don't add too much magic to your code as you will confuse beginners and keeping the code simple will lead to greater maintainability. Anyway, we can make two attributes rely on one another by accessing them through the instance argument:

```python

```

## Can you pickle a class with a descriptor on it?
