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
        public int Add(int x)
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
          <h1>xyz@(numbers.length)</h1>

          @foreach(var number in numbers) {
          <li>@number</li>
          }
        </ul>
      </body>
    </html></int
  ></int
>
```

> [!warning] Razor Engine will convert the csharp code to only html code
>
> - `@age` will be converted to `20`
> - no `if` statement will be rendered in the html code

> [!tip] `_ViewImports.cshtml`
>
> - contains the namespaces that are used in the views
> - if we want to use the namespace in the view, we have to add it to the `_ViewImports.cshtml` file
> - no need to add the namespace in every view

```html
@using Demo.Models
```

> [!done] now we can use the `Student` class in the view

```html
<!--
    Show.cshtml
 -->

 @{
    layout = null;
    int age = 20;
    string name = "Ahmed";
    List<int> numbers = new List<int> { 10, 20, 30, 40, 50 };
    Student student = new Student { Id = 1, Name = "Ahmed" };
    List<Student> students = new List<Student>
    {
        new Student { Id = 1, Name = "Ahmed" },
        new Student { Id = 2, Name = "Ali" },
        new Student { Id = 3, Name = "Mohamed" }
    };
 }

<!DOCTYPE html>
<html>
  <head>
    <title>Show</title>
    <!--
        to use the bootstrap
     -->
     <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css">

     <!--
        we can use the site.css file to override the bootstrap styles
      -->
     <link rel="stylesheet" href="~/css/site.css">
  </head>
  <body>

<h1>
    @student
    <!--
        this will call the ToString method of the object
        so we need to override the ToString method in the Student class
     -->
</h1>

<table class="table table-bordered table-hover">
    <thead>
        <tr>
            <th>Id</th>
            <th>Name</th>
        </tr>
    </thead>
    <tbody>
        @foreach(var s in students)
        {
            <tr>
                <td>@s.Id</td>
                <td>@s.Name</td>
            </tr>
        }
    </tbody>
</html>
```

---

> [!bug] return types of the action methods
>
> - `ViewResult` - to return the view `View()`
> - `ContentResult` - to return the text `Content()`
> - `JsonResult` - to return the json `Json()`
> - `NotFoundResult` - to return the 404 not found `NotFound()`
> - `FileContentResult` - to return the file `File()`
> - but we can use `IActionResult` as the return type of the action method to return any of the above return types

> [!done] `IActionResult`
>
> - `IActionResult` is the base interface of all the return types

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public ViewResult Show()
        {
            return View();
            // return "Welcome to Student Controller";//this will throw an exception
            return Content("Welcome to Student Controller");//this will throw an exception
        }
        public ContentResult Display()
        {
            return Content("Welcome to Student Controller");
        }
        //to return either ViewResult or ContentResult
        //IAcctionResult is the base class of all the return types
        public IActionResult Show(int id)
        {
            Department department = new Department { Id = 1, Name = "IT" };
            //no we can return either ViewResult or ContentResult or JsonResult or any other return type
            if(id == 10)
            {
                return View();
            }
            else if(id==20)
            {
                return Content("Welcome to Student Controller");
            }
            else if(id==2)
            {
                return Json(department);
            }
            else
            {
                // file("path", "content-type", "file-name-on-the-browser")
                return File("/images/studentNames.txt", "text/plain", "studentNames.txt");
                // return File("/images/hussien.jpg", "image/jpg", "hussien.jpg");
                // return File("/images/hussien.pdf", "application/pdf", "hussien.pdf");
                //other MIME types like application/zip, image/jpg
            }
        }
    }
}
```

---

> [!note] `IISEXPRESS`
>
> - MIME types (Multipurpose Internet Mail Extensions):
>   - `.pdf` => application/pdf
> - `.zip` => application/zip
>   - `.jpg` => image/jpg

> [!note] `NotFoundResult`
>
> - to return the 404 not found `NotFound()`
> - `StatusCodeResult` - to return the status code `StatusCode( 404 )`
> - `RedirectResult` - to redirect to another url `Redirect( url )`
>

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public IActionResult Show(int id)
        {

                return NotFound();
                // return StatusCode(404);
                // return Redirect("https://www.google.com");
                // return RedirectPermanent("https://www.google.com"); // redirect one time only and the browser will cache the url and will not send the request to the server

            // we can create content result
            return new ContentResult
            {
                Content = <h1>Welcome to Student Controller</h1>,
                ContentType = "text/html",
                StatusCode = 200
            };

            // IT'S BETTER TO USE jUST Content
            return Content("<h1>Welcome to Student Controller</h1>", "text/html");


            return new JsonResult(new { Id = 1, Name = "IT" });

            return new ViewResult
            {
                ViewName = "Show",//this will search for the view with the name "Show" in the  /Views/Student/ folder
            };
        }
    }
}
```

---

> [!tip] Send data from the controller to the view

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public IActionResult Show()
        {
            Student student = new Student { Id = 1, Name = "Huissen" };
            return View(student);//this will send the student object to the view and the view will have the data of the student in the Model property
        }
    }
}
```

> [!tip] now view has the data of the student in the `Model` property

```html
<!--
    Show.cshtml
 -->

@model Demo.Models.Student
<!-- 
    this will tell the razor engine the type of the model
    so we can use the properties of the model in the view
    for the compiler can throw an exception if the property doesn't exist
  -->

@{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Show</title>
  </head>
  <body>
    <h1>
      @Model
      <!-- 
            this will call the ToString method of the object
            so we need to override the ToString method in the Student class
         -->

      @(Model + 1)
      <!-- 
            will wait for the runtime to throw an exception
            how to fix this?
            we can use directive @model to tell the razor engine the type of the model
          -->
    </h1>
    <h2>
      Id: @Model.Deptname
      <!-- 
            the compiler will not throw an exception if the property doesn't exist as it is  dynamic
            but it will throw an exception in the runtime
         -->
    </h2>
    <h2>Name: @Model.Name</h2>
  </body>
</html>
```

> [!done] ` @model Demo.Models.Student`
>
> - now Model has the type of the `Student` class
> - ==strongly typed view==
>   > [!error] the compiler will throw an exception if another type is passed to the view

> [!tip] `if we passed a string to the view the view will consider it as the name of the view`
>
> - `we can pass the view name to the view method and also the model`

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public IActionResult Show()
        {
            Student student = new Student { Id = 1, Name = "Huissen" };
            return View("Show", student);
        }
    }
}
```

> [!warning] `we can pass only one model to the view`
>
> - when creating razor page it creates it with type of the model `<TModel>`
> - `@model` directive is used to tell the razor engine the type of the model
>   > [!done] we can create `ViewModel` to pass multiple models to the view
>   >
>   > - we can create view model for action method
>   > - name has to end with `ViewModel` like `StudentViewModel`

```csharp
//// ShowViewModel.cs
using Demo.Models;

namespace Demo.ViewModels
{
    public class ShowViewModel
    {
        public Student Student { get; set; }
        public Department Department { get; set; }
    }
}
```

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public IActionResult Show()
        {
            Student student = new Student { Id = 1, Name = "Huissen" };

            Department department = new Department { Id = 1, Name = "IT" };
            ShowViewModel viewModel = new ShowViewModel
            {
                Student = student,
                Department = department
            };

            return View(viewModel);
        }
    }
}
```

> [!done] we add using statement for the `Demo.ViewModels` namespace in the `_ViewImports.cshtml` file
>
> - now we can use the `ShowViewModel` in the view

```html
<!--
    Show.cshtml
 -->

@model Demo.ViewModels.ShowViewModel @{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Show</title>
  </head>
  <body>
    <h1>@Model.Student.Name</h1>
    <h2>@Model.Department.Name</h2>
  </body>
</html>
```

---

> [!tip] `ViewData`
>
> - `ViewData` is a dictionary that is used to pass the data from the controller to the view
> - `ViewData` is a property of the `Controller` class
> - `ViewData["key"] = value` value can be any type(object)

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public IActionResult Show()
        {

            ViewData["Id"] = 1;
            ViewData["Name"] = "Ahmed";
            ViewData["Age"] = 20;
            ViewData["obj"] = new Student { Id = 1, Name = "Ahmed" };
            return View();
        }
    }
}
```

```html
<!--
    Show.cshtml
 -->

@{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Show</title>
  </head>
  <body>
    <h1>@ViewData["Id"]</h1>
    <h1>@ViewData["Name"]</h1>
    <h3>@ViewData["Age"]</h3>

    <h1>
      @(ViewData["Age"] + 10)
      <!-- 
            error as the ViewData["Age"] is of type object and we can't add 10 to it
            so we have to cast it to int
         -->
      @((int)ViewData["Age"] + 10)
    </h1>
    <h1>
      <!-- 
            same in the case of the object
            we have to cast it to Student to access the properties
         -->
      @((Student)ViewData["obj"] as Student).Name
    </h1>
  </body>
</html>
```

> [!done] `ViewBag`
>
> - `ViewBag` is a dynamic property that is used to pass the data from the controller to the view
> - `ViewBag` is a wrapper around `ViewData`
> - any data that is added to the `ViewBag` is added to the `ViewData` dictionary

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public IActionResult Show()
        {
            ViewBag.Id = 1;
            ViewBag.Name = "Ahmed";
            ViewBag.Age = 20;
            ViewBag.obj = new Student { Id = 1, Name = "Ahmed" };
            return View();
        }
    }
}
```

```html
<!--
    Show.cshtml
 -->

@{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Show</title>
  </head>
  <body>
    <h1>@ViewBag.Id</h1>
    <h1>@ViewBag.Name</h1>
    <h3>@ViewBag.Age</h3>

    <h1>
      @(ViewBag.Age + 10)
      <!-- 
            error as the ViewBag.Age is of type object and we can't add 10 to it
            so we have to cast it to int
         -->
      @((int)ViewBag.Age + 10)
    </h1>
    <h1>@((Student)ViewBag.obj as Student).Name</h1>
  </body>
</html>
```

```csharp
// StudentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class StudentController : Controller
    {
        public IActionResult Show()
        {
            ViewBag.Id = 1;
            ViewData["Id"] = 9;//this will override the value of the ViewBag
            return View();
    }
}
```

```html
<!--
    Show.cshtml
 -->

@{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Show</title>
  </head>
  <body>
    <h1></h1>
    <h1>@((int)ViewData["Id"]+ 10)</h1>
    <!-- 
        this will render 19
     -->
  </body>
</html>
```

---

> [!example] DeparmentController
>
> - create a new controller `DepartmentController`

```csharp
// DepartmentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class DepartmentController : Controller
    {
        public IActionResult Create()
        {
            return View();
        }
    }
}
```

> [!done] create a new view for the `Create` action method
>
> - `Add` => `View` => `Create` => `Add` - this will create the view file `Create.cshtml` in the `Views/Department` folder

````html
<!--
    Create.cshtml
 -->

@{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Create</title>
  </head>
  <body>
    <h1>Create Department</h1>
    <form action="/Department/Create" method="post">
      <!--
            if action is not specified, the form will send the request to the same url as the url of the page
            absolute path: /Department/Create
            relative path: Department/Create =>relative to the current url

            to choose different action method, we use the action attribute in the form tag
            action="/Department/Add"
         -->
      <div>
        <label for="deptId">Id</label>
        <input type="text" id="deptId" name="deptId" />
      </div>
      <div>
        <label for="deptName">Name</label>
        <input type="text" id="deptName" name="deptName" />
      </div>
      <div>
        <label for="capacity">Capacity</label>
        <input type="text" id="capacity" name="capacity" />
      </div>

      <button type="submit">Create</button>
    </form>

    ``` >[!]
  </body>
</html>
````

> [!warning] has to specify the name of the input fields so it can be sent to the server
>
> - `<input type="text" id="deptId" name="deptId" />`

> [!example] `Add` action method
>
> - to receive the data from the query string, we use the `Request` object
> - `Request.Query["key"]` - to get the value of the key from the query string

```csharp
// DepartmentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class DepartmentController : Controller
    {
        public IActionResult Create()
        {
            return View();
        }

        public IActionResult Add()
        {
            int id = int.Parse(Request.Query["deptId"].ToString());
            string name = Request.Query["deptName"].ToString();
            int capacity = int.Parse(Request.Query["capacity"]);

            Department department = new Department
            {
                DeptId = id,
                DeptName = name,
                Capacity = capacity
            };


            return View(department);
        }
    }
}
```

```html
<!--
    Add.cshtml
 -->

@model Department @{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Add</title>
  </head>
  <body>
    <h1>Department Added</h1>
    <h2>Id: @Model.DeptId</h2>
    <h2>Name: @Model.DeptName</h2>
    <h2>Capacity: @Model.Capacity</h2>
  </body>
</html>
```

> [!bug] adding `method="post"` to the form tag
>
> - this will throw exception as the `Add` action method is not configured to receive the data from the ==form== tag as it is configured to receive the data from the ==query== string
>   > [!done] to receive the data from the ==form== , we use the `Request.Form["key"]` to get the value of the key from the form tag

```csharp
// DepartmentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class DepartmentController : Controller
    {
        public IActionResult Create()
        {
            return View();
        }

        public IActionResult Add()
        {
            int id = int.Parse(Request.Form["deptId"].ToString());
            string name = Request.Form["deptName"].ToString();
            int capacity = int.Parse(Request.Form["capacity"]);

            Department department = new Department
            {
                DeptId = id,
                DeptName = name,
                Capacity = capacity
            };
```

> [!tip] `model binder`
>
> - `model binder` is a component of the ASP.NET Core that is used to bind the data from the request to the action method parameters
> - `model binder` is used to bind the data from the query string, form tag, route data, and the request body to the action method parameters
> - `model binder` is used to bind the data to the action method parameters based on the name of the parameter

```csharp
// DepartmentController.cs

// to receive the data from the form tag, query string, route data, and the request body
public IActionResult Add(int deptId, string deptName, int capacity)
{
    Department department = new Department
    {
        DeptId = deptId,
        DeptName = deptName,
        Capacity = capacity
    };
    return View(department);
}
```

> [!tip] `model binder` can recieve the data as an object, filling the properties of the object with the data from the request

```csharp
// DepartmentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class DepartmentController : Controller
    {
        public IActionResult Create()
        {
            return View();
        }

        public IActionResult Add(Department department)
        {
            return View(department);
        }
    }
}
```

> [!bug] if we send different property name or was missing in the form , the model binder will not be able to bind the data to the object and the properties will be default values
>
> - like it is expecting the property name to be `deptId` but we send the property name `id` so the `deptId` will be 0

> [!tip] how to recieve 2 objects of same type in the same action method

```csharp
// DepartmentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class DepartmentController : Controller
    {
        public IActionResult Create()
        {
            return View();
        }

        public IActionResult Add(Department department1, Department department2)
        {
            return View(department);
        }

    }
}
```

> [!done] how to send 2 objects of same type in the same action method from the form
>
> - we can use the object name in the form tag to send the data to specific object

```html

<!--
    Create.cshtml
 -->

@model Department
 @{ layout = null; }

<!DOCTYPE html>
<head>
    <title>Create</title>
</head>
<body>
    <h1>Create Department</h1>
    <form action="/Department/Create" method="post">
        <div>
            <label for="deptId">Id</label>
            <input type="text" id="deptId" name="department1.deptId" />
        </div>
        <div>
            <label for="deptName">Name</label>
            <input type="text" id="deptName" name="department1.deptName" />
        </div>
        <div>
            <label for="capacity">Capacity</label>
            <input type="text" id="capacity" name="department1.capacity" />
        </div>
        <div>
            <label for="deptId">Id</label>
            <input type="text" id="deptId" name="department2.deptId" />
        </div>
        <div>
            <label for="deptName">Name</label>
            <input type="text" id="deptName" name="department2.deptName" />
        </div>
        <div>
            <label for="capacity">Capacity</label>
            <input type="text" id="capacity" name="department2.capacity" />
        </div>
        <button type="submit">Create</button>
    </form>

</body>
</html>
```

---

> [!example] `model binder` has priorities to bind the data to the action method parameters
>
> - `form data` has the highest priority
> - `route data` has the second priority
> - `query string` has the third priority
> - `request body` has the lowest priority

```HTML
<!--
    Create.cshtml
 -->

@model Department @{ layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <title>Create</title>
  </head>
  <body>
    <h1>Create Department</h1>
    <form action="/Department/Create/10?Id=20" method="post">
      <div>
        <label for="Id">Id</label>
        <input type="text" id="Id" name="Id" />
      </div>
      <div>
        <label for="deptName">Name</label>
        <input type="text" id="deptName" name="deptName" />
      </div>
      <div>
        <label for="capacity">Capacity</label>
        <input type="text" id="capacity" name="capacity" />
      </div>

      <button type="submit">Create</button>
    </form>
  </body>
```

> [!done] the Id value in this example will be the one in the form tag as it has the highest priority

> [!tip] we can determine the priority of the data to be bound to the action method parameters

```csharp
// DepartmentController.cs
using Microsoft.AspNetCore.Mvc;
using Demo.Models;

namespace MVC.Controllers
{
    public class DepartmentController : Controller
    {
        public IActionResult Create()
        {
            return View();
        }

        public IActionResult Add([FromQuery] int Id, [FromRoute] int Age, [FromBody] string Name)
        //this will get Id from the query string, Age from the route data, and Name from the request body
        {
            return View();
        }
    }
}
```

> [!done] ways to bind the data to the action method parameters according to specific source
>
> - `FromQuery` - to bind the data from the query string
> - `FromRoute` - to bind the data from the route data
> - `FromBody` - to bind the data from the request body
> - `FromForm` - to bind the data from the form tag

---

### Lab

> [!tip] #### #lab
> File sponsored by: Ahmed Al-Sharkawy
> - action to add 2 numbers
> - return all types of the return types(Content, View, Json, File, NotFound, Redirect)
> - insert new department and display in the view
> - send list of students from the controller to the view
> - ViewData and ViewBag and ViewModel
