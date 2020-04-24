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

