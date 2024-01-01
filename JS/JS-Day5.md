# Events

### Event Handling Model

> [!tip] Event registration is the process of registering an event handler to an event source.
> `<button onclick="myFunction()">Click me</button>`
>
> > [!warning] registration on html elements not recommended

> [!tip] Event firing is the process of firing an event to notify all registered event handlers.

> [!tip] Event handling is the process of capturing an event, processing it, and then responding to it.

---

#### selectors specifity

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      /* weights  
    lowest (*)-> 0
    element -> 10
    class -> 100
    id -> 1000
    p.myClass -> 110
    inline style -> 10000
    !important -> infinity
    */
      #para1 {
        color: red;
      }
      p {
        color: green;
      }
      .myClass {
        color: blue;
      }
      p.myClass {
        color: yellow;
      }
    </style>
  </head>
  <body>
    <p id="para1" class="myClass" style="color: black">Hello World!</p>
  </body>
</html>
```

---

#### Events

```html
<!DOCTYPE html>
<html>
  <head>
    <script>
      function myFunction() {
        alert("Hello World!");
      }
      //   function myFun1() {
      //     alert("Hello World!1");
      //   }
      //     function myFun2() {
      //         alert("Hello World!2");
      //     }
      //     function myFun3() {
      //         alert("Hello World!3");
      //     }
      function myFun(element) {
        console.log(element.value);
      }

      //if we didn't pass (this) as a parameter we would get undefined
      //we can't rely only on this as we need more info about the event so we have to pass (event) as a parameter
      //event: is the event object

      function myFun2(e) {
        console.log(e.value); //undefined
        console.log(e.target.value); //Click me1
      }
      //   now we can have properties like x and y to get the position of the mouse and target
      // target : the element that fired the event(imporant if we have nested elements)
    </script>
  </head>
  <body>
    <!-- register event handler (onclick) -->
    <button onclick="myFunction()">Click me</button>
    <!-- won't cause error until you click it  myFunction2()  not defined yet -->
    <button onclick="myFunction2()">Click me</button>

    <!-- can use the same event handler for multiple elements -->
    <!-- <input type="button" value="Click me1" onclick="myFun1()" />
    <input type="button" value="Click me2" onclick="myFun2()" />
    <input type="button" value="Click me3" onclick="myFun3()" /> -->
    <!-- dont create 3 functions as the logic is the same we only need to change the message -->

    <!-- better is to send (this) as a parameter to the function -->
    <!-- this: refers to the element that fired the event -->
    <input type="button" value="Click me1" onclick="myFun(this)" />
    <input type="button" value="Click me2" onclick="myFun(this)" />
    <input type="button" value="Click me3" onclick="myFun(this)" />

    <!-- better is to send (event) as a parameter to the function -->
    <!-- event: is the event object -->
    <input type="button" value="Click me1" onclick="myFun2(event)" />
  </body>
</html>
```

> [!bug] we have to send (event) explicitly as a parameter to the function

---

> [!faq] Seperation of concerns
> we seperate the html from the js code

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- <script src="script.js"></script> will cause error as the script will be excuted before the html is loaded -->
    <!-- we can  use onload event to make sure that the html is loaded before excuting the js code -->
  </head>
  <!-- <body onload="loadRequiredElements()"> we can do this in js -->
  <body onload="loadRequiredElements()">
    <input type="button" value="button1" />
    <input type="button" value="button2" />
    <input type="button" value="button3" />

    <button>remove fun2</button>
    <script src="script.js"></script>
  </body>
</html>
```

```js
//script.js
// registeration
var allButtons = document.getElementsByTagName("input");
for (var i = 0; i < allButtons.length; i++) {
  //   allButtons[i].onclick = myFun();//wrong as we are calling the function not registering it
  allButtons[i].onclick = myFun; //correct
  //registering the function in js will send the event object as a parameter implicitly AS Anonymus object {}
  //   allButtons[i].onclick = myFun2; //will override the previous one
}
function myFun(e) {
  //we recieve the event object as a parameter(e) name for the event object
  console.log(e.target.value);
}
// what if we want to register more than one function
function myFun2(e) {
  console.log("myFun2");
}
//addEventListner: register more than one event handler
// targetElement.addEventListener(eventObjectName : without on, eventHandlerFunction,direction : true=>capture,false=>bubbling)

var allButtons = document.getElementsByTagName("input");
for (var i = 0; i < allButtons.length; i++) {
  allButtons[i].addEventListener("click", myFun); //
  allButtons[i].addEventListener("click", myFun2);
}
//will get excuted in the order of registration

document
  .getElementsByTagName("button")[0]
  .addEventListener("click", function () {
    //remove myFun2 from all buttons
    for (var i = 0; i < allButtons.length; i++) {
      allButtons[i].removeEventListener("click", myFun2); //we can't remove EventListener with  anonymous functions so we have to use named functions
    }
  });
//we put all the code in a function to make sure that the html is loaded before excuting the js code
function loadRequiredElements(e) {
  console.log(e);
  var allButtons = document.getElementsByTagName("input");
  for (var i = 0; i < allButtons.length; i++) {
    allButtons[i].addEventListener("click", myFun); //
    allButtons[i].addEventListener("click", myFun2);
  }
  //will get excuted in the order of registration

  document
    .getElementsByTagName("button")[0]
    .addEventListener("click", function () {
      //remove myFun2 from all buttons
      for (var i = 0; i < allButtons.length; i++) {
        allButtons[i].removeEventListener("click", myFun2); //we can't remove EventListener with  anonymous functions so we have to use named functions
      }
    });
}
window.onload = loadRequiredElements;
//better approach is to use addEventListener

window.addEventListener("load", loadRequiredElements);
//if we want to wait for only the document to be loaded
document.addEventListener("DOMContentLoaded", loadRequiredElements); //this only for the document not the window like (navigator)
```

> [!tip] Advatage of registering the event handler in js
> event is passed as a parameter implicitly

---

#### mouse events

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      div {
        width: 200px;
        height: 200px;
        border: 1px solid black;
        margin: 10px;
      }
    </style>
    <script>
      window.addEventListener("load", function () {
        targetDiv = this.document.getElementByTagName("div")[0];
        targetDiv.addEventListener("mousemove", function (e) {
          targetDiv.innerHTML = "x: " + e.clientX + " y: " + e.clientY;
          targetDiv.style.backgroundColor =
            "rgb(" + e.clientX + "," + e.clientY + ",100)";
        });
      });
      //clientX: the x coordinate of the mouse relative to the client area of the browser window
      //clientY: the y coordinate of the mouse relative to the client area of the browser window
    </script>
  </head>
  <body>
    <div></div>
  </body>
</html>
```

---

> html in lecture (image demo)

```js
//script.js
window.addEventListener("load", function () {
  var allImages = this.document.images;
  for (var i = 0; i < allImages.length; i++) {
    allImages[i].addEventListener("mouseover", function (e) {
      //change backgroundimage for div#imgpreview with the src of the image which fired the event

      document.getElementById("imgpreview").style.backgroundImage =
        "url(" + e.target.src + ")";
    });
    //mouseout
    allImages[i].addEventListener("mouseout", function (e) {
      document.getElementById("imgpreview").style.backgroundImage = "none";
    }); //end of mouseout event
  }
}); //end of load event
```

---

### what is the this

> [!tip] this: functions => caller

> [!tip] what is the difference between this and target

> html in lecture thisandtarget

```js
window.addEventListener("load", function () {
  //get text for seril, text for name
  userSerial = this.document.getElementByTagName("input")[0];
  userName = document.getElementByTagName("input")[1];
  var createdtr = document.createElement("tr");
  var firsttd = document.createElement("td");
  firsttd.innerHTML = userSerial.value;
  var secondtd = document.createElement("td");
  secondtd.innerHTML = userName.value;
  createdtr.appendChild(firsttd);
  createdtr.appendChild(secondtd);
  document.getElementByTagName("table")[0].appendChild(createdtr);

  var alltds = document.getElementByTagName("td");
  for (var i = 0; i < alltds.length; i++) {
    alltds[i].addEventListener("click", function (e) {
      // e.target.style.backgroundColor = "red";//get the target element through the event object
      this.style.backgroundColor = "red"; //this refers to the element listening to the event
      //target and this are the same in this case but not always
    }); //end of click event
    //dblclick
    alltds[i].addEventListener("dblclick", function (e) {
      e.target.style.backgroundColor = "blue";
    }); //end of dblclick event
  }
}); //end of load event
```

> [!error] direct registration and delegation registration
>
> direct registration: register the event handler on the existing elements not created
> delegation registration: register the event handler on the parent element and then check the target element

> [!warning] this vs target
> this: refers to the element listening to the event
> target: refers to the element that fired the event

```js
window.addEventListener("load", function () {
  var alltrs = document.getElementsByTagName("tr");
  for (var i = 0; i < alltrs.length; i++) {
    alltrs[i].addEventListener("click", function (e) {
      // e.target.style.backgroundColor = "red";//get the actual element that fired the event
      //   this.style.backgroundColor = "red"; //this refers to the element listening to the event this will change the color of the tr not the td
      console.log(e.target.nodeName); //TD  //nodeName: returns the name of the element
      if (e.target.nodeName == "TD") {
        e.target.style.backgroundColor = "red";
      }
    }); //end of click event
  }
}); //end of load event
```

> [!bug] created elements won't have event handlers

```js
// we can register the event handler on created elements

window.addEventListener("load", function () {
  //get text for seril, text for name
  userSerial = this.document.getElementByTagName("input")[0];
  userName = document.getElementByTagName("input")[1];
  var createdtr = document.createElement("tr");
  var firsttd = document.createElement("td");
  firsttd.innerHTML = userSerial.value;
  var secondtd = document.createElement("td");
  secondtd.innerHTML = userName.value;

  ///////////////////   first method
  //   firsttd.addEventListener("click", function () {
  //     this.style.backgroundColor = "red";
  //   });
  //   firsttd.addEventListener("dblclick", function () {
  //     this.style.backgroundColor = "none";
  //   });
  //   secondtd.addEventListener("click", function () {
  //     this.style.backgroundColor = "red";
  //   });
  //   secondtd.addEventListener("dblclick", function () {
  //     this.style.backgroundColor = "none";
  //   });

  ////////////////////better to use delegation
  //select the table
  var table = document.getElementByTagName("table")[0];
  table.addEventListener("click", function (e) {
    console.log(this); //table

    if (e.target.nodeName == "TD") {
      e.target.style.backgroundColor = "red";
    }
  });
  createdtr.appendChild(firsttd);
  createdtr.appendChild(secondtd);
  document.getElementByTagName("table")[0].appendChild(createdtr);

  var alltds = document.getElementByTagName("td");
  for (var i = 0; i < alltds.length; i++) {
    alltds[i].addEventListener("click", function (e) {
      // e.target.style.backgroundColor = "red";//get the target element through the event object
      this.style.backgroundColor = "red"; //this refers to the element listening to the event
      //target and this are the same in this case but not always
    }); //end of click event
    //dblclick
    alltds[i].addEventListener("dblclick", function (e) {
      e.target.style.backgroundColor = "blue";
    }); //end of dblclick event
  }
}); //end of load event
```

> [!tip] cancelable vs non cancelable events
> cancelable: we can cancel the event
> non cancelable: we can't cancel the event
> e.preventDefault(): cancel the event if it is cancelable
> e.cancelable: true/false

> html in lecture (cancelandpreventdefault)

```html
<a href="https://www.google.com" onclick="alert('hello')">google</a>
<!-- //will open the link and then alert hello -->
<!-- to cancel the event we have to return false the same as preventDefault-->

<!-- cancelable: true -->
<a href="https://www.google.com" onclick="alert('hello');return false;"
  >google</a
>

<!-- //will not check the checkbox -->
<input type="checkbox" onclick="return false" />

<!-- //will select the option -->
<!-- change event is not cancelable    -->
<!-- cancelable: false -->
<select onchange="return false">
  <option>1</option>
  <option>2</option>
  <option>3</option>
</select>
```

---

```js
function testFun(e) {
  // return false;//only works with html not js
  alert("hello");
  e.preventDefault(); //works with js
  //it is only accessible through event object
  // so we have to pass it in html testFun(event)
  //or addEventListener("click",testFun) it will be passed implicitly
}
```

---

#### propagation

> html in lecture (propagation)

> [!example] capture vs bubbling
> capture: from the window to the target element
> bubbling: from the target element to the window

> [!info] event phases
>
> 1.  capture phase
> 2.  target phase
> 3.  bubbling phase

> [!tldr] `addEventListener(event,handler,direction)`
> direction: true=>capture,false=>bubbling
> default: false

```js
//script.js
window.addEventListener("load", function () {
  this.document.getElementByTagName("div")[0].addEventListener("click", funOne);
});
this.document
  .getElementByTagName("button")[0]
  .addEventListener("click", funBtn);

function funOne(e) {
  console.log("funOne");
}
function funBtn(e) {
  console.log("funBtn");
}
////////bubble
//btn first
// div second

// if we want to change the direction to capture
//set the third parameter to true

//to cancel bubbling
function funBtn(e) {
  console.log("funBtn");
  e.cancelBubble = true; //default false
  //if direction is true
  // it will stop capture aswell
  //more general
  e.stopPropagation();
}
```

---

> html in lecture (stopImmediatePropagation)

> [!error] `stopPropagation()` vs `stopImmediatePropagation()` > `stopPropagation()` > `stopImmediatePropagation()` not about the direction it is about the order of registration
> `stopImmediatePropagation()` will stop the event from firing the next event handler
> if we have 4 event handlers registered on the same element

```js
window.addEventListener("load", function () {
  var targetBtn = this.document.getElementByTagName("input")[0];
  targetBtn.addEventListener("click", funOne);
  targetBtn.addEventListener("click", funTwo);
  targetBtn.addEventListener("click", funThree);
  targetBtn.addEventListener("click", funFour);
}); //end of load event
function funOne(e) {
  //e.stopImmediatePropagation();
  console.log("funOne");
}
function funTwo(e) {
  e.stopImmediatePropagation(); //only funOne,funTwo will be excuted
  console.log("funTwo");
}
function funThree(e) {
  console.log("funThree");
}
function funFour(e) {
  console.log("funFour");
}
```

---

> html in lecture(firingEventwithOtherElements)

```js
window.addEventListener("load", function () {
  this.document
    .getElementByTagName("input")[0]
    .addEventListener("mouseover", function (e) {
      console.log("checkbox is over");
      //fire the click event on the btn
      this.document.getElementByTagName("button")[0].click();
    });
  this.document
    .getElementByTagName("input")[1]
    .addEventListener("click", function (e) {
      console.log("btn is clicked");
    });
}); //end of load event
```

#### keyboard events

> [!tip] keydown vs keypress vs keyup
> keydown: when the key is pressed
> keyup: when the key is released
> keypress (deprecated):as long as the key is pressed
> beforeInput

> [!faq] printed vs non printed keys
> printed: a-z,0-9
> non printed: shift,ctrl,alt,enter,backspace,delete

> [!info] KeyboardEvent
> key: the key that was pressed (shift)
> code: the code of the key that was pressed (shiftLeft)
> keyCode: the code of the key that was pressed (16)

> [!bug] fn key is not working

```js
window.addEventListener("load", function () {
  //   this.document.getElementByTagName("input")[0].addEventListener("keyup", function (e) {
  //       console.log(e);
  //   });//key up event on the input
  this.document
    .getElementByTagName("input")[0]
    .addEventListener("keydown", function (e) {
      console.log(e);
    }); // key down event on the input

  //prevent non digits from being entered
  // key up not preventable so we have to use keydown
  this.document
    .getElementByTagName("input")[0]
    .addEventListener("keyup", function (e) {
      if (e.keyCode >= 48 && e.keyCode <= 57) {
      } else {
        e.preventDefault();
      }
    });

  //prevent non digits from being entered
  this.document
    .getElementByTagName("input")[0]
    .addEventListener("keydown", function (e) {
      if (e.keyCode >= 48 && e.keyCode <= 57) {
      } else {
        e.preventDefault();
      }
    });

  // there is events for copy,paste,cut
  //listen for clipboard events
  this.window.addEventListener("copy", function (e) {
    console.log("copy", e);
  });
  this.window.addEventListener("paste", function (e) {
    console.log("paste", e);
  });
  this.window.addEventListener("cut", function (e) {
    console.log("cut", e);
  });
}); //end of load event
```

---

### Regex (Regular Expressions)

```js
//1-literal
// var myPattern = /pattern/;
//2-constructor
// var myPattern = new RegExp("pattern");
//between the 2 slashes
//^: start with
//$: end with

//flags: after the second slash
//i: case insensitive
//g: global
//m: multiline

var userNamePattern = /[a-zA-Z]{4,6}/; //4-6 characters
// this pattern will match any string that contains 4-6 characters
("ahMed"); //match
("ahMed012"); // match ahmed
("ah"); //not match
("010"); //not match

// 2 ways of matching

//1.stringObject.match(regex)
//return matched string if found || null if not found
"itimans".match(userNamePattern); //["itimans", index: 0, input: "itimans", groups: undefined]

//2.
//regexObject.test(string)
// return true if found || false if not found

// regexObject.exec(string)
// return matched string if found || null if not found

var myPattern = /[a-zA-Z]{4,6}/;
myPattern.constructor.name; //RegExp
"itimans".match(myPattern); //["itimans", index: 0, input: "itimans", groups: undefined]=>.toString() will return the string
"itimansourabranch".match(myPattern); //["itimans", index: 0, input: "itimansourabranch", groups: undefined]

"6799879_____itimansourabranch".match(myPattern); //["itimans", index: 10, input: "6799879_____itimansourabranch", groups: undefined]

"234234234234324_____+++".match(myPattern); //null

/////////////////// adding constrains

var myPattern = /^[a-zA-Z]{4,6}/; //start with 4-6 characters
"itimans8937498237498327".match(myPattern); //["itimans", index: 0, input: "itimans8937498237498327", groups: undefined]

"889itijkldsjfsd".match(myPattern); //null as it doesn't start with 4-6 characters

var myPattern = /[a-zA-Z]{4,6}$/; //end with 4-6 characters

"889itijkldsjfsd".match(myPattern); //["itijkldsjfsd", index: 3, input: "889itijkldsjfsd", groups: undefined]

//sharp match
var myPattern = /^[a-zA-Z]{4,6}$/; //start and end with 4-6 characters

"889itijkldsjfsd".match(myPattern); //null
"itimans2343kdfjlskdjf".match(myPattern); //null
"itimans".match(myPattern); //["itimans", index: 0, input: "itimans", groups: undefined]

//test
mypattern.test("itimans"); //true
mypattern.test("itimans2343kdfjlskdjf"); //false

//exec
mypattern.exec("itimans"); //["itimans", index: 0, input: "itimans", groups: undefined]
mypattern.exec("itimans2343kdfjlskdjf"); //null
```

---

### form validation

> html in lecture (formvalidation)

```js
window.addEventListener("load", function () {
  var userName = this.document.getElementById("userName");
  userName.focus(); //focus on the element
  var userPass = this.document.getElementById("userPass");
  //validate userName
  userName.addEventListener("blur", function (e) {
    if (!isUserNameValid()) {
      this.className = "error";
      this.focus(); //focus on the element
      this.select(); //select the text
    } else {
      this.className = "success";
    }
  }); //end of blur event
  //validate userPass
  userPass.addEventListener("blur", function (e) {
    if (!isUserPassValid()) {
      this.className = "error";
      this.focus(); //focus on the element
      this.select(); //select the text
    } else {
      this.className = "success";
    }
  }); //end of blur event
  this.document.forms[0].addEventListener("submit", function (e) {
    if (!(iseuserpassvalide() && iseusernamevalide())) {
      e.preventDefault();
    }
  }); //end of submit

  this.document.forms[0].addEventListener("reset", function (e) {
    if (!confirm("Are You Sure ?")) {
      e.preventDefault();
    }
  }); //end of submit

  window.oncontextmenu = function (e) {
    alert("You Can't Do It ");

    e.preventDefault();
  };
}); //end of load

function iseusernamevalide() {
  return usernametext.value.match(/^[a-zA-Z]{4,6}$/);
}

function iseuserpassvalide() {
  return userpasstext.value.match(/^[0-9]{3,5}$/);
}
```



