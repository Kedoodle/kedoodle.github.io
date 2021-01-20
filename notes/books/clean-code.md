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

Functions are used for organising code. They should be easy to digest; casual reading should give an understanding for the intent of the function. 

Functions allow code to be organised into differing layers of abstraction - at the highest level, the code will read like natural language while at the lowest level, the smallest of implementation details are revealed. By extracting methods out, we are able to keep functions small. This aids in readability. The different levels of abstraction also ensure that functions do one thing and one thing only.

Excessive indentation is a sign that a function can have some code extracted out. By reducing code blocks within `if`, `else`, `while`, `for`, etc. statements down to one line, we can very clearly see what is going on. The one line is a function call with a descriptive name of what it does.

Traversing through levels of abstraction is aided by appropriate placement of functions. A function at a higher level of abstraction should be followed immediately by the lower level functions which it calls. This makes it easy for readers to follow the code top-down.

Switch statements are magnets for bad code. They do many things and are likely to grow in size. The problem is accentuated when several switch statements of similar structure are sprawled throughout the code. This can be solved through polymorphism - we can have a single switch statement which creates the appropriate polymorphic object.

Function names should be chosen to represent what the function does. It sounds obvious, but naming tends to be one of the hardest things get right. When we call our function, it should do what we expect it to do based on its name.

The number of arguments for a function should be minimal - zero, if at all possible. This makes understanding and testing the function much simpler. There are obvious cases where a single argument (monadic) makes sense. Two or more arguments are less common - any more would be exceptional and hard to justify.

Side effects are to be avoided. We should not be making changes or doing things that are not expected. As an example, in a program for making an instant coffee, checking if the water has boiled should not also start brewing the coffee. More on command-query separation [here](/notes/concepts/command-query-separation).


# Chapter 4: Comments

Comments should be used sparingly. There are situations to where comments may be useful, such as for clarification or legal reference, but these are an exception rather than rule. The goal is to write code which is so expressive in its intent that there is no need for a comment to further explain. Each time you think of using a comment, first see if there is a way to convey it within the code itself.

Comments lie. They are an additional piece of scripture which must be updated alongside the code it elaborates upon. Code changes over time but it does not lie - comments can say whatever they like, even things that do not relate to the code. Misleading comments are the worst. They set expectations which are inaccurate and can lead to a significant waste of time and energy.


# Chapter 5: Formatting

Apply your chosen formatting rules consistently throughout the codebase. We want our code to read as coherently as possible, with increasing levels of detail as one read further down. It should be easy to read and be so neat and organised that it is a pleasure. There is no sense in having functional code which is so difficult to read that it can never be maintained.

Blank lines can be used to separate different concepts. We can clearly identify imports, namespaces, classes, and functions when they are vertically well spaced. Similarly, lines that are related should have close vertical association i.e. no unnecessary separating blank lines. 

Local variables should be defined closely to where they are used. There is no point in their existence until they are used. Instance variables, on the other hand, should be grouped together and placed at the top of the class.

Functions should scaffold down, with the caller above the callees. This affords natural readability of the file as the reader knows that a function will be soon defined after it is called in a higher level function.


# Chapter 6: Objects and Data Structures

We can hide implementation details when there isn't a need to expose the finer details of data; sometimes it is better to provide an abstraction. For example, a battery might have a maximum mAh capacity and a current mAh charge. Instead of exposing these two private variables using getters, we can provide a percentage representation of the current battery level.

Sometimes we don't actually want everything to be an object. Procedural code using data structures makes it easy to add new functions which apply on existing data structures, but hard to add new data structures which use existing functions. Object oriented code makes it easy to add new classes without changing existing functions, but hard to add new functions to existing classes.

The Law of Demeter requires that an object should not assume knowledge of the objects it manipulates. Violations are often in the form of chained functions i.e. `a.b().c()` requires `a` to know that the object returned by `b()` has a method `c()`. We should avoid "reaching through" `b()` to access `c()`. Think about why we need `c()` in the first place and figure out if `a` can do the desired action directly without calling `c()` on `a.b()`.

Data transfer objects (DTOs) are simple data structures without any functions. They're useful when communicating with databases.  Active records are like DTOs but have functionality to navigate e.g. `save`. Any additional methods should be held within a separate object which hides the internal data e.g. an instance of the active record.


# Chapter 7: Error Handling

Error handling is needed, but it doesn't have to be everywhere such that it obscures the actual program logic. It can be done cleanly and with grace.

Exceptions should be preferred to return codes because they separate the concerns of logic and error handling by using `try-catch` blocks. A `try` block should be started whenever an exception could be thrown. This allows you to define a scope within your program - your `catch` must leave the program in a consistent state regardless of what happens in the `try`.

Where checked exceptions are available (e.g. Java using `throws`), understand that their use comes at a cost. When throwing an exception at a lower level, we have to declare that exception as part of the signature of every higher level method until the `catch` which handles it. The higher level methods should not have to know about or care about the implementation details of the lower level method. Checked exceptions are not needed to produce robust software, and generally their costs outweigh the benefits.

Exceptions should provide enough information with them to be able to locate where it happens and what the failure is. What was the operation that failed and what caused it to fail? 

Be careful around the normal flow of the program. Sometimes you don't actually want to abort with an exception - it can create clutter while not actually being an error in the business logic. For example, where a value doesn't exist and you want to use a default value instead, you don't need to throw an exception to catch. Simply return the default value in the absence of a defined value. See the [Special Case Pattern](https://martinfowler.com/eaaCatalog/specialCase.html). 

The same pattern as above should be used where returning a `null` object - return a special case object instead e.g. an empty list instead of a `null` when there is no list of objects to return. In general, avoid passing nulls around. They create the need for null checks everywhere and will inevitably lead to `null` exceptions. 


# Chapter 8: Boundaries

Rarely do we control or write all of the code within our software - instead, we depend on other teams (within the organisation or third-party) for certain components. The way this external code is integrated is crucial to keeping our software clean.

Third-parties provide an interface that is broad and can be used by many users in all kinds of environments. When we use a third-party library, we have a specific use-case and will generally only leverage some of the available interface. By way of example, an interface with CRUD actions is useful broadly but we may not want the delete action to be accessible to all.

We can avoid passing implementation detail throughout our software by encapsulating it into a class. The interface of the external dependency would then be hidden at the boundary, with only the one class handling any casting or type management. The class would also be easy to modify if the external interface were to make a breaking change.

When we are exploring the use of a third-party library, time is spent reading documentation and deciding how it might be useful. Further time is then spent writing code to implement the code and determining if it is fit for purpose. Point is, it is hard to learn and implement third-party code. Doing both at the same time is even harder.

An alternative strategy is to write tests to experiment and understand the third-party library. This way, we test the parts that we intend to use and avoid making breaking changes in our existing code - we might not even know if the breaking change is due to the third-party library or our implementation error. These tests are known as learning tests.

Learning tests help us learn the third-party library, but also provide assurance that new releases haven't changed the expected behaviour of the library. Tests at the boundary allow us to upgrade to newer versions of third-party libraries with greater confidence.

Often the other side of the boundary does not yet exist - it is unknown. By defining our own interface based on what we desire the unknown to look like, we can still work with these boundaries until the unknown is known. Once the boundary interface is known, we can write an adapter or seam to bridge the gap between the known and previous unknown. This pattern serves to separate the boundary clearly, with only one place to change if the dependency API changes.

Avoid having too much knowledge of dependencies dispersed throughout your codebase. This minimises rework when dependencies change.


# Chapter 9: Unit Tests

There is a huge push to write automated unit tests, but little direction in the subtleties to writing good tests. The three laws of Test Driven Development (no writing production code until there is a failing unit test, no writing more of a unit test than needed for it to fail, and no writing more production code than needed to pass the currently failing test) alone are not enough to write good tests. Bad tests are as good as no tests, or sometimes even worse. Test code should be maintained alongside production code to keep both clean.

Tests should be as readable as production code, if not more so. We can remove a lot of detail following a Build-Operate-Check (or Arrange-Act-Assert) pattern. The tests reveal intent and avoid misleading the reader when they are refactored to be more succinct and expressive. One way to do this is to build functions and utilities which help to use the production APIs in tests.

One sacrifice that we can make more often in test code is to favour readability over efficiency. For example, in an embedded system, resources are typically quite constrained. We would want to take all reasonable opportunities to reduce resource usage, whereas in a test environment we can make trade-offs fairly liberally. Keep in mind, this is a trade-off for the sake of cleanliness, not to be lazy and write poor quality code.

The number of assertions per test should be minimised; ideally just one. However, when it makes sense to have multiple related assertions, it can be better to do so in the single test to avoid duplicate code. A better guideline is to keep tests to a single concept. If multiple concepts are being tested, split the test into one test per concept.

The **FIRST** acronym helps keep tests clean:

**Fast** - Tests should be fast such that they can be run frequently. Speed should not be a deterrent to running the tests.

**Independent** - Each test should be independent of another. They should be able to be run in any order and one failing test should not cause other tests to fail.

**Repeatable** - The tests should run the same in any environment. That way, the tests are always available and we know that if the tests pass in one environment e.g. local machine, they'll pass in any environment e.g. production.

**Self-validating** - The test either passes or fails, and it knows so. We don't want to have to do manual evaluation of whether the test passed or failed by looking through run logs.

**Timely** - Tests should be written immediately before the code that makes them pass. This avoids the trap of not writing a test at all because it's too hard to test existing code, or writing production code that is hard to test.
