## 9:30

### Layout

---

###

> [!error] now all pages are using the layout, but the title is set to title of the layout, not the page.
>
> - we need to change the title according to the page dynamically.
>   > [!done] we can use `ViewBag` to send the title , and in the layout we can use it to set the title of the page.
> - as layout renders after the page.

```csharp
//  Departments/Create.cshtml
@{
    ViewBag.Title = "Create Department";
}
```

```html
<!--  _Layout.cshtml -->
<title>@ViewBag.Title</title>
```

> [!tip] we can add links to the layout, and it will be available in all pages.

```html
<!--  _Layout.cshtml -->
<!DOCTYPE html>
<html>
  <head>
    <title>@ViewBag.Title</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/css/bootstrap.css" />
  </head>
  <body>
    <!-- nav bar (students - departments - courses) -->
    <nav>
      <a asp-controller="Student" asp-action="Index">Students</a>
      <a asp-controller="Department" asp-action="Index">Departments</a>
      <a asp-controller="Course" asp-action="Index">Courses</a>
    </nav>
    <div>
      <!-- 
            to render the body of the page 
         -->
      @RenderBody()
    </div>
  </body>
</html>
```

> [!warning] to change all views to use other layout, we have to change the layout in each view.
>
> - Maintenance issue.
>   > [!done] we can use `_ViewStart.cshtml` to set the layout for all views in the folder.
>   >
>   > - it will be executed before any view
>   > - so we can set the layout for all views in the folder.
>   > - we have to delete the layout from the views.
>   > - any change in the layout will be reflected in all views.

```html
<!--  _ViewStart.cshtml -->
@{ Layout = "_Layout"; }
```

> [!tip] Tracing
>
> 1. `_ViewStart.cshtml` is executed before any view.
> 2. `Index.cshtml` is executed after `_ViewStart.cshtml`.
> 3. `_Layout.cshtml` is executed after `Index.cshtml`.

> [!done] we can set layout for specific view or folder.
>
> - only 1 view: we can set the layout in the view.
>
> ```html
> <!--  Departments/Create.cshtml -->
> @{ Layout = "_Layout1"; }
> ```
>
> - all views in the folder: we can create `_ViewStart.cshtml` in the folder and set the layout.
>   > [!warning] Tracing:
>   >
>   > 1. `_ViewStart.cshtml` will be executed.
>   > 2. `_ViewStart.cshtml` in the folder will be executed.
>   > 3. `Index.cshtml` will be executed.
>   > 4. `_Layout2.cshtml` will be executed.

> [!bug] if we have a script for specific page, and it depends on a library in the layout, it will not work.
>
> - as the layout is rendered after the page.
> - we need to find a way to make the script run after the layout is rendered.
>   > [!done] `@section`
>   >
>   > - we can use `@section` to define a section in the page
>   > - in the layout we can use `@RenderSection` to render the section.
>   > - we can set the section to be optional or required.(to avoid errors)

```html
<!--  Departments/Create.cshtml -->
@section scripts{
<script>
  // script for the page
</script>
}
```

```html
<!--  _Layout.cshtml -->
<!DOCTYPE html>
<html>
  <head>
    <title>@ViewBag.Title</title>
    <link rel="stylesheet" href="~/css/site.css" />
    <link rel="stylesheet" href="~/css/bootstrap.css" />
  </head>
  <body>
    <!-- nav bar (students - departments - courses) -->
    <nav>
      <a asp-controller="Student" asp-action="Index">Students</a>
      <a asp-controller="Department" asp-action="Index">Departments</a>
      <a asp-controller="Course" asp-action="Index">Courses</a>
    </nav>
    <div>
      <!-- 
            to render the body of the page 
         -->
      @RenderBody()
    </div>
    <!-- 
        this will render the section but it is required so it will throw an error if it does not exist in the page
     -->
    @RenderSection("scripts")

    <!-- 
        this will render the section if it exists
     -->
    @RenderSection("scripts", false)
  </body>
</html>
```

> [!warning] we can use this approach to add validation scripts to the page.

```html
<!--  Departments/Create.cshtml -->
@section scripts{
<script src="~/lib/jquery-validation/dist/jquery.validate.js"></script>
<script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.js"></script>
}
```

> [!done]it is better to use `_ValidationScriptsPartial` View as we will use the same scripts in alot of pages.

```cs
// <!--  Departments/Create.cshtml -->
@section scripts{
    <partial name="_ValidationScriptsPartial" />
}
```

> [!tip] we can use RedTie Template to create a layout.
>
> - it is a free template.
> - ([RedTie](https://github.com/chrisgotter/RedTie]))
> - and adjust it to our needs.
> - add the css and js files to the `wwwroot` folder.
> - add `@RenderBody` to the layout to render the body of the page.
> - add `@RenderSection` to the layout to render the scripts of the page.

---

> [!done] to create a new View from action in the controller.
>
> - we can right click on the action and select `Add View`.
> - we can select different Layout for the view.
> - we can select the model for the view.
> - we choose `shared/_Layout3` for the layout.
> - it will setup the layout and send the title to the layout.

```html
<!--  Student/Add.cshtml -->
@{ Layout = "~/Views/Shared/_Layout3.cshtml"; ViewBag.Title = "Add"; }
```

> [!example] send file to the server.
>
> - student can upload his photo.
>   > [!done]
>   >
>   > - form `method` to `post`.
>   > - `enctype` to `multipart/form-data`.
>   > - in controller, we can use `IFormFile` to get the file.

```html
<!--  Student/Add.cshtml -->
<form method="post" enctype="multipart/form-data">
  <input type="file" name="stdImg" />
  <input asp-for="Name" />
  <input asp-for="Age" />
  <input asp-for="Email" />
  <select asp-for="DepartmentId" asp-items="ViewBag.Departments"></select>

  <input type="submit" value="Save" />
</form>
```

```csharp
//  StudentController.cs
[HttpPost]
public async Task<IActionResult> Add(Student student, IFormFile stdImg)
//stdImg has to be the same name as the input in the form.
//we use async as we will use await to wait for the file to be copied.
//we use Task<IActionResult> to return the view after the file is copied. (as the function is async)
{
    // we add the extension to the file name according to the content type.
    string fileName = "1" + "." + stdImg.FileName.Split(".").Last();//with this file name, all images will be the same(will be overwritten). => in case of (same name, same extension).
    using (var fs = new FileStream("wwwroot/images/" + fileName, FileMode.CreateNew))
    {
       await stdImg.CopyToAsync(fs);//this will create thread to copy the file to the server.
        //this will copy the file to file stream.
        //we use await to wait for the file to be copied as the next line depends on the file to be copied.
    }//this will dispose the file stream after the using block.
    // we have to do this so we can use(otherwise it will be locked: this file is being used by another process).
    // we don't use the file name from the client.

//    int await = 20;
   //on using async
//    int await = 10;//this will throw an error
    return RedirectToAction("Index");
}
```

---

# Break

---

> [!tip] add student stdImageName to the student model.

```csharp
//  Student.cs
public string? stdImageName { get; set; }
//we use ? to make it nullable we can add student without image.
```

```html
<!--  Student/Create.cshtml -->
<input type="file" name="stdImg" />
```

```csharp
//  StudentRepo.cs
public interface IStudentRepo
{
    void UpdateStudentImage(string stdImageName, int stdId);
}
public class StudentRepo : IStudentRepo
{
    public void UpdateStudentImage(string stdImageName, int stdId)
    {
        var std = db.Students.FirstOrDefault(s => s.Id == stdId);
        std.stdImageName = stdImageName;
        db.SaveChanges();
    }
}
```

```csharp
//  StudentController.cs
[HttpPost]
public async Task<IActionResult> Create(Student student, IFormFile stdImg)
{
    if (ModelState.IsValid)
    {
        StudentRepo.Add(student);
            string fileName = $"{student.Id}.{stdImg.FileName.Split(".").Last()}";
            using (var fs = new FileStream("wwwroot/images/" + fileName, FileMode.CreateNew))
            {
                await stdImg.CopyToAsync(fs);
                student.stdImageName = fileName;
                StudentRepo.UpdateStudentImage(fileName, student.Id);
            }
            //or we can just add the student after updating the image.
        return RedirectToAction("Index");
    }
    return View(student);


}
```

> [!tip] `HttpContext`
>
> - every request creates a new `HttpContext`.
> - it contains the request and the response
> - we can add

> [!example] Middlewares
> ![Middlewares](Pasted%20image%2020240317114430.png)
> - we can use middlewares to add functionality to the request and the response.
> - each middleware sends `HttpContext` to the next middleware.
> - on response, each middleware sends `HttpContext` to the previous middleware (processing the response).
> - any middleware can stop this flow and send the response to the client. (short-circuiting)

> [!example]
>
> - short-circuiting using `app.Run` (last middleware).
> - `RequestDelegate` is a delegate that takes `HttpContext` and returns `Task`.

```csharp
//  Program.cs
var app = builder.Build();
app.Run(async context =>
{
    // we can get request information from the context.
    Console.WriteLine(context.Request.Path);//this will print the path of the request.
    //we can write to the response.
    await context.Response.WriteAsync("First Middleware"); //this will stop the flow and send the response to the client.
});



```

>[!bug] add another middleware.
```csharp
//  Program.cs
app.Run(async context =>
{
.
    await context.Response.WriteAsync("First Middleware"); //this will stop the flow and send the response to the client.
});
app.Run(async context =>
{
    
    await context.Response.WriteAsync("Second Middleware"); //won't be executed as the first middleware will stop the flow.
});
```
>[!done] to add another middleware we have to use `app.Use` instead of `app.Run`.
```csharp
//  Program.cs
app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("First Middleware\n");
    await next.Invoke();//this will send the context to the next middleware.
    await context.Response.WriteAsync("inside first middleware after return from second middleware next");
});
app.Run(async context =>
{
    await context.Response.WriteAsync("Second Middleware");//
});

//result:
//First Middleware
//Second Middleware
//inside first middleware after return from second middleware next
```
>[!warning] `App.Map` to map the middleware to specific path.
> - ==SEARCH==: `App.Map` 


>[!tip] `app.UseExceptionHandler` to handle exceptions.
> - we can use it to handle exceptions in the application.
> - will show the detailed error page 
> - has to be the ==first== middleware in the pipeline.
> - as it will wait for result of all middlewares when the response is sent.

```csharp
//  Program.cs
if(!app.Environment.IsDevelopment())
{
app.UseExceptionHandler("/Home/Error");
}
```

>[!tip] `ASPNETCORE_ENVIRONMENT`
> - Development: will show the detailed error page.
> - Production: will show the generic error page.


>[!warning] `app.UseStaticFiles` to serve static files.
> - we have to add it before `app.UseRouting` to serve the static files.
> - static files: images, css, js, ... etc. 
> - all files in `wwwroot` will be served.
> - we can configure it by passing `StaticFileOptions` to the method.

```csharp
//  Program.cs
app.UseStaticFiles();
```
>[!done] we can use `app.UseRouting` to route the request to the correct controller and action.
> - it will use the routing to find the correct controller and action.
> - as the next middleware needs to know the controller and action to execute. `app.UseAuthorization` 

>[!example] `app.UseAuthorization` to authorize the request.
> - it will check if the user is authorized to access the resource.

>[!done] `app.MapControllerRoute` 
> - create object of the Controller.
> - from the object, call the action.

>[!tip] and now the pipeline is ready to handle the request.
```csharp
//  Program.cs
//main  method

  public static void Main(string[] args)
  {
      var builder = WebApplication.CreateBuilder(args);

      var app = builder.Build();

      // Configure the HTTP request pipeline.
      if (!app.Environment.IsDevelopment())
      {
          app.UseExceptionHandler("/Home/Error");
      
      }
      app.UseStaticFiles();

      app.UseRouting();

      app.UseAuthorization();

      app.MapControllerRoute(
          name: "default",
          pattern: "{controller=Home}/{action=Index}/{id?}");

      app.Run();
  }
```
----

## Lab

>[!tip] #lab
> - make the layout for the application.
> - upload student image.
