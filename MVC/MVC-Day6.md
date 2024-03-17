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

>[!done] to create a new View from action in the controller.
> - we can right click on the action and select `Add View`.
> - we can select different Layout for the view.
> - we can select the model for the view.
> - we choose `shared/_Layout3` for the layout.
> - it will setup the layout and send the title to the layout.

```html
<!--  Student/Add.cshtml -->
@{
    Layout = "~/Views/Shared/_Layout3.cshtml";
    ViewBag.Title = "Add";
}

```

> [!example] send file to the server.
> - student can upload his photo.
> > [!done] 
> > - form `method` to `post`.
> > - `enctype` to `multipart/form-data`.
> > - in controller, we can use `IFormFile` to get the file.

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
