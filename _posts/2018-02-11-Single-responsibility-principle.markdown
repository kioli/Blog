---
title: "SOLID principle 1 - Single responsibility"
last_modified_at: 2017-02-11T16:00:00+01:00
header: 
  overlay_image: /assets/images/post/006-srp/bg-post.jpg
classes: wide
excerpt: "Code should only have one reason to change"
categories:
  - OOP
tags:
  - Java
  - Architecture
---

The first one of the **SOLID** principles is the Single Responsibility Principle and it goes as follows:

> A module should be responsible to one, and only one, actor
>
> <cite>Robert C. Martin</cite>

In a less succint way this sentence says that a module (in Java it would be a class but it can also be intended as a cohesive set of functions and data) should answer to changes requested by only one person or group of people.

It's important to note that this **does not mean that a module should only do one thing**, sure that is also another thing to keep in mind when refactoring classes or functions within them, but that is not what the **SRP** is about.
What is meant here is that the module should be answering only to one other single entity, and serve their purpose and not others.

To better understand it, let's see an example where we have a class `Employee` with two functions, `calculateWage` and `reportHours`.

{: style="text-align:center"}
![employee!][employee]

This class **violates the SRP** because its methods are responsible to different actors.
More specifically `calculateWage` is responding to accounting (**CFO**), whereas `reportHours` is responding to HR (**COO**).

By grouping these functions in the same class now we created a dependency between the CFO and COO.
Let's say for example that both functions depend on an algorithm to calculate the amount of hours an employee works per week. Let's call it the function `calculateHours`.

{: style="text-align:center"}
![employeeFunctions!][employeeFunctions]

Now, if for some reason the CFO determines that overtime hours should be treated differently than normal hours (for salary purposes), then `calculateHours` will need some changes, which will also affect the `reportHours` function used by the COO.

**Note that is pretty easy to overlook that `calculateHours` is called also by the COO.**

This will end up being deployed to production and corrupting the data the COO will gather and use.
This problem grows in proportions with the number of actors involved, and can be exacerbated by merge conflicts arising when different actors modify their area of interest in the same module.

Luckily there is a way to prevent all of this, which is to **separate code that supports different actors**

# Solution

The simplest way to solve this dependency is to extract the behaviours outside the Employee to their own modules, making them directly responsible to only one actor, and having Employee becoming a facade responsible to delegate to the classes with the specific functions

{: style="text-align:center"}
![employeeSolution!][employeeSolution]

In the following posts I will discuss the next principle highlighted by the SOLID acronym, the **Open Closed principle**

[employee]: https://kioli.github.io/assets/images/post/006-srp/employee.jpg
[employeeFunctions]: https://kioli.github.io/assets/images/post/006-srp/employee-functions.jpg
[employeeSolution]: https://kioli.github.io/assets/images/post/006-srp/employee-solution.jpg