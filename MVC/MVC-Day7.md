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
> - create a new Folder (CustomFilters) in the project
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

---

# Break

---

### Authentication

> [!tip] Authentication vs Authorization
>
> - Authentication: the process of identifying the user ==(who)==
> - Authorization: the process of identifying the user's ==(permissions)== what the user can do

> [!example] Roles
>
> - roles are used to group users with similar permissions
> - role can have many users
> - user can have many roles
> - many to many relationship

> [!note] create `User` and `Role` classes

```csharp
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Password { get; set; }
    public string Email { get; set; }
    public List<Role> Roles { get; set; } = new List<Role>();
}
public class Role
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<User> Users { get; set; } = new List<User>();
}
```

> [!done] add `DbSet` for `User` and `Role` in the `ITIContext` class

```csharp
// ITIContext.cs
public class ITIContext : DbContext
{
    public ITIContext(DbContextOptions<ITIContext> options)
        : base(options)
    {
    }


    public DbSet<User> Users { get; set; }
    public DbSet<Role> Roles { get; set; }
}
```

> [!done] override `OnModelCreating` method in the `ITIContext` class

```csharp
// ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    //add this line to create the many to many relationship between the User and Role
    modelBuilder.Entity<Role>(entity =>
    {
        entity.HasData(
            new Role { Id = 1, Name = "Admin" },
             new Role { Id = 2, Name = "Student" },
             new Role { Id = 3, Name = "Instructor" },
             );
    });
}
```

> [!done] `add-migration` and `update-database` to create the tables in the database
>
> - add some users to the database manually

> [!tip] make create action in DepartmentController to be only authenticated users
>
> - we add the `[Authorize]` attribute to the action method
> - this will throw an exception (have to specify what schema to handle the authentication)

```csharp
// DepartmentController.cs

public class DepartmentController : Controller
{
    [Authorize]
    public IActionResult Create()
    {
        return View();
    }
}
```

> [!warning] setup the authentication in the `Startup.cs` file
>
> 1. `app.UseAuthentication()` ==before== `app.UseAuthorization()`
> 2. `builder.Services.AddAuthentication()`
>
> - cookie based authentication: `builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie()` : this will use the cookie to authenticate the user
> - jwt based authentication: `builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme).AddBearerToken()` : this will use the jwt token to authenticate the user

> [!done] Cookie authentication
>
> - to configure the cookie authentication
> - `builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie(options => {})`
> - `options.LoginPath` to specify the login page

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

    //to enable authentication
    builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie(options =>
    {
        options.LoginPath = "/Account/Login";//this will redirect to the login page if the user is not authenticated
        //if not specified, the default login page will be used
    });

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

    //to enable authentication
    app.UseAuthentication();

    app.UseAuthorization();

    //to enable session
    app.UseSession();

    app.MapControllerRoute(
        name: "default",
        pattern: "{controller=Department}/{action=Index}/{id?}");

    app.Run();
}
```

> [!warning] ReturnUrl
>
> - when the user is not authenticated, the user will be redirected to the login page with the return url `?returnUrl=/Department/Create`
> - after the user is authenticated, the user will be redirected to the return url



>[!tip] create a login page
> - create `LoginViewModel` class
> - create `Login` action method in the `AccountController`
> - create `Login.cshtml` view

```csharp
public class LoginViewModel
{
    public string Email { get; set; }

    [DataType(DataType.Password)]//this will change the input type to password
    public string Password { get; set; }
}
````

```html
@model LoginViewModel @{ ViewData["Title"] = "Login"; }
<h1>Login</h1>
<form method="post">
  <!-- 
        this will make the request to same action method (Login) in the AccountController
     -->
  <div class="form-group">
    <label asp-for="Email"></label>
    <input asp-for="Email" class="form-control" />
  </div>
  <div class="form-group">
    <label asp-for="Password"></label>
    <input asp-for="Password" class="form-control" />
  </div>
  <button type="submit" class="btn btn-primary">Login</button>
</form>
```

```csharp
public class AccountController : Controller
{
    private readonly ITIContext db;
    public AccountController(ITIContext context)
    {
        db = context;
    }
    [HttpGet]
    public IActionResult Login()
    {
        return View();
    }
    [HttpPost]
    public async Task<IActionResult> Login(LoginViewModel model)
    {
        if(!ModelState.IsValid)
        //to check if the model is valid
        {
            return View(model);//
        }

        //to authenticate the user
        var user = db.Users.FirstOrDefault(u => u.Email == model.Email && u.Password == model.Password);

        //to check if the user exists in the database
        if(user == null)
        {
            ModelState.AddModelError("", "Invalid email or password");//add an error to the model state
                return View(model);

        }
        // HttpContext.User;//this is a ClaimsPrincipal
        // to create a claim for every part of the user
        Claim claim1 = new Claim(ClaimTypes.Name, user.Name);
        Claim claim2 = new Claim(ClaimTypes.Email, user.Email);

        //create ClaimsIdentity to hold the claims
        // we need to specify the authentication Type
        ClaimsIdentity claimsIdentity1 = new ClaimsIdentity(CookieAuthenticationDefaults.AuthenticationScheme);

        //add the claims to the claims identity
        claimsIdentity1.AddClaim(claim1);
        claimsIdentity1.AddClaim(claim2);

        //create ClaimsPrincipal to hold the claims identity
        ClaimsPrincipal claimsPrincipal1 = new ClaimsPrincipal();

        claimsPrincipal1.AddIdentity(claimsIdentity1);

        //to sign in the user using SignInAsync to add the claims principal to the cookie
      await  HttpContext.SignInAsync(ClaimsPrincipal1);

        //we can redirect to return url if it exists
        //or
        //to redirect to the home page
        return RedirectToAction("Index", "Home");
    }
}
```

> [!done] now we can access `Create` action method in the `DepartmentController` after the user is authenticated

> [!bug] make login || logout || Register links in the layout dynamically
>
> - `@User.Identity.Name` to get the user's name in the layout
> - `User.Identity.IsAuthenticated` to check if the user is authenticated

```html
<!-- 
    add both links to the layout
 -->

@if(User.Identity.IsAuthenticated) {
<a asp-controller="Account" asp-action="Logout"
  >Logout ya @User.Identity.Name</a
>
} else {
<a asp-controller="Account" asp-action="Login">Login</a>
<a asp-controller="Account" asp-action="Register">Register</a>
}
```

> [!warning] `Logout`
>
> - `HttpContext.SignOutAsync()` to delete the cookie from the client's machine

```csharp
public class AccountController : Controller
{
    public async Task<IActionResult> Logout()
    {
        await HttpContext.SignOutAsync();
        return RedirectToAction("Index", "Home");
    }
}
```

---

### Authorization

> [!tip] add a role to the user
>
> - in database in `UserRoles` table we add the `UserId` and `RoleId`
> - user 1 => role 1 (Admin)
> - user 2 => role 2 (Instructor)

> [!done] specify the roles in the `Create` action method in the `DepartmentController`

```csharp
// [Authorize(Roles = "Admin, Instructor")]//this will allow only users with the role "Admin" or "Instructor" to access the action method
[Authorize(Roles = "Admin")]//this will allow only users with the role "Admin" to access the action method
public IActionResult Create()
{
    return View();
}
```

> [!bug] now if the user is authenticated but does not have the role "Admin" the user will not be able to access the `Create` action method
>


> [!tip] add claim with the role to the user

```csharp
public async Task<IActionResult> Login(LoginViewModel model)
{
    //to authenticate the user
    var user = db.Users.Include(u => u.Roles).FirstOrDefault(u => u.Email == model.Email && u.Password == model.Password);

    //to check if the user exists in the database
    if(user == null)
    {
        ModelState.AddModelError("", "Invalid email or password");//add an error to the model state
        return View();
    }
    // HttpContext.User;//this is a ClaimsPrincipal
    // to create a claim for every part of the user
    Claim claim1 = new Claim(ClaimTypes.Name, user.Name);
    Claim claim2 = new Claim(ClaimTypes.Email, user.Email);

    //to create a claim for the role
    List<Claim> claims = new List<Claim>();
    foreach(var role in user.Roles)
    {
        claims.Add(new Claim(ClaimTypes.Role, role.Name));
    }

    //create ClaimsIdentity to hold the claims
    // we need to specify the authentication Type
    ClaimsIdentity claimsIdentity1 = new ClaimsIdentity(CookieAuthenticationDefaults.AuthenticationScheme);

    //add the claims to the claims identity
    claimsIdentity1.AddClaim(claim1);
    claimsIdentity1.AddClaim(claim2);
    claimsIdentity1.AddClaims(claims);//to add the role claims to the claims identity

    //create ClaimsPrincipal to hold the claims identity
    ClaimsPrincipal claimsPrincipal1 = new ClaimsPrincipal();

    claimsPrincipal1.AddIdentity(claimsIdentity1);

    //to sign in the user using SignInAsync to add the claims principal to the cookie
  await  HttpContext.SignInAsync(ClaimsPrincipal1);

    //we can redirect to return url if it exists
    //or
    //to redirect to the home page
    return RedirectToAction("Index", "Home");
}
```

> [!warning] to make some links in the layout visible only to users with specific roles
>
> - `@User.IsInRole("Admin")` to check if the user is in the role "Admin"

```html
@if(User.IsInRole("Admin")) {
<a asp-controller="Department" asp-action="Create">Create</a>
} @if(User.IsInRole("Instructor")) {
<a asp-controller="Department" asp-action="Create">Create</a>
}
```

> [!done] it is prefered to have all this if statements in a partial view
>
> - create a new partial view `_NavLinks.cshtml` in the shared folder
> - move the if statements to the partial view
> - include the partial view in the layout

```html
<!-- 
// _NavLinks.cshtml
 -->
@if(User.IsInRole("Admin")) {
<a asp-controller="Department" asp-action="Create">Create</a>
} @if(User.IsInRole("Instructor")) {
<a asp-controller="Department" asp-action="Create">Create</a>
} @if(User.Identity.IsAuthenticated) {
<a asp-controller="Account" asp-action="Logout"
  >Logout ya @User.Identity.Name</a
>
} else {
<a asp-controller="Account" asp-action="Login">Login</a>
<a asp-controller="Account" asp-action="Register">Register</a>
}
```

```html
<!-- 
// _Layout.cshtml
 -->
<partial name="_NavLinks" />
```

> [!done] to make the whole controller to be accessible only to users with specific roles
>
> - add the `[Authorize(Roles = "Admin, Instructor")]` attribute to the controller

```csharp
[Authorize(Roles = "Admin")]//this will allow only users with the role "Admin" to access the action methods in the controller
public class DepartmentController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
    [Authorize(Roles = "Instructor")]//has to be in the 2 roles admin and instructor to access the action method
    public IActionResult Create()
    {
        return View();
    }
}
```

---

> [!warning] #lab
>
> - add Authentication & Authorization
