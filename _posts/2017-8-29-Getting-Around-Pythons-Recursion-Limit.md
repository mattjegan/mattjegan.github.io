---
layout: post
title: Getting Around Pythons Recursion Limit
---

I recently saw this post on Facebook:

![The meme that started this all]({{ site.url }}/images/computersciencememerecursion.jpg)
[_Credit Computer Science Memes for Travelling Salesman Teens_](https://www.facebook.com/pg/Computer-Science-Memes-for-Travelling-Salesman-Teens-419913568381772/posts/?ref=page_internal)

This led me to want to contradict the statement, although probably not recommended. So, lets consider the following python program:

```python
import sys

def recurse(n):
    print(n)
    recurse(n + 1)

if __name__ == '__main__': recurse(1)
``` 

It is a simply recursive program which simply prints out the value of `n`. If you run it you will find that it will raise a `RecursionError` at some point, for me `997` was the last value printed. 
Now it is important to understand why this happens. Under the hood, the Python interpreter enforces a recursion depth that is set sanely so you don't cause the underlying C implementation to 
produce a [stack overflow](https://en.wikipedia.org/wiki/Stack_overflow).

But sticking with my aim of disproving the original Facebook post, I set out to get around this limitation. What I came up with is this:

```python
import sys

def recurse(n):
    print(n)
    sys.setrecursionlimit(sys.getrecursionlimit() + 1)
    if n == 2000:
        return
    recurse(n + 1)

if __name__ == '__main__': recurse(1)
```

Sure enough, running this increments the recursion depth naively making sure we never hit the limit. If we remove the if-statement we will achieve our goal of seemingly-infinite recursion. However, it's got a slight problem, if we _do_ remove the safety of the if-statement and run the code, we will get a segmentation fault. For me, this occured at `n = 36142` and looked like this:

```
.
.
.
36140
36141
36142
[1]    7655 segmentation fault  python3 main.py
```

Now `36132` seems to be pretty close to `2^15`, perhaps there is some reason behind this? I attempted to look at the [Python interpreter source code](https://github.com/python/cpython) but to no 
avail, 
if anyone has an answer stemming from this I would very much appreciate you contacting me and explaining it. As for my own analysis to why this is the number we land at, I present the following.

First we need to change the code so it doesn't segfault any more. To do this, we limit the size of `n` to `36141`. By profiling our code using `python3 -m cProfile main.py` we get the following output:

```
         144527 function calls (108397 primitive calls) in 0.410 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    0.410    0.410 main.py:1(<module>)
  36131/1    0.094    0.000    0.410    0.410 main.py:3(recurse)
        1    0.000    0.000    0.410    0.410 {built-in method builtins.exec}
    36131    0.294    0.000    0.294    0.000 {built-in method builtins.print}
    36131    0.009    0.000    0.009    0.000 {built-in method sys.getrecursionlimit}
    36131    0.013    0.000    0.013    0.000 {built-in method sys.setrecursionlimit}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
```

Looking at the output we see that there are `144527` function calls, `36131` of them being calls to our `recurse` function, `36131` calls for each of the `print`, `sys.getrecursionlimit` and `sys.setrecursionlimit` functions. Now calling `sys.getsizeof(recurse)` lets us know that our recurse function takes up `136` bytes. Similarly, we find that `print`, `sys.getrecursionlimit` and `sys.setrecursionlimit` are all `72` bytes in size. This means that we are using at least `((36131 * 136) + (36131 * 72 * 3)) / 1024 / 1024 ~= 12mb` during execution. Perhaps this means the limit of the Python call stack is somewhere around this number. 

## Final Thoughts

Upon exploring [Stack Overflow](https://stackoverflow.com/) (No pun intended) and various other sites, I have not been able to find specific reason why I can not increase the recursion limit, and 
the stack size, to accomodate the needs of infinite recursion. 

Final ideas on achieving this would include having to make actual code changes to the Python interpreter and/or predicting the number of calls you will make within the recursive function so you may grow the stack accordingly. For example, `recurse` made `4` function calls in total and hence I imagined I could increase the recursion depth by `4` each time (this did not work surprisingly, leaving me with the same magic number, `36131`, and a segfault.

Conclusively, I suspect it is not possible to have infinite recursion in Python either because our call stack will grow too fast or we simply allocate too much memory. 
