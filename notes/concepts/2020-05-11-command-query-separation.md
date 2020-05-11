---
layout: post
title: "Command-query separation"
date: 2020-05-11
permalink: /notes/concepts/command-query-separation
---

# Introduction

Command–query separation is a term, coined by [Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer), which describes the idea that methods should be either a command or a query, but not both.

A command performs an action which changes the state of the system but it does not return a value. A query returns a result but does not change the state of the system.


# Benefits

1. **No side effects**: Our query methods unlikely to cause unintended behaviour. Our command methods are much easier to develop, debug, and maintain.
2. **Confidence**: By distinguishing methods that change system state from those that do not, we are able to use queries with the confidence of knowing that making such method calls will not cause break the system state.
3. **Testing**: Various assertions and checks can be run against the system without modifying the state.