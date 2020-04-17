---
layout: post
title: "Mocking in .NET Core Unit Tests with Moq: Getting Started"
date: 
permalink: /notes/pluralsight/moq-dot-net-core-unit-tests
---

# What is mocking?

When unit testing, we isolate the parts of a system that we want test from the rest of the system. Sometimes we test classes that have dependencies which are hard to set up. Mocking is the use of mock versions of these dependencies to help with this isolation. Instead of using actual production dependencies, we replace these dependencies with test only versions.


# Reasons for mocking

Using mocks can improve the execution speed of our tests for many reasons. Set-up and tear-down, accessing external resources like databases or web services, and complex algorithms are some ways dependencies slow down our tests.

We can also use mocks in place of objects that are yet to be developed. This allows teams to work on different objects without relying on other teams to finish their work first. Additionally, our tests become more reliable because they won't fail when something external to the system under test is broken.

Mocking reduces the cost of development and testing. Some external dependencies may charge based on usage, in which case we can avoid these additional charges when unit testing with mock versions of the dependency. Developers are also able to spend less time setting up and dealing with these dependencies when mocks are used instead.

Tests with dependencies that are non-deterministic can be difficult because they can give different results based on the same inputs. We want our tests to be deterministic such that they don't randomly pass or fail. Using mock objects of non-deterministic dependencies allows us to remove this randomness e.g. we can mock a `Random` object which always generates a particular random number that we want our test case to use.


# Types of test doubles

Test doubles refer to all of the different types of non-real dependencies that we can create and use in test execution instead of their real counterparts. They include dummies, fakes, stubs, and mocks. The term mock is also used in a generic way to refer to any test double object.

Fakes have working implementations but are not suitable for production environments.

Dummies are objects which are created solely to satisfy parameters and can be passed around the unit as needed. However, the dummy objects are never used or accessed in any way.

Stubs provide return values to calls like property getters and non-void methods.

Mocks are used when we want to check that our system under test interacts with the dependency in a certain way. The mock object can verify that its properties are accessed or changed, and methods are called.


# About Moq

[Moq](https://github.com/moq/moq4) (pronounced "Mock-you" or just "Mock") is an open source mocking library for .NET which allows the easy creation of dummy, stub, and mock objects. It is made to be simple to use. It doesn't differentiate between dummies, stubs, and mocks. They are all created in the same way.

Moq can be installed through NuGet. Search for `Moq` and install into your test project (in this example, Moq is installed into the `MarsRover.Tests` xUnit.net test project using Rider's NuGet package manager).

<div align="center">
    <img src="/assets/nuget-moq-search.png" alt="nuget-moq-search.png" title="Searching for Moq in Rider's NuGet package manager"/>
<br/>
<br/>
    <img src="/assets/nuget-moq-install.png" alt="nuget-moq-install.png" title="Installing Moq into MarsRover.Tests in Rider's NuGet package manager"/>
</div>