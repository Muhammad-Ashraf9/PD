# Web APIs

![](Pasted%20image%2020240421091535.png)

> [!warning] problem with different applications (web, mobile, desktop)
>
> - difficulty to synchronize data between them specially because they are using different databases(sqlserver, sqlite, oracle)
> - repeating the same logic in different applications with different languages(java, c#, ...)
> - mobile applications prefer to only used for UI and not for business logic => need a server to handle the business logic

![](Pasted%20image%2020240421092342.png)

> [!done] Solution: Web APIs
>
> - one database
> - one server that handle the business logic with one language
> - different clients that can interact with the server
>   > [!danger] Problem with this approach
>   >
>   > - different languages are not compatible with each other
>   > - database required to be hosted on a server as well as the server that handle the business logic and different clients
>   > - how different server machines can communicate with each other?
>   > - how can different applications  send and receive data from the server?

> [!done] protocol
>
> - we need to find a way to communicate between different applications and the server
> - protocol is a set of rules that define how the communication between different applications and the server should be done
> - any protocol can be used depending on what we need to do (same machine, different machines, ...)

> [!example] proxy class
>
> - how can java application communicate with a server that is written in c#?
> - calling methods in the server from java application
> - a proxy class that will convert the java object to c# object and vice versa

---

### different protocols

> [!done] SOAP
>
> - Simple Object Access Protocol
> - XML based protocol
> - used to exchange information
> - body can contain any type of data (xml, json) inside SOAP file

> [!example] WSDL
>
> - Web Service Description Language

> [!danger] Problem with SOAP
>
> - heavy: as it uses XML which is heavier than JSON
> - AJAX became popular and no proxy class in javascript so it was not compatible with SOAP
> - problem with mobile applications as they are not compatible with SOAP

> [!done] HTTP/s
>
> - used to communicate between different applications and the server instead of SOAP
> - Make methods (actions, functions) on the server linked to a URIcan be called from the client side
> - supports different types of data (xml, json, text, image, ...)
> - help pages can be created to show the methods that can be called from the client side (Swagger, ...)
> - Known as REST Model (Representational State Transfer)
> - based on the HTTP protocol

> [!tip] URL vs URI
>
> - (URI = URL + URN)
> - `URL` is a subset of `URI` -`http://www.google.com` is a `URL`
> - `http://www.google.com/search` is `URI`

> [!example] Service
>
> - These models (REST, SOAP) are used to build the backend of the application that will handle the business logic as service
> - can be hosted on a server that can be accessed by different clients

---

### ASP.NET WebAPI

> [!tip] ASP.NET WebAPI
>
> - only REST model (HTTP/s)
> - for SOAP model, we can use WCF (Windows Communication Foundation)
> - a framework that makes it easy to build HTTP services that reach a broad range of clients, including browsers and mobile devices
> - is the same as ASP.NET MVC but withouth the view part
> - (controller, model, model binder, routing, security, ...) are the same as ASP.NET MVC

> [!done] Controllers
>
> - `controllers` inherits from `ControllerBase` instead of `Controller`
> - `Controller` has the capability of  controllers  in ASP.NET MVC
> - `ControllerBase` is the base class for `Controller`
> - `ControllerBase` has only the basic functionality that is needed for WebAPI excluding the view part

```csharp
[ApiController]
// is used to define the controller as a WebAPI controller

EmployeeController : ControllerBase{
    getall();
    getbyid();
    post();
    put();
    delete();
}
```

> [!warning] Routing
>
> - `/{controller}/{action}/{id}` is the default routing in ASP.NET MVC
> - not recommended to use this routing in WebAPI
> - `api/{controller}/{id}` is the recommended routing in WebAPI
> - no action in the routing as the action is mapped according to the HTTP method (GET, POST, PUT, DELETE)
> -  thats why every method has to have a unique (URI + HTTP) method

> [!done] HTTP Methods (verbs)
>
> - is used to define the action that will be done on the server
> - `GET` is used to get resources (data) from the server
> - `POST` is used to add resources (data) to the server
> - `PUT` is used to update resources (data) on the server
> - `DELETE` is used to delete resources (data) from the server
>   > [!warning]
>> - but it is not mandatory as we can use `GET` to add resources to the server or `POST` to get resources from the server but it is ==not recommended==

> [!warning] head & body of the request
>
> - no body in `GET`, `DELETE` method (only head) so the data should be sent in the URL like `?id=1`
> - `POST`, `PUT` methods have a body that contains the data that will be sent to the server
> - any response has a body that contains the data that will be sent to the client

> [!done] each method has to have unique URI + HTTP method
>
> - used to map the URI to the method that will be called on the server

---

## Break

---

> [!done] preflight request
>
> - is a request that is used to check if the server allows the client to send the request of the specific method (GET, POST, PUT, DELETE, ...) before sending the actual request

> [!tip] create a WebAPI project
>
> - create a new project in visual studio (ASP.NET Core Web API)

> [!example] authentication type
>
> - none: no authentication

> [!example] Docker
>
> - used to create a container that contains the application and all its dependencies so it can be run on any machine that has docker installed

> [!example] enable OpenAPI support (Swagger)
>
> - used to create a help page that shows the methods that can be called from the client side
> - it will install swagger and swagger UI packages and add the swagger configuration in the `startup.cs` file

> [!example] use controllers
>
> - use controllers instead of minimal APIs
> - minimal APIs are used to create a simple API without the need to create a controller (like: python API)

> [!warning] `app.MapControllers();`
>
> - do not depend on default routing like MVC but it is recommended to use attribute routing

> [!tip] add API controller

```csharp
[Route("api/[controller]")]
//{} string interpolation
//[] token replacement: in runtime the token will be replaced by the controller name

// [Route("api/[controller]/[action]")]
//this will make the action part of the URI

[ApiController]
//is used to define the controller as a WebAPI controller
public class StudentsController : ControllerBase
// inherits from ControllerBase instead of Controller
{
}

```

> [!tip] install required packages
>
> - `Microsoft.EntityFrameworkCore.SqlServer`
> - `Microsoft.EntityFrameworkCore.Tools`
>   > [!warning] working database first
>   >
>   > - reverse engineering: using `EF Core Power Tools` to create the model classes from the database
>   > - moving the connection string to the `appsettings.json` file and add `Trustservercertificate=true` to the connection string
>   > - add dbcontext to the `startup.cs` file

```csharp
builder.Services.AddDbContext<DBContext>(options => options.UseSqlServer(Configuration.GetConnectionString("DBConnection")));
```

> [!tip] students controller

```csharp
//StudentsController.cs

[Rout("api/[controller]")]
[ApiController]
public class StudentsController : ControllerBase
{
    private readonly DBContext db;

    public StudentsController(DBContext context)
    {
        db = context;
    }

    [HttpGet]
    // need to be added or it will not work as there is no default routing like MVC
    public List<Student> GetAll()
    {
        return db.Students.ToList();
    }

    [HttpGet("{id}")]
    public ActionResult GetByID(int id)
    {
       return db.Students.Find(id);

    }

}

```

> [!warning] Globalization vs Localization
>
> - localization: mini language based on the language of the user
> - globalization: the process of designing and developing a software application that functions for multiple cultures ( having 2 buttons for example in the same page in different languages)

> [!error] need to edit project file to avoid globalization-invariant error
>
> - add `<InvariantGlobalization>false</InvariantGlobalization>`

> [!example] `httpstatuscode`
>
> - any response has a status code that indicates the status of the response
> - to inform the client if the request was successful or not
> - 200: OK
> - 201: Created
> - 204: NoContent (successful but no data to return)
> - 400: BadRequest (client error)
> - 401: Unauthorized
> - 404: NotFound
> - 405: MethodNotAllowed: asking for a method that is not allowed(unauthorized method, or not implemented)
> - 415: UnsupportedMediaType
> - 500: InternalServerError (server error)
>
>   > [!done] we have to return status code that indicates the status of the response


>[!tip] - edit the `GetByID` method to return status code

```csharp
[HttpGet("{id}")]
public ActionResult GetByID(int id)
{
    var student = db.Students.Find(id);
    if(student == null)
    {
        return NotFound();//404
    }
    return Ok(student);//200
}

[HttpGet("{name}")]
//AmbiguousMatchException: The request matched multiple endpoints
public ActionResult GetByName(string name)
{
    var student = db.Students.FirstOrDefault(s => s.Name == name);
    if(student == null)
    {
        return NotFound();//404
    }
    return Ok(student);//200
}
```

> [!error] AmbiguousMatchException
>
> - as the routing is the same for both methods (GetByID, GetByName)
>   > [!warning]
>   >
>   > - we cannot use query string as it matches other method (GetAll) `?name=`

> [!done] 1. change the routing

```csharp
[HttpGet("api/sts/{name}")]
public ActionResult GetByName(string name)
{
    var student = db.Students.FirstOrDefault(s => s.Name == name);
    if(student == null)
    {
        return NotFound();//404
    }
    return Ok(student);//200
}
```

> [!done] 2. use constraints in the routing
>
> - it will work as there is difference between the id and the name -`[HttpGet("{id:int}")]` will accept only integer any other will go to the other method
> - `[HttpGet("{name:alpha}")]` will accept only string any other will go to the other method
> - but is not recommended the first solution is better

```csharp
[HttpGet("{id:int}")]
public ActionResult GetByID(int id)
{
    var student = db.Students.Find(id);
    if(student == null)
    {
        return NotFound();//404
    }
    return Ok(student);//200
}

[HttpGet("{name:alpha}")]
public ActionResult GetByName(string name)
{
    var student = db.Students.FirstOrDefault(s => s.Name == name);
    if(student == null)
    {
        return NotFound();//404
    }
    return Ok(student);//200
}
```

> [!example] add a new student

```csharp
[HttpPost]
public ActionResult Add(Student student)
{
    if(student == null)
    {
        return BadRequest();//400
    }
    //we can check if it is valid using model state
    if(!ModelState.IsValid)
    {
        return BadRequest(ModelState);//400
    }
    db.Students.Add(student);
    db.SaveChanges();
    // return Created("ay 7aga", student);//it takes 2 parameters the URI and the data added

    return CreatedAtAction(
        "GetByID"
        , new {id = student.ID}, student);
    //used to return the URI of the added object
    //so we can use it to get the added object


}
```

> [!done] test post method
>
> - swagger
> - postman
> - fiddler

> [!tip] `fiddler`
>
> - composer tab
> - post
> - URL: `https://localhost:7167/api/students`
> - body: get the body from the swagger
>
> ```json
> {
>   "name": "7ido",
>   "age": 25,
>   "gpa": 3.5
> }
> ```
>
> > [!error] 415 UnsupportedMediaType
> >
> > - we need to add the content type in the header
> > - `Content-Type: application/json`

> [!example] MIME type
>
> - is a standard that indicates the type of data that is sent in the body of the request
> - `application/json` is used to indicate that the data is in json format

> [!tip] edit student
>
> - we use `PUT` method to update the student as it has a body thats all that matters
> - as we use `PATCH` as well

- 3:20

```csharp
[HttpPut("{id}")]
// is used to get the id from the URI  and confirm
public ActionResult edit(int id, Student student)
{
    if(student == null)
    {
        return BadRequest();//400
    }
    if(!student.ID != id)
    {
        return BadRequest();//400
    }
    // db.Update(student);

    // db.Students.Update(student);//it is better to use this one


    db.Entry(student).State = EntityState.Modified;
    //this will update the object

    db.SaveChanges();

    return NoContent();//204

}
```

> [!error] if we send the id different from the id in the body
>
> - this will return bad request as the id in the body is different from the id in the URI

> [!done] delete student

```csharp
[HttpDelete("{id}")]
public ActionResult Delete(int id)
{
    Student student = db.Students.Find(id);
    if(student == null)
    {
        return NotFound();//404
    }
    db.Students.Remove(student);
    db.SaveChanges();
    // return NoContent();//204
    return Ok(student);//200 to return the deleted object
}
```

> [!warning] we can specify from where to get the data
> - from the body `FromBody`
> - from the URI `FromRoute`

---

# lab


![](Pasted%20image%2020240421125056.png)

![](Pasted%20image%2020240421125105.png)

