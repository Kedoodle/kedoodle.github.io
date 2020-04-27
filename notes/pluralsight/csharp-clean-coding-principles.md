---
layout: post
title: "Clean Coding Principles in C#"
date: 
permalink: /notes/pluralsight/csharp-clean-coding-principles
---

# Introduction

The average line of code is read ten or more times by fellow developers. We therefore want to write code that is easy to read, understand, and maintain. 

Donald Knuth suggests that programming should be more than just instructing the code to do what is expected:
> Programming is the art of telling another human what one wants the computer to do.

Martin Fowler makes a similar statement:
> Any fool can write code that a computer can understand. Good programmers write code that humans can understand.


# Why write clean code?

1. **Reading is harder than writing**: Programmers are really good at solving problems. The issue is that sometimes we rush to solve these problems and write solutions with ease, but produce code which difficult to read later. We write so much code that after a week, we are no longer able to understand how our solutions work.
2. **Technical debt is depressing**: Writing dirty code creates undue technical debt within the project. This technical debt snowballs and makes the project so cumbersome to work with that attracting new talent becomes difficult and existing talent leave with frustration. 
3. **You're lazy**: If we know that we're lazy, we can be the good kind of lazy. That is, writing clean code to begin with makes it easier and more enticing to work with later.
4. **Sloppy is slow**: We don't have time to be sloppy. The increased frequency of bugs and cost of maintenance mean that we don't have time to clean up old, messy code. By following clean coding practices, we can be both fast and clean.
5. **Don't be a verb**: Writing sloppy code will eventually lead to your colleagues associating you with bad code and unpleasant experiences. By writing clean code, your colleagues are more likely to want to work with you and your code, and recommend you for future opportunities.

Programmers are authors. There are many parallels between programmers and authors. The difference is only in the work that we produce and the methods that we use. Instead of chapters, headings, and paragraphs, we have namespaces, classes, and methods. We want our code to be as cohesive and easy to understand as a well-authored book.
- We practice writing as a profession;
- We write or construct an electronic document or system; and
- We are originators or creators of a theory or plan.


# Right tool for the job

There are many tools available to us as software developers, and each technology has its place, but we must select the right ones for each situation. We must recognise when certain technologies or data structures become inappropriate. Sometimes, we don't even need to write code at all.

Boundaries are where many issues with tooling occur - for example, we should avoid putting JavaScript in `.html` files and HTML in `.js` files. By staying within their own domains, we are able to take advantage of code colouring, syntax checking, separation of concerns, and code reuse. We should not be using strings in one language to create code in another language.

# High signal to noise ratio

We want our code to have the most impact with the least amount of background noise. Our code logic should be easily understandable by revealing intent and minimising noise. To maximise signal, our code should be concise, expressive, and do one thing only.

When reading code, humans are compilers. The problem comes when we consider that most humans can only hold around 7 items in their short-term memory. We should be mindful of this when considering the number of parameters we pass the methods, methods in a class, and simultaneous variables within scope.

Signal naturally decays while noise grows over time - we must refactor often to counteract this. If we do not keep our code clean as we progress, it eventually becomes such a mess that it becomes easier to replace it than it is to clean it.

Bill Gates on lines of code:
> Measuring programming progress by lines of code is like measuring aircraft building progress by weight.

The DRY principle, Don't Repeat Yourself, is all about removing duplication. Duplication decreases signal to noise ratio and increases the number of lines of code to maintain and potential for bugs. When code is duplicated, any maintenance must be repeated across each copy of that code. Clearly, this introduces an unnecessary possibility for human error.


# Self-documenting

The main contributor to code-level documentation is good programming style, not comments. Often when maintaining code, understanding the original intent is the most difficult problem. We want our code to be so expressive that it acts as its own documentation.

The intent should be so obvious that developers new to the team are able to read the code and become familiar with it easily. Using different layers of abstraction will allow the problem and solution to be explored at different levels of detail. Formatting in a friendly and consistent manner aids readability. Using code over comments ensures is expressive without relying on comments to explain ambiguity.


# Naming

Naming aids in our ability to understand the programmer's intent. If authors used random one-letter abbreviations throughout their novels, we would have difficulty understanding what their intent was - we might be able to figure it out using the context around these abbreviations, but it would be much simpler to understand if the words simply were not abbreviated, thereby reducing unnecessary cognitive load. Even worse are names which are misleading. 

Poor name classes are magnets for throwing code into. When we can't be bothered determining where code should go, they end up in poorly named classes. These classes become huge and cumbersome to maintain - we have no idea why they exist and what they do. Classes should have a single responsibility, and the name of the class should reflect that. Good class names are usually nouns, not verbs. They are things that we can instantiate. Specific names are better, and lead to classes that are smaller and more cohesive.

If we're stuck with coming up with a good name, verbalising can help. The rubber ducking technique involves talking through what our problem is and will often lead to coming up with a solution ourselves while explaining the problem. This is because verbalising aids creativity.

Names are there to be honoured. Our methods should avoid side effects. For example, `CheckPassword` should do exactly that. We don't expect `CheckPassword` to also log users out. In this instance, if the method were to also log users out, the name would be misleading and lie to the reader. Our method names should be comprehensive and tell the whole truth rather than hide the truth inside its contents.

There are some smells relating to poor names. If we find ourselves including words like `And`, `If`, or `Or` inside our method names, it is a sign that the method is doing more than one thing. In these instances, we should separate these actions into two or more methods.

Abbreviations used to be used out of necessity - storage was expensive and limited. With modern computers, we no longer have to abbreviate our code. We have ample storage and IDEs have features which afford us the luxury of long, descriptive names. We can type the first few characters and our IDEs display the appropriate items. The problem with abbreviations is that they are ambiguous; there is exists no universal set of abbreviations. Another issue with abbreviations is that they're hard to pronounce, hindering our inner mind "reading" the code as well as our ability to discuss code with the team.

Well-named booleans should sound like they are asking a true or false question. Clean boolean names read very naturally, for example `IsOpen`, `Done`, `LoggedIn`. This becomes especially clear when the boolean is used in context as a predicate:
```csharp
if (LoggedIn)
{
    // do something
}
```

When we are working with opposites, our names should be symmetrical to aid understandability and relatedness. For example, if we are defining speed, `min` and `max` are much better than `slow` and `max`. Asymmetrical names can be difficult and confusing to work with: `on` and `disable`. What do these mean? Are they even related?


# Conditionals

Conditionals should clearly convey intent. We can quite easily see when there is a fork in the execution path, but often we aren't able to understand why these conditionals exist and why we are choosing one path over another. 

Booleans should be compared implicitly. There is no need to explicitly check for equality to `true` or `false` e.g. `if (LoggedIn == true)`. By omitting this check, the statement reads more like plain English. Boolean assignments, where possible, should also be done implicitly. The following code explicitly assigns a boolean:
```csharp
bool havingIceCream;
if (bankAccountBalance > 5)
{
    havingIceCream = true;
}
else
{
    havingIceCream = false;
}
```
Contrasting this with `bool havingIceCream = bankAccountBalance > 5;`, we can see a number of advantages of the implicit assignment. There is no need to declare the variable first and then assign it. There is a lot less code and room for error - we only need to write the variable name once instead of three times. Finally, the code spoken aloud a lot easier in one fluid motion.

Use positive conditionals. Avoid double negatives. `if (!IsNotLoggedIn)` has increased cognitive load and reduced readability compared to `if (LoggedIn)`.

The ternary operator should be used for the same reasons as implicit boolean assignments. These lines of code are cumbersome compared to `int iceCreamPrice = isEmployee ? 0 : 5;`:
```csharp
int iceCreamPrice;
if (isEmployee)
{
    iceCreamPrice = 0;
}
else
{
    iceCreamPrice = 5;
}
```

Conditionals should be strongly typed instead of using string literals. This reduces opportunity for mistyping and the resulting bugs. For example, using `if (obstacle == "boulder")` can cause issues like misspelling `bolder` or casing `BOULDER`. By using `if (obstacle == ObstacleType.Boulder)`, we can define the different types of obstacles once and let our IDE do the memorisation and filling for us. Another advantage is that we define only the valid states - in this case, the different types of obstacles. We are not able to check if the obstacle is a `ObstacleType.Tree` if we do not define `Tree` within the `ObstacleType` enum. People new to the code base are able to get a feel for what the various `ObstacleType` values can be. They can also be more easily searched as they are definitive and unambiguous. 

Magic numbers should also be avoided for similar reasons. They require careful inference and use of the surrounding context in order to understand. We can alleviate this need, and opportunity for error, by defining the numbers as constants. `if (age > 18)` requires the reader to understand the significance of the age 18, whereas defining `const int legalDrinkingAge = 18` and using `if (age > legalDrinkingAge)` removes such need. It is explicit in why the condition exists. Enums can also be used instead of magic numbers - a common use case for enums is to store status or error codes.