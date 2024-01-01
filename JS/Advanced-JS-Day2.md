# prototypes

## Object

```js
var arr = [];
// arr is an object from type Array (Array is a constructor function for any object from type Array)

//instance methods: methods that are available on the instance of the object
arr.push(1);
//type methods: methods that are available on the type of the object

var myObj = {};
myObj.//instance methods
Object.//type methods

```

> [!WARNING] Object is a constructor function for any object

```js
Object; // function Object() { [native code] }

//defineProperty is a type method
//defineProperties is a type method
//keys is a type method
//values is a type method
var myObj = { id: 1, name: "ahmed" }; //instance of Object
typeof myObj; // object

myObj.constructor; // function Object() { [native code] }
Object; // function Object() { [native code] }

myObj.constructor.name; // "Object

myObj.id; // 1\
Object.id; // undefined

myObj.keys(); //error
Object.keys(); // error => TypeError: Object.keys called on non-object

x = 10; //global variable have to initialize
//can be done so we

("use strict"); //strict mode to avoid global variables
x = 10; //error

Object.keys(myObj); // ["id", "name"]
//returns an array of all the keys of the object

Object.keys(myObj); // ["id", "name"]
Object.values(myObj); // [1, "ahmed"]
//returns an array of all the values of the object

myObj.hasOwnProperty("id"); // true
//checks if the object has a property with the name "id"
myObj.hasOwnProperty("ID"); // false => case sensitive
myObj.hasOwnProperty("salary"); // false
myObj.hasOwnProperty("name"); // true

//to add protection to the object properties

var testObj = { id: 1, name: "ahmed" };
//we can change the value of the properties
//we can delete the properties
//we can add new properties
testObj; // {id: 1, name: "ahmed"}
testObj.id = 2;
testObj.id = 3;
testObj.salary = 1000;
testObj; // {id: 3, name: "ahmed", salary: 1000}
delete testObj.id; // true
testObj; // {name: "ahmed", salary: 1000}

Object.keys(testObj); // ["name", "salary"]
//enumerable: true

//by default defined properties are deletable(configurable: delete or reconfigure), enumerable:displayed within result,

//defineProperty  used to define a property for an object
//specify descriptor for the property

var test = {};
//defineProperty(obj, prop, descriptor(enumerable, writable, configurable, value))
Object.defineProperty(test, "id", {
  value: 1,
  //   writable: false,
  //   configurable: false,
  //   enumerable: false,
});
//default for all false
test; // {id: undefined}
delete test.id; // false
test.id = 2999; // 1
test; // {id: undefined}

Object.defineProperty(test, "id", {
  value: 1,
  writable: true,
  configurable: false, //default false for all
  enumerable: true,
});
test; // {id: 1}
test.id = 2999; // 2999
test; // {id: 2999} writable: true
Object.keys(test); // ["id"] enumerable: true
delete test.id; // false configurable: false

Object.defineProperty(test, "id", {
  value: 1,
  writable: true,
  configurable: false, //can't delete or reconfigure
  enumerable: true,
});
//can't reconfigure
Object.defineProperty(test, "id", {
  value: 1,
  writable: true,
  configurable: true,
  enumerable: true,
}); // TypeError: Cannot redefine property: id

//more than one property
Object.defineProperties(test, "name", {
  value: "ahmed",
  writable: true,
  configurable: true,
  enumerable: true,
});
test; // {id: 1, name: "ahmed"}

//better way
//defineProperties
Object.defineProperties(test, {
  id: {
    value: 1,
    writable: true,
    configurable: true,
    enumerable: true,
  },
  name: {
    value: "ahmed",
    writable: true,
    configurable: true,
    enumerable: true,
  },
});
test; // {id: 1, name: "ahmed"}

var test = { id: 1 }; //writeable: true, configurable: true, enumerable: true
//we can redefine the property
```

> [!note] set and get clause descriptors

---

### Prototypes

> [!success] Object is the base object for all objects in JS
> It is a constructor function for any object

```js
var myObj2 = new Object();
var myObj = {};

//myObj and myObj2 are instances of Object

//type loader: loads the type of the object when we create an instance of the object

//for any constructor function including Object constructor function property called prototype is created

//prototype refers to an object

//__proto__ refers to the prototype of the object

//constructor within object refers to the constructor function of the object

//__proto__ for object of type Object refers null
//this all for the Object

Object.prototype; //{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}

Object.prototype.constructor; //ƒ Object() { [native code] }

Object.prototype.__proto__; //null

//with any created object has prototype __proto__ refers to the prototype of the object

var test = { id: 1, name: "ahmed" }; //{id: 1, name: "ahmed"}

test.__proto__; //{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}

Object.prototype; //{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}

//the same prototype for both

test.__proto__.__proto__; //null
test.__proto__.constructor; //ƒ Object() { [native code] }

var test2 = { id: 2, name: "ali" }; //{id: 2, name: "ali"}
test2.__proto__; //{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
//refers to Object prototype not the Object constructor function
//Object constructor function has  TYPE methods like keys, values, defineProperty, defineProperties, ...

//Object prototype has instance methods like toString, hasOwnProperty, ...

// prototype chain

test.toString(); //"[object Object]"
test.toStringX(); //error => TypeError: test.toStringX is not a function
// search for toStringX in test object then in test.__proto__ then in test.__proto__.__proto__ then in test.__proto__.__proto__.__proto__ and so on

//we can define toString in test object
test.toString = function () {
  return "test object";
};
test.toString(); //"test object" like overriding
test.__proto__.toString(); //"[object Object]"

//we can edit Object prototype toString
//XXXXX not recommended XXXXX

Object.prototype.toString = function () {
  return "Object prototype";
};
test.toString(); //"test object"

//we can make test.__proto__ refers to another object
var test1 = { id: 1, name: "ahmed" }; //{id: 1, name: "ahmed"}
var test2 = { id: 1, name: "ahmed", address: "cairo" }; //{id: 1, name: "ahmed", address: "cairo"}
//it is like inheritance test2 inherits from test1
//we can make test2.__proto__ refers to test1
//multiple ways to do that
//1- test2.__proto__ = test1;
//2- Object.setPrototypeOf(test2,test1);
//       setPrototypeOf(obj,prototype||null) obj: the object to set its prototype,
// prototype: the prototype to set for the object or null to set it to null

var test2 = { address: "cairo" }; //{address: "cairo"}
test2.id; //undefined
test2.name; //undefined
test2.__proto__ = test1; //{id: 1, name: "ahmed"}
test2.id; //1
test2.name; //"ahmed"
test2.address; //"cairo"
test2.__proto__; //{id: 1, name: "ahmed"}
test2.__proto__.__proto__;
test2.__proto__.__proto__.__proto__; //null

//3- Object.create(prototype) prototype: the prototype to set for the object

// return a new object with prototype set to the prototype passed to the function(empty object its __proto__ refers to the prototype passed to the function)

var test2 = Object.create(test1); //{} empty object its __proto__ refers to test1

//prototype chain
test2.id; //1
test2.name; //"ahmed"

var myObj = {};
myObj.id; //undefined
myObj.id = 1;

//if we set id to test2 it will be set to test2 not to test1
test2.id = 2; //{id: 2}
test2.__proto__.id; //1
test2.id; //2
test1.id; //1

test2; //{id: 2} [[Prototype]]: {id: 1, name: "ahmed"}=> [[Prototype]]: Object.prototype=> [[Prototype]]: null

test1.isPrototypeOf(test2); //true

//if we didn't chained them
test1.isPrototypeOf(test2); //false

Object.isPrototypeOf(test1); //false => Object constructor function is not prototype of test1

Object.prototype.isPrototypeOf(test1); //true => Object prototype is prototype of test1

//instead of having same implementation for all objects we can have it in the prototype of the object

var test = {
  id: 1,
  name: "ahmed",
  print: function () {
    console.log(this.id, this.name);
  },
}; //{id: 1, name: "ahmed", print: ƒ}

var test2 = {
  id: 2,
  name: "ali",
  //   print: function () {
  //     console.log(this.id, this.name);
  //   },
}; //{id: 2, name: "ali", print: ƒ}

Object.setPrototypeOf(test2, test); //{id: 2, name: "ali", print: ƒ}
test2; //{id: 2, name: "ali"}[[Prototype]]: {id: 1, name: "ahmed", print: ƒ}
test2.print(); //2 "ali" => this refers to test2
```

> [!bug] be careful of `window.print()||print()` it will print the page

---

### user defined constructor function

```js
function Student(id, name) {
  this.id = id;
  this.name = name;
  this.print = function () {
    console.log(this.id, this.name);
  };
}

Student.prototype; //Student {}
Student.prototype.__proto__; //Object.prototype
Student.prototype.__proto__.__proto__; //null
Student.prototype.__proto__.constructor; //ƒ Object() { [native code] }

//create an instance of Student
var std1 = new Student(1, "ahmed"); //Student {id: 1, name: "ahmed", print: ƒ}
std1.__proto__; //Student {} prototype
std1.__proto__.constructor; //ƒ Student(id,name){this.id = id;this.name = name;this.print = function(){console.log(this.id,this.name);}}

var std2 = new Student(2, "ali"); //Student {id: 2, name: "ali", print: ƒ}
std2.__proto__; //Student {} prototype
std2.__proto__.__proto__; //Object.prototype
std2.__proto__.__proto__.__proto__; //null
std2.__proto__.constructor; //ƒ Student(id,name){this.id = id;this.name = name;this.print = function(){console.log(this.id,this.name);}}

//better to define the print function in the prototype of the object(Student)
//as print function is the same for all objects
function Student(id, name) {
  this.id = id;
  this.name = name;
}
Student.prototype.print = function () {
  console.log(this.id, this.name);
};
//no print function in the object itself but in the prototype of the object (Student)
var std1 = new Student(1, "ahmed"); //Student {id: 1, name: "ahmed"}[[Prototype]]: Student {print: ƒ}
var std2 = new Student(2, "ali"); //Student {id: 2, name: "ali"}[[Prototype]]: Student {print: ƒ}

std1.print(); //1 "ahmed"
std2.print(); //2 "ali"

Array; //ƒ Array() { [native code] }
//object from type Array doesn't have the array functions like push, pop, shift, unshift, ... in the object itself but in the prototype of the object

Student.sayHello = function () {
  console.log("hello");
}; //this is a type method not an instance method(STATIC METHOD)
//we can't call it on the instance of the object
std1.sayHello(); //error as it searches for sayHello in the object itself then in the prototype of the object then in the prototype of the prototype of the object and so on

Student.sayHello(); //"hello"
```

---

### Inheritance

```js
function Person(id, name) {
  this.id = id;
  this.name = name;
}
function Student(id, name, address, intake) {
  this.id = id;
  this.name = name;
  this.address = address;
  this.intake = intake;
}
//no relation between Person and Student
Person.prototype; //Person {}
Student.prototype; //Student {}
var std1 = new Student(1, "ahmed", "cairo", "2020"); //Student {id: 1, name: "ahmed", address: "cairo", intake: "2020"}

std1 instanceof Student; //true
std1 instanceof Object; //true
std1 instanceof Person; //false

//we can make Student inherits from Person

Person.prototype.printPerson = function () {
  console.log("print person");
};

Student.prototype.printStudent = function () {
  console.log("print student");
};

std1.printPerson(); //error not on the chain of std1
std1.printStudent(); //print student

//1.classical inheritance
// child.prototype  = object from type parent
//Student.prototype = object from type Person

Student.prototype = new Person();
//any created object  has __proto__ refers to prototype
//we lost all the properties of Student previous prototype and we have only the properties of Person.prototype
//__proto__ refers to Person.prototype no longer refers to Student.prototype
//we can't access the properties of Student.prototype

Student.prototype; //Person {id: undefined, name: undefined, printPerson: ƒ} created object from type Person

Student.prototype.__proto__; //Person {printPerson: ƒ, constructor: ƒ}

Student.prototype.__proto__.constructor; //ƒ Person(id,name){this.id = id;this.name = name;}

std1 instanceof Student; //true
std1 instanceof Person; //true
std1 instanceof Object; //true

std1.constructor; //ƒ Person(id,name){this.id = id;this.name = name;}
std1.constructor.name; //"Person"

std1.printPerson(); //"print person"
std1.printStudent(); //error not on the chain of std1

Person.prototype.constructor = Student;

std1.constructor; //ƒ Student(id,name,address,intake){this.id = id;this.name = name;this.address = address;this.intake = intake;}
std1.constructor.name; //"Student"

std1.printPerson(); //"print person"
std1.printStudent(); //error not defined

////////////////////
//we have to move the line of inheritance to the top of the code
////////////////////////

function Person(id, name) {
  this.id = id;
  this.name = name;
}
function Student(id, name, address, intake) {
  this.id = id;
  this.name = name;
  this.address = address;
  this.intake = intake;
}

Person.prototype.printPerson = function () {
  console.log("print person");
};
Student.prototype = new Person();

Person.prototype.constructor = Student;

Student.prototype.printStudent = function () {
  console.log("print student");
};

var std1 = new Student(1, "ahmed", "cairo", "2020"); //Student {id: 1, name: "ahmed", address: "cairo", intake: "2020"}

std1.printPerson(); //"print person"
std1.printStudent(); //"print student"

//////////
// if we removed the repeated properties in the constructor function

function Student(id, name, address, intake) {
  this.address = address;
  this.intake = intake;
  //explicit bind this
  // Person(id,name);//undefined
  Person.call(this, id, name); //1 "ahmed"///VIP step
}

var std1 = new Student(1, "ahmed", "cairo", "2020"); //Student {id: 1, name: "ahmed", address: "cairo", intake: "2020"}

std1.id; //1
std1.__proto__.id; //undefined as we didn't set it in the constructor function

function Employee(id, name, salary) {
  this.id = id;
  this.name = name;
  this.salary = salary;
}

var emp1 = new Employee(1, "ahmed", 1000); //Employee {id: 1, name: "ahmed", salary: 1000}

std1 instanceof Person; //true
emp1 instanceof Person; //false

//to make Employee inherits from Person
Employee.prototype = new Person(); //{id: undefined, name: undefined, printPerson: ƒ}

emp1.__proto__; //Student {address: "cairo", intake: "2020"} prototype :)

//because of this line
//Person.prototype.constructor = Student;
// and if we tried to fix it for Employee we will ruin it for Student

emp1 instanceof Person; //true
std1 instanceof Person; //true

//psudo classical inheritance only for one type inheritance

//2. prototypal inheritance
//prototype of child = prototype of parent
//Student.prototype = Person.prototype

function Person(id, name) {
  this.id = id;
  this.name = name;
}
function Student(id, name, address, intake) {
  this.address = address;
  this.intake = intake;
  Person.call(this, id, name);
}
Person.prototype.printPerson = function () {
  console.log("print person");
};
Student.prototype = Person.prototype;
Student.prototype.printStudent = function () {
  console.log("print student");
};

Student.prototype; //person prototype {printPerson: ƒ, constructor: ƒ}

var std1 = new Student(1, "ahmed", "cairo", "2020"); //Student {address: "cairo", intake: "2020", id: 1, name: "ahmed"}

std1.constructor; //ƒ Person(id,name){this.id = id;this.name = name;}

std1.printPerson(); //"print person"
std1.printStudent(); //"print student"
//but all the objects of type Person will have the printStudent function

//enhanced prototypal inheritance
//child prototype = Object.create(Parent.prototype)
//Student.prototype = Object.create(Person.prototype);
//created object __proto__ refers to the prototype passed to the function
// we can change the constructor of the created object without affecting the constructor of the parent prototype
//then we can add instance methods to the created object

Student.prototype = Object.create(Person.prototype);

Student.prototype.constructor = Student;

Student.prototype.printStudent = function () {
  console.log("print student");
};
var std1 = new Student(1, "ahmed", "cairo", "2020"); //Student {address: "cairo", intake: "2020", id: 1, name: "ahmed"}
std1.constructor; //ƒ Student(id,name,address,intake){this.address = address;this.intake = intake;Person.call(this,id,name);}
std1 instanceof Person; //true
std1 instanceof Student; //true

//make another type to inherit from Person
function Employee(id, name, salary) {
  this.salary = salary;
  Person.call(this, id, name);
}
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;
Employee.prototype.printEmployee = function () {
  console.log("print employee");
};

var emp1 = new Employee(1, "ahmed", 1000); //Employee {id: 1, name: "ahmed", salary: 1000}

std1 instanceof Employee; //false
emp1 instanceof Student; //false
emp1 instanceof Person; //true
emp1 instanceof Employee; //true

///////////
```

#### arguments with arrays

```js
var t;
function myfun() {
  var t = arguments; //Arguments(5) [1, 2, 3, 4, 5, callee: ƒ, Symbol(Symbol.iterator): ƒ]
  //arguments not array object
  arguments.forEach(); //error arguments.forEach is not a function
  console.log(arguments); //Arguments(5) [1, 2, 3, 4, 5, callee: ƒ, Symbol(Symbol.iterator): ƒ]
  Arguments; //ƒ Arguments() { [native code] } constructor function
  Arguments.prototype; //Arguments {constructor: ƒ, callee: ƒ, Symbol(Symbol.iterator): ƒ}

  t.__proto__; //Arguments {constructor: ƒ, callee: ƒ, Symbol(Symbol.iterator): ƒ}

  ////////
  arguments.__proto__ = Array.prototype; //Arguments(5) [1, 2, 3, 4, 5, callee: ƒ, Symbol(Symbol.iterator): ƒ]

  //now we can call array methods
  arguments.forEach(function (item) {
    console.log(item);
  }); //1 2 3 4 5
}
myfun(1, 2, 3, 4, 5);

var p = new Person(1, "ahmed"); //Person {id: 1, name: "ahmed"}
//prevent explicit creation of object from type Person

function Person(id, name) {
  console.log(this); //Person {id: 1, name: "ahmed"}
  //Student {id: 1, name: "ahmed"} //Student {id: 2, name: "ali"}
  //Employee {id: 1, name: "ahmed"} //Employee {id: 2, name: "ali"}
  //   if (this.constructor === Person) {//strict equality check for type then value
  //     throw new Error("can't create object from type Person Abstract ");
  //   }
  if (this.constructor.name === "Person") {
    throw new Error("can't create object from type Person");
  }
  this.id = id;
  this.name = name;
}
```

---

### freessel

```js
//freessel
"use strict"; //to show the error
var myObj = { id: 1, name: "ahmed" }; //{id: 1, name: "ahmed"}

//seal() => can't add new properties, can't delete properties, can edit the value of the properties

delete myObj.id; //true

Object.seal(myObj); //{id: 1, name: "ahmed"}
myObj.name = "ali"; //ali
delete myObj.name; //false
myObj.salary = 1000; //cannot add property salary, object is not extensible

//freeze() => can't add new properties, can't delete properties, can't edit the value of the properties

var myObj = { id: 1, name: "ahmed" }; //{id: 1, name: "ahmed"}

Object.freeze(myObj); //{id: 1, name: "ahmed"}
myObj.name = "ali"; //cannot assign to read only property 'name' of object '#<Object>'
delete myObj.name; //false
myObj.salary = 1000; //cannot add property salary, object is not extensible

myObj = myObj; //we can do this
//so we use const aswell
const myObj = { id: 1, name: "ahmed" }; //{id: 1, name: "ahmed"}

Object.isSealed(myObj); //true
Object.isFrozen(myObj); //true
//there is difference between sealed and frozen
```
