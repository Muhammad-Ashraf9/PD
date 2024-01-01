### ECMAScript realeses

> [!info] ECMAScript is the standard upon which JavaScript is based.
>
> - ES5 (2009)
> - ES6 (2015)
> - ES7 (2016)
> - ...
>   ECMANext : ES6+

> [!tip] Transpiler
>
> - Babel
>   it is a toolchain that is mainly used to convert ECMAScript 2015+ code into a backwards compatible version of JavaScript in current and older browsers or environments.

### ES6

> [!info] ES6 features
>
> - Modules
> - Classes
> - Arrow functions
> - Promises
> - Block scoping
> - Enhanced object literals

---

##### let and const

> [!example] Hoisting
>
> - Hoisting is a JavaScript mechanism where variables and function declarations are moved to the top of their scope before code execution.
>   `var myVar = 10;` > `function myFunc(){...}`

> [!info] let and const are block scoped
> var is function scoped

> [!tip] let and const are not hoisted
> var is hoisted to the top of the function scope

```js
//Hoisting
// console.log(myVar);//undefined : myVar is hoisted and its value is undefined
//XXXXXXX No Error : myVar is not definedXXXXXXX as it is hoisted
var myVar = 10;
console.log(myVar); //10
// function statement
//function expression

//1.code is scanned for variable declarations
var myVar; //move to the top of the scope (undefined)
//myVar is hoisted
//myVar exists but it is undefined before line of declaration

console.log(i); //undefined
for (var i = 0; i < 10; i++) {
  // i  = 0
  console.log(i); //0,1,2,3,4,5,6,7,8,9
}
console.log(i); //10
//i is hoisted to the top of the function scope
//as var is function scoped not block scoped

function myFunc() {
  //j is hoisted to the top of the function scope but not defined outside the function
  console.log(j); //undefined
  var j = 100;
  console.log(j); //100
}
myFunc();
console.log(j); //j is not defined
```

##### function statement vs function expression

```js
//function statement: function declaration

//start with function keyword followed by function name

//function statement will be introduced as function expression in hoisting section

//var myFunc = function(){...}

//already exists as object from type function

//totally hoisted unlike function expression which the name is hoisted not the function body

myFunc(); //function call before declaration : no error as function declaration is hoisted
function myFunc() {
  console.log("myFunc");
} //function declaration
myFunc(); //function call after declaration normal
```

###### fuction expression

```js
//fuction expression

//var myFunc = function(){...}

// it is considered as variable declaration

var myFunc; // is hoisted to the top of the scope(undefined)

//just the name is hoisted not the function body unlike function declaration

//if we call the function before declaration we will get error

myFunc(); //TypeError: myFunc is not a function
console.log(myFunc); //undefined : myFunc is hoisted and its value is undefined
var myFunc = function () {
  console.log("myFunc");
}; //function expression
myFunc(); //function call after declaration normal
```

###### quiz

```js
console.log(foo()); //10
function foo() {
  function bar() {
    return 10;
  }
  return bar();
}
console.log(foo()); //10
///////////////////////////////

console.log(foo()); //10
function foo() {
  return bar();
  function bar() {
    return 10;
  }
}
///////////////////////////////
function foo() {
  return bar();
  var bar = function () {
    return 10;
  };
}
console.log(foo()); //Error: bar is not a function

///////////////////////////////
console.log(foo()); //Error: foo is not a function
var foo = function () {
  return bar();
  var bar = function () {
    return 10;
  };
};
```

---

##### let and const

```js
//let and const are not hoisted
//block scoped
console.log(x); //ReferenceError: x is not defined
console.log(myVar); //cannot access myVar before initialization

let myVar = 10;
console.log(myVar); //10

//let like var in :
//can be reassigned
let myVar; //can be declared without initialization

 myVar = 20;
 myVar = "hello";
 myVar.length;//5

    myVar = true;
myVar.length;//TypeError: myVar.length
    //const cannot be reassigned and must be initialized
    const myConst;//SyntaxError: Missing initializer in const declaration

    const myConst = 10;
    myConst = 20;//TypeError: Assignment to constant variable.
    ///////////////////////////////
    //cannot be accessed before initialization
    console.log(myConst);//ReferenceError: Cannot access 'myConst' before initialization
    const myConst = 10;

    //const Object
    //myObj  is reference to object in memory not the object itself so we can change the object properties but we cannot change the reference itself as it is const and cannot be reassigned
    const myObj = {
        name:"Ahmed",
        age:30
    };
myObj = 12;//TypeError: Assignment to constant variable.
myObj.name = "Mohamed";//valid
myObj.age = 40;//valid
myObj = {name:"Ali",age:50};//TypeError: Assignment to constant variable.

///freeze object
"use strict";
const myObj = {
    name:"Ahmed",
    age:30
};
Object.freeze(myObj);
myObj.name = "Mohamed";//TypeError: Cannot assign to read only property 'name' of object '#<Object>'
myObj.age = 40;//TypeError: Cannot assign to read only property 'age' of object '#<Object>'

//but with var we can reassign the object itself
var myObj = {
    name:"Ahmed",
    age:30
};
myObj = {name:"Ali",age:50};//valid

```

---

###### block scope

```js
//block scope
//let and const are block scoped
//var is function scoped
console.log(myVar); //undefined
console.log(myLet); //Error: myLet is not defined
if (true) {
  console.log(myVar); //undefined
  console.log(myLet); //ReferenceError: Cannot access 'myLet' before initialization
  var myVar = 10;
  let myLet = 20;
  const myConst = 30;
  console.log(myVar); //10
  console.log(myLet); //20
}
console.log(myVar); //10
console.log(myLet); //Error: myLet is not defined

///////////////////
let myLet = 10;
console.log(myLet); //10
if (true) {
  console.log(myLet); //ReferenceError: Cannot access 'myLet' before initialization
  //as block scoped it has x
  let myLet = 20;
  console.log(myLet); //20
}
console.log(myLet); //10
//

var x = 10;
var x = 20;
//var can be redeclared

///////////
var myVar = 10; //SyntaxError: Identifier 'myVar' has already been declared
let myVar = 20;
//let cannot be redeclared even with var
//////////////
let myLet = 10;
var myLet = 20; //SyntaxError: Identifier 'myLet' has already been declared

/////////////////
var x = 10;
if (true) {
  console.log(window.x); //10
  var x = 20;
  console.log(x); //20
}
console.log(x); //20

///////////
console.log(x); //ReferenceError: x is not defined
for (let i = 0; i < 10; i++) {
  console.log(i); //0,1,2,3,4,5,6,7,8,9
}
console.log(i); //ReferenceError: i is not defined

/////////

var x = 2;
if (x == 2) {
  var x = 3;
  console.log(x); //3
}
console.log(x); //3
```

---

##### backticks

```js
//backticks
// strings
// multiline strings
// strings with variables
// strings with expressions
// `` vs '' single vs ""double quotes


let mystr = "iti
"//SyntaxError: Invalid or unexpected token
//  \n

let mystr = `iti
`;//valid

let mystr = "iti "df"fd"//SyntaxError: Unexpected identifier

let mystr = `iti'df' "dsfsdf"dfsf"df"fd`;//valid

//concatenation
let fname = "ahmed";
let lname = "ali";
console.log("my name is " + fname + " " + lname); //my name is ahmed ali

//string literals
console.log(`my name is ${fname} ${lname}`); //my name is ahmed ali

// we can use expressions
console.log(`my name is ${fname} ${lname} and my age is ${10 + 20}`); //my name is ahmed ali and my age is 30


```

---

##### function part

```js
//default parameters
function sum(x, y, z = 0) {
  return x + y + z;
}
console.log(sum(10, 20, 30)); //60
console.log(sum(10, 20)); //30
console.log(sum(10)); //NaN

function sum(x = 0, y = 0, z = 0) {
  return x + y + z;
}
console.log(sum(10, 20, 30)); //60
console.log(sum(10, 20)); //30
console.log(sum(10)); //10
//left to right
// can't use z before y
// XXXXXX y = z*2 XXXXXX
function sum(x, y = 0, z = y * 2) {
  return x + y + z;
}
console.log(sum(10, 20, 30)); //60
console.log(sum(10, 20)); //50
console.log(sum(10)); //30

function getRandomValue() {
  return Math.random() * 10;
}
function sum(x, y = 0, z = getRandomValue()) {
  return x + y + z;
}
console.log(sum(10, 20, 30)); //60
console.log(sum(10, 20)); //50
console.log(sum(10)); //30
//can declare function as default parameter
function sum(
  x,
  y = 0,
  z = function () {
    return 0;
  }
) {
  //   return x + y + z();//but z not always a function
  return x + y + z;
}

//rest  || spread operator (...)
// arrow functions
```

---

##### rest and spread operator

```js
//rest operator

// ...  => rest operator || spread operator
//... (rest) if wrriten in function declaration as parameter it is rest operator
//... (rest)  in destructuring it is spread operator
//constrains: must be last formal parameter
// can't have more than one rest operator
// rest inside function body will be an array of object

// function doCalculations(op, a, b, c) {
// for (let i = 1; i < arguments.length; i++) {
//     // console.log(arguments[i]);
//     result += arguments[i];//for sum
//     result *= arguments[i];//for multiply

// }
//////////////////refactor////////////////////
// function doCalculations(op) {
//   let result = 0;
//   let arr = [];
//   for (let i = 1; i < arguments.length; i++) {
//     arr.push(arguments[i]);
//   }
//   case: "+":
//     arr.reduce(function (acc, item) {
//       return acc + item;
//     }, 0);
//     case: "*":
//         arr.reduce(function (acc, item) {
//             return acc * item;
//         }, 1);
////////////////////////refactor//////////////////////

// function doCalculations(op) {
//   let result = 0;
//   let arr = [];
//   for (let i = 1; i < arguments.length; i++) {
//     arr.push(arguments[i]);
//   }
//   return eval(arr.join(op));
// }
////////////////////////refactor//////////////////////

console.log(doCalculations("+", 10, 20, 30)); //60
// function doCalculations( ...hamada,op) {//Error: rest paramter must be last formal parameter
// function doCalculations(op, ...hamada,...selmy) {//Error: rest paramter must be last formal parameter
function doCalculations(op, ...hamada) {
  console.log(hamada); //[10,20,30]
  return eval(arr.join(op));
}
//rest operator collect the rest of parameters in array
let myArr = [10, 20, 30, 40, 50];
//if we passed array as parameter  it will be the last element in the array
console.log(doCalculations("+", myArr)); //50 only last element
//spread operator spread the array elements in case of function accept rest we need to use spread operator
console.log(doCalculations("+", ...myArr)); //150

console.log(Math.max(myArr)); //NaN
//as it accept list of numbers not array (...values)
//we have to use spread operator
console.log(Math.max(...myArr)); //50
```

---

###### arrow functions

```js
//arrow functions
//lambda expression
//decal
//1. declared as function expression
//2. not hoisted as it is declared using let as function expression
//3. no this(bind to outer scope) keyword or arguments object
//4. you can't use new keyword with arrow function
//can't use to create object

// let myFunc = function () {
//   console.log("myFunc");
// };
// myFunc(); //myFunc

let myFunc = () => {
  return console.log("myFunc");
};
myFunc; //[Function: myFunc]
myFunc(); //myFunc
//if body has only return statement we can omit the curly braces
let myFunc = () => console.log("myFunc");

let sum = (a, b) => a + b;
sum(10, 20); //30
//if we write curly braces we have to use return keyword
let sum = (a, b) => {
  return a + b;
};
sum(10, 20); //30

let checkEven = (num) => num % 2 == 0;
//if we have only one argument we can omit the parenthesis
// let checkEven = num => num % 2 == 0;

let Student = function (id, name) {
  this.id = id;
  this.name = name;
};
let std = new Student(1, "ahmed"); //Student { id: 1, name: 'ahmed' }
console.log(std); //Student { id: 1, name: 'ahmed' }

/////////XXXXXXXXXXXXXXXXXXXXXXXXXXXXX////////////

let Student = (id, name) => {
  this.id = id;
  this.name = name;
};
let std = new Student(1, "ahmed"); //TypeError: Student is not a constructor
let std = Student(1, "ahmed"); //undefined
// will only call the function

let id = 1;
let obj = {
  id: 1,
  name: "ahmed",
  print: function () {
    // return id + " " + this.name;//id is not defined
    // return id + " " + this.name;//id  = 1
    return this.id + " " + this.name;
  },
};
///////
let obj = {
  id: 1,
  name: "ahmed",
  print: () => {
    return this.id + " " + this.name; //this here is the outer scope this(window)
    // undefined + " " + undefined
  },
};
obj.print(); //undefined undefined

let func = function () {
  console.log(arguments); //[Arguments] { '0': 10, '1': 20, '2': 30 }
};
func(10, 20, 30); //[Arguments] { '0': 10, '1': 20, '2': 30 }

let func = () => {
  console.log(arguments); //ReferenceError: arguments is not defined
};
func(10, 20, 30); //ReferenceError: arguments is not defined
// NO this
// NO arguments
```

---

> html (arrow function)

```js
window.addEventListener("load", function () {
  // let allLis = document.querySelectorAll("li");
  // for (let i = 0; i < allLis.length; i++) {
  //     allLis[i].addEventListener("click", function () {
  //         console.log(this.innerText);//li text
  //     });
  // }
  ///////////////////////
  this; //window
  //   this.document.querySelector("ul").addEventListener("click", function (e) {
  //     console.log(this); //ul
  //     let allLis = document.querySelectorAll("li");
  //     for (let i = 0; i < allLis.length; i++) {
  //       allLis[i].addEventListener("click", function () {
  //         console.log(this.innerText); //li text
  //         this;//li
  //       });
  //     }
  //   });
  /////////////////////////
  this.document.querySelector("div").addEventListener("click", function (e) {
    this.document.querySelector("ul").addEventListener("click", function (e) {
      console.log(this); //ul
      let allLis = document.querySelectorAll("li");
      for (let i = 0; i < allLis.length; i++) {
        allLis[i].addEventListener("click", () => {
          this; //ul as arrow function has no this bind to outer scope
        });
      }
    });
  });
});
```

### bind to outer this using arrow function

```js
function Student(id, name) {
  this.id = id;
  this.name = name;
  var that = this; //
  setTimeout(function () {
    //this here is the window caller of setTimeout
    console.log(this.id + " " + this.name); //undefined undefined
    console.log(that.id + " " + that.name); //1 ahmed
    //we want this to be the object itself

    //we can use arrow function
  }, 1000);
  setTimeout(() => {
    //this here is the outer scope this
    console.log(this.id + " " + this.name); //1 ahmed
  }, 1000);
}
let std = new Student(1, "ahmed"); //undefined undefined
```

> [!danger] Arrow functions bind to outer scope this not caller this

---

#### exponential operator

```js
//exponential operator
// **
// Math.pow(2,3);//8
console.log(2 ** 3); //8
console.log(2 ** 4); //16
let x = 2;
x **= 3;
console.log(x); //8
```

---

#### destructuring

```js
//destructuring
//object destructuring
let myObj = {
  id: 1,
  name: "ahmed",
  age: 30,
};
// let id = myObj.id;
// let name = myObj.name;
// let age = myObj.age;

//destructuring
let { id, name, age } = myObj;
console.log(id); //1
console.log(name); //ahmed
console.log(age); //30

//we have to use the same name of the property case sensitive
let { ID, name, age, city } = myObj;
console.log(city); //undefined
console.log(ID); //undefined

//if we have variable with the same name of the property
let id = 10;
let { id, name, age, city } = myObj;//SyntaxError: Identifier 'id' has already been declared
{id, name, age, city} = myObj;//SyntaxError: Unexpected token '='

({ id, name, age, city } = myObj);//valid
//statement must be wrapped with parenthesis

let {stdId, stdName, stdAge} = myObj;//undefined
//we can use alias
let {id:stdId, name:stdName, age:stdAge} = myObj;
console.log(stdId);//1
console.log(stdName);//ahmed
console.log(stdAge);//30


//factory function
function getObj(obj) {
  return {
    id: obj.id,
    name: obj.name,
    age: obj.age,
  };
}
let obj1 = {
  id: 1,
  name: "ahmed",
  age: 30,
};

let obj2 = getObj(obj1);//same state but different identity

function getObj({id,name,age}) {
  return {
    id,
    name,
    age,
  };//enhanced object literals
  //same as return {id:id,name:name,age:age};
}
let obj2 = getObj(obj1);//{ id: 1, name: 'ahmed', age: 30 }

function getObj({stdId,stdName,stdAge}) {
  return {
  stdId,
    stdName,
    stdAge,
  };//undefined
}
let obj2 = getObj(obj1);//{ stdId: undefined, stdName: undefined, stdAge: undefined }

function getObj({id:stdId,name:stdName,age:stdAge}) {
  return {
  stdId,
    stdName,
    stdAge,
  };
}
let obj2 = getObj(obj1);//{ stdId: 1, stdName: 'ahmed', stdAge: 30 }

function getObj({id,name,age}) {
  return {
  stdId:id,
    stdName:name,
    stdAge:age
  };
}
let obj2 = getObj(obj1);//{ stdId: 1, stdName: 'ahmed', stdAge: 30 }

```

##### array destructuring

```js
//array destructuring
let myArr = [100, 200, 300, 400, 500];
// let x = myArr[0];
// let y = myArr[1];
///////////////////////// array destructuring
let [x, y] = myArr;
console.log(x); //100
console.log(y); //200

let [x, , , y] = myArr;
console.log(x); //100
console.log(y); //400
let [x, y, , ...z] = myArr;
console.log(x); //100
console.log(y); //200
console.log(z); //[400,500]

//concise methods:written only inside object or class
let myObj = {
  id: 1,
  name: "ahmed",
  age: 30,
  //   print: function () {
  //     return this.id + " " + this.name + " " + this.age;
  // },
  print() {
    return this.id + " " + this.name + " " + this.age;
  },
};
 print() {
    return this.id + " " + this.name + " " + this.age;
  },//SyntaxError: Unexpected token '}'

console.log(myObj.print()); //1 ahmed 30
```

> [!warning] computed property
> last few pages in presentation
