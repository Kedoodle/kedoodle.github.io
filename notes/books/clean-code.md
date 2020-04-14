---
layout: post
title: "Clean Code: A Handbook of Agile Software Craftsmanship"
date: 
permalink: /notes/books/clean-code
---



# Benefits of automated tests

Using a suite of automated tests can lead to smaller feedback loops and reduced problems in production. These tests are simple to run on demand or on a scheduled basis e.g. overnight or on push. They can detect errors much faster and more reliably than manual testing.


# What are we testing?

We should test behaviour, not implementation details. To do this, we test the public interface rather than coupling our tests to the methods which are called upon.


# Phases of an automated test

1. **Arrange**: Setting up object instances and inputs.
2. **Act**: Execute code that makes a change e.g. set a property.
3. **Assert**: Determine test results.


# Asserts

Asserts make an evaluation against the outcome of a test based on a returned result, final object state, or the occurrence of an event. Each individual assert can either pass or fail. For a test to pass, all asserts must pass; a single fail causes the entire test to fail.

Each test should have at least one assert. When adding more than one assert, ensure that it relates to the test. Do not test multiple unrelated concepts or behaviours under a single test. Separating these into their own test ensures greater clarity when a test fails.

xUnit.net is a unit testing framework which has many different asserts. Using xUnit.net, we are able to make asserts against:
1. **Boolean values**: Methods return true or false. Predicates are true or false.
2. **String values**: Equality, starts with, ends with, contains, matches regular expression. Can change case sensitivity with an optional precision parameter.
3. **Numeric values**: Equality, within a range. An optional precision parameter is useful when testing floating point values.
4. **Null values**: Is `null` or not `null`.
5. **Collections**: Contains or does not contain. Can use predicates to filter e.g. checking a list of weapons for any sword rather than a specific type of sword. We can also use the `Assert.All` method to check each element in the collection against a predicate.
6. **Object types**: Object is or is not a certain type. Can also check for inheritance using the `Assert.IsAssignableFrom` method.
7. **Object instances**: Two objects are or are not the same instance i.e. have the same or different references.
8. **Throws exceptions**: An exception is thrown when the code is run.
9. **Event raised**: An event is raised when the code is run.


# Controlling execution of tests

There are different techniques that we can use in xUnit.net to control our test execution.

By specifying a `[Trait]` attribute, we can categorise tests into groupings of our liking. This can be done for individual tests or at the class level. The test explorer will then be able to group tests according to these traits.

We are also able to skip tests by specifying the `Skip` value with a reason for skipping the test e.g. `[Fact(Skip = "No need to run this test")]`. Generally, tests should only be skipped temporarily. If they are never run, they should be removed from the project altogether so as to avoid confusion around their value.


# Test output

Tests are not able to use `Console.WriteLine` to output information. To write custom output using xUnit.net, we need to reference `Xunit.Abstractions` and create a constructor for the test class with an `ITestOutputHelper` parameter. An instance of an ITestOutputHelper will be injected by xUnit.net automatically. The instance can then be used to write output.

```csharp
public class OutputExample
{
    private readonly ITestOutputHelper _output;

    public OutputExample(ITestOutputHelper output)
    {
        _output = output;
        _output.WriteLine("Log some additional test information");
    }
}
```


# Initialisation and teardown

Each `[Fact]` and `[Theory]` in a class will cause xUnit.net to create an instance of the class. Initialisation duplication within a test class can be reduced by moving the arrange phase into the class constructor and assigning the system under test to a private field.

Teardown can be done by implementing `IDisposable` and placing code that needs to be run after each test in the `IDisposable.Dispose` method.

```csharp
public class InitialisationAndTeardownExample : IDisposable
{
    public InitialisationAndTeardownExample()
    {
        // Do initialisation. Run before each test method.
    }

    public void Dispose()
    {
        // Do teardown here. Run after each test method.
    }
}
```


# Shared context

Sometimes we want to run multiple tests using the same instance, for example when set up costs are high. Within a single class, we can run all of the tests using the same instance of an object by implementing `IClassFixture` and including a parameter of the specified fixture in our constructor. The same fixture instance will be automatically injected by xUnit.net for each test. The fixture properties can then be accessed by our test class.

```csharp
public class FixtureExample : IClassFixture<ExampleFixture>
{
    private readonly ExampleFixture _fixture;

    public FixtureExample(ExampleFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void TestExample()
    {
        // Code using _fixture
    }
}
```

Take care when using shared context as we need to ensure each test can be run independently without breaking another test. The order of tests should not matter and one test should not rely upon another.

It is also possible to share context across multiple classes by defining a class which implements `ICollectionFixture` of the fixture type. This class should use the `[CollectionDefinition]` attribute and define the name of the collection. The test classes which we want to share context between should then also use the same `[CollectionDefinition]` and do not need to individually implement `IClassFixture`.


# Data-driven tests

Often we want to execute the same test code using different test data. We don't want to have to write and maintain these tests as individual duplicated code. We can use the same test method with different test cases and data. xUnit.net provides different ways to specify test data:
1. **Inline attribute**: Local to the individual method and requires the developer to add data at the source code level. 
2. **Property, field, or method**: Shareable across multiple test methods and classes but still requires the developer to add data at the source code level.
3. **Custom attribute**: Same as above.
4. **External data**: Shareable but other people can change the test data as it is stored externally to the source code e.g. business analysts or quality assurance analysts can modify a `.csv` file.

Instead of using `[Fact]`, our data-driven tests will use the `[Theory]` attribute. This tells xUnit.net that we will be executing the method multiple times with different test data. We can use test data by adding parameters to our test method and specifying data following the `[Theory]` attribute.

The simplest way to specify data is using an `[InlineData(params)]` attribute for each test case and specifying the parameters within the attribute. To share test data across multiple methods and classes, we can instead define a public class with a list of object arrays and use the `[MemberData]` attribute.

```csharp
public class InternalTestDataExample
{
    public static IEnumerable<object[]> TestData
    {
        get
        {
            yield return new object[] {0, 1};
            yield return new object[] {1, 2};
            yield return new object[] {2, 3};
        }
    }
}
```

``` csharp
[Theory]
[MemberData(nameof(InternalTestDataExample.TestData),
    MemberType = typeof(InternalTestDataExample))]
```

We can also use the `[MemberData]` attribute to use data from an external source such as a `.csv` file.

```csharp
public class ExternalTestDataExample
{
    public static IEnumerable<object[]> TestData
    {
        get
        {
            string[] csvLines = File.ReadAllLines("TestData.csv");       
            var testCases = new List<object[]>();
            foreach (var csvLine in csvLines)
            {
                IEnumerable<int> values = csvLine.Split(',').Select(int.Parse);
                object[] testCase = values.Cast<object>().ToArray();
                testCases.Add(testCase);
            }

            return testCases;
        }
    }
}
```

``` csharp
[Theory]
[MemberData(nameof(ExternalTestDataExample.TestData),
    MemberType = typeof(ExternalTestDataExample))]
```

Creating custom data attributes can improve the readability of tests while also allowing test data to be shared across test methods and classes. We do this by creating a class which derive from the xUnit.net `DataAttribute` base class. We can then use this custom attribute when specifying data sources in a `[Theory]` test method.

```csharp
public class CustomDataAttributeExample : DataAttribute
{
    public override IEnumerable<object[]> GetData(MethodInfo testMethod)
    {
        yield return new object[] {0, 1};
        yield return new object[] {1, 2};
        yield return new object[] {2, 3};
    }
}
```

```csharp
[Theory]
[CustomDataAttributeExample]
```

The `ExternalTestDataExample` and `CustomDataAttributeExample` techniques can be combined to create a custom data attribute which uses test data from an external source.