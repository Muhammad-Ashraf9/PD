### Node as RESTful API vs Node as a Web application

> [!tip] Web application
>
> - statful server
> - request:
> - session

> [!warning] Search
>
> - API vs RESTful API

---

# Break

---

### Node as RESTful API

```js
//app.js
const express = require("express");
const server = express();
server.listen(8080, () => {
  console.log("Server is running on port 8080");
});
// ------------------------------ First Middleware
server.use((request, response) => {
  console.log("First use function", request.url, request.method);

  response.json({
    message: "Hello World",
  });
});

//----------------------------- Second Middleware (Authorization)
server.use((request, response) => {
  console.log("Authorization Middleware");
});
```

> [!tip] response.json
>
> - send a JSON response
> - automatically sets Content-Type to application/json
> - automatically stringifies the JSON

> [!warning] logging
>
> - print data about the request

> [!bug] second middleware does not get called
>
> - we need to control the flow of the middleware
> - call next() to call the next middleware
> - `next` is a function that is passed to the middleware function (third argument)
> - `(request, response, next) => { ... next() ... }`

```js
//app.js
const express = require("express");
const server = express();
server.listen(8080, () => {
  console.log("Server is running on port 8080");
});
// ------------------------------ First Middleware
server.use((request, response, next) => {
  console.log("First use function", request.url, request.method);
  response.json({
    message: "Hello World",
  });
  next();
});

//----------------------------- Second Middleware (Authorization)
server.use((request, response, next) => {
  console.log("Authorization Middleware");
  response.json({
    message: "Authorization Middleware",
  }); //Error: Can't set headers after they are sent.
});
```

> [!error] `Error`: Can't set headers after they are sent.
>
> - we can't send multiple responses
>   > [!done] response is sent only once at the end of the middleware chain

```js
//app.js
const express = require("express");
const server = express();
server.listen(8080, () => {
  console.log("Server is running on port 8080");
});
// ------------------------------ First Middleware
server.use((request, response, next) => {
  console.log("First use function", request.url, request.method);
  next();
});

//----------------------------- Second Middleware (Authorization)
server.use((request, response, next) => {
  console.log("Authorization Middleware");
  //   response.json({
  //     message: "Authorization Middleware",
  //   });
  //response has to be sent only once at the end of the middleware chain
  next();
});

//----------------------------- Third Middleware (Not Found)
server.use((request, response, next) => {
  console.log("Not Found Middleware");
  response.status(404).json({
    message: "Not Found",
  });
  //no need to call next() as this is the last middleware
});
// ------------------------------ Fourth Middleware (Error)
//we have to write all 4 parameters to define an error middleware
// function(error, request, response, next)
// function.length = 4 => that's how express knows that this is an error middleware
server.use((error, request, response, next) => {
  console.log("Error Middleware");
  response.status(500).json({
    message: error + "",
  });
});
```

> [!bug] 404 Not Found
>
> - used to handle the case when the requested resource is not found
> - no need to call next() as this is the last middleware

> [!bug] Error Middleware
>
> - used to handle the case when an error occurs
> - we have to write all 4 parameters to define an error middleware
> - `function(error, request, response, next)`
> - `function.length = 4` => that's how express knows that this is an error middleware
>   > [!done] error & not found middleware both can have `response.json()`
>   >
>   > - as error get called when an error occurs (automatically jumps to the error middleware)
>   > - and not found get called when the requested resource is not found
>   > - No conflict between the two middleware as they are called in different scenarios

---

> [!warning] Only `Error` & `Not Found` & `Routes` can send a response (`response.json()`,...)
>
> - ` middlewares` not recommended to send a response
> - better way is to call the `error` middleware
>   > [!example]
>   >
>   > - authorization middleware's job is to check if the user is authorized or not (not to send a response)

```js
//----------------------------- second middleware (Authorization)
server.use((request, response, next) => {
  console.log("Authorization Middleware");
  if (true) {
    //if authorized
    next();
  } else {
    // response.status(401).json({
    //   message: "Unauthorized",
    // });
    //instead of sending a response, we can call the error middleware
    next(new Error("Unauthorized"));
    //next() with an error object will automatically jump to the error middleware

    //this is better as we can have a single error middleware for all the errors
    throw new Error("Unauthorized"); //this will also jump to the error middleware as express will catch the error and pass it to the error middleware
  }
});
```

---

##### URL and HTTP Methods

> [!error] NOT Recommended URL and HTTP Methods
>
> - `/students`
>
> 1. `GET` `/students` => get all students
> 2. `POST` `/students/insert` => add a new student
> 3. `POST` `/students/update` => update a existing student
> 4. `POST` `/students/delete` => delete a existing student
>    </br>
>    > [!done] Recommended
>    >
>    > - Instead of using the HTTP methods in the URL, we can use the HTTP methods themselves
>    >
>    > 1. `GET` `/students` => get all students
>    > 1. `POST` `/students` => add a new student
>    > 1. `PATCH` `/students` => update existing student
>    > 1. `DELETE` `/students` => delete existing student

> [!example] `PATCH` vs `PUT`
>
> - `PATCH` is used to update a part of the resource
> - `PUT` is used to replace the resource

---

### Routes

> [!Error] ==order of the middleware and routes matters==
>
> - if we put the not found middleware before the routes, then it will be called for every request
> - we need to order the middleware and routes
>
> 1. logging middleware
> 1. authorization middleware
> 1. routes
> 1. not found middleware
> 2. error middleware

> [!tip] `use` vs `get`, `post`, `patch`, `delete`
>
> - `use` is used to define a middleware that will be called for every request
> - `server.get` is used to define a route that will be called only for the `GET` requests
> - `server.get('/students', (request, response) => { ... })` => this route will be called only for the `GET` requests to `/students`

```js
//app.js
const express = require("express");
const server = express();
server.listen(8080, () => {
  console.log("Server is running on port 8080");
});
// ------------------------------ First Middleware
server.use((request, response, next) => {
  console.log("First use function", request.url, request.method);
  next();
});

//----------------------------- Second Middleware (Authorization)
server.use((request, response, next) => {
  console.log("Authorization Middleware");
  if (true) {
    //if authorized
    next();
  } else {
    next(new Error("Unauthorized"));
  }
});

//----------------------------- Routes
server.get("/students", (request, response) => {
  response.json({
    data: "Get all students",
  });
});

server.post("/students", (request, response) => {
  response.json({
    data: "Add a new student",
  });
});

server.get("/departments", (request, response) => {
  response.json({
    data: "Add a new department",
  });
});

//----------------------------- Third Middleware (Not Found)
//this will be called if url is something other than /students or /departments
server.use((request, response, next) => {
  console.log("Not Found Middleware");
  response.status(404).json({
    message: "Not Found",
  });
});

// ------------------------------ Fourth Middleware (Error)
server.use((error, request, response, next) => {
  console.log("Error Middleware");
  response.status(500).json({
    message: error + "",
  });
});
```

---

> [!warning] postman & thunder client(visual studio code)
>
> - to test the API as we can't test it in the browser ( the browser only supports `GET` requests)

#### #Nodejs-lab-2
> -  CORS
> - Morgan