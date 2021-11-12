---
title: "Init only setters"
date: 2021-09-21
categories:
  - C#
tags:
  - init, C#, .net 5, C# 9
---

Back in 2020 the new C# 9.0 was launched along with .NET 5.0 with a bunch of new features, I have barely time to test them so this post is a way to force me to test and try these new features. The first one I want to talk about is Init Only Setters.

Prior object initializers in order to assign properties to a new object we had to make something like this:
~~~ csharp
var me = new Person();
me.name = "Proco";
me.age = 36;
me.title = "Software Developer";
~~~
With the introduction of object initializers this can be done in one line:

~~~ csharp
var me = new Person { name = "Proco", age = 36, title = "Software Developer" }
~~~
Pretty neat uh? We can also of course have a constructor for this purpose like this:
~~~ csharp
class Person {
  public string name { get; set;}
  public int age { get; set;}
  public string title { get; set;}

  public Person(string name, int age, string title){
    this.name = name;
    this.age = age;
    this.title = title;
  }
}
~~~

The problem with this approach is that we need to provide all the parameters when creating a new instance. If we use object initializers this is not a problem because if we don't provide any value to a specific parameter, this will be initialized with the default value.

## The init keyword

Now, what if we want to limit the initialization of a specific parameter to only the instantiation? (Like if we declare a field as read-only or using only get). This is when the Init only setters come into action, `init` is a simple keyword that will make a property settable ONLY at initialization and construction, it will throw an error otherwise:
~~~ csharp
class Person {
  public string name { get; init;}
  public int age { get; init;}
  public string title { get; init;}
}

//Program.cs
static void  Main(string[] args) {
  var me = new Person { name = "Proco", age = 36, title = "Software Developer" }
  me.name = "Edu";
}

//Output: error CS8852: Init-only property or indexer 'Program.Person.name' can only be assigned in an object initializer, or on 'this' or 'base' in an instance constructor or an 'init' accessor
~~~

## When to use
You are probably a bit confused right now about when and where to use that new feature (Like me). The way I see is a matter of mutability and if the parameter is required or not. If your property is:

|  | REQUIRED | OPTIONAL |
|--|--|--|
| **INMUTABLE** | Constructor parameter with Get-only property | Object initializer with Init-only property |
| **MUTABLE** |Constructor parameter with Get/Set property|Get/Set property|

## Conclusion
This is a great addition to the language, and of course, this is a feature built for a more complex improvement such as Records.

And as always: happy coding!
