
> [!tip] Course
>
> - relation between student and course is many to many
> - relation between department and course is many to many
> - when adding a new Column or Property `Degree` to the relation between student and course, we need to add `StudentCourse` class

```csharp
// Model/Course.cs
public class Course
{
    public int CrsId { get; set; }
    public string CrsName { get; set; }

    public int Duration { get; set; }

    public List<Department> Departments { get; set; } = new List<Department>();//Course has many departments

    public List<StudentCourse> StudentCourses { get; set; } = new List<StudentCourse>();
}
```

```csharp
// Model/Department.cs
public class Department
{
    public int DeptId { get; set; }
    public string DeptName { get; set; }

    public List<Course> Courses { get; set; } = new List<Course>();//Department has many courses

}
```

```csharp
// Model/Student.cs
public class Student
{
    public int StdId { get; set; }
    public string StdName { get; set; }


    public List<StudentCourse> StudentCourses { get; set; } = new List<StudentCourse>();
}
```

```csharp
// Model/StudentCourse.cs
public class StudentCourse
{
    [ForeignKey("StudentNavigation")]//Foreign Key for Student
    public int StdId { get; set; }
    [ForeignKey("CourseNavigation")]//Foreign Key for Course
    public int CrsId { get; set; }

    public int Degree { get; set; }

    public Student StudentNavigation { get; set; }
    public Course CourseNavigation { get; set; }
}
```

> [!bug] Fluent API
>
> - we need to use Fluent API to define the composite key for the `StudentCourse` class

```csharp
//Data/ITIContext.cs
//add DbSet for StudentCourse
public virtual DbSet<StudentCourse> StudentCourses { get; set; }

//override OnModelCreating
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<StudentCourse>(entity =>
    {
        entity.HasKey(e => new { e.StuId, e.CrsId });
    });
}
```

> [!done] `add-migration` and `update-database` commands
>
> - run the `add-migration` command to create the migration file
> - run the `update-database` command to apply the migration to the database

```powershell
add-migration AddStudentCourse
update-database
```

> [!example] create a new Controller to manage the data of the `DepartmentCourse` class

```csharp
//Controllers/DeptCoursesController.cs
public class DeptCoursesController : Controller
{
    ITIContext db = new ITIContext();//we can use dependency injection to inject the context into the controller
    public IActionResult ShowCoursesByDept(int id)
    {
        var dept = db.Departments.Include(d => d.Courses).FirstOrDefault(d => d.DeptId == id);
        return View(dept);
    }
}
```

> [!warning] `ShowCoursesByDept` View
>
> - right click on the `ShowCoursesByDept` method and select `Add View`
> 

```html
@model IEnumerable<Department>
  @{ Layout = null; }

  <!DOCTYPE html>

  <html>
    <head>
      <meta name="viewport" content="width=device-width" />
      <title>ShowCoursesByDept</title>
    </head>
    <body>
      <div class="alert alert-info">
        <h1>@Model.deptId</h1>
        <h1>@Model.deptName</h1>
        <h1>@Model.Capacity</h1>
      </div>

      <table>
        <tr>
          <th>Course Name</th>
          <th>Duration</th>
        </tr>
        @foreach (var item in Model.Courses) {
        <tr>
          <td>@item.CrsName</td>
          <td>@item.Duration</td>
        </tr>
        }
      </table>
    </body>
  </html>
```

> [!tip] add link in `Index` view to navigate to `ShowCoursesByDept` view

```html
<!-- 
    Views/Departments/Index.cshtml
 -->

<a
  asp-controller="DeptCourses"
  asp-action="ShowCoursesByDept"
  asp-route-id="@item.DeptId"
  >@item.DeptName</a
>
```

> [!bug] `ManageDeptCourses`
>
> - `[HttpGet]` to get the view to add or remove courses from a department

```csharp
//Controllers/DeptCoursesController.cs

    [HttpGet]

    public IActionResult ManageDeptCourses(int id)
    {
        //get courses in dept
        var dept = db.Departments.Include(d => d.Courses).FirstOrDefault(d => d.DeptId == id);
        var courses = dept.Courses.ToList();

        //get all courses
        var allCourses = db.Courses.ToList();

        //get courses not in dept
        var notInDept = allCourses.Except(courses);

        //send data to view
        ViewBag.Dept = dept;
        ViewBag.NotInDept = notInDept;
        return View();
    }
```

> [!tip] add link in `Index` view to navigate to `ManageDeptCourses` view

```html
<!-- 
    Views/Departments/Index.cshtml
 -->
<a
  asp-controller="DeptCourses"
  asp-action="ManageDeptCourses"
  asp-route-id="@item.DeptId"
  >change Department Courses</a
>
```

> [!done] `ManageDeptCourses` View

```html
<!-- 
    Views/DeptCourse/ManageDeptCourses.cshtml

 -->

@{ 

	Department dept = ViewBag.Dept as Department; 
	List<Course> notInDept = ViewBag.NotInDept as List<Course>;
	SelectList coursesInDept = new SelectList(dept.Courses, "CrsId",
    "CrsName"); 
    SelectList coursesNotInDept = new SelectList(notInDept, "CrsId",
    "CrsName"); 
}

    <!DOCTYPE html>

    <html>
      <head>
        <meta name="viewport" content="width=device-width" />
        <title>MangeDeptCourses</title>
      </head>

      <body>
        <h2>Add Course</h2>
        <form method="post">
          <input type="hidden" name="deptId" value="@dept.DeptId" />
          <!-- 
            multiple attribute to allow selecting multiple courses
         -->
          <select
            name="crsId"
            multiple
            name="coursesToRemove"
            asp-items="coursesInDept"
          ></select>
          <!-- 
            add courses to department
         -->
          <select
            name="crsId"
            multiple
            name="coursesToAdd"
            asp-items="coursesNotInDept"
          ></select>

          <input type="submit" value="Change" />
        </form>
      </body>
      </html>
```

> [!done] `ManageDeptCourses` method
>
> - `HttpPost` method to add or remove courses from a department

```csharp
//Controllers/DeptCoursesController.cs
[HttpPost]

public IActionResult ManageDeptCourses(int deptId, List<int> coursesToAdd, List<int> coursesToRemove)
{
    //get department
    var dept = db.Departments.Include(d => d.Courses).FirstOrDefault(d => d.DeptId == deptId);

    foreach (var item in coursesToRemove)
    {
        //remove course from department
        Course course = db.Courses.FirstOrDefault(c => c.CrsId == item);
        dept.Courses.Remove(course);

    }
    foreach (var item in coursesToAdd)
    {
        //add course to department
        Course course = db.Courses.FirstOrDefault(c => c.CrsId == item);
        dept.Courses.Add(course);
    }
    db.SaveChanges();

    return RedirectToAction("Index", "Departments");//redirect to index action in departments controller
}
```

> [!example]
>
> - choose course in department to show list of students in this course and be able to change the degree of the student in this course
> - create a new controller `StudentCoursesController` to manage the data of the `StudentCourse` class

```csharp
//  Controllers/StudentCoursesController.cs
public class StudentCoursesController : Controller
{
    ITIContext db = new ITIContext();//we can use dependency injection to inject the context into the controller

    [HttpGet]//get the view to show students in a course
    public IActionResult UpdateStudentDegrees(int deptId, int crsId)
    {
        var model = db.Departments.Include(d => d.Students).FirstOrDefault(d => d.DeptId == deptId);

        return View(model);

    }
}
```

> [!done] add view to `UpdateStudentDegrees` method
>
> - first: add link in `ShowCoursesByDept` view to navigate to `UpdateStudentDegrees` view

```html
<!-- 
    Views/DeptCourses/ShowCoursesByDept.cshtml
 -->
<a
  asp-controller="StudentCourses"
  asp-action="UpdateStudentDegrees"
  asp-route-deptId="@Dept.DeptId"
  asp-route-crsId="@item.CrsId"
  >add Students degrees</a
>
```

```html
<!-- 
    Views/StudentCourses/UpdateStudentDegrees.cshtml
 -->

@model Department @{ Layout = null; }

<!DOCTYPE html>

<html>
  <head>
    <meta name="viewport" content="width=device-width" />
    <title>UpdateStudentDegrees</title>
  </head>
  <body>
    <h2>Students</h2>
    <form method="post">
      @foreach (var item in Model.Students) {
      <label> @item.Name </label>
      <input type="number" name="degrees.@item.Id" />
      <!-- 
                we used the name attribute to send the degree of the student to the server as a dictionary
                degrees.@item.Id
                where @item.Id is the key of the dictionary
             -->
      }
    </form>
  </body>
</html>
```

> [!done] `UpdateStudentDegrees` method
>
> - `HttpPost` method to update the degree of the students in a course

```csharp
//Controllers/StudentCoursesController.cs
[HttpPost]

public IActionResult UpdateStudentDegrees(int deptId, int crsId, Dictionary<int, int> degrees)
{
   foreach (var item in stdDegree)
   {
    //we need to check if the student is already in database or not
    var model = db.StudentCourses.FirstOrDefault(sc => sc.StdId == item.Key && sc.CrsId == crsId);
    if (model != null)
    {
        model.Degree = item.Value;
    }
    else
    {
        db.StudentCourses.Add(new StudentCourse { StdId = item.Key, CrsId = crsId, Degree = item.Value });
    }
    }
    db.SaveChanges();
    return RedirectToAction("Index", "Departments");//redirect to index action in departments controller

}
```

---

# Break

---

> [!tip] how to create student in last session file [[MVC-Day3.md]](MVC-Day3.md)

> [!warning] Validation in MVC
>
> - we write in model, view and controller
> - in model we use DataAnnotations like `[Required]`,`[StringLength]`, `[Range]`,

```csharp
//Model/Student.cs
public class Student
{
    public int StdId { get; set; }

    [Required]//this field is required
    [StringLength(50, MinimumLength = 3)]//max length is 50 and min length is 3
    public string StdName { get; set; }

    //constraint only the type of the field(int)
    [Range(18, 60)]//age must be between 18 and 60
    //can be used with DateTime type
    [Compare("stdId")]//to match the value of the field with another field
    public int Age { get; set; }

    [Required]
    [RegularExpression(@"[a-zA-Z0-9_]+@[a-zA-Z_]+[A-Za-z]{2,4}", ErrorMessage = "Invalid Email")]//email pattern
    public string Email { get; set; }
}
```

> [!warning] `ModelState` in Controller
>
> - it contains the state of the model binding process

```csharp
//Controllers/StudentsController.cs
[HttpPost]
public IActionResult Create(Student student)
{
    if (ModelState.IsValid)//check if all the validation rules are passed
    {
        db.Students.Add(student);
        db.SaveChanges();
        return RedirectToAction("Index");
    }
    ViewBag.Dept = db.Departments.ToList();
    return View(student);
}
```

> [!done] `Create` View
>
> - to show the validation messages
> - use `asp-validation-for` tag helper to show the validation messages

```html
<form asp-action="Create" method="post">
  <label asp-for="Id"></label>
  <input asp-for="Id" />
  <span asp-validation-for="Id"></span>

  <label asp-for="Name"></label>
  <input asp-for="Name" />
  <span asp-validation-for="Name"></span>

  <label asp-for="Age"></label>
  <input asp-for="Age" />
  <span asp-validation-for="Age"></span>

  <label asp-for="Email"></label>
  <input asp-for="Email" />
  <span asp-validation-for="Email"></span>

  <label asp-for="DeptNo"></label>
  <select asp-for="DeptNo" asp-items="deptsList"></select>
  <span asp-validation-for="DeptNo"></span>
  <input type="submit" value="Create" class="btn btn-primary" />
  <a href="/Student/Index" class="btn btn-info">Back</a>
</form>
```

> [!done] add jquery and validation libraries

```html
      <!--
    add jquery and validation libraries

    order is important as jquery must be loaded before the validation libraries to work
 -->
      <script src="~/lib/jquery/dist/jquery.js"></script>
      <script src="~/lib/jquery-validation/dist/jquery.validate.js"></script>
      <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.js"></script>
      <!--
            enabling client side validation
            this will validate the form and show the validation messages without refreshing the page
         -->
    </body>
  </html>
```

> [!danger] any date annotation has `ErrorMessage` property to show the error message
>
> - `[Required(ErrorMessage="This field is required")]`

> [!warning] we can change color of the validation message
>
> - by adding class `data-valmsg-for` to css file
> - or by giving class to span

```css
/* wwwroot/css/site.css */
.data-val-error {
  color: red;
}
```

> [!example]
>
> - we don't have to use `[Required]` attribute in the model as it is required according to the datatype `int` => not nullable

> [!danger] validate `deptId` when creating a new department
>
> - we need to check if the department is already in the database or not
> - and show the error message if it is already in the database
> - we can use `[Remote]` attribute to validate the field it will send a request to the server to check the value of the field
> - we need to add a method in the controller to check the value of the field `CheckDeptId` and add the `[Remote]` attribute to the `DeptId` property

```csharp
//Model/DepartmentController.cs
[HttpGet]
public IActionResult CheckDeptId(int deptId)
{
    var model = db.Departments.FirstOrDefault(d => d.DeptId == deptId);
    if (model == null)
    {
        return Json(true);//valid department id
    }
    int x = db.Departments.Max(d => d.DeptId);

    return Json($"{deptId} is invalid department id, you can use {x + 1}");//invalid department id
}
```

```csharp
//Model/Department.cs
public class Department
{
    public int DeptId { get; set; }
    [Remote("CheckDeptId", "Department")]
    public string DeptName { get; set; }
}
```

> [!tip] add validation message to the view
>
> - `asp-validation-for` tag helper to show the validation message
> - add validation libraries to the view to enable client side validation

```html
<!-- 
    Views/Departments/Create.cshtml
 -->
<span asp-validation-for="DeptId"></span>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/jquery-validation/dist/jquery.validate.js"></script>
<script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.js"></script>
```

> [!danger] we have to add validation to `Edit` method using `ModelState`

> [!edit] check id in `Edit` method
>
> - id is supposed to exist in the database so we don't need to check it #lab

> [!done] using `AJAX` to display department details in the `Index` view
>
> - create Method in the `Department` controller to get the department details `DetailsAjax`
>   include the `jquery` library in the view before the script

```csharp

//Controllers/DepartmentsController.cs
[HttpGet]
public IActionResult DetailsAjax(int id)
{
    var dept = db.Departments.FirstOrDefault(d => d.DeptId == id);
    return PartialView(dept);
}
```

> [!example] create a new view to display the department details `DetailsAjax.cshtml`

```html
@model Department

<div class="alert alert-info">
  <h1>@Model.DeptId</h1>
  <h1>@Model.DeptName</h1>
  <h1>@Model.Capacity</h1>
</div>
```

```html
<!-- 
    Views/Departments/Index.cshtml
 -->
<button onclick="showDetails(@item.DeptId)">Show Details</button>
<div id="deptContainers">
  <!-- 
        this div will be filled with the department details
     -->
</div>
<script src="~/lib/jquery/dist/jquery.js"></script>
<script>
  function showDetails(id) {
    $("#deptContainers").load("/Departments/DetailsAjax?id=" + id);

    //or we can use ajax try in lab
    // $.ajax({
    //   url: "/Departments/DetailsAjax",
    //   data: { id: id },
    //   success: function (data) {
    //     $("#deptContainers").html(data);
    //   },
    // });
  }
</script>
```

>[!tip] #lab
> - manage degrees of student (text box filled with the degree of the student in the course)
> - validate student data (email=> unique)
> - show department details in the same view