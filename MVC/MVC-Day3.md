### Models

> [!tip] Student & Department Relationship
>
> - create new project `Demo1` with `ASP.NET Core Web App (Model-View-Controller)` template
> - create 2 classes `Student` and `Department` in the `Models` folder
> - set `nullable` in project properties to `disable` to stop the warning for nullable reference types

```csharp
//Department.cs
namespace Demo1.Models
{
    public class Department
    {
        public int DeptId { get; set; }
        public string DeptName { get; set; }
        public int Capacity { get; set; }

        public ICollection<Student> Students { get; set; } = new HashSet<Student>();
        //we use ICollection<T> interface to represent a collection of objects of type Student.
        //HashSet<T> is a collection of unique objects of type Student. as we don't want duplicate students in a department.
    }
}
```

```csharp
//Student.cs
namespace Demo1.Models
{
    public class Student
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public int Age { get; set; }
        public string Email { get; set; }

        // public int? DeptNo { get; set; } //? optional
        public int DeptNo { get; set; } // required field
        [ForeignKey("DeptNo")] //foreign key attribute

        public Department DepartmentNavigation { get; set; }
    }
}
```

> [!done]
>
> - install `Microsoft.EntityFrameworkCore.SqlServer`
> - install `Microsoft.EntityFrameworkCore.Tools`
> - create a new folder `Data` in the project
> - create a new class `ITIDbContext.cs` in the `Data` folder
> - `ITIDbContext` class should inherit from `DbContext` class
> - add `DbSet` properties for `Student` and `Department` classes
> - override `OnConfiguring` method to configure the database connection
> - we add a constructor other than the default constructor to pass the `DbContextOptions` to the base class constructor

```csharp
//ITIDbContext.cs
using Microsoft.EntityFrameworkCore;

namespace Demo1.Data
{
    public class ITIDbContext : DbContext
    {
        // public ITIDbContext(DbContextOptions<ITIDbContext> options) : base(options)
        // {
        // }

        public DbSet<Student> Students { get; set; }
        public DbSet<Department> Departments { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer("Server=.;Database=ITIDb;integrated security=true; trust server certificate=true");
        }
    }
}
```

> [!done] `add-migration` and `update-database` commands to create the database and tables

```powershell
add-migration InitialCreate
update-database
```

> [!example] Controllers
> create a new Controller `DepartmentController`
>
> - `Index` action method to display the list of departments

```csharp
//DepartmentController.cs
using Demo1.Data;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace Demo1.Controllers
{
    public class DepartmentController : Controller
    {
         ITIDbContext db = new ITIDbContext();
         //department/index

        public IActionResult Index()
        {
            var departments = db.Departments.ToList();
            return View(departments);
        }
    }
}
```

> [!tip] right-click on the `Index` method and select `Add View` to create a view for the `Index` action method
>
> > [!warning] Views just for display, no logic
> >
> > - views should not manipulate data
>
> > [!done] so we use `IEnumerable` instead of `List` in the view

> [!example] `IEnumerable`
>
> - This allows readonly access to a collection then a collection that implements `IEnumerable` can be used with a for-each statement.

```html
<!-- @model List<Department> -->
<!-- 
    to specify the model type for the view, we use the @model directive
 -->

@model IEnumerable<Department>
  @{ Layout = null; }

  <!DOCTYPE html>
  <html>
    <head>
      <meta name="viewport" content="width=device-width" />
      <title>Index</title>
      <link rel="stylesheet" href="~/css/site.css" />
      <!-- 
        bootstrap
     -->
      <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    </head>
    <body>
      <div class="container">
        <h2>Department List</h2>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>DeptId</th>
              <th>DeptName</th>
              <th>Capacity</th>
            </tr>
          </thead>
          <tbody>
            @if (Model.Count() == 0) {
            <tr>
              <td colspan="3">No Department Found</td>
            </tr>
            } else {
            <!-- we use foreach not @foreach as we are already in c# block -->
            foreach (var dept in Model) {
            <tr>
              <td>@dept.DeptId</td>
              <td>@dept.DeptName</td>
              <td>@dept.Capacity</td>
            </tr>
            } }
          </tbody>
        </table>
      </div>
    </body>
  </html>
```

> [!bug] `app.MapControllerRoute` to map the controller to the route

```csharp
//Startup.cs
app.MapControllerRoute(
    name: "default",
    // pattern: "{controller=Home}/{action=Index}/{id?}");
    pattern: "{controller=Department}/{action=Index}/{id?}");//to make the department controller the default controller

```

> [!example] add anker tag to the `Index` view to navigate to the `Create` Method

```html
<a href="/Department/Create">Create</a>
```

> [!tip] `Create` action method to create a new department
>
> - right-click on the `Create` method and select `Add View` to create a view for the `Create` action method

```csharp
//DepartmentController.cs
public IActionResult Create()
{
    return View();//return the view with the form to create a new department no need to pass any data
}
```

> [!danger] `Create` view

```html
@{ Layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <title>Create</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
  </head>
  <body>
    <form action="/Department/Add" method="post">
      <!-- 
            we use the action attribute to specify the URL of the action method that will be called when the form is submitted
         -->
      <label for="DeptId">DeptId</label>
      <input type="text" id="DeptId" name="DeptId" />
      <label for="DeptName">DeptName</label>
      <input type="text" id="DeptName" name="DeptName" />
      <label for="Capacity">Capacity</label>
      <input type="text" id="Capacity" name="Capacity" />
      <input type="submit" value="Create" class="btn btn-primary" />
    </form>
    <!-- 
        button to show the list of departments
     -->
    <a href="/Department/Index" class="btn btn-info">Back</a>
  </body>
</html>
```

> [!danger] `Add` action method to add a new department to the database
>
> - depend on `modal binder` to bind the form data to the `Department` object
> - more on `modal binder` in the previous session [MVC-Day1](MVC-Day1.md)

```csharp
//DepartmentController.cs

public IActionResult Add(Department dept)
   {
        db.Departments.Add(dept);//this will add the department in the memory
        db.SaveChanges();//this will save the department in the database
        // return View("Index");//return the view to show the list of departments but we need to pass the list of departments to the view
        //this will throw exception as the view is expecting a list of departments(@Model.Count() == 0)
        return View("Index", db.Departments.ToList());//return the view with the list of departments
   }


```

> [!bug] URL: is displaying the `Add?DeptId=1&DeptName=IT&Capacity=100` in `Index` view
>
> - `db.Departments.ToList()` is used 2 times.
>   > [!done] `RedirectToAction` to redirect to the `Index` action method after adding the department
>   >
>   > - no need to pass the list of departments to the `Index` view
>   > - avoid code duplication
>   > - URL: `https://localhost:5001`

```csharp
//DepartmentController.cs
public IActionResult Add(Department dept)
{
    if(dept.DeptId == 0 || dept.DeptName == null)//validation  for the department id and name
    {
        return View("Create", dept);
    }

    db.Departments.Add(dept);
    db.SaveChanges();
    return RedirectToAction("Index");
}

```

> [!bug] `return View("Create")`
>
> - return the view with the form to create a new department
> - sent the user back to the create view with the form to create a new department
> - to set the data in the form to the department object
> - we return the view with the department object
> - this will throw exception the first time as the view is expecting department object
>   > [!done] we can use null propagation to avoid the exception or we can use the `Create` method to create a new department object
>   >
>   > - `return View("Create", new Department());`
>   > - `<input  value="@Model?.DeptId" />`

```html
//Create.cshtml @model Department

<form action="/Department/Add" method="post">
  <label for="DeptId">DeptId</label>
  <input type="text" id="DeptId" name="DeptId" value="@Model?.DeptId" />
  <label for="DeptName">DeptName</label>
  <input type="text" id="DeptName" name="DeptName" value="@Model?.DeptName" />
  <label for="Capacity">Capacity</label>
  <input type="text" id="Capacity" name="Capacity" value="@Model?.Capacity" />
  <input type="submit" value="Create" class="btn btn-primary" />

  <a href="/Department/Index" class="btn btn-info">Back</a>
</form>
```

> [!danger] if you have multiple Methods with the same name in the controller, It will throw an exception
>
> - to avoid this exception, we can use the `HttpPost` , `HttpGet` attributes to specify the type of the request that the method will handle
> - `HttpPost` attribute to specify that the method will handle the post request
> - `HttpGet` attribute to specify that the method will handle the get request
> - `Action Selector` to select the action method based on the request type

```csharp
//DepartmentController.cs
[HttpGet]
public IActionResult Create()
{
    return View(new Department());
}

[HttpPost]
public IActionResult Create(Department dept)
{
    if(dept.DeptId == 0 || dept.DeptName == null)
    {
        return View(dept);
    }

    db.Departments.Add(dept);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

> [!done] we remove the `Add` method from controller and remove action attribute from the form in the `Create` view

```html
//Create.cshtml @model Department

<!-- <form action="/Department/Create" method="post"> -->
<!-- 
        we can remove the action attribute from the form as the form will be submitted to the same action method (Create)
     -->
<form method="post">
  <label for="DeptId">DeptId</label>
  <input type="text" id="DeptId" name="DeptId" value="@Model?.DeptId" />
  <label for="DeptName">DeptName</label>
  <input type="text" id="DeptName" name="DeptName" value="@Model?.DeptName" />
  <label for="Capacity">Capacity</label>
  <input type="text" id="Capacity" name="Capacity" value="@Model?.Capacity" />
  <input type="submit" value="Create" class="btn btn-primary" />

  <a href="/Department/Index" class="btn btn-info">Back</a>
</form>
```

> [!bug] `name` attribute in the form has to match the property name in the `Department` class
>
> - otherwise, the `modal binder` will not be able to bind the form data to the `Department` object
> - `name="DeptId"` , `name="DeptName"` , `name="Capacity"`
>   > [!done] we use tag helpers to create the form in the `Create` view
>   >
>   > - `asp-for` attribute to specify the property that the input element will bind to
>   > - now we can choose the property from the `Department` object from the intellisense
>   > - `<input asp-for="DeptId" >`, `<input asp-for="DeptName" >`, `<input asp-for="Capacity" >`
>   > - this will return HTML according to the property type, and if it is required or not
>   > - `<input type="text" id="DeptId" name="DeptId"  value=""  data-val="true" data-val-required="The DeptId field is required." />` as it is int type and required
>   > - same for the label tag
>   > - `<label asp-for="DeptId">DeptId</label>`

```html
//Create.cshtml @model Department

<form method="post">
  <label asp-for="DeptId"></label>
  <!-- 
    we can use label without writitng  in the label text and it will work
    <label for="DeptId">DeptId</label>
   -->
  <input asp-for="DeptId" />
  <!-- 
    it will give warning if we write the property name wrong
    like DeptIdd instead of DeptId
   -->
  <label asp-for="DeptName">Department Name</label>
  <!-- 
    if we write in the label it will take Department Name as the label text
    <label for="DeptName">Department Name</label>
   -->
  <input asp-for="DeptName" />
  <label asp-for="Capacity">Capacity</label>
  <input asp-for="Capacity" />
  <input type="submit" value="Create" class="btn btn-primary" />

  <a href="/Department/Index" class="btn btn-info">Back</a>
</form>
```

> [!example] to change default text in label tag we use `[Display(Name="Department Name")]` attribute in the `Department` class
>
> - now if we left the label tag empty it will take the text from the `Display` attribute
> - `<label asp-for="DeptName"></label>` will take `Department Name` as the label text instead of `DeptName`

```csharp
//Department.cs

public class Department
{
    public int DeptId { get; set; }
    [Display(Name="Department Name")]
    public string DeptName { get; set; }
    public int Capacity { get; set; }

    public ICollection<Student> Students { get; set; } = new HashSet<Student>();
}
```

---

# Break

---

> [!example] `<a asp-controller="Department" asp-action="Create">Create</a>`
>
> - we use tag helpers to create the anchor tag
> - `asp-controller` attribute to specify the controller that the anchor tag will navigate to
> - `asp-action` attribute to specify the action method that the anchor tag will navigate to
>   > [!warning] it is better to use tag helpers instead of writing HTML tags
>   >
>   > - as we can change the routing pattern in the `Startup` class and the tag helpers will still work

> [!tip] html helper
>
> - `@Html.TextBoxFor` to create the input element for the property (strongly typed view)
> - `@Html.TextBox` to create the input element for the property (weakly typed view)
> - `@Html.TextBoxFor(model => model.DeptId)` , `@Html.TextBox("DeptId")`
> - there are alot more html helpers like `@Html.DropDownList` ,...etc

> [!danger] `Details` action method to display the details of a department
>
> - we use `id` parameter to get the department id from the URL
> - `url` : `https://localhost:5001/Department/Details/1`
> - right-click on the `Details` method and select `Add View` to create a view for the `Details` action method

```csharp
//DepartmentController.cs
public IActionResult Details(int? id)
{
    if(id == null)
    {
        return BadRequest();//return 400 status code
    }
    var dept = db.Departments.FirstOrDefault(d => d.DeptId == id);
    if(dept == null)
    {
        return NotFound();//return 404 status code
    }
    return View(dept);
}
```

> [!done] `Details` view

```html
@model Department @{ Layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <title>Details</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
  </head>
  <body>
    <div class="container">
      <h2>Department Details</h2>
      <table class="table table-bordered">
        <tr>
          <th>DeptId</th>
          <td>@Model.DeptId</td>
        </tr>
        <tr>
          <th>DeptName</th>
          <td>@Model.DeptName</td>
        </tr>
        <tr>
          <th>Capacity</th>
          <td>@Model.Capacity</td>
        </tr>
      </table>
      <a href="/Department/Index" class="btn btn-info">Back</a>
    </div>
  </body>
</html>
```

> [!warning] add new column to the `Department` table in `Index` view to navigate to the `Details` action method

```html
//Index.cshtml
<!-- 
    we add a new column to the table to show the details of the department
 -->
@model IEnumerable<Department>
  @{ Layout = null; }

  <!DOCTYPE html>
  <html>
    <head>
      <meta name="viewport" content="width=device-width" />
      <title>Index</title>
      <link rel="stylesheet" href="~/css/site.css" />
      <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    </head>
    <body>
      <div class="container">
        <h2>Department List</h2>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>DeptId</th>
              <th>DeptName</th>
              <th>Capacity</th>
              <th>Details</th>
            </tr>
          </thead>
          <tbody>
            @if (Model.Count() == 0) {
            <tr>
              <td colspan="4">No Department Found</td>
            </tr>
            } else { foreach (var dept in Model) {
            <tr>
              <td>@dept.DeptId</td>
              <td>@dept.DeptName</td>
              <td>@dept.Capacity</td>
              <td>
                <a
                  asp-controller="Department"
                  asp-action="Details"
                  asp-route-id="@dept.DeptId"
                  >Details</a
                >
              </td>
            </tr>
            } }
          </tbody>
        </table>
        <a href="/Department/Create" class="btn btn-primary">Create</a>
      </div>
    </body>
  </html>
```

> [!bug] we can send multiple parameters to the action method using `asp-route-` attribute
>
> - `asp-route-id="@dept.DeptId"` to send the department id to the `Details` action method
> - if we use sid instead of id url will be `https://localhost:5001/Department/Details?sid=1` instead of `https://localhost:5001/Department/Details/1`
> - `asp-route-attributename` to send the attribute to the action method

> [!tip] `Edit` action method to edit the details of a department

```csharp
//DepartmentController.cs

[HttpGet]//to specify that the method will handle the get request
public IActionResult Edit(int? id)
{
    if(id == null)
    {
        return BadRequest();
    }
    var dept = db.Departments.FirstOrDefault(d => d.DeptId == id);
    if(dept == null)
    {
        return NotFound();
    }
    return View(dept);
}

[HttpPost]//to specify that the method will handle the post request

public IActionResult Edit(Department dept)
{
    if(dept.DeptId == 0 || dept.DeptName == null)
    {
        return View(dept);
    }
    // var oldDept = db.Departments.FirstOrDefault(d => d.DeptId == dept.DeptId);
    // oldDept.DeptName = dept.DeptName;
    // oldDept.Capacity = dept.Capacity;
    //to change the whole object we can use db.Departments.Update(dept);
    db.Departments.Update(dept);//this will search for the department with the same id and update it
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

> [!done] `Edit` view

```html
<!-- 
    Edit.cshtml
 -->
@model Department @{ Layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <title>Edit</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
  </head>
  <body>
    <form method="post">
      <!-- 
        we can use tag helper to write action attribute
        <form  asp-action="Edit" method="post">
        -->
      <label asp-for="DeptId"></label>
      <input asp-for="DeptId" readonly />
      <!-- 
        we use readonly attribute to make the input element readonly
        or we can use hidden input element
       -->
      <label asp-for="DeptName"></label>
      <input asp-for="DeptName" />
      <label asp-for="Capacity"></label>
      <input asp-for="Capacity" />
      <input type="submit" value="Save" class="btn btn-primary" />
      <a href="/Department/Index" class="btn btn-info">Back</a>
    </form>
  </body>
</html>
```

> [!bug] `disabled` attribute: will not send the value to the server

> [!done] we add `Edit` column to the `Index` view to navigate to the `Edit` action method

```html
<!-- 
    Index.cshtml
 -->
@model IEnumerable<Department>
  @{ Layout = null; }

  <!DOCTYPE html>
  <html>
    <head>
      <meta name="viewport" content="width=device-width" />
      <title>Index</title>
      <link rel="stylesheet" href="~/css/site.css" />
      <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    </head>
    <body>
      <div class="container">
        <h2>Department List</h2>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>DeptId</th>
              <th>DeptName</th>
              <th>Capacity</th>
              <th>Details</th>
              <th>Edit</th>
            </tr>
          </thead>
          <tbody>
            @if (Model.Count() == 0) {
            <tr>
              <td colspan="5">No Department Found</td>
            </tr>
            } else { foreach (var dept in Model) {
            <tr>
              <td>@dept.DeptId</td>
              <td>@dept.DeptName</td>
              <td>@dept.Capacity</td>
              <td>
                <a
                  asp-controller="Department"
                  asp-action="Details"
                  asp-route-id="@dept.DeptId"
                  >Details</a
                >
              </td>
              <td>
                <a
                  asp-controller="Department"
                  asp-action="Edit"
                  asp-route-id="@dept.DeptId"
                  >Edit</a
                >
              </td>
            </tr>
            } }
          </tbody>
        </table>
        <a href="/Department/Create" class="btn btn-primary">Create</a>
      </div>
    </body>
  </html>
```

> [!example] we can delete deptId input element and use the parameter in url to send the department id to the `Edit` action method
>
> - `https://localhost:5001/Department/Edit/1`
> - but we will have to add parameter to the `Edit` action method as it is `DeptId` not `id`

```csharp
//DepartmentController.cs

[HttpPost]
public IActionResult Edit(Department dept,int id)
{
    if(dept.DeptId == 0 || dept.DeptName == null)
    {
        return View(dept);
    }
    db.Departments.Update(dept);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

> [!done] we can use tag helper with form
>
> - `<form asp-action="Edit" >` default method is post
> - if we make changes to routing methods we don't have to change the form action attribute in the view

> [!tip] `Delete` action method to delete a department
>
> - we use `id` parameter to get the department id from the URL
> - `url` : `https://localhost:5001/Department/Delete/1`
> - confirmaion message before deleting the department(JavaScript) #lab

```csharp
//DepartmentController.cs
public IActionResult Delete(int? id)
{
    if(id == null)
    {
        return BadRequest();
    }
    var dept = db.Departments.FirstOrDefault(d => d.DeptId == id);
    if(dept == null)
    {
        return NotFound();
    }
    db.Departments.Remove(dept);

    return RedirectToAction("Index");
}
```

> [!warning] we add new column to the `Index` view to navigate to the `Delete` action method
>
> - we use `onclick` attribute to show the confirmation message before deleting the department
> - `onclick="return confirm('Are you sure you want to delete this department?')"`
> - if the user clicks `OK` the form will be submitted and the department will be deleted
> - if the user clicks `Cancel` the form will not be submitted and the department will not be deleted

```html
<!-- 
    Index.cshtml
 -->

@model IEnumerable<Department>
  @{ Layout = null; }

  <!DOCTYPE html>
  <html>
    <head>
      <meta name="viewport" content="width=device-width" />
      <title>Index</title>
      <link rel="stylesheet" href="~/css/site.css" />
      <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    </head>
    <body>
      <div class="container">
        <h2>Department List</h2>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>DeptId</th>
              <th>DeptName</th>
              <th>Capacity</th>
              <th>Details</th>
              <th>Edit</th>
              <th>Delete</th>
            </tr>
          </thead>
          <tbody>
            @if (Model.Count() == 0) {
            <tr>
              <td colspan="6">No Department Found</td>
            </tr>
            } else { foreach (var dept in Model) {
            <tr>
              <td>@dept.DeptId</td>
              <td>@dept.DeptName</td>
              <td>@dept.Capacity</td>
              <td>
                <a
                  asp-controller="Department"
                  asp-action="Details"
                  asp-route-id="@dept.DeptId"
                  >Details</a
                >
              </td>
              <td>
                <a
                  asp-controller="Department"
                  asp-action="Edit"
                  asp-route-id="@dept.DeptId"
                  >Edit</a
                >
              </td>
              <td>
                <a
                  asp-controller="Department"
                  asp-action="Delete"
                  asp-route-id="@dept.DeptId"
                  onclick="return confirm('Are you sure you want to delete this department?')"
                  >Delete</a
                >
              </td>
            </tr>
            } }
          </tbody>
        </table>
        <a href="/Department/Create" class="btn btn-primary">Create</a>
      </div>
    </body>
  </html></Department
>
```

> [!tip] make the `Delete` action method to handle the post request and one to handle the get request
>
> - to make a page that will show the details of the department and ask the user to confirm the deletion
> -

```csharp
//DepartmentController.cs
[HttpGet]
public IActionResult Delete(int? id)
{
    if(id == null)
    {
        return BadRequest();
    }
    var dept = db.Departments.FirstOrDefault(d => d.DeptId == id);
    if(dept == null)
    {
        return NotFound();
    }
    return View(dept);
}

[HttpPost]
public IActionResult Delete(int id)
{
    var dept = db.Departments.FirstOrDefault(d => d.DeptId == id);
    if(dept == null)
    {
        return NotFound();
    }
    db.Departments.Remove(dept);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

> [!done] `Delete` view
>
> - we use `form` tag to submit the form to the `Delete` action method
> - we use `asp-action` attribute to specify the action method that the form will be submitted to
> - we use `asp-controller` attribute to specify the controller that the form will be submitted to
> - we don't need to specify the `method` attribute as the default method is `post`

```html
<!-- 
    Delete.cshtml
 -->
@model Department @{ Layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <title>Delete</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
  </head>
  <body>
    <form asp-action="Delete" method="post">
      <input asp-for="DeptId" type="hidden" />
      <input type="submit" value="Delete" class="btn btn-danger" />
      <a href="/Department/Index" class="btn btn-info">Back</a>
    </form>
  </body>
</html>
```

> [!warning] we can remove the `DeptId` input element from the `Delete` and it work the same as it will send the id from the URL to the `Delete` action method
>
> - if the delete action for post method has other name like `DeleteConfirmed` we need to specify the action attribute in the form or .......
>   <br>
>   > [!done] we can use `[ActionName("Delete")]` attribute to specify the action method name that the form will be submitted to

```csharp
//DepartmentController.cs
[ActionName("Delete")]
[HttpPost]
public IActionResult DeleteConfirmed(int id)
{
    var dept = db.Departments.FirstOrDefault(d => d.DeptId == id);
    if(dept == null)
    {
        return NotFound();
    }
    db.Departments.Remove(dept);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

---

> [!tip] `StudentController`

```csharp
//StudentController.cs

namespace Demo1.Controllers
{
    public class StudentController : Controller
    {
        ITIDbContext db = new ITIDbContext();

        public IActionResult Index()
        {
            var students = db.Students.ToList();
            return View(students);
        }

    }
}
```

```html
<!-- 
    /Views/Student/Index.cshtml
 -->
@model IEnumerable<Student>
  @{ Layout = null; }

  <!DOCTYPE html>
  <html>
    <head>
      <meta name="viewport" content="width=device-width" />
      <title>Index</title>
      <link rel="stylesheet" href="~/css/site.css" />
      <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    </head>
    <body>
      <div class="container">
        <h2>Student List</h2>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>Id</th>
              <th>Name</th>
              <th>Age</th>
              <th>Email</th>
              <th>Department</th>
            </tr>
          </thead>
          <tbody>
            @if (Model.Count() == 0) {
            <tr>
              <td colspan="5">No Student Found</td>
            </tr>
            } else { foreach (var student in Model) {
            <tr>
              <td>@student.Id</td>
              <td>@student.Name</td>
              <td>@student.Age</td>
              <td>@student.Email</td>
              <td>@student.DepartmentNavigation.DeptName</td>
              <!-- 
                this will throw exception as we didn't include the department in the query 
                we have to use include method to include the department in the query
                or we can use lazy loading
               -->
            </tr>
            } }
          </tbody>
        </table>
        <a href="/Student/Create" class="btn btn-primary">Create</a>
      </div>
    </body>
  </html>
</Student>
```

> [!tip] `Include` method to include the department in the query

```csharp
//StudentController.cs
public IActionResult Index()
{
    var students = db.Students.Include(s => s.DepartmentNavigation).ToList();
    return View(students);
}
```

> [!tip] `Create` action method to create a new student

```csharp
//StudentController.cs
[HttpGet]
public IActionResult Create()
{
    // var depts = db.Departments.ToList();
    // return View(depts);
    ViewBag.Departments = db.Departments.ToList();//we can use viewbag to pass the list of departments to the view
    return View(new Student());
}

[HttpPost]
public IActionResult Create(Student student)
{
    if(student.Id == 0 || student.Name == null)
    {
        ViewBag.Departments = db.Departments.ToList();
        return View(student);
    }
    db.Students.Add(student);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

```html
<!-- 
    /Views/Student/Create.cshtml
 -->
@model Student
<!-- 
    we need to specify the model type for the view to use the tag helpers
 -->

@{ Layout = null; var depts = ViewBag.Departments as List<Department
  >;
  <!-- 
            we use object from SelectList to pass the list of departments to the view
         -->
  SelectList deptsList = new SelectList(depts, "DeptId", "DeptName",200);
  <!-- 
        SelectList(IEnumerable items, string dataValueField, string dataTextField, object selectedValue)

        now we can use the deptsList object to create the select element
     -->
  }

  <!DOCTYPE html>
  <html>
    <head>
      <meta name="viewport" content="width=device-width" />
      <title>Create</title>
      <link rel="stylesheet" href="~/css/site.css" />
      <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    </head>
    <body>
      <form asp-action="Create" method="post">
        <label asp-for="Id"></label>
        <input asp-for="Id" />
        <label asp-for="Name"></label>
        <input asp-for="Name" />
        <label asp-for="Age"></label>
        <input asp-for="Age" />
        <label asp-for="Email"></label>
        <input asp-for="Email" />
        <label asp-for="DeptNo"></label>
        <!-- <input asp-for="DeptNo" />
        we have to restrict the user to select the department from the list of departments to avoid foreign key constraint violation
      -->
        <!-- <select asp-for="DeptNo">
        <option value="">Select Department</option>
        @foreach (var dept in depts) {
        <option value="@dept.DeptId">@dept.DeptName</option>
        }

      </select> -->
        <select asp-for="DeptNo" asp-items="deptsList">
          <option>Select Department</option>
          <!-- 
            we need to check if the value is not null to avoid exception
           -->
        </select>
        <input type="submit" value="Create" class="btn btn-primary" />
        <a href="/Student/Index" class="btn btn-info">Back</a>
      </form>
    </body>
  </html></Department
>
```

> [!done] `@* *@` to write comments in the view

---

>[!example] Partial Views 
> - we can create `CreateEdit.cshtml` view in Department folder to use it across other Departments views
> - to use it in the `Create` and `Edit` views
> - `<partial name="CreateEdit"  />`
> - this will search for it in the same folder as the view if it is not found it will search for it in the shared folder

```html
<!-- 
    /Views/Department/CreateEdit.cshtml
 -->

@model Department
<label asp-for="DeptName"></label>
<input asp-for="DeptName" />
<label asp-for="Capacity"></label>
<input asp-for="Capacity" />
```
```html
<!-- 
    /Views/Department/Create.cshtml
 -->
@model Department
@{ Layout = null; }
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <title>Create</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
  </head>
  <body>
    <form asp-action="Create" method="post">
      <partial name="CreateEdit" />
      <input type="submit" value="Create" class="btn btn-primary" />
      <a href="/Department/Index" class="btn btn-info">Back</a>
    </form>
  </body>
</html>
```
```html
<!-- 
    /Views/Department/Edit.cshtml
 -->

@model Department

@{ Layout = null; }

<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <title>Edit</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
  </head>
  <body>
    <form asp-action="Edit" method="post">
      <input asp-for="DeptId" readonly />
      <partial name="CreateEdit" />
      <input type="submit" value="Save" class="btn btn-primary" />
      <a href="/Department/Index" class="btn btn-info">Back</a>
    </form>
  </body>
```
>[!warning] what if we added property to the `Department` class
> - we can add the property to the `CreateEdit` view and it will be added to the `Create` and `Edit` views
> - Status property to the `Department` class

```csharp
//Department.cs

public class Department
{
    public int DeptId { get; set; }
    [Display(Name="Department Name")]
    public string DeptName { get; set; }
    public int Capacity { get; set; }
    public string Status { get; set; }
    public ICollection<Student> Students { get; set; } = new HashSet<Student>();
}
```
```html
<!-- 
    /Views/Department/CreateEdit.cshtml
 -->

@model Department
<label asp-for="DeptName"></label>
<input asp-for="DeptName" />
<label asp-for="Capacity"></label>
<input asp-for="Capacity" />
<label asp-for="Status"></label>
<!-- 
    check box to set the status of the department
 -->
<input asp-for="Status" />
```



---

#### #Lab

>[!danger] #### #Lab
> - create CRUD on Department & Student & Course(start with Course)
