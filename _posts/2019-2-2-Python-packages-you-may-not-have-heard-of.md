---
layout: post
title: Python packages that you may not have heard of
---

The python package ecosystem is a vast forest with many great packages available. If you have been using python professionally you are more than likely to have stumbled across some of the more popular packages such as [requests](http://docs.python-requests.org/en/master/), [django](https://www.djangoproject.com/) and [flask](http://flask.pocoo.org/). In this post however, I seek to bring your attention to a few packages that I have been using over the last 6 months or so and have found to be quite useful.

## [Glom](https://github.com/mahmoud/glom)

[Glom](https://github.com/mahmoud/glom) is a package that was first released in April 2018, and it has since become my go to solution for accessing nested data structures in python. It has a very simple interface that allows you to declare what you are looking for in some structure and define a few other options around that. For example, if we have the following dictionary:

```python
my_dict = {
    'foo': {
        'foofoo': 1,
        'foobar': 2
    },
    'bar': {
        'barfoo': 3,
        'barbar': 4
    }
}
```

and we wanted to find the value for `foobar`, we could simply write `glom(my_dict, 'foo.foobar')`. Furthermore, if we wanted to define a default for a `KeyError` we could pass a keyword argument like `glom(my_dict, 'foo.foobar', default=None)` which would then return `None` if there was nothing at the `foo.foobar` path.

Now glom's not just a data access package, but also a data formatting package. For example, given a dictionary of animals we might want to get a list of all the different colors that are in our data. So if we had this dictionary:

```python
my_dict = {
    'animals': [
        {
            'name': 'dog',
            'num_legs': 4,
            'color': 'brown'
        },
        {
            'name': 'cat',
            'num_legs': 4,
            'color': 'orange'
        },
        {
            'name': 'frog',
            'num_legs': 4,
            'color': 'green'
        }
    ]
}
```

we could get our list of colors with the following call to glom `glom(my_dict, ('animals', ['color']))` which would result in `['brown', 'orange', 'green']`.

## [Crayons](https://github.com/kennethreitz/crayons)

[Crayons](https://github.com/kennethreitz/crayons) is another package from serial-creator Kenneth Reitz. This package excels at making it easy to color your text in command line applications. I know I've certainly found it useful when writing reporting tools or for coloring prompts for dangerous actions. The API itself is well designed, you pass a string to a function named after the color you want the text to be and then it will return the decorated string for you so that when you print it to the console it's already colored for you. For example:

```python
import crayons

red = crayons.red("red")
print(f"The end of this string is {red}.")
```

which prints

<style>
code.red {
    color: red
}
</style>

`The end of this string is `<code class="red">red.</code>

Another useful feature that crayons provides is that of turning off the colors when you want to via calling `crayons.disable()`.

## [Hypothesis](https://hypothesis.readthedocs.io/en/latest/)

[Hypothesis](https://hypothesis.readthedocs.io/en/latest/) is a testing framework that will make you think differently about testing your python code. Rather than writing standard unit tests where you come up with the input for some function or generate random fake input each time, Hypothesis actually inspects your code and figures out what your edge cases are and tries to test against them, furthermore Hypothesis attempts to report back to you the minimal test case that will break your code so you can find and fix bugs faster than ever. One example of Hypothesis power is as follows, say we are trying to write a function to square a number. Our first attempt might be to produce the square via addition:

```python
def square(x):
    total = 0
    for _ in range(x):
        total += x

    return total
```

and our normal, naive, test case might look like:

```python
def test_square():
    assert square(4) == 16
```

Notice that this is a bad test case because we forgot to check edge cases. Well, if we rewrite the test case using Hypothesis, we can find those edge cases. Here's how we would write it:

```python
from hypothesis import given
from hypothesis.strategies import integers

@given(x=integers(max_value=100))
def test_square(x):
    assert square(x) == x * x
```

In this test case, we are asking Hypothesis to generate us test cases where we are testing an integer up to 100 and checking that it is squared correctly. Upon running this, Hypothesis alerts us:

```
Falsifying example: test_square(x=-1)
```

indicating that when `x = -1` we fail our test. We can then fix our function and noticed all tests now pass:

```python
def square(x):
    return x * x
```

## Marshmallow

## Nameko

## Pystache