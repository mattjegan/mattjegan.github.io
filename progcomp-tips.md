---
layout: page
title: Competitive programming tips
permalink: /progcomp-tips/
---

## Strategy
* Don't think of the syntax/code straight way, take time to understand the problem in depth and try to explain your solutions before you start programming.
* Depending on how confident each team member is, it isn't a bad strategy to divide and conquer, either one person tackling the hard problems first with the other team members getting through the easier ones or vice versa.
* Be critical of time and be wary of when you can't solve a problem. The faster you accept that you can't find a solution, the more time you will have to work on other problems.

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

## More Information

I hope this document has helped show some quick ways you can solve common problems in competitive programming, [Trey Hunner has a brilliant article covering some more core python builtin functions that you may find useful.](https://treyhunner.com/2019/05/python-builtins-worth-learning/).

Like any skill, programming is improved through practice, there are various sites out there to hone your skills but I personally prefer using https://www.hackerrank.com/ for preparing for competitions/interviews.

For UNSW ProgComp, there are prior problems available [here](https://cgi.cse.unsw.edu.au/~progcomp/2017/home/pastcomps.php), note the 2018 links don't work.
