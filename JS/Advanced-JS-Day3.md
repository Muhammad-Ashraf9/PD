# AJAX

> [!info] client server model
> client sends request to server
> server sends response to client
> client receives response

> [!example] AJAX
> Asynchronous JavaScript and XML
>
> > [!tip] JavaScript is single threaded
> >
> > - JavaScript can only do one thing at a time
> >
> > - synchronous: one thing at a time
> >   waits for one request to finish before starting another

> [!success] Asynchronous calls
>
> - asynchronous: multiple things at a time
>   > [!tip] AJAX is asynchronous
>   > no need to reload the page
>   >
>   > - we can change it to be synchronous if there is dependency

---

## XMLHttpRequest

> [!tip] XML
>
> - eXtensible Markup Language
> - used to be popular for data transfer
> - now JSON is more popular

> [!info] `new XMLHttpRequest()`
>
> - used to make AJAX calls
>   > [!tip] Attributes
>   >
>   > 1. `readyState` 2.`status` 3.`responseText` 4.`responseXML` > > <br>Methods
>   > 2. `open()` 2.`send()` <br>
>   >    Events
>   > 3. `onreadystatechange` 2.`onload` 3.`onerror`

> [!tip] `readyState`
>
> 0: request not initialized (unset)
> 1: server connection established (opened)
> 2: request received (headers received)
> 3: processing request ()
> 4: request finished and response is ready

> [!tip] `status`
>
> 100: "Information"
> 200: "Completed" like 200 OK
> 300: "Redirection"
> 400: "Client Error" like 404 not found
> 500: "Server Error"

---

> [!faq] How to check if request is successful?
> 2 conditions
>
> 1. `readyState` is 4
> 2. `status` is 200

```js
var xhr = new XMLHttpRequest();
// xhr.open("HTTP method", "URL+ResourceName", "async", "username", "password");
//HTTP method: GET, POST, PUT, DELETE
//async: true, false(synchronous)  default is true(async)

// xhr.send("");//must be sent after open to complete the request (empty string for GET)
// xhr.send(data);in case of POST, PUT, DELETE

// xhr.onreadystatechange = function () {
//   if (xhr.readyState === 4 && xhr.status === 200) {
//     console.log(xhr.responseText);
//   }
// };
// fired every time readyState changes

//responseText: (text, JSON, HTML)
//responseXML: (XML)
```

---

> html in lecture(loaddata.html)

> [!danger] we need to run it with live server
> client -> server

```js
//load text
window.addEventListener("load", function () {
  var targetDiv = document.getElementByTagName("div")[0];
  this.documet
    .getElementByTagName("input")[0]
    .addEventListener("click", function () {
      //ajax call xmlHttpRequest
      //1. create object from xmlHttpRequest
      var xhr = new XMLHttpRequest(); // xhr naming convention
      console.log(xhr.readyState); //0 request not initialized
      xhr.open("GET", "http://localhost:3000/file.txt", true);
      xhr.open("GET", "file.txt", true); // works aswell  because we have local server
      xhr.send("");
      //xhr.responseText;// this should be after readyState change
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
          console.log(xhr.responseText); //text - json - html
          targetDiv.innerHTML = xhr.responseText;
        }
      };
    });
});
//ajax caches the data by default
```

---

```js
//load HTML
window.addEventListener("load", function () {
  var targetDiv = document.getElementByTagName("div")[0];
  this.documet
    .getElementByTagName("input")[0]
    .addEventListener("click", function () {
      //ajax call xmlHttpRequest
      //1. create object from xmlHttpRequest
      var xhr = new XMLHttpRequest(); // xhr naming convention
      console.log(xhr.readyState); //0 request not initialized
      xhr.open("GET", "ile.html", true);
      xhr.send("");
      //xhr.responseText;// this should be after readyState change
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
          console.log(xhr.responseText); //text - json - html
          targetDiv.innerText = xhr.responseText; //print source code
          targetDiv.innerHTML = xhr.responseText; //render html
        }
      };
    });
});
```

---

#### JSON

```js
var jsObj = {
  id: 1,
  name: "ahmed",
  passed: true,
};
//JSON : JavaScript Object Notation
// all keys and values must be in  quotes ""
// boolean values can be either in  quotes "" or not
var jsonString = { id: 1, name: "ahmed", passed: true };

// we can convert js object to json string using JSON.stringify()
jsObj; //{id: 1, name: "ahmed", passed: true}
JSON.stringify(jsObj); //'{"id":1,"name":"ahmed","passed":true}'
var jsonString = JSON.stringify(jsObj);
jsonString.id; //undefined we can't access it like this we need to parse it first
JSON.parse(jsonString); //{id: 1, name: "ahmed", passed: true}
JSON.parse(jsonString).id; //1
```

```js
//load JSON
window.addEventListener("load", function () {
  var targetDiv = document.getElementByTagName("div")[0];
  this.documet
    .getElementByTagName("input")[0]
    .addEventListener("click", function () {
      //ajax call xmlHttpRequest
      //1. create object from xmlHttpRequest
      var xhr = new XMLHttpRequest(); // xhr naming convention
      console.log(xhr.readyState); //0 request not initialized
      xhr.open("GET", "file.json", true);
      xhr.send("");
      //xhr.responseText;// this should be after readyState change
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
          console.log(xhr.responseText); // [{"id":1,"name":"ahmed","passed":true},{"id":2,"name":"ali","passed":false}]
          //we directly get json  from xhr.responseText

          //we can render it in a table
          console.log(xhr.responseText); //290 length of string
          //we need to parse it first
          var table = document.createElement("table");
          var result = JSON.parse(xhr.responseText);
          for (var i = 0; i < result.length; i++) {
            var createRow = document.createElement("tr");
            for (var key in result[i]) {
              var createCell = document.createElement("td");
              createCell.innerText = result[i][key];
              createRow.appendChild(createCell);
              console.log(key, result[i][key]);
            }
            table.appendChild(createRow);
          }
          targetDiv.appendChild(table);
        }
      };
    });
});
```

> [[jsonplaceholder.typicode.com]](https://jsonplaceholder.typicode.com/) is a free fake API for testing and prototyping.

```js
//use jsonplaceholder.typicode.com
window.addEventListener("load", function () {
  var targetDiv = document.getElementByTagName("div")[0];
  this.documet
    .getElementByTagName("input")[0]
    .addEventListener("click", function () {
      //ajax call xmlHttpRequest
      //1. create object from xmlHttpRequest
      var xhr = new XMLHttpRequest(); // xhr naming convention
      console.log(xhr.readyState); //0 request not initialized
      xhr.open("GET", "https://jsonplaceholder.typicode.com/users", true);
      xhr.send("");
      //xhr.responseText;// this should be after readyState change
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
          console.log(xhr.responseText); // [{"id":1,"name":"ahmed","passed":true},{"id":2,"name":"ali","passed":false}]
          //we directly get json  from xhr.responseText

          //we can render it in a table
          console.log(xhr.responseText); //290 length of string
          //we need to parse it first

          var table = document.createElement("table");
          var result = JSON.parse(xhr.responseText);
          var createdHeaderRow = document.createElement("tr");
          var headers = Object.keys(result[0]);
          for (var j = 0; j < headers.length; j++) {
            var createHeader = document.createElement("th");
            createHeader.innerText = headers[j];
            createdHeaderRow.appendChild(createHeader);
          }
          table.appendChild(createdHeaderRow);
          for (var i = 0; i < result.length; i++) {
            var createRow = document.createElement("tr");

            for (var key in result[i]) {
              var createCell = document.createElement("td");
              createCell.innerText = result[i][key];
              createRow.appendChild(createCell);
              console.log(key, result[i][key]);
            }
            table.appendChild(createRow);
          }
          targetDiv.appendChild(table);
        }
      };
    });
});
```

---

#### XML

> [!danger] XML
>
> - it must have one root element (students)
> - case sensitive
> - all tags must be closed

```xml
<?xml version="1.0" encoding="UTF-8"?>
<students>
    <student>
        <id>1</id>
        <name>ahmed</name>
        <city>cairo</city>
    </student>
    <student>
        <id>2</id>
        <name>ali</name>
        <city>alex</city>
    </student>
    <student>
        <id>3</id>
        <name>hassan</name>
        <city>giza</city>
</students>

```

```js
//load XML
window.addEventListener("load", function () {
  var targetDiv = document.getElementByTagName("div")[0];
  this.documet
    .getElementByTagName("input")[0]
    .addEventListener("click", function () {
      //ajax call xmlHttpRequest
      //1. create object from xmlHttpRequest
      var xhr = new XMLHttpRequest(); // xhr naming convention
      console.log(xhr.readyState); //0 request not initialized
      xhr.open("GET", "file.xml", true);
      xhr.send("");
      //xhr.responseText;// this should be after readyState change
      var table = document.createElement("table");
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
          console.log(xhr.responseXML);
          //#document <students>​…​</students>​
          //Html => document.children[0] => html
          //XML => document.children[0] => students
          var root = xhr.responseXML.children[0];
          for (var i = 0; i < root.children.length; i++) {
            var createRow = document.createElement("tr");
            for (var j = 0; j < root.children[i].children.length; j++) {
              var createCell = document.createElement("td");
              createCell.innerText = root.children[i].children[j].textContent;
              createRow.appendChild(createCell);
            }
            table.appendChild(createRow);
          }
          targetDiv.appendChild(table);
        }
      };
    });
});
```

> [!tip] innerText vs textContent
> innerText vs textContent
> visiblity: hidden elements
> innerText for html (can't get the text of hidden elements)
> textContent for both (can get the text of hidden elements)

---

> [!success] `localStorage`
>
> - stores data as key-value pairs in the browser
> - data is stored even after the browser is closed
> - data is stored without expiration date
> - domain access between pages
> - `localStorage.setItem("key", "value")`
> - `localStorage.getItem("key")`
> - `localStorage.removeItem("key")`
> - storage limit is 10MB

```js
//localStorage
//save data in local storage
window.addEventListener("load", function () {
  var targetDiv = document.getElementByTagName("div")[0];
  this.documet
    .getElementByTagName("input")[0]
    .addEventListener("click", function () {
      //ajax call xmlHttpRequest
      //1. create object from xmlHttpRequest
      var xhr = new XMLHttpRequest(); // xhr naming convention
      console.log(xhr.readyState); //0 request not initialized
      xhr.open("GET", "https://jsonplaceholder.typicode.com/users", true);
      xhr.send("");
      //xhr.responseText;// this should be after readyState change
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
          console.log(xhr.responseText); // [{"id":1,"name":"ahmed","passed":true},{"id":2,"name":"ali","passed":false}]
          //we directly get json  from xhr.responseText

          //we can render it in a table
          console.log(xhr.responseText); //290 length of string
          //we need to parse it first

          var table = document.createElement("table");
          var result = JSON.parse(xhr.responseText);
          /////////////////
          //we have to stringify it first
          // to avoid [object object]
          //localStorage.setItem("ourObjects", result);
          //localStorage.getItem("ourObjects");//"[object object],[object object]"
          ////////
          //if we set item with the same key it will override it
          localStorage.setItem("ourObjects", JSON.stringify(result));
          //localStorage.getItem("ourObjects");//return string
          //we need to parse it first
          localStorage.getItem("ourObjects")[0]; //"["
          var ourObjects = JSON.parse(localStorage.getItem("ourObjects")); //return array of objects [{},{},{}]

          var createdHeaderRow = document.createElement("tr");
          var headers = Object.keys(result[0]);
          for (var j = 0; j < headers.length; j++) {
            var createHeader = document.createElement("th");
            createHeader.innerText = headers[j];
            createdHeaderRow.appendChild(createHeader);
          }
          table.appendChild(createdHeaderRow);
          for (var i = 0; i < result.length; i++) {
            var createRow = document.createElement("tr");

            for (var key in result[i]) {
              var createCell = document.createElement("td");
              createCell.innerText = result[i][key];
              createRow.appendChild(createCell);
              console.log(key, result[i][key]);
            }
            table.appendChild(createRow);
          }
          targetDiv.appendChild(table);
        }
      };
    });
});
```

---

> html in lecture(associativeArray.html)

```js
//associative array
var arr = [];
arr[0] = "1";
// not recommended
arr["id"] = 100;
arr["name"] = "ahmed";
arr.length; //0
arr; //[id: 100, name: "ahmed"]
arr.push(300);
arr.push(400);

arr; //
//we can't use for loop
for (var i = 0; i < arr.length; i++) {
  console.log(arr[i]); //1,300,400
}
//we can use for in loop
for (var key in arr) {
  console.log(key, arr[key]); //0 1, id 100, name ahmed, 1 300, 2 400
} //length is not printed only user defined properties
//length setter and getter
arr.length; //0
arr.length = 10;

//dense array is what we use (normal array)
```

### #lab-AJS-3
