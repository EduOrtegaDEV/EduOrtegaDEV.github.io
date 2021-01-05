---
title: "Console.log like a pro!"
date: 2021-01-04
categories:
  - Node.JS
tags:
  - log
  - debug
  - console.log
---

We have a lot a tools to debug our applications but the most beloved is with no doubt using console.log, something like this:

```javascript
if (isOdd) {
  console.log("is odd!");
}
```
But...

![What if I told you that the console object has more methods?](/assets/images/what_if_i_told_you_console.jpg)

We can format the output in a table for instace using the `console.table` methods, like so:

```javascript
const me = {
  name: "Eduardo",
  lastName: "Ortega",
  favLanguage: "C#",
};

console.table(me);
```

```
┌─────────────┬───────────┐
│   (index)   │  Values   │
├─────────────┼───────────┤
│    name     │ 'Eduardo' │
│  lastName   │ 'Ortega'  │
│ favLanguage │   'C#'    │
└─────────────┴───────────┘
```

We have also the count method which basically counts how many times that line of code has been called, perfect if you want to count how many times a method is called:

```javascript
const hoorayGenerator = (name) => {
  console.count("Hoorays count");
  return `Hooray for ${name}`;
};

hoorayGenerator("Eduardo");
hoorayGenerator("Alicia");
hoorayGenerator("Rafael");
```

```
Hoorays count: 1
Hoorays count: 2
Hoorays count: 3
```

You can add an assertion and be sure that that line is only being logged if a specific condition is false:

```javascript
const IsThisTheRealLife = (isRealLife) => {
  console.assert(isRealLife, "It is just fantasy");
};

IsThisTheRealLife(true);
IsThisTheRealLife(false);
```

```
Assertion failed: It is just fantasy
```

And many more, you have the full documentation at [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Console)


Bonus for web developers, you can style the output of our beloved console.log:

```javascript
console.log("%cWHAT!?!", "font-family:arial;color:red;font-weight:bold;font-size:20px");
```
![console.log styled](/assets/images/console.log_styled.png)

Happy coding!
