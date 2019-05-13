---
layout: post
title: 
---

## What is load testing and why do we do it?
Load testing is a general class of testing methods:
* Load testing: normal levels of usage
* Stress testing: increasing levels of usage until things start to break
* Soak testing: normal or slightly increased levels of usage for prelonged periods of time to see if anything changes (mem leaks etc)
* Spike testing: normal load with periodic extreme increases in usage to test capacity

Load testing can be used to test if you are ready for a predicted event or for longer term capacity planning. It's also useful for evaluating whether your current setup is substantial.

## When to load test?
* Launching
* Upcoming events (PR, conference, related seasonal events, etc.)
* New features
* Reported low performance of specific features

## How can I load test?
* Paid services (can get very expensive)
* Open source tools (recommend some, vegeta etc)
* Build your own

Which one you choose depends on the fund you have access to, the time you have to execute the tests, how often you want to test.

## What metrics do I care about?
Min/max for getting an idea of the range of responses different users could see. Median for seeing what most people will see. Most important is probably the 95th and 99th percentiles (also called p95 and p99). We watch p95 and p99 because we want to see what most people will see and these metrics tend to remove most outliers.

We also care about seeing these metrics over time as the load increases, decreases or remains constant. If any metric is changing at a rate faster than the load you may have performance issues.

## How can I action these metrics?
Based off the metrics you can tell what your slowest operations are and decide to focus performance efforts there. You can also use these metrics to tell whether something is performing as well as it should. If you have a goal to have every endpoint below N milliseconds, you can verify that with these metrics. If you notice that p99 is a lot larger than p95 you might find that you have more outliers than you expected and perhaps you need to focus efforts on reducing that since it's affecting 5% of your users.

## Going further
- Recommended articles
- Recommeded books
- Try to test your own site/API
- Try write your own tool
- Recommended conference talks