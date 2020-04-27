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