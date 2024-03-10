## Recap

> [!tip] Recap of the previous session
> [EF-Day2](../EF/EF-Day2.md)

---

> [!tip] steps to create a new web application project
>
> - from visual studio installer, install `ASP.NET and web development` workload
> - create a new project(Template: `ASP.NET Core Web APP(Model-View-Controller)`)
> - run the application will open the browser with the default page `localhost:5098`

> [!note] http vs IISEXPRESS
>
> - for the application to run, it needs a server to host the application
> - http: `Kestrel`
> - IISEXPRESS

> [!example] project dependencies
>
> - `Microsoft.NETCore.App` - contains the core runtime (is used in all .NET Core applications)
> - `Microsoft.AspNetCore.App` - contains the core libraries

> [!note] launchSettings.json
>
> - contains the settings for the application to run
> - `applicationUrl` - the url to run the application
> - `launchBrowser` - to open the browser when the application runs
> - `environmentVariables` - to set the environment variables like `ASPNETCORE_ENVIRONMENT`
> - `ASPNETCORE_ENVIRONMENT` - to set the environment of the application (Development, Production)

> [!warning] `wwwroot` folder
>
> - contains the static files like css, js, images
> - lib: contains the third-party libraries like bootstrap, jquery
> - any request for static files will search in the `wwwroot` folder and can be accessed directly from the browser but this can be changed

> [!tip] `Controllers` folder
>
> - contains the controllers
> - the controller is a class that handles the user request and returns the response
> - the controller class should inherit from `Controller` class

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

> [!note] `Models` folder
>
> - contains the models
> - the model is a class that represents the data

> [!example] `Views` folder
>
> - `.cshtml` - C# and HTML
> - contains the views
> - the view is a file that contains the html and the c# code
> - the view is used to display the data to the user
> - Shared: contains the shared views like `_NavBar.cshtml`, `_Layout.cshtml`

> [!example] `appsettings.json`
>
> - contains the application settings
> - `ConnectionStrings` - to store the connection strings
> - `appsettings.Development.json` - contains the development settings
> - `appsettings.Production.json` - contains the production settings
> - depends on the environment variable `ASPNETCORE_ENVIRONMENT` to load the settings if the same setting is in both files with different values

> [!bug] `Program.cs`
>
> - Entry point of the application

> [!warning] start with the controller as it is the one that handles the user request and returns the response
>
> - From `Add` => `Controller` => choose the template `MVC Controller - Empty` => `Add` => `HomeController` => `Add`
> - can add class as normal but ==MUST== end with `Controller` like `HomeController`, `StudentController`
> - different templates for the controller gives different code to start with
>   > [!bug] any Created Controller suffix with `Controller` like `HomeController`, `StudentController` ==MUST==
>   >
>   > - has to inherit from `Controller` class

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public string Display()
        {
            return "Welcome to Student Controller";
        }
    }
}
```

> [!tip] to request the controller `StudentController` and the method `Display`
>
> - `localhost:5098/Student` - this will get the controller `StudentController` but to get the method `Display` we need to add the method name to the url `localhost:5098/Student/Display`
> - result: `Welcome to Student Controller`
> - as `Content-Type` is not specified, the browser will assume it as `text/html` and display the text in the browser
> - display is action method
> - action method is a method that returns the response to the user
> - when request comes to the `StudentController` :
>
> 1. object of `StudentController` is created
> 1. and from the object, the method `Display` is called
>
> - the method `Display` has to be public and instance method (not static) so it can be called from the object of the class

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public string Display()
        {
            return "Welcome to Student Controller";
        }
        public int Add()
        {
            return 10 ;
        }
        //to call the method Add from the browser use the url localhost:5098/Student/Add
    }
}
```

> [!warning] we want to send data to add method
>
> - to send data to the method, we can use the query string
> - query string is the part of the url that comes after the `?` and contains the key-value pairs like `localhost:5098/Student/Add?x=10&y=20`

> [!done] to receive the data in the method `Add`
>
> - we use `Request` object to get the data from the query string
> - `Request` object is a property of the `Controller` class

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public string Display()
        {
            return "Welcome to Student Controller";
        }
        public int Add(x)
        {
            return x + 10 ;
            // url localhost:5098/Student/Add?x=10
            //result: 20
            // url localhost:5098/Student/Add?y=20
            //result: 10
            //if we don't pass the value of x, it will make the value of x = 0
            //case insensitive
            // url localhost:5098/Student/Add?X=10
            //result: 20
        }
    }
}
```

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public string Display()
        {
            return "Welcome to Student Controller";
        }
        public int Add(int x, int y)
        {
            return x + y ;
            // url localhost:5098/Student/Add?x=10&y=20
            //result: 30
            // url localhost:5098/Student/Add?y=20
            //result: 20 (x=0)
            // url localhost:5098/Student/Add/30?y=20
            //30 not sent as query string we have to use routing system and make parameter (id)

        }
    }
}
```

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public string Display()
        {
            return "Welcome to Student Controller";
        }
        public int Add(int id, int y)
        {
            return id + y ;
            // url localhost:5098/Student/Add/30?y=20
            //result: 50
            // url localhost:5098/Student/Add?id=30&y=20
            //result: 50

        }
    }
}
```

> [!bug] Routing system is more secure than query string
>
> - as the query string is showing the key-value pairs in the url `localhost:5098/Student/Add?y=20`
> - the routing system is more secure as it hides the key `localhost:5098/Student/Add/30`

> [!done] `ControllerName/ActionMethod/Parameter`
>
> - until now to receive the parameter we use `id` as the parameter name

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public string Display()
        {
            return "<h1>Welcome to Student Controller</h1>";//this will return the text not the html and will displayed as it is
        }
    }
}
```

> [!tip] return HTML not text
>
> - to return the html, we use `View` method
> - `View` method is a method of the `Controller` class
> - `View` method is used to return the view to the user
> - `View` method has many overloads
> - `View` method can be used to pass the data to the view

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public ViewResult Show()
        {
            return View("Show");//this will search for the view with the name "Show" in the  /Views/Student/ folder
        }
    }
}
```

> [!tip] we have to create new view for `Show` method
>
> - create a new folder `Student` in the `Views` folder
> - `Add` => `View` => `Show` => `Add` - this will create the view file `Show.cshtml` in the `Views/Student` folder
> - this will create the view file `Show.cshtml` in the `Views/Student` folder

```html
<!-- Show.cshtml -->

@{ layout = null; }
<!--used for  csharp code inside  html-->
<!DOCTYPE html>
<html>
  <head>
    <title>Show</title>
  </head>
  <body>
    <h1>Welcome to Show View</h1>
  </body>
</html>
```

> [!tip] naming convention
>
> - if the name of the view is the same as the name of the action method, we don't have to pass the name of the view to the `View` method
> - `View` method will search for the view with the same name as the action method in the `Views` folder

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public ViewResult Show()
        {
            return View();//this will search for the view with the name "Show" in the  /Views/Student/ folder
            //if it doesn't find the view, it will search in the /Views/Shared/ folder
            //if there is no view with the same name as the action method, it will throw an exception

            //we can pass full path of the view
            //return View("~/Views/Student/Show.cshtml");
            //~ is the root of the application
        }
    }
}
```

> [!error] can't access the view from the browser
>
> - `localhost:5098/Student/Show.cshtml` - this will not work 404 not found
> - you can only route to the action method not the view

---

# Break

---

> [!tip] any code in `.cshtml` file is html code and any code in `@{}` is csharp code
>
> - `@{}` - used to write the csharp code inside the html
> - `@` - used to write the csharp code inside the html

```html
<!-- Show.cshtml -->

@{ layout = null; } @{ int age = 20; string name = "Ahmed"; List<int>
  numbers = new List<int>
    { 10, 20, 30, 40, 50 }; }

    <!DOCTYPE html>
    <html>
      <head>
        <title>Show</title>
      </head>
      <body>
        <h1>Welcome to Show View</h1>
        <!-- we use @ to use csharp code inside the html -->
        <h2>Age: @age</h2>
        @if(age > 18) {
        <h2>Adult</h2>
        } else {
        <h2>@age is less than 18</h2>
        }
        <ul>
          <!-- this will display the length of the list -->
          <h1>@numbers.length</h1>
          <!-- this will display it as text -->
          <h1>xyz@numbers.length</h1>
          <!-- so we have to explicitly tell the razor engine that this is csharp code -->
          <h1>xyz(@numbers.length)</h1>

          @foreach(var number in numbers) {
          <li>@number</li>
          }
        </ul>
      </body>
    </html>
```

> [!warning] Razor Engine will convert the csharp code to only html code
>
> - `@age` will be converted to `20`
> - no if statement will be in the html code
