---
layout: post
title: "Clean Coding Principles in C#"
date: 2020-04-29
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

Use positive conditionals. Avoid double negatives. `if (!IsNotLoggedIn)` has increased cognitive load and reduced readability compared to `if (LoggedIn)`.


# Implicit comparisons and assignments

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

# Avoid magic strings and numbers

Conditionals should be strongly typed instead of using string literals. This reduces opportunity for mistyping and the resulting bugs. For example, using `if (obstacle == "boulder")` can cause issues like misspelling `bolder` or casing `BOULDER`. By using `if (obstacle == ObstacleType.Boulder)`, we can define the different types of obstacles once and let our IDE do the memorisation and filling for us. Another advantage is that we define only the valid states - in this case, the different types of obstacles. We are not able to check if the obstacle is a `ObstacleType.Tree` if we do not define `Tree` within the `ObstacleType` enum. People new to the code base are able to get a feel for what the various `ObstacleType` values can be. They can also be more easily searched as they are definitive and unambiguous. 

Magic numbers should also be avoided for similar reasons. They require careful inference and use of the surrounding context in order to understand. We can alleviate this need, and opportunity for error, by defining the numbers as constants. `if (age > 18)` requires the reader to understand the significance of the age 18, whereas defining `const int legalDrinkingAge = 18` and using `if (age > legalDrinkingAge)` removes such need. It is explicit in why the condition exists. Enums can also be used instead of magic numbers - a common use case for enums is to store status or error codes.


# Intermediate variables and encapsulation of conditionals

When conditionals become complex with several booleans linked together by logical `&&` and `||` operators, we can simplify them and clear up their intention by using intermediate variables and method encapsulation.

An intermediate variable is assigned the value of the result of the entire predicate. For example, the following conditional could use an intermediate variable instead, which removes the need for the reader to infer that the conditional is checking to see if the employee is eligible for employer super guarantee contributions.
```csharp
if (employee.Age >= 18
    && employee.GrossMonthlyIncome >= 450
    && employee.TFN != null)
{
    // do something
}
```
The intermediate variable might look something like this:
```csharp
bool isEligibleForSuperannuation = employee.Age >= 18
    && employee.GrossMonthlyIncome >= 450
    && employee.TFN != null;
```

We can also encapsulate complex conditionals by extracting them into methods. Within these methods, pass parameters and use well-named intermediate variables to determine the final `bool` return value. These layers of abstraction make reading through the code much smoother and allow the reader to avoid reading lines that they do not need to (especially when they're looking for something else entirely).


# Polymorphism over enums

Polymorphism can be used when we notice several repeated switch statements within our code. We can create an `abstract` base class and create several derived classes to eliminate switching logic. This is because each derived class knows exactly what to do based on the previous switch condition.


# Declarative over imperative

We should favour being declarative when possible. LINQ makes it possible to remove the need for many looping structures and intermediate storage vessels in C#. For example, we do not need to loop through a list of `Fruit` objects in order to get all of the rotten bananas as we would below:
```csharp
List<Fruit> rottenBananas = new List<Fruit>();

foreach (var fruit in fruits)
{
    if (fruit.IsRotten && fruit.IsBanana)
    {
        rottenBananas.Add(fruit);
    }
}

return rottenBananas;
```
Instead, we can be declarative and expressive by simply stating what it is we want - all of the rotten bananas.
```csharp
return fruits
    .Where(f => f.IsRotten)
    .Where(f => f.IsBanana);
```


# Table driven methods

Sometimes, writing more code is not the right solution to our problem. Avoid hard coding values into our code with complex nested `else if` statements. Generally, these statements signal that the data would be better stored in an external database. Think insurance premiums for different ages - each time we want to update the premiums, we would have to release a new build of our application. Instead, we can store the premiums and ages in a database and have our code retrieve the values when needed. This way, we can change premiums in our database and our application will retrieve those new values the next time it calculates a premium. 


# Why create functions?

Functions are able to organise code into small, targeted pieces, that are able to be called by name. They act like paragraphs in a book, but for code. There are several reasons why we might create a function:

1. **Duplication**: In order to adhere to the DRY principle, we can extract repeated code into a function which can then be called multiple times.
2. **Indentation**: Several levels of indentation make code hard to read and maintain. The arrow shaped code formed by excessive indentation is caused by having too many distinct paths through the method. Comprehension decreases beyond three levels of nested `if` blocks. We can alleviate this issue by extracting methods (creating different layers of abstraction), failing fast (throw an exception as soon as an unexpected situation which can't be handled occurs), and returning early (return as soon as we know when something is valid or invalid).
3. **Unclear intent**: When our code logic is hard to understand by reading alone, we can create a well-named function which describes what exactly is happening. This also creates another layer of abstraction, aiding speed reading and finding the right pieces of code during maintenance work.
4. **More than one task**: Methods should do one thing only, and do it well. If we find our methods doing several things, we can extract these things into separate functions and call these functions instead. Small, focused functions help aid reading, promote reuse, eases naming and testing, and avoids side-effects.


# Mayfly variables

Variables should be defined when they are needed. If we were to define variables all in one place, it creates undue cognitive load on the reader in the form of having to remember all of the information upfront. Remember the rule of 7 for the number of items the human brain can keep in short term memory at any one time. Without any of the surrounding context, the variables are meaningless. 

Mayflies are organisms with really short lifespans. We want our variables to live only in the scope they are needed, and be left behind once no longer in use. This aids refactoring as we no longer need to consider the implications of every variable on the entire scope of the function. We can create mayfly variables by initialising them just-in-time.


# Less parameters is more

We should strive for 0-2 parameters. This makes code easier to read, understand, and test because the function is doing less. Separate concerns should not be passed into the function as parameters. Flag arguments are a clear sign that we are doing more than one thing.


# Signs that a method is too long

Functions should rarely be longer than 20 lines of code, and almost never over 100 lines. They should also very rarely have more than 3 parameters. Some more specific smells of a function that is becoming too long:
- The method starts needing whitespace newlines and comments to split pieces of logic. Sometimes newlines and comments are necessary, but too many can be a smell that the method is too long.
- If the method requires scrolling through and doesn't fit on the screen, this is a clear indicator. We want our reader to be able to read one autonomous piece of code without having to keep too many things in memory.
- When it becomes difficult to name a function based on what it does, we should check to ensure that it isn't doing more than one thing and refactor into separate functions if needed.
- Complex conditionals should be extracted out to a separate function, providing a clear intent for what needs to be done based on the condition.
- Several layers of abstraction are present. This makes the code harder to digest. We can extract things out to keep the method in one layer of abstraction and aid readability.


# Handling exceptions

There are three types of exceptions, generally speaking - unrecoverable, recoverable, and ignorable. Unrecoverable exceptions include null reference, file not found, and access denied. A recoverable exception could be something like error connecting, in which case we can just try again. An ignorable exception could simply be something which isn't imperative, like a failed logging of a click.

We should only catch exceptions which we can handle. If we can't handle the exception, let the application fail hard and fast. It is dangerous to let the application keep running in an inconsistent state, as it could compromise data, itself, and users.

The body of a `try` block in a `try-catch` statement should be extracted out into its own function, making it clear what is being attempted, and what handling should happen if unsuccessful. 


# Classes

Classes should act like headings which signal what kind of related methods should be inside. The methods should relate to a single, clear responsibility. There are several reasons for creating a class:
1. **New concept**: When there is an object that we wish to model, it makes sense to create a class to represent the object. The object can be an abstract or a real-world concept. Classes which represent abstract concepts are especially valuable, as the provide a concrete representation of the concept.
2. **Low cohesion**: If the methods within an existing class are largely unrelated to one another, this is an indicator that the class should be split into several smaller classes with single responsibilities. Watch out for standalone methods and fields which don't interact with other methods and fields of the class; these methods are an indicator of low cohesion. If we are changing one class often in source control, it might signal the need to refactor into separate classes with higher cohesion.
3. **Promote reuse**: Smaller, more targeted classes are easier to digest and reuse, especially because they do one thing.
4. **Reduce complexity**: Once a part of the problem is solved, the solution details can be hidden away into a class so that the reader no longer has to keep it in mind. We can trust that the class does what it is supposed to do and move onto a different part of the problem.
5. **Clarify parameters**: A group of related variables can be converted into something more concrete in the form of a class, making them easier to work with. 


# Primitive obsession

Passing a bunch of related primitives as parameters to a method usually shows the need for a class with the primitives as properties. Lumping these related data items helps the reader to conceptualise along with the code. The relationship becomes explicit, rather than requiring the user to make the connection. If another property is later added to the encapsulating class, we no long have to change the method signature for every method that requires the use of the new property. The use of a reference aids maintenance as we are now able to search for uses of the class rather than separate primitives.


# Proximity principle

Reading is naturally done top to bottom. We can aid readability by placing related code nearby. This helps the reader find the method being called easily, and prevents unnecessary scrolling. Methods can only be placed once, but can be called several times, but generally we should strive to keep the method definitions nearby their usage.


# The outline rule

Our code should have multiple levels of abstraction so that it looks like an outline. We can then read code at our desired level of abstraction, and only at the lowest level do we see the actual implementation details. 


# Comments

Over-reliance on comments is a code smell. Often, alternatives are better and comments should only be used after considering such alternatives. The existence of comments should be justifiable. Comments can lie when they are not up to date, be redundant, and be a crutch for bad code. That's not to say that comments don't have their place.

Comments are both a necessity and a crutch, but generally we should prefer expressive code over comments. This leads to code which is self-documenting, and the code is more likely to be updated. It is the only source of truth which can be relied upon. If code is changed but the associated comment is not, then we can only tell what is happening by referring to the code. When the code is insufficient in providing the necessary documentation, then a comment is appropriate.

Comments to avoid:
1. **Redundant**: These comments repeat exactly what the code is doing e.g. `var iceCream = new Dessert(); // Instantiate a dessert` or `int i = 0; // Set i to 0`. These comments circumvent the DRY principle and create additional noise which needs to be maintained for no value added. We can assume that the reader is capable of reading code.
2. **Intent**: We can often eliminate comments like `// Check if the ice cream has melted` associated with code `if (dessert.State == 1)` by using well-named constants or enums. Consider `if (dessert.State == State.Melted)` instead. Improved naming, intermediate variables, declaring constants or enums, and extracting conditionals to functions are some of the ways we can avoid intent comments.
3. **Apology**: These comments are excuses for bad code. Instead of making apologetic comments, fix the code before committing or merging or add a `TODO` marker comment if absolutely unable to do so immediately.
4. **Warning**: Often a sign that an area of code needs to be refactored. These are even worse than apology comments because they signal bad code ahead, but do nothing about it and also don't apologise for it. They often exist in the form of `// Don't even bother below - talk to Bob the Builder`, which further discourages the fixing of the code.
5. **Zombie code**: Half-baked code existing in the form of comments disrupts flow and creates questions around why it even exists. It is ambiguous. We become reluctant to touch it because we don't know if the coder that left the comment still wants it. Often zombie code comments exist because developers are wanting to keep it there as a fail-safe in case the new changes don't work. The issue is that the comment creates a liability - more code to maintain. Source control has already solved the need for keeping old code intact - we can simply revert back to previous versions of code in our source control.
6. **Divider**: Using comments as signposts for blocks of code within a method shows that the function is probably too long. Consider extracting methods and breaking the function into smaller pieces instead.
7. **Brace tracker**: Using comments to tell us what closing braces are closing is another smell for the need to refactor code. We should extract out functions to aid readability and reduce cyclomatic complexity.
8. **Bloated header**: Boilerplate headers with filenames, authors, and creation dates are unnecessary stores of information. The filename exists as part of the file, and so do the author and modification timestamps in source control. Having block style headers with aligned characters at the end of each line to border the header just creates more maintenance work. 
9. **Defect log**: There is no need to comment where code has changed and for what reason. These comments are redundant as the information exists in source control. We can see where code changes and for what reason by simply looking at commits. The reader does not need to know that there existed a bug which was fixed 3 years ago.

Some comments are good. Short-lived `TODO` comments make it easy for developers to reach parts of the code where they want to do some additional work, but they should be used sparingly. Often there are well-meaning developers that sprawl `TODO` comments everywhere but never have the time to get around to them. Summary comments are great for providing a high-level overview of what the reader should expect to soon see, but they should only be used to add context when well-named classes and methods alone are not enough to convey intent.


# Boy scout rule

The boy scouts have a rule: "Always leave the campground cleaner than you found it." Robert C. Martin (Uncle Bob) has a similar philosophy towards code:
> Always check a module in cleaner than when you checked it out.