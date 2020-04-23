---
layout: post
title: "Mocking in .NET Core Unit Tests with Moq: Getting Started"
date: 2020-04-23
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

<br/>

# Creating mock objects

We can create mock objects by calling `new Mock<T>()`. For example, if we have a `SystemExample` class that we are testing which takes an `IDependencyExample` as a constructor parameter, we need to pass an `IDependencyExample` object when testing `SystemExample`.

```csharp
public class SystemExample
{
    private readonly IDependencyExample _dependencyExample;

    public SystemExample(IDependencyExample dependencyExample)
    {
        _dependencyExample = dependencyExample;
    }

    public bool Foo()
    {
        return !_dependencyExample.Bar();
    }
}

public interface IDependencyExample
{
    public bool Bar();
}
```

Instead of instantiating a concrete `IDependencyExample`, we can create a mock `IDependencyExample` object to be passed to the `SystemExample` during testing.

```csharp
public class SystemExampleShould
{
    [Fact]
    public void InvertDependencyExampleMethodBar()
    {
        var mockDependencyExample = new Mock<IDependencyExample>();
        var systemExample = new SystemExample(mockDependencyExample.Object);

        Assert.True(systemExample.Foo());
    }
}
```


# Mocking methods

We can choose what the mock object methods return using the `Setup` method. By default, `bool` return types will return `false`.

```csharp
mockSetupExample.Setup(x => x.Bar()).Returns(true);
```

If the method requires a parameter, we can choose to specify different return values based on the parameter passed. 

```csharp
mockSetupExample.Setup(x => x.Foo(1)).Returns(true);
mockSetupExample.Setup(x => x.Foo(0)).Returns(false);
```

Alternatively, we can return the same value for any parameter of the matching type using the `It.IsAny<T>()` feature of Moq.

```csharp
mockSetupExample.Setup(x => x.Foo(It.IsAny<int>())).Returns(true);
```

There are also other methods in `It` which allow specified parameters based on a predicate using `It.Is` or parameters in a list of options using `It.IsIn`. We can filter parameters within a range using `It.IsInRange`. Matching based on regular expressions can be done using `It.IsRegex`.

When we want to mock methods that use `out` parameters, we set the up slightly differently. Instead of specifying `Returns`, we assign a variable our desired return value and pass that variable to the `out` parameter.

```csharp
var bar = true;
mockSetupExample.Setup(x => x.Bar(out bar));
```

# Loose and strict mocks

Moq mock objects by default are loose. This means that they will not throw an exception if a mocked method is called when it hasn't been configured using `Setup`. We can see this when a `bool` return type method is called but not configured, the mock object will return `false`.

We can alternatively instruct the mock object to be strict by specifying `MockBehavior.Strict` as a parameter when creating a the mock object. A `MockException` would be thrown on any invocations on the mock that do not have a corresponding setup.

Loose mocks have less setup code due to their default value. This makes them less brittle in that they are less likely to break when implementation details are changed. Strict mocks should only be used when necessary, with loose mocks preferred for their ease of use.


# Mocking properties

In addition to methods, dependencies can also have properties. Mock objects can be configured to return specific values for properties - either hard coded or from return values. Mock objects can also track their property values over time.

To configure the return value for a property getter, simply use the `Returns` method similar to when configuring a method return value. For example, `mockExample.Setup(x => x.PropertyExample).Returns("An example string!");`

We can also use a function return value instead of a hard coded value: `mockExample.Setup(x => x.PropertyExample).Returns(FunctionExample);` with `FunctionExample` defined within the same class:
```csharp
string FunctionExample()
{
    return "An example string!";
}
```

When properties are nested within other properties, our mocking setup can be simplified by navigating through the properties within the `Setup` method. Moq will then automatically create mocks for the inner properties. For example, `mockExample.Setup(x => x.ParentPropertyExample.NestedPropertyExample).Returns("An example string!");` would avoid us having to create `ParentPropertyExample` mock with the `NestedPropertyExample` property setup to return `"An example string!"`. Instead, Moq does this for us implicitly.

We can change the default behaviour which Moq exhibits for loose mocks by setting the `DefaultValue` property. By default, Moq uses `DefaultValue.Empty`, which returns empty values for value types, arrays, and enumerables, and nulls for other reference types. We can change this to `DefaultValue.Mock`, which instructs Moq to create mocks in place of using nulls if the type can be mocked.

To instruct Moq to track changes on the value of a property during test execution, we call `SetupProperty` and use a lambda function to specify which property we wish to track e.g. `mockExample.SetupProperty(x => x.PropertyExample);`. Alternatively, `SetupAllProperties` will enable tracking changes on all properties for the mock.


# Behaviour verification

When we make assertions against the system under test, we are testing the state of the system after it has completed certain behaviours. This is state based testing as we are looking at the resulting state of the system to determine whether the test should pass.

Behaviour based testing will instead check if the expected methods and properties are called and accessed. We are testing the interactions of the system with its dependencies. We often do this when our dependency has no exposed state to test against, and we instead verify that a certain thing has occurred.

Moq allows verification of method calls through the `Verify` method, specifying the method we're checking through a lambda function e.g. `mockExample.Verify(x => x.MethodExample("A string parameter example"));`. We can also argument match our method parameters using `It.IsAny` e.g. `It.IsAny<string>`. The `Verify` method has an overload which allows us to specify the failure message displayed when the verification fails.

We can specify how many times we expect a method to be called using the `Times` struct. By default, `Verify` checks that a method is called at least once. An example to verify that a method was not called at all: `mockExample.Verify(x => x.MethodExample("A string parameter example"), Times.Never);`

To verify that property getters are being called, Moq has the `VerifyGet` methods on mock objects. This is used similarly to the `Verify` method for method invocation verification.

For property setters, the `VerifySet` method checks if the property is set to the expected value e.g. `mockExample.VerifySet(x => x.PropertyExample = "An expected value");`. We can also use the `It` feature if we want to be less strict about what the actual value is.


# Mock exception throwing

Our mock objects can be set up to throw exceptions when a mocked method is called with certain or all parameters. This can be done using the `Throws` method and specifying the exception type:
```csharp
mockExample.Setup(x => x.Foo()).Throws<Exception>();
```

Alternatively, an exact exception instance can be given:
```csharp
mockExample.Setup(x => x.Foo()).Throws(new Exception("Message example"));
```


# Mock event raising

A manual event can be raised using the `Raise` method on a mock object. An event can also be automatically raised by chaining the `Raise` method to the `Setup` for the mock method.


# Different return values for same method

Using the `SetupSequence` method instead of the `Setup` method allows us to return different values for each addition invocation of the mocked method. We simply chain several `Returns` methods to the end of the `SetupSequence`. For example, we can have a method which returns `true` for the first invocation and `false` for the second like so:
```csharp
mockExample.SetupSequence(x => x.Foo())
    .Returns(true)
    .Returns(false);
```


# Mocking concrete classes

Sometimes it isn't feasible or desired to create an interface solely for the purposes of being able to mock the class. We are able to mock the methods within concrete classes by specifying those methods as `virtual` in order to allow Moq to override the method in the mock object.

We can mock protected virtual methods by referencing `using Moq.Protected` and accessing the `Protected` extension method at the beginning of the configuration. The method details and argument matching syntax are slightly different than for mocking regular methods:
```csharp
mockExample.Protected()
    .Setup<bool>("Foo", ItExpr.IsAny<string>())
    .Returns(true);
```


# LINQ to Mocks

Moq allows for specifying mock behaviours using declarative specification queries. LINQ to Mocks can make mocking much cleaner and readable, especially when there is a lot of setup to do on the mock object.

To create a mock object using LINQ to Mocks, instead of using `new Mock<T>()`, we use `Mock.Of<T>`. This creates a mock of type `T`, so we no longer need to use the `Object` property when passing the mock as a parameter. Instead of using the `Returns` method, we simply use the `==` operator. For each additional configuration, we logical `&&` and add our desired configuration:
```csharp
var mockDependencyExample = Mock.Of<IDependencyExample>(
    x =>
    x.Foo(1) == true &&
    x.Foo(0) == false;
)
```

Finally, should we need to verify invocations on a dependency created using LINQ to Mocks, we can use the `Mock.Get()` method to retrieve the mock object which allows the `Verify` method to be called.