---
layout: post
title: Competitive Programming Tips
---

These are some rough notes from a session I presented to a number of high school students preparing to compete in a programming competition. The notes are heavily centered around the use of Python 3 however the concepts can be adapted to most languages. If you notice any errors please feel free to let me know in a [Github issue](https://github.com/mattjegan/mattjegan.github.io/issues/new) or [submit a fix here.](https://github.com/mattjegan/mattjegan.github.io/blob/master/progcomp-tips.md)

## Strategy
* Don't think of the syntax/code straight way, take time to understand the problem in depth and try to explain your solutions before you start programming.
* Depending on how confident each team member is, it isn't a bad strategy to divide and conquer, either one person tackling the hard problems first with the other team members getting through the easier ones or vice versa.
* Be critical of time and be wary of when you can't solve a problem. The faster you accept that you can't find a solution, the more time you will have to work on other problems.

## Finding Attributes and Methods
Besides using your favourite search engine and searching [the python documentation](https://docs.python.org/3/), a quick way to find out what attributes and methods are available to a module or object is to use the `dir` function. For example, we might want to find all the things we can do to a `list`:

```python
my_list = [1, 2, 3]
dir(my_list)

# ['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', 
# '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', 
# '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', 
# '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 
# 'insert', 'pop', 'remove', 'reverse', 'sort']
```

Notice at the end of the output there are a number of common list methods (`append`, `clear`, `copy` etc.) We can also try to find out what each of these do by checking if they have a docstring attached (a common way to generate documentation in python):

```python
print(my_list.append.__doc__)
# L.append(object) -> None -- append object to end
```

The docstrings tend to be rather concise so if you need a better explanation of a particular object/method then the [python documentation](https://docs.python.org/3/) is the place to go.

## Lambda Functions
Lambda functions are a quick way in Python to write simple functions. They are not named unless you assign them to a variable. For example:

```python
square = lambda x: x * x
print(square(2))  # 4
```

this is the same as:

```python
def square(x):
    return x * x

print(square(2))  # 4
```

Using lambda functions may make it faster to implement certain solutions as you will see further in this document.

## Common Sub-problems
When you are programming competitively you will come across a number of sub-problems that can be very quickly solved so you can focus on the main problem. Theses are some of those sub-problems.

### Filtering collections
You might have a collection/array/list of items that you need to filter so you can continue your program. There are a few ways to do this in Python: 

```python
my_list = [1, 2, 3, 4, 5]
new_list = []

for element in my_list:
    if your_condition(element):   # This line can be substituted for whatever condition you're filtering
        new_list.append(element)
```

or even shorter:

```python
my_list = [1, 2, 3, 4, 5]
new_list = filter(your_condition, my_list)  # This returns a generator so you may need to cast it to a list: list(filter(your_condition, my_list))
```

### Altering collections
Often you will need to apply some effect to a whole collection, for example you may need to square every number in a sequence. This can be quickly implemented by following this pattern:

```python
my_list = [1, 2, 3, 4, 5]
new_list = map(lambda x: x * x, my_list)  # [1, 4, 9, 16, 25]
```

### Combining two collections
Sometimes you will need to join two different collections as pairs, triplets etc. This can be down quickly using the `zip` function:

```python
letters = ['a', 'b', 'c']
numbers = [1, 2, 3]

print(list(zip(letters, numbers)))  # [('a', 1), ('b', 2), ('c', 3)]
```

### Quickly finding the total of a collection of numbers

```python
my_list = [1, 2, 3, 4, 5]
print(sum(my_list))  # 15
```

### Quickly finding the maximum of a collection of numbers

```python
my_list = [1, 2, 3, 4, 5]
print(max(my_list))  # 5
```

### Quickly finding the minimum of a collection of numbers

```python
my_list = [1, 2, 3, 4, 5]
print(min(my_list))  # 1
```

### Maintaining a unique set
You might need to make sure that you have a collection of only unique items, this can be done using a `set`:

```python
s = set()  # {}
s.add(1)   # {1}
s.add(2)   # {1, 2}
s.add(2)   # {1, 2}
```

## Counters
Sometimes you need to be able to count things really quickly and don't have the time to use a dictionary with a bunch of if statements. We can turn:

```python
my_str = "the quick brown fox jumped over the lazy dog"
counter = {}

for element in my_str.split(' '):
    if element in counter:
        counter[element] += 1
    else:
        counter[element] = 1

# counter = {'the': 2, 'quick': 1, 'brown': 1, 'fox': 1, 'jumped': 1, 'over': 1, 'lazy': 1, 'dog': 1}
```

into this:

```python
from collections import Counter

my_str = "the quick brown fox jumped over the lazy dog"
counter = Counter(my_str.split(' '))

# counter = Counter({'the': 2, 'quick': 1, 'brown': 1, 'fox': 1, 'jumped': 1, 'over': 1, 'lazy': 1, 'dog': 1})
```

## Reading files
Some competitions require participants to read from a file to gather test cases, you can do this quickly in Python 3 like so:

```python
with open('your_file.txt', 'r') as f:
    lines = f.readlines()

do_whatever_you_want(lines)  # The 'lines' variable is still in scope when we leave the context manager/outdent
```

## Writing to files
Similarly, you may be required to write files:

```python
content = ['my\n', 'output\n']  # '\n' is important as it is the newline character
with open('your_output.txt', 'w') as f:   # 'w' mode will overwrite whatever is in the file, 'a' will append an existing file
        for line in content:
                f.write(content)
```

## More Information
I hope this document has helped show some quick ways you can solve common problems in competitive programming, [Trey Hunner has a brilliant article covering some more core python builtin functions that you may find useful.](https://treyhunner.com/2019/05/python-builtins-worth-learning/).

Like any skill, programming is improved through practice, there are various sites out there to hone your skills but I personally prefer using https://www.hackerrank.com/ for preparing for competitions/interviews.

For UNSW ProgComp, there are prior problems available [here](https://cgi.cse.unsw.edu.au/~progcomp/2017/home/pastcomps.php), note the 2018 links don't work.
