# DOM

> [!tip] Document Object Model
>
> - DOM: is the HTML document represented as a tree of objects

> [!tip] JQuery
>
> select elements in the DOM using CSS selectors and manipulate them using jQuery methods
> Later

> [!info] document object
>
> - The document object is the root node of the HTML document
>   HTML root element: `<html>` > `<head>` and `<body>` are children of `<html>` > `<title>` is a child of `<head>` > `<div>` elements are children of `<body>` > `<h1>` and `<p>` are children of `<div>`
>   there is a path from the root to each element
>
> > [!faq] how to know which is the first child of an element?
> > it is index 0 of the children

> [!tip] Node
>
> - Node: any created element in the DOM
>   nodes has different types:
> - Element nodes: HTML elements
> - Attribute nodes: HTML attributes
> - Text nodes: HTML text
> - Comment nodes: HTML comments
> - every node has its own properties and methods

> [!tip] Interfaces
>
> htmlElement: is an interface that represents an HTML element
> htmlAttribute: is an interface that represents an HTML attribute

---

> [!WARNING] everything is saved in the memory even new lines and spaces

> [!warning] every element has its interface and properties
> like `htmlpElement`

> [!bug] Node vs Element
>
> - Node: is an interface that represents a node in the DOM tree
> - Element: is an interface that represents an element in the DOM tree
> - Element is a type of Node
>   all elements are nodes but not all nodes are elements

> [!example] document pointing to the root element HTML

```html
<!DOCTYPE html>
<html>
  <head>
    <title>DOM</title>
  </head>
  <body>
    <div>
      <h1>DOM</h1>
      <p>Document Object Model</p>
    </div>
    <div>
      <h1>DOM</h1>
      <p>Document Object Model</p>
    </div>
  </body>
</html>
```

---

> [!bug] Html in lec

```js
document; //all the html including title
//document is object and it has properties and methods
document.title; //'title for page'
document.title = "new title"; //change the title
document.children; //[head, body] // tags not nodes
document.childNodes; //[html] // nodes => NodeList
document.childNodes[0].childNodes; //[head,text,body] // nodes => NodeList
document.childNodes[0].childNodes[2].childNodes; //[p,div,text,div,text] // nodes => NodeList
document.childNodes[0].childNodes[2].childNodes[0].innerText = "new text"; //change the text
//style
document.childNodes[0].childNodes[2].childNodes[0].style.border =
  "1px solid red";
//it is about how to select correctly
// in this we have select using index
//not good

document.childNodes[0].childNodes[2].childNodes[4].childNodes; //[text,div#first,text]

//nodeType
document.childNodes[0].childNodes[2].childNodes[4].childNodes[0].nodeType; //3 => text
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].nodeType; //1 => div

//comment node
document.childNodes[0].childNodes[2].childNodes[4].childNodes[1].nodeType; //8 => comment

//nodeName
document.childNodes[0].childNodes[2].childNodes[4].childNodes[1].nodeName; //#comment
//return tag name Capitalized
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].nodeName; //DIV

//parentNode is the same as parentElement
//parents can't be anything but elements
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].parentNode; //div#first

//nextSibling
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].nextSibling; //text
//nextElementSibling
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2]
  .nextElementSibling; //div#second

//previousSibling
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2]
  .previousSibling; //text
//previousElementSibling
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2]
  .previousElementSibling; //div#first

//attributes
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].attributes; //NamedNodeMap {0: id, id: id, length: 1}
//attributes length
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].attributes
  .length; //1
//check if it has attribute
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].hasAttribute(
  "id"
); //true
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].hasAttribute(
  "class"
); //false

//relation between br and form
//how to get get only the elements that related to the form(input,textarea) and exclude (br,div)
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].childNodes; //[text,br,input,br,textarea,br,button,br]

//children:exclude text and comments and  <!DOCTYPE html> :not tag but Node :(Directive)

document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].children; //[input,br,textarea,button] //HTMLCollection all tags including br
//elements :for forms to get only the elements that related to the form(input,textarea) and exclude (br,div)

document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].elements; //HTMLFormControlsCollection [input, textarea, select, button]
//options & selected
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].elements[2]
  .options; //HTMLOptionsCollection [

document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].elements[2]
  .options[0].selected; //true
//firstChild (Node)
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].firstChild; //text

//lastChild
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2].lastChild; //text

//firstElementChild (Element)
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2]
  .firstElementChild; //input

//lastElementChild
document.childNodes[0].childNodes[2].childNodes[4].childNodes[2]
  .lastElementChild; //button
```

> [!bug] any change in html removing spaces and new lines will change the index of the elements

---

> there are better ways to select elements
> collection
> images
> forms
> links
> that's it

```js
document.images; //HTMLCollection [img, img]
document.images[0]; //img
document.images[0].src; //1.png
document.images[0].src = "2.png"; //change the image
document.images[0].style.border = "1px solid red"; //change the image

document.links; //HTMLCollection [a, a]
document.links[0].innerText; //google
document.links[0].href; //https://www.google.com/
document.links[0].href = ""; //remove the link
```

---

### Getters

```js
//better way to select
//getters
document.getElementById("id"); //Element || null
//XXX if we had two elements with the same id it will return the first one
document.getElementsByClassName("className"); //HTMLCollection[empty] || HTMLCollection[div#first, div#second]
//we should check the length before using it
document.getElementsByTagName("tagName"); //HTMLCollection[empty] || HTMLCollection[div#first, div#second]
document.getElementsByName();

document.getElementsByName("name")[0].innerText; //name
document.getElementsByName("name")[0].innerText = "new name"; //change the name

//better way to select using css selectors in js
document.getElementByTagName("p[class='className']"); //HTMLCollection[]  //this is not tag name

//so we use querySelector
```

> [!bug] `document.getElementsByClassName("className").innerText = "new text";` will not work because it is a collection not an element
> this adds property to the collection not the element
>
> > [!success] `document.getElementsByClassName("className")[0].innerText = "new text";`

---

### querySelector

```js
//querySelector
document.querySelector("p"); //Element || null
//will return the first element
document.querySelectorAll("p"); //NodeList [p, p] || NodeList [empty]
//will return all elements

//class
document.querySelector(".className"); //Element || null
document.querySelectorAll(".className"); // NodeList [div#first.className, div#second.className] || NodeList [empty]

//id
document.querySelector("#id"); //Element || null
document.querySelectorAll("#id"); //NodeList [div#first.id] || NodeList [empty]

document.querySelectorAll("li").parentNode; //ul
document.querySelectorAll("li").parentNode.nodeName; //UL
```

---

### CRUD

> [!info] CRUD
>
> - Create
> - Read
> - Update
> - Delete

> innerHTML - innerText - value(input)

> [!bug] input element has value property not innerText

> [!bug] other elements has innerText not value

> HTML in lecture

```js
//innerHTML : get the html inside the element
document.getElementByTagName("div")[0].innerHTML; //"<h1>DOM</h1><p>Document Object Model</p>"

//innerText : get the text inside the element
document.getElementByTagName("div")[0].innerText; //"DOMDocument Object Model"

document.getElementByTagName("div")[0].innerText = "new text"; //change the text
//if we add html tags it will be added as text
document.getElementByTagName("div")[0].innerText = "<h1>new text</h1>"; //"<h1>new text</h1>"
//if we want to add html tags we should use innerHTML

//value : get the value of the input
// if we use innerText or innerHTML it will return empty string
function myFunction() {
  //select user input from input type text
  //then add it to the div
  var userInput = document.querySelector("input[type=text]").value;
  ///////////////vlidation
  var container = document.querySelector("div");
  container.innerText = userInput; //this will remove the previous text
  container.innerText += userInput; //this will add the new text to the previous text
  //if we want to add html tags we should use innerHTML
  container.innerHTML += userInput; //this will add the new text to the previous text
}
```

---

```js
//outerHTML

//outerHTML : get the html of the element
//this reutrn the element with its html as string
document.getElementByTagName("div")[0].outerHTML; //"<div><h1>DOM</h1><p>Document Object Model</p></div>"
```

---

##### classList & className

> html in lecture

```js
function applyStyleOn() {
  var targetP = document.getElementById("pone");
  //Every element has a property style (not Node)
  //style is an object that has all the css properties
  //change properties in its camelCase
  //background-color => backgroundColor
  targetP.style.width = "200px";
  targetP.style.backgroundColor = "red";
  targetP.style.border = "1px solid red";
  targetP.style.borderTopLeftRadius = "10px";

  //className
  // targetP.className = "styleon";//this set the class name
  //if we want to add more than one class
  //we should use classList
  targetP.classList.add("styleon");
}

function removeStyle() {
  var targetP = document.getElementById("pone");

  targetP.style.border = "none";
  targetP.style.width = "100px";
  targetP.style.backgroundColor = "white";

  //better way to use classes

  //className
  //targetP.className = "";//remove all classes

  //classList
  targetP.classList.remove("styleon");
}

function applyStyleTwo() {
  var targetP = document.getElementById("pone");
  // targetP.className = "styletwo";//this set the class name and remove the previous class
  //targetP.className += " styletwo ";//this add the class name to the previous class
  // beeter way to use classList
  targetP.classList.add("styletwo");
}
function removeStyleTwo() {
  var targetP = document.getElementById("pone");
  targetP.classList.remove("styletwo");
}

//toggle
function toggleStyle() {
  var targetP = document.getElementById("pone");
  targetP.classList.toggle("styletwo");
}
```

---

#### creation

> html in lecture

```js
//createElement

function addRow() {
  //1- get user input
  var userInput = document.querySelector("input[type=text]").value;
  //2- create tr
  // document.querySelector("table:first-of-type > tbody");
  document.getElementsByTagName("tbody")[0].innerHTML +=
    "<tr><td>1</td><td>" + userInput + "</td></tr>"; //this would work but it is not the best way

  var createdTr = document.createElement("tr"); //<tr></tr> as node we use properties and methods on it

  //3- create 2 td
  var td1 = document.createElement("td"); //<td></td>
  var td2 = document.createElement("td"); //<td></td>
  //they are totally isolated from each other and from the DOM

  //4- assign for first td serial increment number
  td1.innerText = 200; //<td>200</td>

  //5- assign for second td user input
  td2.innerText = userInput; //<td>userInput</td>
  //6- append created td to created tr
  //appendChild : add element as a child to another element
  createdTr.appendChild(td1); //<tr><td>200</td></tr>
  createdTr.appendChild(td2); //<tr><td>200</td><td>userInput</td></tr>
  //this all in the memory not in the DOM

  //7- select table
  //8- append created tr to table

  //we can add attribute to the created element
  //createAttribute
  var createdTd1Attribute = document.createAttribute("id"); //id=""
  createdAttribute.value = "100"; //id="100"
  //setAttributeNode : accept reference to attribute
  td1.setAttributeNode(createdTd1Attribute); //<td id="100">200</td>
  //or
  //setAttribute : accept attribute name and value
  td1.setAttribute("id", "100"); //<td id="100">200</td>
  document.getElementsByTagName("tbody")[0].appendChild(createdTr); //<tbody><tr><td>200</td><td>userInput</td></tr></tbody>
}
```

> [!success] we can append to body directly

---

### insertAdjacentHTML

> html in lecture

> [!info] insertAdjacentHTML
>
> - insertAdjacentHTML(position, text)
> - position: beforebegin, afterbegin, beforeend, afterend
> - text: html text

```js
//insertAdjacentHTML

function addFun() {
  var dummyH3 = document.createElement("h3");
  dummyH3.innerText = "dummy";
  var targetDiv = document.getElementByTagName("div")[0];
  targetDiv.insertAdjacentHTML("beforebegin", dummyH3); //<h3>dummy</h3><div><h1>DOM</h1><p>Document Object Model</p></div>
  targetDiv.insertAdjacentHTML("afterbegin", dummyH3); //<div><h3>dummy</h3><h1>DOM</h1><p>Document Object Model</p></div>

  targetDiv.insertAdjacentHTML("beforeend", dummyH3); //<div><h1>DOM</h1><p>Document Object Model</p><h3>dummy</h3></div>
}
```

---

### remove

> [!info] remove vs removeChild
> removeChild:parent.removeChild(child to be removed)
> remove: element.remove()//remove the element itself

```js
//remove
//return undefined
document.getElementsByTagName("li")[0].remove(); //remove the element itself
//removeChild
//return the removed element
document
  .getElementsByTagName("ul")[0]
  .removeChild(document.getElementsByTagName("li")[0]); //remove the element itself
```

#### document.cookie

> [!warning] cookie
>
> - cookie is a small piece of data stored on the user's computer by the web browser while browsing a website
> - cookies can also be used to remember pieces of information that the user previously entered into form fields such as names, addresses, passwords, and credit card numbers
> - cookies are mainly used for these three purposes:
>   - Session management: Logins, shopping carts, game scores, or anything else the server should remember
>   - Personalization: User preferences, themes, and other settings
>   - Tracking: Recording and analyzing user behavior
>     max size of cookie is 4kb
>     cookie sent with every request
>     localStorage and sessionStorage are not sent with every request but it has size of 10mb
>     cookie is identified by domain and can be accessed by pages in that domain

```js
//cookie
//it expires after session ends
document.cookie; //"" //empty string
var userName = "ahmed";
var userPass = "123";
//key value pair =>userName=ahmed
document.cookie = "userName=" + userName; //userName=ahmed
var date = new Date("2023-01-01");
document.cookie = "userName=" + userName + ";expires=" + date; //userName=ahmed;expires=Wed Jan 01 2023

//get from cookie from multiple cookies
document.cookie.split(";")[0].split("=")[1]; //ahmed
```

---

## Delete a Cookie with JavaScript

Deleting a cookie is very simple.

You don't have to specify a cookie value when you delete a cookie.

Just set the expires parameter to a past date:

document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;";

1. taskForExam:

- same layout for slide show:
  -startExam , endExam, next
  -ques = [{qTitle:wht is js, qAns:[a,b,c,d], qCorrectAns:0,
  qdegree:10},{qTitle:wht is js, qAns:[a,b,c], qCorrectAns:0,
  qdegree:10},{qTitle:wht is js, qAns:[a,b,c,d], qCorrectAns:0,
  qdegree:10}]
  var Exams = [{examTitle:js, examQues:ques, examDegree:30, examTime:30}],

  2. Form : but save in cookie

  3. create table and add row and delete row
  4. todo list

```js
//taskForExam
//same layout for slide show:
```
