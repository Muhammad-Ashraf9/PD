# MVC-Day7

> [!tip] Outline
>
> - filters
> - state management
> - Authentication
> - Authorization

---

> [!example] middlewares
>
> - any request passes through the middlewares
> - any response passes through the middlewares in reverse order

> [!warning] filters
>
> - some filters are executed ==before== the action method is called and some are executed ==after== the action method is called
> - create a new Floder (CustomFilters) in the project
> - create class (ExceptionFilterTemp) in the folder
> - inherit from `ActionFilterAttribute`
> - override `OnActionExecuting`: this method is called ==before== the action method is called
> - `OnActionExecuted`: this method is called after the action method is called

```csharp
public class ExceptionFilterTemp : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        // before the action method is called


        context.HttpContext;// has information about the request
        context.Exception;// has information about the exception
        if(context.Exception != null)
        {
            context.ExceptionHandled = true;//this indicates that the exception is handled by the filter
            context.Result = new ContentResult()//this is the response
            {
                Content = "An error occured"
            };
        }
    }

    public override void OnActionExecuted(ActionExecutedContext context)
    {
        // after the action method is called
    }
}
```

> [!done] `[ExceptionFilterTemp]` is a custom filter that can be used to handle exceptions in the application
>
> - add `[ExceptionFilterTemp]` to the controller or action method to use it
> - this will replace the action method's response with the response in the filter if an exception occurs

```csharp
public class HomeController : Controller
{
[ExceptionFilterTemp]
    public IActionResult Index()
    {
        throw new Exception("I am an exception");
        return View();
    }
}
```

> [!bug] this only works for the action method it is applied to in the controller
>
> - any other action method in the controller will not be affected by the filter
>
>   > [!done] to apply the filter to all action methods in the controller
>   >
>   > - add the filter to the controller class

```csharp
[ExceptionFilterTemp]
public class HomeController : Controller
{
    public IActionResult Index()
    {
        throw new Exception("I am an exception in the index action method");
        return View();
    }
    public IActionResult About()
    {
        throw new Exception("I am an exception in the about action method");
        return View();
    }
}
```

> [!done] to make it work for ==all== controllers ==(Globally)==
>
> -

> [!warning] create an Error View
>
> - create a new View (Error) in the shared folder
> - `_error2.cshtml`

```csharp
@{
    ViewData["Title"] = "Error";
}
```

```csharp
// ExceptionFilterTemp.cs
public class ExceptionFilterTemp : ExceptionFilterAttribute
{
    public override void OnException(ExceptionContext context)
    {
        context.ExceptionHandled = true;
        //to return a view
        context.Result = new ViewResult()
        {
            ViewName = "_error"
        };

        //to redirect to a different action  in different controller
        context.Result = new RedirectToActionResult("error", "Home", null);//third parameter is route values used to pass data to the action method


        //to return a view with a model
        ViewResult viewResult = new ViewResult()
        {
            ViewName = "_error",
            // Model is ReadOnly (get)
        }

        viewResult.ViewData["Model"] = "hghgh";//to pass data to the view
        context.Result = viewResult;
        //try in lab
    }
}
```

> [!Example] `AuthFilters`
>
> - create a new class (AuthFilter) in the CustomFilters folder
> - inherit from `ActionFilterAttribute`
> - override `OnActionExecuting` method : this method is called before the action method is called
> -

```csharp
// AuthFilter.cs
public class AuthFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext context)
//before the action method is called
    {
        //to check if the user is authenticated
        if(context.HttpContext.User.Identity.IsAuthenticated == flase)
        {
           //redirect to the login page
            context.Result = new RedirectToActionResult("Login", "Account", null);
        }
    }
}
```

> [!example] `AccountController`
>
> - `Login` action method

```csharp
// AccountController.cs
public class AccountController : Controller
{
    public IActionResult Login()
    {
        return View();
    }
}
```

> [!tip] apply the filter to the controller `DepartmentController` and the action method `Index`
>
> - this will redirect to the login page if the user is not authenticated

```csharp
[AuthFilter]//to make the filter fol all action methods in the controller
public class DepartmentController : Controller
{
[AuthFilter]//this will only work for the index action method
    public IActionResult Index()
    {
        return View();
    }

    public IActionResult About()
    {
        return View();
    }

}
```

> [!warning] ==SEARCH==
>
> - `onResultExecuting` vs `onResultExecuted`

---

### state management

> [!tip] HTTP is a stateless protocol
>
> - how to save data between requests despite the stateless nature of HTTP
> - we can make use of the client `cookies`
> - `cookies` are stored on the client's machine (per user per browser)
>
> > [!warning] `cookies` disadavantages:
> >
> > - Security issues as the data is stored on the client's machine
> > - can't store sensitive data
>
> > [!bug] Server memory: Not a good idea to store data in the server memory
>
> > [!done] can store in database
> >
> > - can store in a database
> > - but we have to connect to the database to get the data

---

### Cookies

> [!tip]
>
> - to store data in the client's machine
> - we use `Response.Cookies.Append` to store data in the client's machine
> - we use `Request.Cookies` to get the data from the client's machine
> - `Response.Cookies.Append("name", "value", new CookieOptions())` > - value has to be a string
> - `new CookieOptions()` to add expiration date
> - `Response.Cookies.Delete("name")` to delete a cookie with the key "name"

```csharp
// testController.cs

public class TestController : Controller
{
    public IActionResult CreateCookie()
    {
        int id = 1;
        string name = "Hissen";
        Response.Cookies.Append("id", id.ToString());//value has to be a string
        Response.Cookies.Append("firstName", name);

        //to add expiration date
        Response.Cookies.Append("id", id.ToString(), new CookieOptions()
        {
            Expires = DateTime.Now.AddMinutes(10)//this cookie will expire in 10 minutes
        });
        return View();
    }

    public IActionResult GetCookie()
    //we get the data saved from the client's machine
    {
        int id = int.Parse(Request.Cookies["id"]); // to get the cookie from the client's machine with the key "id"

        //this will throw an exception if the cookie does not exist

        string name = Request.Cookies["firstName"];// to get the cookie from the client's machine with the key "firstName"


        //to delete a cookie
        Response.Cookies.Delete("id");

        return Content($"id: {id}, name: {name}");
    }
}

```

---

### Session

> [!bug] we have to enable session in the `Startup.cs` file
>
> > [!done] to enable session
> >
> > - in the `Startup.cs` file
> > - `app.UseSession()` after `app.UseAuthorization()`
> > - `builder.Services.AddSession()` to add session services

```csharp
// Startup.cs
 public static void Main(string[] args)
 {
     var builder = WebApplication.CreateBuilder(args);

     // Add services to the container.
     builder.Services.AddControllersWithViews();

     builder.Services.AddDbContext<ITIContext>();
     builder.Services.AddTransient<ICourseRepo, CourseRepo>();
     builder.Services.AddTransient<IDeptRepo, DeptRepo>();
     builder.Services.AddTransient<IStudentRepo, StudentRepo>();

     //to enable session
        builder.Services.AddSession();


     var app = builder.Build();

     // Configure the HTTP request pipeline.
     if (!app.Environment.IsDevelopment())
     {
         app.UseExceptionHandler("/Home/Error");
         // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
         app.UseHsts();
     }

     app.UseHttpsRedirection();
     app.UseStaticFiles();

     app.UseRouting();

     app.UseAuthorization();

        //to enable session
        app.UseSession();

     app.MapControllerRoute(
         name: "default",
         pattern: "{controller=Department}/{action=Index}/{id?}");

     app.Run();
 }


```

```csharp
// testController.cs

public class TestController : Controller
{
    public IActionResult CreateSession(int id, string name)
    {
        //to store data in the session
        HttpContext.Session.SetInt32("id", id);
        HttpContext.Session.SetString("name", name);
        return View();

    }

    public IActionResult GetSession()
    {
        //to get data from the session
        int id = HttpContext.Session.GetInt32("id");//can't convert nullable int to int
        int? id = HttpContext.Session.GetInt32("id");//nullable int

        string name = HttpContext.Session.GetString("name");
        return Content($"id: {id}, name: {name}");
    }
}
```

> [!tip] Session
>
> - on request, the server will create a session id and store the data in the server memory
> - will return session id to the client in the response (cookie)
> - then on the next request, the client will send the session id in the request
> - the server will use the session id to get the data from the server memory and send it back to the client
> - default duration of the session is 20 minutes from the last request
> - we can change the duration of the session in the `Startup.cs` file

```csharp
// Program.cs

app.addSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);//this will change the duration of the session to 30 minutes

    options.Cookie.HttpOnly = true;//this will  prevent the session cookie from being accessed by client side scripts

});
```

> [!tip] `TempData`
>
> - used to store data for the duration of the request
> - data will be removed from the `TempData` after the request
> - will be saved in the `Session` encrypted for the request
> - `TempData["key"] = value` to store data in the `TempData`
> - `TempData["key"]` to get the data from the `TempData` and remove it from the `TempData`: won't be available in the next request
> - `TempData.Peek("key")` to get the data from the `TempData` and keep it in the `TempData`

```csharp
// testController.cs

public class TestController : Controller
{
    public IActionResult CreateTempData()
    {
        TempData["id"] = 1;
        TempData["name"] = "Hissen";
        return View();
    }

    public IActionResult GetTempData()
    {
        int id = (int)TempData["id"];//as it is an object
        //this will throw an exception if the key does not exist
        //the data will be removed from the TempData after the request
        string name = (string)TempData["name"];
        return Content($"id: {id}, name: {name}");
    }

    public IActionResult GetTempData2()
    {
        int id = (int)TempData.Peek("id");//as it is an object
        //the data will be kept in the TempData after the request
        string name = (string)TempData.Peek("name");
        return Content($"id: {id}, name: {name}");
    }
}

```

```cs
// CreateTempData.cshtml

@{
    ViewData["Title"] = "CreateTempData";
}
@TempData["id"]
//this will read the data from the TempData and remove it from the TempData
@TempData["name"]

//to read the data from the TempData and keep it in the TempData we use Peek
@TempData.Peek("id")
@TempData.Peek("name")

<h1>CreateTempData</h1>
```
