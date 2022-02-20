---
title: "Deconstructing in C#"
date: 2022-02-20
categories:
  - c#
  - .net
tags:
  - development
---

Beign a .net developer for a very long time when I was learning NodeJS I was amazed by the flexibility of JS, one of my favourite features of javascript is deconstructing things and I didn't know that we have that in C# as well since it's 7.0 version! (Well kind of), let's take a look.

We can use deconstructuring in tuples or even objects, for tuples we only need to return the value as a tuple and receive the tuple using the deconstructing syntax, like this:

~~~ csharp
public (string firstName, string lastName, int age) GetData()
{
  return (FirstName, LastName, Age);
}

var (firstName, lastName, age) = GetData();

Console.WriteLine($"Hello {firstName} {lastName}, I am {age}");
~~~

If we want to get only the FirstName for instance we need to use discards for the parameters we don't want to use:

~~~ csharp
public (string firstName, string lastName, int age) GetData()
{
  return (FirstName, LastName, Age);
}

var (firstName, _, _) = GetData();

Console.WriteLine($"Hello {firstName}");
~~~

Now, the thing gets more interesting when we implement deconstructuring in classes, we need to define a deconstruct method and then we can use the values as shown above:

~~~ csharp
//Users.cs
namespace Users;

public class User
{
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public int Age { get; set; }
  
  public void Deconstruct(out string firstName, out string lastName, out int age)
  {
    firstName = FirstName;
    lastName = LastName;
    age = Age;
  }
}

//Program.cs
using Users;

var myUser = new User()
{
  FirstName = "Eduardo",
  LastName = "Ortega",
  Age = 37
};

var (firstName, lastName, age) = myUser;
Console.WriteLine($"Hello {firstName} {lastName}, I am {age}");
~~~

Here you can find the whole documentation of this exciting feature.

And as always, happy coding!
