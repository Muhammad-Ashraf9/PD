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
            var departments = _db.Departments.ToList();
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
  </html></Department
>
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
      <input type="text" id="DeptId" name="DeptId"  />
      <label for="DeptName">DeptName</label>
      <input type="text" id="DeptName" name="DeptName"  />
      <label for="Capacity">Capacity</label>
      <input type="text" id="Capacity" name="Capacity"  />
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
  <input
    type="text"
    id="DeptId"
    name="DeptId"
    
    value="@Model?.DeptId"
  />
  <label for="DeptName">DeptName</label>
  <input
    type="text"
    id="DeptName"
    name="DeptName"
    
    value="@Model?.DeptName"
  />
  <label for="Capacity">Capacity</label>
  <input
    type="text"
    id="Capacity"
    name="Capacity"
    
    value="@Model?.Capacity"
  />
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
  <input
    type="text"
    id="DeptId"
    name="DeptId"
    
    value="@Model?.DeptId"
  />
  <label for="DeptName">DeptName</label>
  <input
    type="text"
    id="DeptName"
    name="DeptName"
    
    value="@Model?.DeptName"
  />
  <label for="Capacity">Capacity</label>
  <input
    type="text"
    id="Capacity"
    name="Capacity"
    
    value="@Model?.Capacity"
  />
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
> > - same for the label tag
> > - `<label asp-for="DeptId">DeptId</label>`

```html
//Create.cshtml
 @model Department

<form method="post">
  <label asp-for="DeptId"></label>
  <!-- 
    we can use label without writitng  in the label text and it will work
    <label for="DeptId">DeptId</label>
   -->
  <input asp-for="DeptId"  />
  <!-- 
    it will give warning if we write the property name wrong
    like DeptIdd instead of DeptId
   -->
  <label asp-for="DeptName">Department Name</label>
  <!-- 
    if we write in the label it will take Department Name as the label text
    <label for="DeptName">Department Name</label>
   -->
  <input asp-for="DeptName"  />
  <label asp-for="Capacity">Capacity</label>
  <input asp-for="Capacity"  />
  <input type="submit" value="Create" class="btn btn-primary" />

  <a href="/Department/Index" class="btn btn-info">Back</a>

</form>
```

>[!example] to change default text in label tag we use `[Display(Name="Department Name")]` attribute in the `Department` class
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
