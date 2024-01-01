# Functions

1. Function statement
2. Function expression
3. Arrow function
4. anonymous function

```js
// function statement
// start with function keyword
// function name is required
//function declaration
function functionName(arguments) {
  // code block
} //this returns undefined
// function call
functionName(arguments);

// any function without a return statement will return undefined

alert("Hello World"); // returns undefined

function myFunction() {
  return "Hello World!";
}
myFunction(); // returns "Hello World!"

function sum(a, b) {
  return a + b;
}
sum(1, 2); // returns 3
sum(); // returns NaN => undefined + undefined
sum(1); // returns NaN => 1 + undefined
// so we have to validate the arguments
sum(1, 2, 3); // returns 3 => 3rd argument is ignored

function myFunc() {
  return 10;
}
// we can send arguments to a function that doesn't have any parameters
myFunc(1, 2, 3); // returns 10 =>  argument is ignored

// NO OVERLOADING IN JS
// function overloading is when we have multiple functions with the same name but different parameters

function myFunc(a, b) {
  return a + b;
}
function myFunc(a, b, c) {
  return a + b + c;
}
// the second function will override the first one as js interpreted
```

> [!error] No overloading in JS

> [!tip] any function in ==ES5== has 2 members: arguments(like an array:length,[index]) , ==this== : caller of the function

```js

function myFunc(a, b) {
  console.log(arguments);//returns [1,2,3] length:3,...
  console.log(this);
  //arguments.forEach((arg) => console.log(arg));// ERROR: arguments.forEach is not a function its collection like array not array
}//returns undefined

//sum of any number of arguments

function sum(a, b) {//we can send any number of arguments
//return a + b;
  let sum = 0;
  for (let i = 0; i < arguments.length; i++) {
    sum += arguments[i];
  }
  return sum;

}
sum(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);//valid
sum();//valid

//this keyword : refers to the caller object
var name = "Mohamed";
var age = 30;
var student = {
  name: "Ahmed",
  age: 20,
 print: function () {
    return "Name: " + name + ", Age: " + age;
}
};
// . notation
student.print();//returns "Name: Mohamed, Age: 30"
// if name and age are not defined in the global scope
// Error: name is not defined

student.name = "Ali";
student.age = 25;

var student2 = {
  name: "Hassan",
  age: 22,
  print: function () {
    return "Name: " + student2.name + ", Age: " + student2.age;
  }
};
student2.print();//returns "Name: Hassan, Age: 22"

var student3 = {
  name: "Hassan",
  age: 22,
  print: function () {
    return "Name: " + this.name + ", Age: " + this.age;
  }//this refers to the caller object
};

student3.print();//returns "Name: Hassan, Age: 22"
//this refers to the student3 object

//we send arguments to a print and use arguements in the function
var student4 = {
  name: "Hassan",
  age: 22,
  print: function (name, age) {
    console.log(arguments);//returns ["Ali",25]
    return "Name: " + name + ", Age: " + age;
  }
};
student4.print("Ali", 25);//returns "Name: Ali, Age: 25"

//this.id => is is property of the caller object
//id => is variable in the function scope

//; semicolon is optional in JS
//but it is recommended to use it

mufunc(){
    return{//return should be in the same line
        name:"Mohamed",
        age:30
    }
}//this will return the object
mufunc(){
    return//semicolon is added automatically
    {
        name:"Mohamed",
        age:30
    }
}//this will return undefined


//in c#
//public int sum(int a,int b)
// {
//     if(x>y)
//     {
//         return x+y;
//     }//error: not all code paths return a value
// we have to add else
//     else
//     {
//         return x-y;
//     }
// }

```

---

#### Function expression

```js
// function expression
// start with (var) keyword function name = function(arguments) {}

function sum(a, b) {
  return a + b;
}

var sum = function (a, b) {
  return a + b;
};

//there is difference between function statement and function expression but will be discussed later

sum; //f(a,b){return a+b;}

var sum = function temp(a, b) {
  return a + b;
}; //temp used for recursion

sum(1, 2); //returns 3
temp(1, 2); //error: temp is not defined
```

> [!tip] functions in JS are first class objects
>
> 1. can declare as a variable: function expression
> 2. can pass as an argument to another function : callback function
> 3. can return from a function : closure
> 4. can be assigned as a property of an object : method

> [!error] can't be done directly in c#
> use delegates in c# to do that

---

#### constructor function

> [!tip] no classes in es5

```js
/*
class Person{
    //private fields
    int id;
    Public Person(int id)
    {
        this.id=id;
    }
}
*/
//constructor function
// we can use it to create objects with new keyword

function Person(id) {
  return id;
}
var p1 = Per;
var p1 = new Person(1); //returns Person {} object

function Person(id) {
  var result = 0; //local variable
  //in case constructor function : result is private field
  return id;
}
var p1 = new Person(1); //Person{} empty object as no property is added to it
p1.result; //undefined
p1 instanceof Person; //true

//to make result public
function Person(id) {
  var result = 0; //local variable
  this.id = id; //public field : adding property to this ( instance of Person) created object
  this.print = function () {
    return this.id;
  };
  x = 10; //global variable : x is added to the window object
}

var p1 = new Person(1); //Person{id:1,print:f}
p1.id; //1
p1.print(); //1
p1.result; //undefined

function Person(id) {
  function print() {
    console.log("Hello World");
  }
}
var p1 = new Person(1);
print(); //error: print is not defined
Person.print(); //error: print is not defined
p1.print(); //error: p1.print is not a function
//closure: discuss later
```

> [!tip] function declaration vs function expression
> the same for now
>
> > [!bug] they are different in hoisting

---

## Models

### BOM

#### Window Object

> [!tip] Window Object
> is the global object in the browser
> window is the root object of the browser
> window is the parent object of all objects in the browser

```js
//window object

// Nested objects
var student = {
  name: "Mohamed",
  age: 30,
  address: {
    city: "Cairo",
    street: "El Haram",
  },
  courses: [
    { name: "JS", hours: 30 },
    { name: "C#", hours: 30 },
  ],
  print: function () {
    return "Hello World";
  },
}; //this object is added to the window object
window.student; //is the same as student
student; //returns {name: "Mohamed", age: 30, address: {…}, courses: Array(2), print: ƒ}

//same with prompt,alert,confirm

student.name; //Mohamed
student.address; //{city: "Cairo", street: "El Haram"}
student.address.city; //Cairo
student.courses.length; //2
student.courses[0].name; //JS
student.print(); //Hello World
```

> [!tip] JS is object based
> `prompt("")` is a method of window object
> `window.prompt("")` is the same

> [!error] Window vs window || document vs Document || navigator vs Navigator
> window is the object
> Window is the Prototype (constructor function) of the window object

> [!tip] window is object that has many properties and methods
> `alert("")` searches for alert method in the window object
> `window.alert("")` is a method of window object
>
> > [!error] `Alert()` is not a function in window object

```js
function Alert() {
  return "Hello World";
}
window.Alert(); //Hello World
```

> [!tip] window object properties
> window.name : returns the name of the window
> window.closed : returns true if the window is closed

```js
window; //returns Window {window: Window, self: Window, document: document, name: "", location: Location, …}
//window object from Window Prototype


var student = {
  name: "Mohamed",
  age: 30,
  address: {
    city: "Cairo",
    street: "El Haram",
  },
  courses: [
    { name: "JS", hours: 30 },
    { name: "C#", hours: 30 },
  ],
  print: function () {
    return "Hello World";
  },
};

//accessing object properties
student.name; //Mohamed
student["name"]; //Mohamed

// same with arrays
let arr = [1, 2, 3, 4, 5];
arr[0]; //1
arr["0"]; //1
arr.0; //error
//if property name is a number we can't use . notation
arr.length; //5
arr["length"]; //5

arr."length"; //error
arr."0"; //error

//so we use bracket notation [] as general notation
student["name"]; //Mohamed

//we can't loop through object properties using for loop
//we can use for in loop
let arr = [10, 20, 30, 40, 50];
for (var i in arr) {//i is key->index
arr.i;//error
  console.log(i); //returns 0,1,2,3,4
  console.log(arr[i]); //returns 10,20,30,40,50
}
for (var prop in student) {
  console.log(prop); //returns name,age,address,courses,print
  console.log(student[prop]); //returns Mohamed,30,{city: "Cairo", street: "El Haram"},[{…}, {…}],ƒ ()
}

//print all properties of window object
for (var prop in window) {
  console.log(prop);
  console.log(window[prop]);
}

alert;//ƒ alert() { [native code] }
//native code : code written in c++ or c# or any other language

this;//Window {window: Window, self: Window, document: document, name: "", location: Location, …}
//this refers to the caller object
//in this case window object
function myFunc() {
  console.log(this);//Window {window: Window, self: Window, document: document, name: "", location: Location, …}
}
window.myFunc();//Window {window: Window, self: Window, document: document, name: "", location: Location, …}
```

---

# break 10:40

---

#### Window Methods

```js
//window methods
//open
//open a new window
//window.open(url,target,options,replace)=>returns reference to the new opened window
open("child.html", "", "width=300,height=300"); //returns Window {window: Window, self: Window, document: document, name: "", location: Location, …}
function openWindow() {
  childWindow = open("child.html", "", "width=300,height=300");
  console.log(win); //returns Window {window: Window, self: Window, document: document, name: "", location: Location, …}
}
close(); //close the window parent => window.close() => close the parent window
childWindow.close(); //close the child window
function closeWindow() {
  //we have to check if the child window is opened or not
  childWindow.close();
}

//scrollTo child window
function scrollToChildWindow() {
  childWindow.scrollTo(0, 100);
}
//scrollBy child window
function scrollByChildWindow() {
  childWindow.scrollBy(0, 100);
}
//moveBy child window
function moveByChildWindow() {
  childWindow.moveBy(100, 100);
  childWindow.focus(); //to focus on the child window
}
```

````html
<!-- parent.html -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Parent</title>
  </head>
  <body>
    <h1>Parent</h1>
    <button onclick="openWindow()">Open</button>
    <button onclick="closeWindow()">Close</button>
    <button onclick="scrollToChildWindow()">Scroll To</button>
    <button onclick="scrollByChildWindow()">Scroll By</button>
    <button onclick="moveByChildWindow()">Move By</button>

    <script src="parent.js"></script>
  </body>
</html>
```html
<!-- child.html -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Child</title>
  </head>
  <body>
    <h1>Child</h1>
    <button onclick="closeWindow()">Close</button>
    <button onclick="closeParentWindow()">Close Parent</button>
    <button onclick="alertParentStudentName()">
      Alert Parent Student Name
    </button>
    <script>
      function closeWindow() {
        close(); //close the child window
      }
      function closeParentWindow() {
        opener.close(); //close the parent window
      }
      //we can access the parent window using opener
      function alertParentStudentName() {
        alert(opener.student.name);
      }
    </script>
  </body>
</html>
````

> [!tip] properties of window object
> scrollTo(x,y) : scroll to the specified coordinates
> scrollBy(x,y) : scroll by the specified coordinates from the current position
> resizeTo(width,height) : resize the window to the specified width and height

---

### Timers

> [!tip] `setInterval`
> calls a function or evaluates an expression at specified intervals (in milliseconds)
> returns a reference to the timer
> clearInterval(intervalID) : stops the interval
> `setInterval(function,milliseconds)`

> [!tip] `setTimeout`
> calls a function or evaluates an expression after a specified number of milliseconds
> returns a reference to the timer
> clearTimeout(timeoutID) : stops the timeout
> `setInterval(function,milliseconds)`

> [!warning] Timers are APIs not part of JS

> [!bug] Timers are not accurate

> [!error] JS is single threaded

```js
//setInterval(function,milliseconds)

function displayTime() {
  var today = new Date();
  document.title = today.toLocaleTimeString();
}
function startTimer() {
  displayTime(); //to display the time immediately
  setInterval("displayTime()", 1000); //to display the time every second
  timerId = setInterval(displayTime, 1000);
}

function stopTimer() {
  clearInterval(timerId); //to stop the timer
}

//setTimeout(function,milliseconds)
setTimeout("alert('Hello World')", 3000); //to display the alert after 3 seconds
```

---

#### slide show

- html in slideShow.html

```js
//slideShow.js
var images = ["img1.jpg", "img2.jpg", "img3.jpg", "img4.jpg"];
var index = 0;
var img = document.getElementById("img");
var timerId;
function nextImg() {
  index++;
  if (index == images.length) {
    index = 0;
  }
  img.src = images[index];
}
function prevImg() {
  index--;
  if (index < 0) {
    index = images.length - 1;
  }
  img.src = images[index];
}
function startSlideShow() {
  timerId = setInterval(nextImg, 1000);
}
function stopSlideShow() {
  clearInterval(timerId);
}
```

---

### Location Object

> [!tip] Location Object
> is a property of window object

>

```js
//location object

//href
//returns the entire url
// you can go back
location.href; //http://localhost:63342/location.html

//to set you have to change within the same domain
location.href = "http://google.com"; // policy: same origin policy
//recommended to use assign instead of href to change the url

//search
//returns the query string
location.search; //?id=1&name=Mohamed

//reload
//reload the current page
location.reload(); //reload the current page

//replace
//replace the current page with the specified url
//you can't go back not saved in history
location.replace("https://www.google.com/"); //replace the current page with google

// to get query from href
// we can use split
location.href.substr(indexOf("?") + 1).split("&"); //["id=1", "name=Mohamed"]
location.href
  .substr(indexOf("?") + 1)
  .split("&")[0]
  .split("=")[0]; //id
location.href
  .substr(indexOf("?") + 1)
  .split("&")[0]
  .split("=")[1]; //1

//or we can use search
location.search.substr(1).split("&"); //["id=1", "name=Mohamed"]
location.search.substr(1).split("&")[0].split("=")[0]; //id
location.search.substr(1).split("&")[0].split("=")[1]; //1

//assign
//load the specified url
location.assign("https://www.google.com/"); //load google
//can go back saved in history
```

- check history & navigator

---

# #lab-3

- slide show
