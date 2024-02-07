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
