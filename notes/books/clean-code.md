---
layout: post
title: "Clean Code: A Handbook of Agile Software Craftsmanship"
date: 
permalink: /notes/books/clean-code
---

# Chapter 1: Clean Code

Code will exist for many years to come, whether written by humans or computer generated. It is the language in which we express requirements. The level of abstraction may continue to increase, but code will always require enough precision to meet requirements.

It is easy to write bad code - perhaps we are burdened by a deadline or just want something done so we can move on. Often, we write bad code with an intention to come back and clean it up. Except that never happens.

**Good code matters because we have to deal with it**. When the code is bad, attempting to implement a new feature or make a change can cause several other things to break. The total cost of maintaining code increases as more features are implemented.

Reading and maintaining bad code becomes nearly impossible to the point where products have to be axed or rewritten from the ground up. This is then a problem of itself - the original code must be maintained, with new features being developed as usual, as the new rewrite happens. The rewrite must then also implement these new features, creating a race of sorts.

There are many excuses as to why good code has become bad, but the truth is professionals should never let code become bad. We must set expectations to non-technical leaders, sales, and marketing. They can only base their promises and deadlines to customers off our commitments to them.

There will always exist deadlines. Writing messy code is an impediment to meeting these deadlines. The only way to meet these deadlines consistently is to write clean code. So what is clean code? It is elegant and efficient, handles all errors, and does one thing well.

There should be no ambiguity for what the code should do - intent and purpose are demonstrated. The code should be sufficiently optimised but not at the expense of readability and understandability. It should be minimal without unnecessary detail, yet have all of the needed abstractions.

Clean code is easy for others to read and enhance. The four rules of simple design should be adhered to: passes the tests, reveals intention, no duplication, and fewest elements. The code will then do exactly what we expect it to do - what it says it will do. 

The key to clean code is not only to write it well to begin with - we must also keep it clean. In fact, we should aim to leave the code cleaner than we found it. **The code that we check in should be cleaner than the code we checked out**.


# Chapter 2: Meaningful Names

Meaniningful names are all about revealing intent. Names should be used to be explicit such that the reader does not have to question such things as why it exists, what it does, and how it is used. It takes time to choose a good name, but it is worth the time saved when later maintaining the code. **Replace names when a better one is found**.

It is easy to mislead and provide disinformation with bad naming. Common abbreviations and programmer specific terms should be avoided when they do not apply e.g. a variable for a collection of objects should not include `List` in the name unless it is actually a `List` object. Better yet, `List` objects can simply be named the plural of what they contain e.g. `fooList` can be `foos`. This means that if we find a more suitable collection type later, such as a `Queue`, there's no need to rename `fooList` to `fooQueue`.

Names should not be too similar to one another. This not only avoids extra time spent by the programmer to distinguish them, but also minimises the chance of using the wrong object or method.

While a compiler may accept meaningless or misspelled names, they can cause confusion later down the track. Do not purposefully misspell or add noise to a name simply because the name you wanted to use already exists e.g. using `a1` and `a2` provide no information as to what they are.

Names should ideally be able to be pronounced. This makes them easier to mentally process, but more importantly, empowers discussion and collaboration. It is much easier to refer to `startTimestamp` than `startyyyymmdd`.

Similarly, they should be searchable. Single-letter names should be restricted to areas of small scope, where it is unlikely that they would need to be searched for (e.g. `i` is appropriate for an incrementing counter for a small `for` loop but not for a product ID code). Numeric constants (magic numbers) should also be avoided for this reason (in addition to revealing intent and minimising errors).

Variables should not be prefixed or named with their type. Modern editing environments will display variable scopes and types, removing any justification for such names as `m_foo` and `fooDictionary`.

We can reduce the cognitive load for readers by keeping names aligned with existing knowledge. Requiring the reader to remember that `fn` refers to the first name and `ln` to the last name is a waste of memory when we can just use `firstName` and `lastName` instead. Why introduce ambiguity?

Consistency is important. Use one word to describe a particular concept e.g. don't use `Delete`, `Remove`, and `Discard` interchangeably within the same project. When one word can be used to describe several concepts, pick a different word for each concept e.g. don't use `Withdraw` to describe both the action of taking money from an account and the backing out of a loan application.

Use names that are familiar to the people that will be reading the code i.e. programmers. Take from the solution domain e.g. if you're using a well-known design pattern, use names stemming from that pattern. Use names from the problem domain where suitable e.g. `BankStatement` is much preferred over `AccountReport`.

The meaning of names often change depending on the surroundings. Add context where necessary to make interpretation simpler - `Name` is too generic and means nothing if it's not also surrounded by `Email` and `Mobile` to form a `Contact`. Don't add superfluous context e.g. `ContactName`, `ContactEmail`, and `ContactMobile` when the three names are already associated by being part of the `Contact` class.


# Chapter 3: Functions


# Chapter 4: Comments


# Chapter 5: Formatting


# Chapter 6: Objects and Data Structures


# Chapter 7: Error Handling

