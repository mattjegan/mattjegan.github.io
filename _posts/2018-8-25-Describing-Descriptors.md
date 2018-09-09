---
layout: post
title: Describing Descriptors
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/lmcgtUw5djw" frameborder="0" allowfullscreen></iframe>

Slides available [here](https://speakerdeck.com/mattjegan/describing-descriptors)

Oftentimes beginner programmers go through traditional features when learning a language. For Python beginners this might involve variables, control structures like if-statements, while and for loops, dictionaries, and finally classes. However, if we read the Python documentation we find that another feature that can be used in Python is that of the descriptor protocol. Descriptors allow the programmer to override the storing and retrieving of different class instance variables such that special behaviours can be followed. For example, we might want some variable to follow some special validation. We could do this using `__setattr__` on the containing class but perhaps we want to reuse the validation in another class, or we want other validations for other variables and we don’t want `__setattr__` to become a huge if/elif/else block. In this talk, I will walk attendees through what a descriptor is, what use cases they can use them in, how to implement a descriptor, and common descriptors in the Python ecosystem that users may or may not have identified as descriptors (often just referred to as magic).