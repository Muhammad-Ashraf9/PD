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
>   > - how can different applications will send and receive data from the server?

> [!done] protocol
>
> - we need to find a way to communicate between different applications and the server
> - protocol is a set of rules that define how the communication between different applications and the server should be done
> - any protocol can be used depending on what we need to do (same machine, different machines, ...)

> [!example] proxy class
>
> - how can java application communicate with a server that is written in c#?
> - calling methods from the server in java application
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
> - controller inherits from `ApiController` instead of `Controller`
> - `Controller` has the capability in ASP.NET MVC
> - `ControllerBase` is the base class for both `Controller` and `ApiController` has only the basic functionality

```csharp
[apiController]
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
> - `api/{controller}/{id}` is the default routing in WebAPI
> - no action in the routing as the action is mapped according to the HTTP method (GET, POST, PUT, DELETE)

> [!done] HTTP Methods (verbs)
>
> - is used to define the action that will be done on the server
> - `GET` is used to get resources (data) from the server
> - `POST` is used to add resources (data) to the server
> - `PUT` is used to update resources (data) on the server
> - `DELETE` is used to delete resources (data) from the server
>   > [!warning]
> - but it is not mandatory as we can use `GET` to add resources to the server or `POST` to get resources from the server

> [!warning] head & body of the request
>
> - no body in `GET`, `DELETE` method (only head) so the data should be sent in the URL like `?id=1`
> - `POST`, `PUT` methods have a body that contains the data that will be sent to the server
> - any response has a body that contains the data that will be sent to the client

> [!done] each method has to have unique URI + HTTP method
>
> - used to map the URI to the method that will be called on the server
