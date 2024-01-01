> [!warning] Self study
> generators
> iterators
> - task in lab

---

### Classes

> [!tip] Class: ES6
>
> - classes not hoisted
> - you can't deal with classes as functions(unlike constructor functions)
> - member constructor: initialize members fields
> - private or public fields
> - concise methods (no need to write function keyword)
> - accessors (getters and setters)
> - static
> - inheritance : extends (multilevel inheritance: only one parent): super() to call parent constructor

```js
class Person {
  //fields : public or private
  //methods : or override for ingherited methods, setters and getters
  //constructor : only one constructor
  //properties : use accessors set or get
  //instance members :  accessible from object
  //static members : accessible from class
}
///////////////////////////////////
let p1 = new Person(100, "ahmed"); //Error: Classes are not hoisted

class Person {
  // fields :if public: dfine it inside constructor : bind it to this
  ////XXXX DO NOT THIS XXXX
  constructor(name, age) {
    this.name = name; //public
    this.age = age; //public
  }
  //instance methods
  //callable using object from this class
  print() {
    console.log(`name: ${this.name}, age: ${this.age}`);
  }
}

Person(); //Uncaught TypeError: Class constructor Person cannot be invoked without 'new'
//classes is only used for creating objects

//public can be accessed from outside the class
let p1 = new Person("ahmed", 100);
console.log(p1.name); //ahmed
console.log(p1.age); //100
p1.name = "ali";
console.log(p1.name); //ali

p1.print(); //name: ali, age: 100

//public can be set and get from outside the class

//private can't be set or get from outside the class
```

> [!tip] public fields pros vs cons
>
> - pros: easy to access
>   `object.field`//get
>   `object.field = value`//set
> - cons: no protection

---

```js
//private fields
//declared inside class scope using #
//can't be accessed from outside the class
//can be accessed from inside the class
//we can use setters and getters to access private fields
class Person {
  #id = 1; //private
  #name = "new"; //private
  constructor(id, name) {
    this.#name = name; //private
    this.#id = id; //private
  }
  print() {
    console.log(`name: ${this.#name}, id: ${this.#id}`);
  }
  //getters and setters
  setID(id) {
    //we can add validation
    if (id > 0) this.#id = id;
  }
  getID() {
    return this.#id;
  }
  setName(name) {
    if (name.trim().length > 0) this.#name = name;
  }
  getName() {
    return this.#name;
  }
}
let p1 = new Person(100, "ahmed");
//get
console.log(p1.#name); //Uncaught SyntaxError: Private field '#name' must be declared in an enclosing class
//set
p1.#name = "ali"; //Uncaught SyntaxError: Private field '#name' must be declared in an enclosing class

///
//getters and setters
p1.setID(-200);
p1.print(); //name: ahmed, id: 1 not changed as validation failed
p1.setID(200);
p1.print(); //name: ahmed, id: 200 changed as validation passed
p1.setName("    "); //not changed as validation failed
p1.print(); //name: ahmed, id: 200
p1.setName("ali"); //changed as validation passed
p1.print(); //name: ali, id: 200

////////
```

> [!tip] private fields pros vs cons
>
> - pros: protection
> - cons: hard to access
>   as setter and getter can have different names `setID` and `getID`
>   we can use `set id` and `get id` to make it easier to access

> [!tip] properties
>
> - protection from private fields
> - simplicity of public fields
> - declaration as methods `set id (id){}` and `get id(){}`
> - access using variables `object.id`//get and `object.id = value`//set

---

```js
//private fields

class Person {
  #id = 1; //private
  #name = "new"; //private
  constructor(id, name) {
    this.#name = name; //private
    this.#id = id; //private
  }
  print() {
    console.log(`name: ${this.#name}, id: ${this.#id}`);
  }
  //getters and setters
  //   ID(){
  //     return this.#id;
  //   }
  // ID(id){
  //     if (id > 0) this.#id = id;
  //   }//this overwrites the previous method
  // we need to use it as a value
  set id(id) {
    //we can add validation
    if (id > 0) this.#id = id;
  }
  get id() {
    return this.#id;
  }
  set name(name) {
    if (name.trim().length > 0) this.#name = name;
  }
  get name() {
    return this.#name;
  }
}
let p1 = new Person(100, "ahmed");
// p1.ID = 100;// this will add property ID to p1
// we use id as a variable not a method
p1.id = 100; //this will call the setter
p1.id; //this will call the getter

// we can make id read only by removing the setter
p1.id = 200; //this will not change the value

// if we input invalid value in construction  it won't be validated so we need to use setters
let p1 = new Person(-100, "");
// p1.print(); //name: , id: -100

class Person {
  #id = 1; //private
  #name = "new"; //private
  constructor(id, name) {
  //using setter in construction for validation
    this.id = id; //private
    this.name = name; //private
  }
  print() {
    console.log(`name: ${this.#name}, id: ${this.#id}`);
  }
  //getters and setters
  set id(id) {
    //we can add validation
    if (id > 0) this.#id = id;
  }
  get id() {
    return this.#id;
  }
  set name(name) {
    if (name.trim().length > 0) this.#name = name;
  }
  get name() {
    return this.#name;
  }
}
let p1 = new Person(-100, "");
p1.print(); //name: new, id: 1
// default values
```

```js

class Person {
  #id = 1; //private
  #name = "new"; //private
  constructor(id = 1, name = "new") {
    this.id = id; //private
    this.name = name; //private
  }
  print() {
    console.log(`name: ${this.#name}, id: ${this.#id}`);
  }
  //getters and setters
  set id(id) {
    //we can add validation
    if (id > 0) this.#id = id;
  }
  get id() {
    return this.#id;
  }
  set name(name) {
    if (name.trim().length > 0) this.#name = name;
  }
  get name() {
    return this.#name;
  }
  //ovveride toString()
  toString() {
      return this.print();
  }
  
      valueOf() {
          return this.id;
        }
    print() {
        return `name: ${this.#name}, id: ${this.#id}`;
    }
        static getIdentity(){
            return "Person";
        }
}

let p1 = new Person(100, "ahmed");
document.write(p1);//[object Object] as it is an object it calls toString() of Object through prototype chain

// we need to override toString() in class above to print the object(case senstive)
 toString() {
      return this.print();
  }
  ////

let p2 = new Person(200, "ali");

//p1 + p2;// with no toString() it will print '[object Object][object Object]'

//with toString() it will print 'name: ahmed, id: 100name: ali, id: 200'

//what if we need to sum the ids
//we can override valueOf() in class above to return the id
let n = new Number(10);
n.valueOf();//10
p1.valueOf();//Person {#id: 100, #name: "ahmed"}
valueOf() {
    return this.id;
  }
  
p1 + p2;//300

//instance methods declared in class are on the potoype (__proto__)  not on the object

//type scoped methods : static
//instance method
getIdentity(){//not static can be called with objects
    return "Person";
}
p1.getIdentity();//Person

//static method
static getIdentity(){
    return "Person";
}
//can't be called with objects 
p1.getIdentity();//Uncaught TypeError: p1.getIdentity is not a function
//not available on the prototype chain
Person.getIdentity();//Person
```

---

### Inheritance

```js
//inheritance
//extends
//super()

class Person {
  constructor(id, name) {
    this.id = id;
    this.name = name;
  }
  print() {
    return `name: ${this.#name}, id: ${this.#id}`;
  }
}
class Student extends Person {
  //extends => instaceOf Person
  constructor(id, name, grade) {
    this.id = id; //Uncaught ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    this.name = name;
    this.grade = grade;
  }
  print() {
    return `name: ${this.name}, id: ${this.id}, grade: ${this.grade}`;
  }
}
let s1 = new Student(100, "ahmed", 100); //Uncaught ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor

/////////////////
class Student extends Person {
  constructor(id, name, grade) {
  console.log(this); //Uncaught ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor

    super(id, name); //must be first line if we write a constructor 

    this.grade = grade;
  }
//   print() {
//     return `name: ${this.name}, id: ${this.id}, grade: ${this.grade}`;
//   }
//to use parent class print fuction in child class
print() {
    return `${super.print()}, grade: ${this.grade}`;
  }
}
let s1 = new Student(100, "ahmed", 100); //name: ahmed, id: 100, grade: 100
std.constructor.name; //Student
std instanceof Person; //true
std.id; //100
std.print(); //name: ahmed, id: 100 grade: 100 not overriden
//it adds method print to the prototype of student so it gets accessed when searching for print on object from student
//it is overshadowing it access the student print() as it is in the prototype chain

//we can refator print to use super.print()
print() {
    return `${super.print()}, grade: ${this.grade}`;
  }
  
let s1 = new Student(100, "ahmed", 100); //created Person instance implicitly both constructors

let p1 = new Person(100, "ahmed");//created Person instance explicitly

//check this.constructor.name on both constructors
//Person
//Student
class Person {
  constructor(id, name) {
    if (this.constructor.name == "Person") throw new Error("can't create instance of Person");
    this.id = id;
    this.name = name;
    }
    print() {
        return `name: ${this.name}, id: ${this.id}`;
      }
    }
    class Student extends Person {
      constructor(id, name, grade) {
        super(id, name); //must be first line
        this.grade = grade;
      }
      print() {
        return `${super.print()}, grade: ${this.grade}`;
      }
    }
    let s1 = new Student(100, "ahmed", 100);
    let p1 = new Person(100, "ahmed");//Uncaught Error: can't create instance of Person

    //new.target.name if we have derived class thats a base class for another class we want to check and stop the cycle before calling super() so we use new.target.name instead of this.constructor.name
    // as this should be after calling super()
    class Person {
      constructor(id, name) {
        if (new.target.name == "Person") throw new Error("can't create instance of Person");
        this.id = id;
        this.name = name;
        }
        print() {
            return `name: ${this.name}, id: ${this.id}`;
          }
        }
        class Student extends Person {
          constructor(id, name, grade) {
            super(id, name); //must be first line
            this.grade = grade;
          }
          print() {
            return `${super.print()}, grade: ${this.grade}`;
          }
        }
        let s1 = new Student(100, "ahmed", 100);
        let p1 = new Person(100, "ahmed");//Uncaught Error: can't create instance of Person
```



> [!danger] class has implicit constructor
> but if we define a constructor it will override the implicit one
> so we need to call super() to call the parent constructor

---

### class expression

```js
//class expression
let Person = class {
  constructor(id, name) {
    this.id = id;
    this.name = name;
  }
  print() {
    return `name: ${this.name}, id: ${this.id}`;
  }
};
```

---

### Modules

> [!warning] need for modules
>
> - naming conflicts
> - namespace pollution

> html in lecture(external.html)

```js
document.querySelector("p").innerHTML = "hello"; //dependent on dom
//js in header => p element not found
//we can put it in the end of body or use onload event
// but we want to run it in its position in the html(head)
console.log("hello"); //independent from dom
//1.default(tradition)
// - html interpreter => js download => js run => html interpreter

//2. async
// - html interpreter won't stop for downloading js
// work in parallel
// <script async src="script.js"></script>
//load script.js in parallel with html
//stop with only with the run
// works only with small html and large js
// so it depends => could work or not

//3. defer
// - html interpreter won't stop for downloading js
// <script defer src="script.js"></script>
//load script.js in parallel with html
// won't run js until html is loaded

// defer vs async

// if  we have multiple js files
// we need to load them in order as they depend on each other
//defer will load them in order
// async will run the which got downloaded first

// defer and async works only with external js files
// not inline js

// <script defer>document.querySelector("p").innerHTML = "hello";</script>; //won't work
```

---

> [!danger] namspace pollution
>
> we can use IIFE to avoid namespace pollution
>
> > [!success] Modules
> >
> > - all members are private by default in modules
> > - we need to export them to be public
> > - any exported member is read only
> > - don't need to load other modules to use it

```js
//modules
//import
//export
//////XXXXXXXXX we must use them in order XXXXXXXXX
<script src="module1.js"></script>
<script src="module2.js"></script>

// <script  src="module1.js"></script>
// we don't need  module1.js as it is used in module2.js
<script  src="module2.js"></script>
// if we didn't specify type="module" it will be treated as script

<script type="module" src="module2.js"></script>
//module is defer by default
//this loads dependencies module1.js
//so we add only module2.js in html

//module1
// let namesarr = ["ahmed", "ali", "mohamed"];
export let fname = "ahmed";//we dont need to export it as it is not used outside the module
//private : can't be accessed from outside the module
export let namesarr = ["ahmed", "ali", "mohamed"];//we need to export it as it is used outside the module
//public : can be accessed from outside the module
// we have to import it to use it
export class Person {
  constructor(id, name) {
    this.id = id;
    this.name = name;
  }
  print() {
    return `name: ${this.name}, id: ${this.id}`;
  }
}
function sum(a, b) {
  return a + b;
}
function sub(a, b) {
  return a - b;
}
///////////////////////////////////////////////
//module 2
//import
// we have to import it with the same name
import { namesarr, Person,fname as hamadaName } from "./module1.js";//always start with ./ or ../ (relative path)
//we have to specify the extension .js
//we can use alias to avoid naming conflicts and same Person

//we choose what to import and what to export no need to import all

// let fname = "ahmed";//Error: Duplicate declaration "fname"
//assuming that module 1 is loaded before module 2
namesarr.forEach((n) => console.log(n));
let p1 = new Person(100, "ahmed");
console.log(p1.print());

```

> [!tip] named export vs default export
>
> - only one default export per module `export default`
> - we can have multiple named exports per module `export`
>
> - we can import default export with any name `import anyName from "./module1.js";`
>   > [!error] we can't export multiple default exports

```js
//to import all from module1
import * as m1 from "./module1.js";
m1.namesarr.forEach((n) => console.log(n));
```

> [!info] we can export multiple named exports as one object
>
> `export {sum,sub,Person as Human, namesarr as names};`
>
> > [!tip] we can import them as one object
> > `import * as m1 from "./module1.js";` > > `m1.names.forEach((n) => console.log(n));` > > `let p1 = new m1.Human(100, "ahmed");`

> [!bug] imported members are read only
>
> > [!danger] read only 
> > `m1.fname = "ali";`//Error:
> > `Uncaught TypeError: Cannot assign to read only property 'fname' of object '#<Object>'`
>
> > [!success] we can export setter
> > `export function setFname(name){fname = name;}`

> [!warning] we can export imported members
>
> `export {m1.Human as Student};`

> [!danger] constraints
>
> - we can't export part of type like print() of Person
> - we can't export within block: export must be at top level
> - same for import : import must be at top level
> - module use strict by default
>   `use strict`
> - `fname = "ali";`//Error: Uncaught ReferenceError: fname is not defined
> - `let fname = "ali";`
