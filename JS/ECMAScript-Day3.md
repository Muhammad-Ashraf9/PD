> [!tip] Async code
> ES5: Callbacks => callback hell
> ES6: Promises
> EsNext: Async/Await

---

> html in lecture(asynctest.html)

```js
//js is synchronous: execution of code is line by line
console.log("start");
function myFunc() {
  console.log("myFunc");
}
myFunc();
console.log("end");
//output: start, myFunc, end
```

```js
console.log("start");
function myFunc() {
  //fetch data from api:takes time
  setTimeout(() => {
    console.log("data loaded");
  }, 2000);
}
myFunc();
console.log("end");
//output: start, end, data loaded(after 2 sec)
```

```js
console.log("start");
function myFunc() {
  //fetch data from api:takes time
  setTimeout(() => {
    // console.log("data loaded");
    //if we want data from this function
    return 10;
  }, 2000);
}
let result = myFunc(); //it will start the timer and continue to next line without waiting for the timer to finish (return undefined)
console.log("result" + result); //result undefined
console.log("end");
//output: start, result undefined, end
```

```js
//global excution context: syncronous code
//after global execution context is done, it will check for async code

console.log("start");
setTimeout(() => {
  console.log("data loaded");
}, 2000); //best case it will take 2 sec
//but if we have a loop that takes 10 sec to finish, then it will take 10 sec to print data loaded
for (let i = 0; i < 1e10; i++) {
  //data loaded will be printed after a long time
  //logic
}
console.log("end");

//output: start, (after a long time)end, data loaded
```

---

> [!tip] client side js vs server side js
> nodejs is runtime environment for js(run js on server side)
> browser is runtime environment for js(run js on client side)

> [!info] JS runtime environment : Browser environment
>
> - JS engine: syntax parser, compiler, interpreter(js execution + memory management)
> - Web API (Browser API): DOM, AJAX, Timeout, Fetch, Console, Promises, XMLHttpRequest, etc
> - Callback Queue : (FIFO) callback functions that are ready to be executed after the execution stack is empty from Web API
> - Microtask Queue : (FIFO) Promises, Mutation Observer, etc
> - Event Loop : checks for execution stack and callback queue and microtask queue

> [!info] web apis is available in browser environment for window object
> `window.setTimeout()` is same as `setTimeout()`

---

#### global execution context (GEC)

> context of script that is not inside any function

> [!tip] excution stack
>
> - on run : global execution context is created and pushed to execution stack

> [!bug] there is a difference between excution stack and memory stack
>
> - excution stack: keeps track of the execution of the program
> - memory stack: keeps track of the memory allocation of the program

```js
console.log("start"); //stack frame loaded to excution stack and popped out
setTimeout(() => {
  console.log("data loaded");
}, 2000); //excute setTimeout and  we continue to next line without waiting for the timer to finish
console.log("end"); //stack frame loaded to excution stack and popped out
//GEC is popped out of excution stack
//timer is running in background (web api)
//when timer is finished(callback function is ready to be excuted), callback function is pushed to callback queue
//event loop checks for execution stack and callback queue
//if execution stack is empty, callback function is pushed to execution stack and excuted
```

> [!tip] event loop
>
> - checks for execution stack
> - if execution stack is empty, checks for callback queue
> - if callback queue is not empty, callback function is pushed to execution stack and excuted
>   > [!faq] why callback queue is not pushed to execution stack directly?
>   >
>   > - for dependency management (if we have multiple callback functions, we can control the order of execution)

---

> [!tip] callback queue vs microtask queue
>
> - microtask queue is checked before callback queue
> - microtask queue is used for promises and mutation observer
> - callback queue is used for setTimeout, setInterval, ajax, etc
> - callback queue won't be checked until microtask queue is empty (microtask queue has higher priority)
> - starvation: if we have a lot of promises, callback queue won't be checked until all promises are resolved
> - and both callback queue and microtask queue are checked after execution stack is empty

---

###### before GEC is loaded to execution stack

> [!tip] Steps of execution of js code
>
> 1. Parsing: syntax parser: checks tokens
>
> - checks for syntax errors and throws error if there is any (before execution)
> - AST: abstract syntax tree: tree representation of code
>
> 2. Interpreter: converts AST to Bytecode (machine code)
>    > [!warning] interpreter is slow
>    > `for(let i=0;i<100000;i++){sum(2,2);}`
>    > optimization: compiler - optimizer
>    > JIT: just in time compiler: compiler + interpreter
>    > same steps for all engines but different in optimization

---

> [!tip] js engine
>
> v8 engine: chrome, nodejs => compiler: turbofan + interpreter: ignition
> spidermonkey: firefox => compiler: ionmonkey + interpreter: baseline
> chakra: edge => compiler: chakracore + interpreter: chakrainterpreter
> webkit: safari => compiler: ftl + interpreter: llint

![](Pasted%20image%2020231226135100.png)

> [!info] [AST explorer](https://astexplorer.net/)

> [!success]
> correct syntax

![](Pasted%20image%2020231226135000.png)

> [!fail]  
> wrong syntax

![](Pasted%20image%2020231226135206.png)

---

### Callback hell

> html in lecture(example1.html)

```js
//nasr
function getAllStudents() {
  setTimeout(() => {
    let students = [
      { id: 1, name: "nasr" },
      { id: 2, name: "ahmed" },
    ];
    console.log(students);
    return students;
  }, 2000);
}
//abdelrahman
function printAllStudents(studentsArray) {
  setTimeout(() => {
    studentsArray.forEach((student) => {
      console.log(`id: ${student.id}, name: ${student.name}`);
      let deptArray = [
        { deptId: 1, deptName: "ITI" },
        { deptId: 2, deptName: "ITI" },
      ];
      return deptArray;
    });
  }, 2000);
}

//amr
function displayDepartments(deptArray) {
  setTimeout(() => {
    let deptNames = [];
    deptArray.forEach((dept) => {
      console.log(`deptId: ${dept.deptId}, deptName: ${dept.deptName}`);
      mgArray.push(dept.deptName);
    });
  }, 2000);
}

//mariam
function displayDeptNames(deptNames) {
  setTimeout(() => {
    deptNames.forEach((deptName) => {
      console.log(deptName);
    });
  }, 2000);
}

//omar run
let studentsArray = getAllStudents(); //start only timer and return undefined
let deptArray = printAllStudents(studentsArray);
let deptNames = displayDepartments(deptArray);
displayDeptNames(deptNames);

//output: Uncatched TypeError: Cannot read property 'forEach' of undefined
//output: Uncatched TypeError: Cannot read property 'forEach' of undefined
//output: Uncatched TypeError: Cannot read property 'forEach' of undefined
```

---

> example2.html

> solution

```js
// call function inside function

//nasr
function getAllStudents() {
  setTimeout(() => {
    let students = [
      { id: 1, name: "nasr" },
      { id: 2, name: "ahmed" },
    ];
    printAllStudents(students); //call printAllStudents inside getAllStudents
  }, 2000);
}
//abdelrahman
function printAllStudents(studentsArray) {
  setTimeout(() => {
    studentsArray.forEach((student) => {
      console.log(`id: ${student.id}, name: ${student.name}`);
      let deptArray = [
        { deptId: 1, deptName: "ITI" },
        { deptId: 2, deptName: "ITI" },
      ];
      displayDepartments(deptArray); //call displayDepartments inside printAllStudents
    });
  }, 2000);
}

//amr
function displayDepartments(deptArray) {
  setTimeout(() => {
    let deptNames = [];
    deptArray.forEach((dept) => {
      console.log(`deptId: ${dept.deptId}, deptName: ${dept.deptName}`);
      mgArray.push(dept.deptName);
    });
    displayDeptNames(deptNames); //call displayDeptNames inside displayDepartments
  }, 2000);
}

//mariam
function displayDeptNames(deptNames) {
  setTimeout(() => {
    deptNames.forEach((deptName) => {
      console.log(deptName);
    });
  }, 2000);
}

//omar run
getAllStudents(); //start timer
```

---

> example3.html

> [!fail] problem : function names
>
> > [!success] solution : send function name as parameter

```js
// callback
// send function name as parameter

//nasr
function getAllStudents(callback) {
  setTimeout(() => {
    let students = [
      { id: 1, name: "nasr" },
      { id: 2, name: "ahmed" },
    ];
    callback(students, dipslayDepartments);
  }, 2000);
}
//abdelrahman
function printAllStudents(studentsArray, callback) {
  setTimeout(() => {
    studentsArray.forEach((student) => {
      console.log(`id: ${student.id}, name: ${student.name}`);
      let deptArray = [
        { deptId: 1, deptName: "ITI" },
        { deptId: 2, deptName: "ITI" },
      ];
    });
    callback(deptArray, displayDeptNames);
  }, 2000);
}

//amr
function displayDepartments(deptArray, callback) {
  setTimeout(() => {
    let deptNames = [];
    deptArray.forEach((dept) => {
      console.log(`deptId: ${dept.deptId}, deptName: ${dept.deptName}`);
      mgArray.push(dept.deptName);
    });
    callback(deptNames, dipslayDeptNames);
  }, 2000);
}

//mariam
function displayDeptNames(deptNames, callback) {
  setTimeout(() => {
    deptNames.forEach((deptName) => {
      console.log(deptName);
    });
  }, 2000);
}

//omar run
function test(array) {
  console.log(array);
  console.log(array);
}
getAllStudents(test); //start timer
//output: (after 2 sec) [{id: 1, name: "nasr"}, {id: 2, name: "ahmed"}]
// ///////////////
getAllStudents(printAllStudents); //start timer
```

---

> example4.html

```js
// callback hell
```

---

### Promises

> [!tip] promise
>
> - object that represents the eventual completion or failure of an asynchronous operation
> - promise is in one of these states:
>   - pending: initial state, neither fulfilled nor rejected
>   - fulfilled: operation completed successfully (resolve)
>   - rejected: operation failed
> - promise is settled: it is either fulfilled or rejected
> - once settled, it cannot be resettled
> - promise is asynchronous
> - promise is chainable
> - promise is immutable

```js
//promise
function getNames() {
  setTimeout(() => {
    let names = ["nasr", "ahmed"];
    return names;
  }, 2000);
}
let names = getNames(); //return undefined
names; //undefined
////////////////////
function getNames() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let names = ["nasr", "ahmed"];
      // resolve(names);// will be resolved after 2 sec
      reject("Server busy"); // will be rejected after 2 sec
    }, 2000);
  });
}
let names = getNames(); //return promise object
names; //Promise {<pending>}

//pending => resolved : fulfilled return value
//pending => rejected : rejected message
names; //Promise {<pending>}
names; //Promise {<fulfilled>: Array(2)}//only after 2 sec
//before that we can't access the return value

//in case of reject
names; //Promise {<pending>}
names; //Promise {<rejected>: "Server busy"}//only after 2 sec
//Error: Uncaught (in promise) Server busy
//stop execution of code
//so we need to handle the error

///

// with any returned promise => 2 methods : then, catch
names
  .then((result) => {
    //if promise is resolved or fulfilled
    //then has callback function as parameter that will be called after promise is resolved has access to return value as parameter

    console.log(result);
  })
  .catch((error) => {
    //if promise is rejected
    //catch has callback function as parameter that will be called after promise is rejected has error message as parameter
    console.log(error);
  });
```

> [!tip] resolve vs reject
> `resolve( value )` : fulfilled
> `reject( message )` : rejected

---

> example5.html

```js
//promise
function getAllStudents() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let students = [
        { id: 1, name: "nasr" },
        { id: 2, name: "ahmed" },
      ];
      resolve(students);
    }, 2000);
  });
}

function printAllStudents(studentsArray) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      studentsArray.forEach((student) => {
        console.log(`id: ${student.id}, name: ${student.name}`);
        let deptArray = [
          { deptId: 1, deptName: "ITI" },
          { deptId: 2, deptName: "ITI" },
        ];
        resolve(deptArray);
      });
    }, 2000);
  });
}

//amr
function displayDepartments(deptArray) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let deptNames = [];
      deptArray.forEach((dept) => {
        console.log(`deptId: ${dept.deptId}, deptName: ${dept.deptName}`);
        mgArray.push(dept.deptName);
      });
      resolve(deptNames);
    }, 2000);
  });
}

//mariam
function displayDeptNames(deptNames) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      deptNames.forEach((deptName) => {
        console.log(deptName);
      });
      resolve();
    }, 2000);
  });
}

//omar run
//nesting then and catch
getAllStudents()
  .then((studentsArray) => {
    printAllStudents(studentsArray)
      .then((deptArray) => {
        displayDepartments(deptArray)
          .then((deptNames) => {
            displayDeptNames(deptNames)
              .then(() => {
                console.log("done");
              })
              .catch((error) => {
                console.log(error);
              });
          })
          .catch((error) => {
            console.log(error);
          });
      })
      .catch((error) => {
        console.log(error);
      });
  })
  .catch((error) => {
    console.log(error);
  });

/////
// /refactor returning promise from each function and chaining then and catch
//////XXXXXXXXXXXnot explained in lectureXXXx//
getAllStudents() //return promise
  .then((studentsArray) => {
    return printAllStudents(studentsArray); //return promise
  })
  .then((deptArray) => {
    return displayDepartments(deptArray);
  })
  .then((deptNames) => {
    return displayDeptNames(deptNames);
  })
  .catch((error) => {
    console.log(error);
  });
```

---

#### async/await

> example6.html

```js
//async/await

//omar run
let stdArray = await getAllStudents(); //await: wait for promise to be resolved or rejected
//Error: Uncaught SyntaxError: await is only valid in async function
// await can only be used inside async function or  first level in module

//solution
//  function caller(){//we need to make caller async

async function caller() {
  let stdArray = await getAllStudents();
  let deptArray = await printAllStudents(stdArray);
  let deptNames = await displayDepartments(deptArray);
  displayDeptNames(deptNames);
}
caller();
//we can make it IIFE
(async function caller() {
  let stdArray = await getAllStudents();
  let deptArray = await printAllStudents(stdArray);
  let deptNames = await displayDepartments(deptArray);
  displayDeptNames(deptNames);
})();

//handle error
// Exception handling
//exception: runtime error that stops the execution of code
// try{} catch(error){} finally{}

try {
  (async function caller() {
    let stdArray = await getAllStudents();
    let deptArray = await printAllStudents(stdArray);
    let deptNames = await displayDepartments(deptArray);
    displayDeptNames(deptNames);
  })();
} catch (error) {
  console.log(error);
} //global try catch
//we can use try catch inside each function

reject("Server busy");
// we catch string "Server busy" not error object

let x = throw new Error("error message"); //throw error
//object
x.message; //"error message"
x.name; //"Error"

//we can have multiple try catch
try {
  //this catches error in outer try catch
  (async function caller() {
    try {
      //this catches error in inner try catch
      let stdArray = await getAllStudents();
      let deptArray = await printAllStudents(stdArray);
      let deptNames = await displayDepartments(deptArray);
      displayDeptNames(deptNames);
    } catch (error) {
      console.log(error); //if any error happens, it will be caught here or promise is rejected
      console.log(error.message); //error message "Server busy"
    }
  })();
  throw new Error("error message"); //throw error
  //this error will be caught in outer try catch
} catch (error) {
  console.log(error); //if any error happens, it will be caught here or promise is rejected
  console.log(error.message); //error message "Server busy"
} finally {
  //  catch (error) {//we can't have catch without try

  // }
  ///finally
  //runs after try or catch whether there is error or not
  console.log("finally");
}

// we can use try finally without catch
```

> [!tip] finally
>
> - runs after try or catch whether there is error or not
> - we can use try finally without catch
>   > [!example] Database connection
>   >
>   > - try: connect to database
>   > - catch: if there is error, log error
>   > - finally: close connection whether there is error or not

---

##### fetch

```js
// load data from server :xmlhttprequest

//fetch
//xmlhttprequest behind the scenes
//fetch api: built in api in browser
// fetch(url,options)
//returns promise
let response = await fetch("https://jsonplaceholder.typicode.com/users");
response; //Response {type: "cors", url: "https://jsonplaceholder.typicode.com/users", redirected: false, status: 200, ok: true, …}
response.body; //ReadableStream {locked: false}
//we need to convert response to json

function loadPosts() {
    let response = await fetch("https://jsonplaceholder.typicode.com/users");
    //response.json() returns  promise that resolves to json
let allposts = await response.json();
allposts; //array of objects
JSON.stringify(allposts); //convert to string
allposts.forEach((post) => {
  document.write(`<li>${post.name}</li>`); //write to html
});
}


```

> [!danger] Search(promises)
> Promise.all([promise1,promise2,promise3])
> Promise.any([promise1,promise2,promise3])
> Promis.race([promise1,promise2,promise3])
> ...
