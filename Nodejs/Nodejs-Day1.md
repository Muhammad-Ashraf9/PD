# Node.js

#### REPL

#### Global Object

> [!tip] global object is a global object that contains all the global variables, global functions, and global objects.
>
> - like `window` object in browser
> - `global` is the global object in Node.js
>   > [!bug] we should not declare variable without `var` , `let` or `const` in Node.js, it will be treated as global variable.
> - `__dirname` : It will give the directory name of the current module.

> [!tip] this run on cmd

```bash
node  path/to/file.js
# if cmd is open in the directory where the file is present
# node <filename>.js
node <filename>
# no need to write .js
```

```js
console.log(global); // global object in Node.js
console.log(__dirname); // directory name of the current file(module)

setTimeout(() => {
  console.log("Hello World");
}, 1000);
```

### Modules

> [!bug] problem with adding js files in html
>
> - order of adding js files in html is important (if one file is dependent on another file)
> - Namespace pollution
> - Naming conflicts

> [!warning] Node.js has a module system (CommonJS) to avoid these problems.
>
> - as ES6 didn't have module system, so Node.js created its own module system.

```js
// 1- destructuring
import { add, sub } from "./utils.js";
// 2- import all
import * as utils from "./utils.js"; //name should be same as the file name (nameing convention)

// 3- run the file
import "./utils.js";
```

### CommonJS

> [!tip] `module`
>
> - `module` object is a property of `global` object
> - has a property `exports` which is an empty object
> - has information about the current module

> [!tip] `module.exports` is used to export the module
>
> - `exports` is a reference to `module.exports`
>   > [!warning] there is a condition we need to use `module.exports` to export the module

```js
//module1.js
let projectName = "Node.js";
console.log(module); // module object: {exports: {}}
// module is property of global object (global.module)
function add(a, b) {
  return a + b;
}
module.exports.projectName = projectName;
module.exports.add = add;

//we can also write
exports.projectName = projectName;
```

```js
//module2.js
//import === require

// 1- destructuring
const { projectName, add } = require("./module1"); //no need to write .js only file name (unlike in browser)
console.log(projectName); //Node.js

// 2- entire module
const module1 = require("./module1");

// 3- run the file
require("./module1");
```

> [!example] run module2

```bash
node module2
```

> [!danger] importing (require) module1.js in module2.js will ==run== the module1.js file

> [!tip] `children` property of `module` object
>
> - it is an array of all the modules that are required by the current module
> - it is empty if no module is required by the current module

```js
//module1.js
//we can export with different name
exports.addition = add;

//we can export directly in the declaration
module.exports = {
  projectName,
  addition,
};
```

> [!danger] AGAIN: modules code are encapsulated, and it will run on import (require)
>
> - it will run only once, no matter how many times it is imported

> [!bug] in ES6, module import is hoisted, but in CommonJS, it is not hoisted
>
> - so, if we have code before require, it will run before running the required module code

---

### Default Exports

> [!example]
>
> - when we have a function that runs server, we can export it as default (name is not important)

```js
//module1.js
//default export
module.exports = () => {
  console.log("Hello World");
};
//should come first as it overwrites the previous exports
//should use module.exports not exports

module.exports.projectName = projectName;
module.exports.add = add;

exports.selmy = "selmy"; //won't be exported
```

```js
//module2.js
const module1 = require("./module1");
module1();
```

> [!danger] in case of Default Export
>
> - default export should come first as it overwrites the previous exports
> - should use module.exports not exports

---

# Break

---

### Node Modules

> [!tip] `fs` module
>
> - file system module : to read, write, update, delete files

```js
//app.js

// 1- folder
let module1 = require("./modules/module1");

// 2- nodejs modules || nodejs packages

const fs = require("fs"); // we don't need to write .js or path if it is a built-in module as it is in node_modules folder

//http
const http = require("http");

console.log(http.STATUS_CODES); //status codes: 200, 404, 500, 400, 300

// 3- third-party modules
const express = require("express");
```

> [!warning] not all the modules are built-in, some are third-party modules
>
> - npm (node package manager) is used to install third-party modules
> - [npmjs.com](https://www.npmjs.com/) is the official website to search for third-party modules
> - like: express,...etc

> [!tip] installing third-party modules
>
> - `npm install <module-name>`: `npm i <module-name>`
> - this will create a folder `node_modules` and install the module in it (if it is not present)
> - it will also create a file `package.json` (if it is not present)
> - `package.json` contains information about the project and the third-party modules used in the project

```bash
# on cmd (in the directory where the project is present)
npm i express
```

> [!tip] `package.json`
>
> - when installing a module, it will download the module and its dependencies, and its dependencies' dependencies, and so on
> - we use `package.json` to install all the dependencies of the (modules used in the project)

```js
//app.js
const fs = require("fs"); //this will look for the module in node_modules folder if it is not present it will look for built-in modules
```

> [!danger] `^` vs `~` in `package.json` package version
>
> - Search
> - `^` : it will install the latest version of the package (major version)
> - `~` : it will install the latest version of the package (minor version)

> [!tip] `npm install` vs `npm i`
>
> - `npm install` is used to install all the dependencies of the project in `package.json`
> - `npm i` is used to install all the dependencies of the project

> [!tip] installing multiple modules
>
> - `npm i express mongoose` : to install multiple modules at once

> [!done] installing specific version of a module
>
> - `npm i xxxxxxx@x.xx.xx : to install a specific version of a module

> [!done] uninstalling a module
>
> - `npm uninstall <module-name>` : to uninstall a module
>   > [!bug] Don't remove the module folder manually, it will not remove the module from `package.json`

> [!warning]
>
> - Its recommended to start the project with `npm init` to create `package.json` file
> - `npm init -y` : to create `package.json` file with default values
> - naming convention project name: `kebab-case` (small letters, separated by hyphen)

```json
//package.json
{
  "name": "nodejs-day1",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

> [!done] `npm install express`

```json
//package.json
{
  "name": "nodejs-day1",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1"
  }
}
```

> [!danger] `devDependencies` vs `dependencies`
>
> - `dependencies` : modules that are required to run the project
> - `devDependencies` : modules that are required to develop the project (like: testing, ...etc)
> - `npm i <module-name> --save-dev` : to install a module as a dev dependency
> - `npm i` : to install all the dependencies (both `dependencies` and `devDependencies`)
> - `npm i --production` : to install only `dependencies` (not `devDependencies`)

> [!tip] `npm i -g nodemon`
>
> - `npm i -g <module-name>` : to install a module globally
> - installed modules globally can be used in any project(installed in the system)

### setup project steps

```bash
# project init
npm init -y
# install
npm i express mongoose
# uninstall
npm uninstall express
# install as dev dependency
npm i nodemon --save-dev

# global install
npm i -g nodemon

# production: d
npm i --production

# install all the dependencies
npm i
```

> [!warning] `scripts` in `package.json`
>
> - `scripts` is used to run commands
> - `start` : to run the project "node app"
> - to run the project: `npm start`

```bash
npm start
# is the same as
node app
```

> [!example] `nodemon`
>
> - `npm i nodemon --save-dev` : to install nodemon as a dev dependency
> - `nodemon` is used to run the project and restart the server on any change in the project
> - instead of `nodemon app` we can use `npm test` (in script) to run the project

```json
//package.json
{
  "name": "nodejs-day1",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "start": "nodemon app",
    "test": "nodemon app",
    "deploy": ""
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.7"
  }
}
```

> [!warning] 
> - we need to use `run` with `npm` to run the script if the script name is not `start` or `test`
> - `npm run <script-name>` : to run the script
> - `npm run deploy` : to run the deploy script
```bash
npm run deploy
```

# #Nodejs-lab1
