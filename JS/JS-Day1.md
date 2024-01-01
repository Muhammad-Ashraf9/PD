> [!tip] JavaScript is a Scripting Language
> ==scripting== language means it needs a host (host) to run (browser, nodejs, etc)
> ==Interpreted== language means it is not compiled to machine code
> Loosely typed Language : not strongly typed like C++
> ==Looosely typed== : variable type will be determined according to the value assigned to it
>
> > [!Example] > > `var a;` // a is undefined
> > `var a = 5;` // a is number
> > `var a = "hello";` // a is string

## JavaScript Type System

> [!tip] primitive types
>
> - Undefined
> - Number
> - String
> - Boolean
> - Null
> - Symbol
> - BigInt

> [!tip] Object types
>
> - Date
> - Array
>   -...

> [!tip] Language Objects vs Browser Objects (BOM) vs Html Objects (DOM) vs User Defined Objects
>
> - Language Objects: String, Number, Boolean, Date, Array, Math, RegExp, Function, Object
> - Browser Objects (BOM): Window, Navigator, Screen, History, Location, Document
> - Html Objects (DOM): Document, Element, Attribute, Text
> - User Defined Objects: Any object created by the user

> [!tip] String - Number - Boolean - Math - Date - Array ...
> can be created using new keyword
>
> - String: `var a = "hello";` `var a = new String("hello");`
>   Null and Undefined can not

> [!error] Interpreter
> line1
> line2
> line3:Erorr
>
> > [!success] line1 and line2 will be executed

### Where to write JavaScript and why

> [!tip] Where to write JavaScript and why
>
> - Inline: inside HTML tags
> - Internal: inside `<script>` tag in the head
> - External: in a separate file with .js extension

```HTML
<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
    <script src="myScript.js">
        alert("Hello JavaScript!");//invalid as we have src attribute
        //it is either src or the script
    </script>
  </head>
  <body>
    <h1>My First Heading</h1>
    <p id="demo">My first paragraph.</p>
    <script>
        document.getElementById("demo").innerHTML = "Hello JavaScript!";
      //if it depends on the DOM, it should be written in the body
    </script>
  </body>

```

## Loosely Typed

```HTML

<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
    <script>
        var myVar;//undefined
        //typeof operator
        typeof myVar;//'undefined'=> undefined but string

        //console
        console.log("myVar is " + myVar + " and its type is " + typeof myVar);//myVar is undefined and its type is undefined
        myVar = "hamda";
        console.log("myVar is " + myVar + " and its type is " + typeof myVar);//myVar is hamda and its type is string
        myVar = 5.2;
        console.log("myVar is " + myVar + " and its type is " + typeof myVar);//myVar is 5.2 and its type is number
        myVar = true;
        console.log("myVar is " + myVar + " and its type is " + typeof myVar);//myVar is true and its type is boolean
        myVar = null;
        console.log("myVar is " + myVar + " and its type is " + typeof myVar);//myVar is null and its type is object
    //null is used to reset the value of a variable
    //null is an object

    //
    var myVar1 = 5;//literal declaration
    /*
        1-type
        2-value
        3-address
        4-name
    */
   //value type
   //totaly saved in the stack
   console.log(myVar1);//5 value

   var myVar2 = new Number(5);//constructor declaration
   //myvar2 => Number object
    //myvar2.valueOf() => 5

    var myVar3 =  new Number(5);// myVar3 => Number object //same state but different identity
    myVar3 == myVar2;//false //different identity
    myVar3 = myVar2;//myVar3
    myVar3 == myVar2;//true //both are pointing to the same object
    //any change in any of them will affect the other
    // thats the case with arrays and objects
    var arr1 = [1,2,3];
    var arr2 = [1,2,3];
    arr1 == arr2;//false
    arr1[0] == arr2[0];//true
    arr1[0] = 5;
    arr1[0] == arr2[0];//false every array has its own (identity)
    arr1 = arr2;//arr1 is pointing to the same object as arr2
    arr1[0] = 100;
    arr1[0] == arr2[0];//true affect the other
    //thats not the case  with stack variables
    var a = 5;
    var b = 5;
    a == b;//true
    a = 10;
    a == b;//false
    a = b;
    </script>
  </head>
  <body>
    <h1>My First Heading</h1>
    <p id="demo">My first paragraph.</p>
    <script>
        document.getElementById("demo").innerHTML = "Hello JavaScript!";
    </script>
  </body>
```

---

### variables scope

```js
//2 types of scope
//1- global scope or script scope: use var keyword to declare a variable
var a = 5;
//b;//ReferenceError: b is not defined
//when we declare variable withou var keyword, it will be global but we have to initialize it

//2- function scope(local scope): use var keyword to declare a variable inside a function

//in ES5 we dont have block scope
var testGlobal = 5;
function test() {
  console.log(testGlobal);
  //decalaring without var keyword will make it global
  globalFromFunction = 10;
  var testLocal = 5;
}
test(); //5
console.log(testLocal); //ReferenceError: testLocal is not defined

//NO Block Scope
for (var i = 0; i < 10; i++) {
  console.log(i);
}
console.log(i); //10
```

---

## Number

```js
//Number
//1.literal declaration
var myNum = 5;
conslone.log(typeof myNum); //"number"
//2.constructor declaration
var myNum2 = new Number(5);
console.log(typeof myNum2); //"object"
//NOT THE SAME

//thats why typeof not the best way to check the type
function sum(a, b) {
  //   if (typeof a == "number" && typeof b == "number")
  if (a.constructor.name == "Number" && b.constructor.name == "Number") {
    return a + b;
  }

  //return;//undefined
}
//Every function in JS returns even if we dont write return statement

console.log(sum(5, 6)); //11
console.log(sum(5, new Number(6))); //undefined //despite the fact that we have a number object

new Number(6).constructor.name; //"Number"
(10).constructor.name; //"Number"

var x = 5.123;
//in stack
//type Number
// can call methods although it is not an object
x.toFixed(1); //"5.1" => string

var test = {}; //literal declaration of an object
//empty object except for the inherited properties from Object like toString
test.myFunc(); //TypeError: test.myFunc is not a function
test.myFunc = function () {
  console.log("hello");
}; //assigning a function to a property
test.myFunc(); //"hello"

console.log(test.constructor.name); //"Object"
console.log(typeof test); //"object"

var test2 = {
  myFunc: function () {
    console.log("hello");
  },
};

var x = 5; //can't add properties to it
x.myFunc = (function () {
  console.log("hello");
})(); //myFunc exists only in this line to execute the function we have to call it in the same line as the created object destroyed after the line better to use the constructor
x.myFunc(); //undefined

console.log(x.toFixed(1));
//- .create a Number(type of the variable) object in the heap
// - x becomes a reference to the object
//- .call the method
//- .destroy the object
//- .return the result
//- .assign the result to x
```

---

```js
//Number literals
var myNum = 1024;
var myNum2 = 0x400; //hexadecimal 1024
var myNum3 = 0100; //
var myNum4 = 0b10000000000; //binary 1024
var myNum5 = 1e6; //1*10^6

//IEEE 754 standard
//0.1 + 0.2 = 0.30000000000000004
//0.1 + 0.2 == 0.3 //false
//so to add numbers with decimals we have to use a toFixed()

//1.0000000000000000000001 == 1.0000000000000000000002 //true
```

---

```js
//Number methods
//toFixed()
(111111.123).toFixed(); //"111111"
(111111.123).toFixed() + 50; //"11111150"
//string

//toPrecision()
(100.22).toPrecision(6); //"100.220"
(100).toPrecision(6); //"100.000"
//string

//Number.isInteger()
Number.isInteger(5); //true
Number.isInteger(5.1); //false

//Number.isFinite()
//Infinity
//1/0;//Infinity
//10/-0;//-Infinity
Number.isFinite(5); //true
Number.isFinite(10 / 0); //false
```

>[!success]+ `Number.isFinite()` is the best way to check if a value is a Number.
>>[!bug] `Number.isNaN()` doesn't exclude infinity 
>>`Number.isNaN(10/0);` => false so we use `isFinite` instead.
>>`Number.isFinite('20');` => false => it is not a number

>[!cite] in modern JS we use `Number` object like  `Number.isNaN()`
>to add namespace although we can use them as global functions
>`parseInt, isNaN, isFinite`
### Type Coercion

> [!tip] Type Coercion
> implicitly converting a value from one type to another
>
> - Implicit: done by the interpreter

```js
//Type Coercion
//implicitly converting a value from one type to another
var myVarStr = "5"; //String
var myVarNum = 5; //Number
myVarStr == myVarNum; //true as the interpreter converts the number to string
//Implicit type coercion "10" == "10"
//also happens with + operator
myVarStr + myVarNum; //"55"
"test" + 5; //"test5"

//not always converting to string

3 * "5"; //15 //implicit type coercion to number as * operator is only for numbers
//also happens with - / %

true > 5; //false //implicit type coercion to number as > operator is only for numbers
1 > 2 < 5 > true < false > 100 < 0; //false
//every two operands are compared and the result is compared with the next operand from left to right

// Strict Equality Operator
// ===
// !==
//no type coercion
"5" === 5; //false

//use () to change the order of evaluation
1 > 2 < 5; //false
```

---

### parsing

#### parseInt - parseFloat Algorithm

1. Trim the string
1. if the length is 0 return NaN
1. if the first character is not a number or a sign return NaN
1. check the next characters until you find a non number character

```js
"1234"; //string
parseInt("1234"); //1234 //number
parseFloat("1234.5"); //1234.5 //number
Number("1234"); //1234 //number
//+ operator
+"1234"; //1234 //number

parseInt("  1234  "); //1234
parseInt("  1234A5  "); //1234
parseInt("    "); //NaN
parseInt("    A2123  "); //NaN
```

---

##### Dialogs

```js
//Dialogs
//alert
var test = alert("Hello"); //returns undefined
test; //undefined

//prompt
var res = prompt("Enter your name"); //returns string: if the user clicks  ok, null: if the user cancel or esc
res; //string or null
res = prompt("Enter your name", "hamda"); //default value

//confirm
var res = confirm("Are you sure?"); // cancel or esc => false, ok => true

var fn = prompt("Enter first number", "10");
var sn = prompt("Enter second number", "20");
//var sum = fn + sn;//concatenation
var sum = parseInt(fn) + parseInt(sn); //30
```

---

### NaN

- NaN type is number
- NaN is not equal to anything even itself
- NaN is not equal to NaN

```js
var test;
test + 5; //NaN
10 / 0; //Infinity
0 / 0; //NaN
parseInt(null); //NaN
parseInt(true); //NaN
Infinity + Infinity; //Infinity
Infinity - Infinity; //NaN
Infinity / Infinity; //NaN

isNaN("123"); //false
isNaN("123A"); //true
//check isNan before parseInt
//if false then it is a number and we can parse it

isNaN("   "); //false as it is not using parseInt algorithm but Number or + algorithm
```

### Number , + Algorithm

1. Trim the string
1. if the length is 0 return 0
1. if all the characters are digits return the number
1. else return NaN

```js
Number("1234"); //1234
Number("1234A"); //NaN
Number("   "); //0
parseInt("   "); //NaN
Number("   1234Abc"); //NaN
Number(null); //0
Number(undefined); //NaN
Number(NaN); //NaN
Number(true); //1
parseInt(true); //NaN

+"1234"; //1234
+"1234A"; //NaN
+"   "; //0
var test = +prompt("Enter a number"); //returns number or NaN
//better to use Number in some cases for readability
```

---

### document.write

```js
//document.write
//write to the document

document.writeln("Hello");
document.writeln("Hello");

document.writeln("<pre>Hello");
document.writeln("Hello</pre>");

document.writeln("Hello<br>");
document.writeln("Hello");


var arr = [1,2,3,4,5];
var sum = 0;
document.writel("<table border='1'
cellpadding='10' align='center' cellspacing='10' >");
for(var i = 0; i < arr.length; i++){
    document.write("<tr><td>" + arr[i] + "</td></tr>");
```

### #lab-js1

   