---
layout: post
title: Explained Simply - P vs NP
---

Recently I was asked to explain a complex topic simply. I had the opportunity to choose any topic of my liking and so I chose the P vs NP problem.

## What is it?

The P vs NP problem is one of [the seven Millenium Problems](http://www.claymath.org/millennium-problems) put forward by the Clay Mathematics Institute, which will award $1 million to the person or group that solves the problem.

The problem statement is simply put, does P == NP or does P != NP? To understand this question we must first understand the terms involved?

## P? NP?

In [computational complexity theory](https://en.wikipedia.org/wiki/Computational_complexity_theory) computer scientists and mathematicians try to classify certain problems into classes of similar problems. This is done by proving that certain problems are in fact similar. For example, if I needed to sort fruit into different classes, I might put a red apple with a green apple because they both had the same shape, however I might not include bananas as they are radically different. 

### P

So the first class of computational problems we are going to talk about is the class P. P (standing for polynomial) represents a class of problems that can be solved in some reasonable amount of time. For a problem to belong in this class P, the time it takes to solve the problem must not radically change between different variations of the problem. For example, if I asked you to sort 5 cards, you might do it in 5 seconds and if I asked you to sort 10 cards, you might do it in 10 seconds. Since this time is predictable, this problem is said to be of type P.

### NP

The second class of problems we need to know about is NP, which stands for Non-Polynomial. NP represents the class of problems that we don't know we can produce solutions for in some predictable time. For example, we need to sort 5 cards and it takes 5 seconds however sorting 10 cards might take 37 seconds for some reason, and furthermore, 11 cards might take 2 hours. If this were the case for sorting cards, we would call in an NP type problem. Another important fact about NP typed problems is that we must be able to check that the solution is correct very quickly. For example, it might take you ages to give me the solution to the sorted cards, but once you do I can very easily check that they are actually sorted.

## So what's with the whole P vs NP thing?

So remember the million dollars we want? Well to get it, we need to prove P == NP or P != NP. To prove the former, we would need to prove that all problems in NP, can actually be solved by some method that fits the description of P type problems (for everyones sanity, card sorting is actually in P). That is, all hard problems can be transformed into easy problems. To prove the latter, P != NP, we would need to prove that all NP problems are actually [NP-Complete](https://en.wikipedia.org/wiki/NP-completeness) (simply put, these are definitely hard problems, perhaps I'll explain this in another blog post). 

I hope that this short ramble has simplified the problem of P vs NP to something that is more digestable, however I recommend to the reader that wants to dive into this stuff more to checkout out [Michael Sipser's Theory of Computation](https://www.amazon.com/Introduction-Theory-Computation-Michael-Sipser/dp/113318779X).  