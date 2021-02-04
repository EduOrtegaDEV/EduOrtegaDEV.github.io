---
title: "Javascript vs. Type Script, which one should you use?"
date: 2021-02-03
categories:
  - javascript
tags:
  - javascript
  - typescript
---


If you are into software development industry you might have heard of Typescript, a Javascript superset which enables several attributes like strong types, interfaces and many other, that lead us to write less prone to error code and with object oriented syntax.

Since it's creation back in 2012, the Typescript use has been growing mainly because Google decided to use it as the default language for his Angular framework. If we take a look to the [stateofjs.com](https://2020.stateofjs.com/en-US/technologies/javascript-flavors/) website, we can see that the insteret for using Typescript is growing each year but: what is Typescript and why should I use it?.

![Javascript flavors popularity until 2020](/assets/images/javascript-flavors-popularity-2020.png)

## What exactly is Typescript?
Typescript is a superset of javascript which is an implementation of ecmascript, something like this:

![Schema that shows how ecmascript adds features to javascript, and how typescript adds features to ecmascript](/assets/images/typescript-superset.png)

This all means that Typescript transpiles into Javascript that browsers can understand and excecute, in fact, we can transpile it to an older version of the Ecmascript standard, like Ecmascript 5 which of supported by even Internet Explorer.

## What makes Typescript "superior"?
So having clear what TypeScript is we can see what are his advantages over plain Javascript.

|Feature|Vanilla JS|Typescript|
|---|---|---|
|**Type checking**|Dynamically-typed: The type of a variable is checked during *run-time*, variables are bound to objects at run-time, and it is possible to bind the same variables to objects of different types during execution.|Statically-typed: The type of a variable is known at *compile-time*, once a variable has been declared with a type, it cannot ever be assigned to some other variable of different type and doing so will raise a type error at compile-time.|
|**Generics**|N/A|Enable _types_ (classes and interfaces) to be parameters when defining classes, interfaces and methods.|
|**Structural typing**|N/A|Allows to relate types based solely on their members.
|**Object Oriented Programming**|Prototype based, *syntax sugar* for classes. Interfaces are not supported.|Enables abstract classes, interfaces, visibility modificators and much more.

This is only a brief summary of all the advantages that Typescript has over Vanilla JS, so should we use Typescript in all our projects?, should we doom Javascript?.

## Use the right tool for the job
So, it seems that there all just advantages but since Typescript needs to be transpiled we require time to setup everything, VanillaJS is "universal" to put it in some way, so I would say that for small projects it doesn't worth the effort.

Now, is we are talking about medium or large projects, with a team (several people), Typescript can avoid us errors before deploying and we can take advantage of the characteristics we saw earlier.

So, in conclusion my advice is: use it for medium/large projects, you will see the benefits in short and medium term.
