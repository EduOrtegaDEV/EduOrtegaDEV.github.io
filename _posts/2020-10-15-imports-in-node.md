---
title: "ECMAScript 6 Imports in Node.JS"
date: 2020-10-15
categories:
  - Node.JS
tags:
  - 
---

Lately I've been into NodeJS, I worked with Node a year ago but back then I was working on a project using a headless CMS and basically what we had to do was just a proxy for the CMS API, so I didn't have the chance to go deep with Node. Now I have some time to study in deep and the first thing I crashed with was the lack of support of ES6 imports (We are using the LTS version of Node so at the moment of writing this is 12.19), and it was kind of frustrating because the first thing that VS Code suggest is to use the new ES6 import feature:

![VS Code suggestion](/assets/images/vscode-express-refactor-suggestion.png)

So if you apply this suggestion you will see this beatiful error when you try to run the app:

~~~
import express from "express";
^^^^^^

SyntaxError: Cannot use import statement outside a module
~~~

So, what can I do in order to have this feature available in my code?, googling a little bit I found that by Node 13 this is an experimental feature (using a special flag) and by Node 14 still [Experimental](https://nodejs.org/api/esm.html#esm_modules_ecmascript_modules) but it doesn't need the flag anymore. This was not an option for me because we are using the LTS version of node, so what can I do?.

## The Solution

Well this is Node, think about anything you want and I can assure you there is a package for it, so this was the case and I found [esm](https://www.npmjs.com/package/esm), a babel-less light-weight solution for enabling imports on our code, just install the package and add `-r esm` to your start script and that's it, real magic!:

![VS Code esm](/assets/images/vscode-esm.png)

Now you'll have full import support, and the best is that when this feature will be fully supported in Node we will need just to delete the flags. Wait, you don't like the flags? esm got you covered, just add a entrypoint file that loads esm before the app:

~~~ javascript
require = require("esm")(module/*, options*/)
module.exports = require("./main.js")
~~~

And that's it, you don't need the flags, just the normal  node app.js.

Hope this helps you, and as always

Happy Coding
