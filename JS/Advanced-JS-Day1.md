> html in lecture (testMultipleExternalJsFiles.html)

```js
// fileone.js
var firstName = "ahmed-fileone";
function sum(a, b) {
  return a + b;
}
function sub(a, b) {
  return a - b;
}

var namesArray = ["ahmed", "mohamed", "ali"];
namesArray.forEach(function (name) {
  console.log(name);
});
```

```js
// filetwo.js
var firstName = "nasr-filetwo";
function sum(a, b, c) {
  return a + b + c;
}
function sub(a, b, c) {
  return Math.abs(a - b - c);
}

var namesArray = ["ahmed", "mohamed", "ali"];
namesArray.forEach(function (name) {
  console.log(name);
});
```

> global namespace (html)

```html
<!DOCTYPE html>
<html>
  <head>
    <title>testMultipleExternalJsFiles</title>
    <!-- namespace pollution -->
    <!-- order is important -->
    <script src="fileone.js"></script>
    <script src="filetwo.js"></script>
    <!-- second script will override first script -->
    <script>
      console.log(firstName); // nasr-filetwo
      console.log(sum(1, 2)); //undefined => third parameter is undefined
      console.log(sub(1, 2));
    </script>
  </head>
  <body></body>
</html>
```

> [!info] Solution: make each file in its own namespace (ES5)
> we can wrap code in each file in IIFE (Immediately Invoked Function Expression)
> IIFE is a function that is executed right after it is created
> IIFE has its own scope
> IIFE is used to avoid polluting the global namespace
> IIFE is used to avoid naming conflicts
> IIFE is used to avoid overriding variables and functions

```js
// fileone.js
//regualr function
function fileOne() {
  var firstName = "ahmed-fileone";
  function sum(a, b) {
    return a + b;
  }
  function sub(a, b) {
    return a - b;
  }

  var namesArray = ["ahmed", "mohamed", "ali"];
  namesArray.forEach(function (name) {
    console.log(name);
  });
}
fileOne();

//problem: we can access fileOne function from outside
// pollution
// solution: IIFE
```

```js
//IIFE
(function () {
  var firstName = "ahmed-fileone";
  function sum(a, b) {
    return a + b;
  }
  function sub(a, b) {
    return a - b;
  }

  var namesArray = ["ahmed", "mohamed", "ali"];
  namesArray.forEach(function (name) {
    console.log(name);
  });
})();
//    var namesArray = ["ahmed", "mohamed", "ali"];// we can access it from outside
```

> html in lecture(iifePractice.html)

```js
var namesarray = ["ahmed", "mohamed", "ali"];
function printNames() {
  for (var i = 0; i < namesarray.length; i++) {
    setTimeout(function () {
      console.log(namesarray[i]); //undefined undefined undefined
    }, 2000);
    //as we know that setTimeout is async function and it will be executed after 2 seconds and the loop will be finished before 2 seconds so i will be 3 and namesarray[3] is undefined so we will get undefined 3 times
  }
}
printNames();
```

> [!info] Solution: IIFE

```js
var namesarray = ["ahmed", "mohamed", "ali"];
function printNames() {
  for (var i = 0; i < namesarray.length; i++) {
    (function (name) {
      setTimeout(function () {
        console.log(name);
      }, 2000);
    })(namesarray[i]); // we pass namesarray[i]
  }
}
```

---

### this excplicit binding

```js
//implicit binding
function myFunction() {
  console.log(this); //window
}
myFunction();
var myObject = {
  id: 1,
  name: "ahmed",
  print: function () {
    return this.id + " " + this.name;
  },
};
console.log(myObject.print()); //1 ahmed

//explicit binding (call, apply, bind)
var myObject1 = {
  id: 2,
  name: "mohamed",
  print: function () {
    return this.id + " " + this.name;
  },
};

var myObject2 = {
  id: 3,
  name: "ali",
  print: function () {
    return this.id + " " + this.name;
  },
};

myObject1; //{id: 2, name: "mohamed", print: ƒ}
myObject2; //{id: 3, name: "ali", print: ƒ}
myObject1.print(); //"2 mohamed" => this is myObject1 caller
myObject2.print(); //"3 ali" => this is myObject2 caller
// calling print function from myObject1
myObject2 = {
  id: 3,
  name: "ali",
};
myObject2.print(); //error no print function in myObject2

//call  | apply
// call(thisArg, arg1, arg2, arg3, ...)//arguments
// apply(thisArg, [arg1, arg2, arg3, ...])//array of arguments
//thisArg: the object that will be the caller of the function

myObject1.print.call(myObject2); //"3 ali" => this is myObject2 caller
myObject1.print.apply(myObject2); //"3 ali" => this is myObject2 caller
//call and apply will call print function from myObject1 but the caller will be myObject2

var arr = [1, 2, 3, 4, 5];
console.log(Math.max(arr)); //NaN
//Math.max() expects comma separated arguments
console.log(Math.max.apply(null, arr)); //5

//if we want to take a copy of other object method and use it in our object without saving the method in our object
//bind
//bind(thisArg, arg1, arg2, arg3, ...)//arguments
//return a copy of the function with the new thisArg

var myObject3 = {
  id: 4,
  name: "ahmed",
};
var myObject3Print = myObject1.print.bind(myObject3); //return a copy of the function with the new thisArg
// myObject3Print is a copy of myObject1.print with myObject3 as a caller

myObject3Print; //ƒ () {return this.id + " " + this.name;}

myObject3Print(); //"4 ahmed" => this is myObject3 caller

myObject3; //{id: 4, name: "ahmed"}
//doesn't change the original function no print function in myObject3``
```

```js
//counter
var conter = 0;
function increaseCounter() {
  conter++;
  console.log(conter);
}
increaseCounter(); //1
increaseCounter(); //2
increaseCounter(); //3
increaseCounter(); //4
counter = -100; //we can change the counter value from outside

//solution: closure
```

---

## closure

> [!tip] Closure cases
>
> 1.  return function from another function & returned function depend on the outer function variables (lexical scope)
> 2.  registeration
> 3.

> [!info]

```js
function myFun() {
  var x = 10;
}
// myFun scope:
// Local =>
//this
//arguments
// x

function increaseCounter() {
  var conter = 0; //local scope | lexical scope for increase function | closure
  function increase() {
    conter++;
    console.log(conter);
  }
  return increase;
}

var caller = increaseCounter(); //caller is a copy of increase function which has access to conter variable in increaseCounter function after increaseCounter function is finished and returned increase function
caller(); //1
caller(); //2
caller(); //3
caller(); //4
//we can't access conter variable from outside

caller; //ƒ increase() {conter++;console.log(conter);}
//has 2 scopes: local scope and closure scope (increaseCounter)
//closure scope: conter
var caller1 = increaseCounter(); //counter = 0
caller1(); //1
caller1(); //2
caller1(); //3
caller1(); //4
var caller2 = increaseCounter(); //counter = 0
caller2(); //1
caller2(); //2
//if we want only one counter we can use IIFE

var caller = (function () {
  var conter = 0;
  return function () {
    conter++;
  };
})();


//closure + IIFE
var counter = (function(){
    var count = 0;
    return {
        increase: function(){
            count++;
        },
        decrease: function(){
            count--;
        },
        getCount: function(){
            return count;
        }
        changeBy(value){
            count += value;
        }
    }
})();
counter.getCount(); //0
counter.increase();//count = 1
counter.increase();//count = 2
counter.decrease();//count = 1
counter.changeBy(10);//count = 11
counter.getCount();//11
```

```js
window.addEventListener("load", function () {
  for (var i = 0; i < 10; i++) {
    var createdButton = document.createElement("input");
    createdButton.type = "button";
    createdButton.value = "button " + i;
    // createdButton.addEventListener("click", function () {
    //   alert(i); //i will resolve on click when the loop is finished i = 10
    // });
    //solution: closure + IIFE
    createdButton.onclick = (function (i) {
      return function () {
        alert(i);
      };
    })(i);
    document.body.appendChild(createdButton);
  }
}); //onclick on any button we will get 10
```

```js

```

> [!info] Tracing in JS
> `debugger;` //stop the code execution and open the browser debugger

---

##### uses of call, apply

```js
function sum() {
  //arguments is array like object
  // we need to use array methods
  consloe.log([].slice.call(arguments)); //[1,2,3,4,5,6,7,8,9,10] array
  return [].slice.call(arguments).reduce(function (acc, item) {
    return acc + item;
  }, 0);
}
sum(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
```

---

### patterns for functions

```js
//constructor function
function Person(name, id) {
  this.name = name;
  this.id = id;
  this.print = function () {
    console.log(this.name + " " + this.id);
  };
}
Person(); //nothing will happen
//but can be called as normal function
var pone = { id: 1, name: "ahmed" };
var ptwo = pone; //identity equality any change in pone will affect ptwo
//we use constructor function to create new objects
var pone = Person("ahmed", 1); //pone is undefined

var pone = new Person("ahmed", 1); //pone is an object Person {name: "ahmed", id: 1, print: ƒ}

var ptwo = new Person(po.name, po.id); //ptwo is an object with different identity but same state
//Person {name: "ahmed", id: 1, print: ƒ}

//[{},new String(), new Number(), new Boolean(), new Date(), new Person(), new Array(), ]

pone instanceof Person; //true

//instanceof
//  returns true if the object is an instance of the constructor function or any of its parents

//constructor.name
// return the constructor function name of the object not any of its parents
```

### factory function

```js
//factory function
//return an object
function Person(id, name) {
  return {
    id: id,
    name: name,
    print: function () {
      console.log(this.id + " " + this.name);
    },
  };
}
var pone = Person(); //pone is an object {id: undefined, name: undefined, print: ƒ}

var pone = Person(1, "ahmed"); //pone is an object {id: 1, name: "ahmed", print: ƒ}

ptwo.print(); //1 ahmed

//////////////////
// no type Person here
// type is object
//////////////////

pone instanceof Person; //false

//copy state of pone to ptwo
var ptwo = { id: pone.id, name: pone.name, print: pone.print }; //repeated code

var ptwo = Person(pone.id, pone.name);
```

# #lab-A-JS-1

- simple datatables.net
