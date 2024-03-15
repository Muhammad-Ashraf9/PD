> [!tip] `asp-validation-summary` is a tag helper that renders a list of validation messages for the specified
>
> - send new message from server to client(not to specific field)

> [!bug] disable ==client== side validation

```csharp
//StudentController.cs
[HttpPost]
public IActionResult Create(Student student)
{
    if (ModelState.IsValid)
    {
        db.Students.Add(student);

        db.SaveChanges();
        return RedirectToAction("Index");
    }
    ModelState.AddModelError("", "invalid model data");//first parameter is empty string as it is not related to any specific field (it is for the whole model)

    //to specify the field
    ModelState.AddModelError("Name", "Name is required");

    return View();
}
```

```html
//Create.cshtml
<form asp-action="Create">
  <div asp-validation-summary="All"></div>
  <!-- 
      display all validation messages (added and from field validation)
   -->
  <div asp-validation-summary="ModelOnly"></div>
  <!-- 
            display only validation messages added by ModelState.AddModelError("", "invalid model data");
       -->
</form>
```

---

> [!example] soft delete
>
> - delete department will only be hidden from the user but not deleted from the database
> - set Status to `false` instead of deleting the record
> - so we want to do something every time we read Department from the database
> - so to not do this in every action we need to make `ITIContext` separate from the controllers
> - use Repository pattern

> [!done] repository pattern
>
> - make repository for each entity like `DeptRepository` for Department and it will have all the methods that we need to do with the entity `GetAll`, `GetById`, `Add`, `Update`, `Delete`
> - midlle layer between the controller and the database `ITIContext`
> - better maintainability
> - change is done in one place

> [!tip]
>
> - create a new folder `Repositories` and add a new class `DeptRepo`

```csharp
// DeptRepo.cs
public class DeptRepo
{
    ITIContext db = new ITIContext();//only the repository will have access to the database context

    public List<Department> GetAll()
    {
        //no we can add code to do something every time we read Department from the database
        //this will be done in one place
        return db.Departments.Where(d => d.Status == true).ToList();
    }

    public Department GetById(int id)
}
```

> [!example] use the repository in the controllers
>
> - no we can replace every time we read department from the database with `GetAll` method
> - create an instance of the repository in the controller`DeptRepo deptRepo = new DeptRepo();`
> - use the repository to get the departments `deptRepo.GetAll()`

```csharp
//StudentController.cs
public class StudentController : Controller
{
    DeptRepo deptRepo = new DeptRepo();//create an instance of the repository

    public IActionResult Index()
    {
        //now we can use the repository to get the departments
        return View(deptRepo.GetAll());
    }
}
```

> [!done] `StudentRepo`
>
> - same as `DeptRepo` but for `Student` entity
> - we need to include the `Department` every time we read `Student` from the database

```csharp
//StudentRepo.cs
public class StudentRepo
{
    ITIContext db = new ITIContext();

    public List<Student> GetAll()
    {
        //to include the Department every time we read Student from the database
        return db.Students.Include(s => s.Dept).Where(s => s.Status == true).ToList();
    }

    public Student Add(Student student)
    {
        try
        {
            db.Students.Add(student);
            db.SaveChanges();
            // return student.Id;//as it gets the id from the database as it is auto generated
        }
        catch (Exception ex)
        {
            throw;//this will throw the exception to the controller to specify the same line that caused the exception
            //instead of throwing a new exception
            // throw new Exception("Error in adding student");
            //we can throw exception inside exception
            throw new Exception("Error in adding student", ex);
        }

    }
}
```

> [!done] now we can use the repository in the controllers -`StudentRepo studentRepo = new StudentRepo();`
>
> - `DeptRepo deptRepo = new DeptRepo();`

> [!done] `GetById` , `Add` , `Delete` , `Update` methods

```csharp
//DeptRepo.cs
public class DeptRepo
{
    ITIContext db = new ITIContext();

    public List<Department> GetAll()
    {
        return db.Departments.Where(d => d.Status == true).ToList();
    }

    public Department GetById(int id)
    {
        return db.Departments.FirstOrDefault(d => d.Id == id);
    }

    public void Add(Department dept)
    {
        db.Departments.Add(dept);
        db.SaveChanges();
    }

    public void Delete(int id)
    {
        Department dept = GetById(id);
        dept.Status = false;//on delete set the status to false
        db.SaveChanges();
    }

    public void Update(Department dept)
    {
        //this is used to update some properties of the object
        var model = db.Departments.FirstOrDefault(d => d.Id == dept.Id);
        model.DeptName = dept.DeptName;
        model.Status = dept.Status;
        model.Capacity = dept.Capacity;

        //this is used to update the whole object
        db.Departments.Update(dept);
        db.SaveChanges();
    }
}
```

> [!bug] when using `GetById` method in the `Details` action
>
> - Details action is recieving nullable int `id` so we need to check if it is null
>   > [!done] we check if the `id` is null and return `NotFound`
>   >
>   > - then we can use `GetById(id.Value)` Value is used to get the value of the nullable int
>   > - or we can cast the `id` to int `GetById((int)id)` but it is not safe as it will throw an exception if the `id` is null
>   > - better to use `??` operator `GetById(id ?? 0)` if `id` is null it will be replaced with `0`

```csharp
//DeptController.cs
public IActionResult Details(int? id)
{
    if (id == null)
    {
        return NotFound();
    }
    return View(deptRepo.GetById(id.Value));
}

```

> [!warning] replace all the code in the controllers with the repository methods `Add` , `Delete` , `Update` , `GetById` , `GetAll`

> [!example] `DeptMockRepo` for testing
>
> - it uses a list of departments instead of the database
> - it is used to test the repository methods without the need to connect to the database and without the need to add, delete or update the database

```csharp
//DeptMockRepo.cs
public class DeptMockRepo
{
   static List<Department> depts = new List<Department>
    {
        new Department{Id=1,DeptName="IT",Capacity=100,Status=true},
        new Department{Id=2,DeptName="CS",Capacity=100,Status=true},
        new Department{Id=3,DeptName="IS",Capacity=100,Status=true},
        new Department{Id=4,DeptName="SW",Capacity=100,Status=true},
    };

    public List<Department> GetAll()
    {
        return depts;
    }

    public Department GetById(int id)
    {
        return depts.FirstOrDefault(d => d.Id == id);
    }

    public void Add(Department dept)
    {
        depts.Add(dept);
    }

    public void Delete(int id)
    {
        Department dept = GetById(id);
        dept.Status = false;
    }

    public void Update(Department dept)
    {
        var model = depts.FirstOrDefault(d => d.Id == dept.Id);
        model.DeptName = dept.DeptName;
        model.Status = dept.Status;
        model.Capacity = dept.Capacity;
    }
}
```

> [!faq] how to replace the repository with the mock repository without changing the code in the controllers?
>
> - how to make another repository for the same entity but with different database (`nosql`)?
> -

> [!tip] `Controller` vs `Repository`
>
> - `Controller` is Client or Dependant
> - `Repository` is Service or Dependency

> [!done] we need to change the architecture of the project
>
> 1. Inversion of Control (IoC)
>
> - make Factory class to create the repository and return object from it instead of creating it in the controller(we can make the factory ==singleton==: create only one instance of the repository or ==Transient==: create new instance every time it is called)
> - to solve the problem of returning different objects types we use Dependency Injection Principle (DIP)
>
> 2. Dependency Injection Principle (DIP)
>
> - make the repository interface and make the repository class implement it
> - now Factory class can return object of the interface type

> [!example] Dependency Injection Principle (DIP)
>
> - High-level modules should not depend on low-level modules. Both should depend on ==abstractions==.
> - Abstractions should not depend on details. Details should depend on abstractions.

```csharp
//IRepository.cs
public interface IRepository<T>
{
    List<T> GetAll();
    T GetById(int id);
    void Add(T entity);
    void Delete(int id);
    void Update(T entity);
}
```

```csharp
//DeptFactory.cs
//singleton
public class DeptFactory
{
    static DeptRepo deptRepo = null;

    public static IRepository<Department> Create()
    {
        if (deptRepo == null)
        {
            deptRepo = new DeptRepo();
        }
        return deptRepo;
    }
}
```

---

# Break

---

> [!tip] Client vs Injectors vs Service class
>
> -

> [!example] IoC Container: injector
>
> - Register: register the repository in the injector (create object of the repository)
> - Resolve: get the object from the injector (get the object of the repository)=> Constructor, Property, Method parameter (mostly Constructor => Constructor Injection)
> - Dispose: remove the object from the injector (remove the object of the repository)

```csharp
// StudentRepo.cs

public interface IStudentRepo
{
    List<Student> GetAll();
    Student GetById(int id);
    void Add(Student student);
    void Delete(int id);
    void Update(Student student);
}

public class StudentRepo : IStudentRepo
{
    ITIContext db = new ITIContext();

    public List<Student> GetAll()
    {
        return db.Students.Include(s => s.Dept).Where(s => s.Status == true).ToList();
    }

}
```

```csharp
//StudentController.cs
public class StudentController : Controller
{
    // IStudentRepo studentRepo = new StudentRepo();
    //instead of creating the object of the repository in the controller we use the injector
    IStudentRepo studentRepo;//so we can use the repository passed from the injector

    //we need to register first the repository in the injector
    public StudentController(IStudentRepo _studentRepo)
    {
        studentRepo = _studentRepo;
    }


}
```

```csharp
//Program.cs
public class Program
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        //Add services to the container.
        builder.Services.AddControllersWithViews();

        //register the repository in the injector
        builder.Services.AddScoped<IStudentRepo, StudentRepo>();//when someone asks for IStudentRepo return object of StudentRepo
        //we can change IStudentRepo to StudentRepo and it will work but it is better to use the interface
    }

}
```

> [!done] now if we add `StudentMockRepo` we can change the repository in the injector without changing the code in the controllers

> [!example] `AddTransient` vs `AddScoped` vs `AddSingleton`
>
> - AddTransient: create new instance every time it is called
> - AddScoped: create new instance every time the request is created
> - AddSingleton: create only one instance of the repository

```csharp
//StudentRepo.cs

public class StudentMockRepo : IStudentRepo
{
    static List<Student> students = new List<Student>
    {
        new Student{Id=1,Name="Ahmed",DeptId=1,Status=true},
        new Student{Id=2,Name="Ali",DeptId=2,Status=true},
        new Student{Id=3,Name="Amr",DeptId=3,Status=true},
        new Student{Id=4,Name="Aly",DeptId=4,Status=true},
    };
    //rest of the methods

}
```

```csharp
//Program.cs
public class Program
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        //Add services to the container.
        builder.Services.AddControllersWithViews();

        //register the repository in the injector
        builder.Services.AddScoped<IStudentRepo, StudentMockRepo>();//when someone asks for IStudentRepo return object of StudentMockRepo
    }

}
```

> [!tip] const vs readonly
>
> - const: has to be initialized at the time of declaration and cannot be changed
> - readonly: const but can change in constructor

> [!done] `[fromServices]` attribute
>
> - to get the object from the injector without using the constructor

> [!example] `TestController` to test the repository methods
>
> - Case:`AddSingleton`
> - ```csharp
>     //Program.cs
>   builder.Services.AddSingleton<IStudentRepo, StudentRepo>();
>   ```

```csharp
//TestController.cs
public class TestController : Controller
{
    IStudentRepo studentRepo;

    public TestController(IStudentRepo _studentRepo)
    {
        studentRepo = _studentRepo;
    }

    public IActionResult Index( [FromServices] IStudentRepo _r2){
        Console.WriteLine($"studentRepo: { studentRepo.GetHashCode() }", $"_r2: { _r2.GetHashCode() }");//both will have the same hash code
    }
}
```

> [!done] `TestController` to test the repository methods
>
> - Case:`AddScoped` : create new instance every time the request is created(same request will have the same instance)

```csharp
//Program.cs
builder.Services.AddScoped<IStudentRepo, StudentRepo>();
```


> [!done] `TestController` to test the repository methods
>
> - Case:`AddTransient` : create new instance every time it is called
> - ```csharp
>     //Program.cs
>   builder.Services.AddTransient<IStudentRepo, StudentRepo>();
>   ```

```csharp
//TestController.cs
public class TestController : Controller
{
    IStudentRepo studentRepo;

    public TestController(IStudentRepo _studentRepo)
    {
        studentRepo = _studentRepo;
    }

    public IActionResult Index([FromServices] IStudentRepo _r2){
        Console.WriteLine($"studentRepo: { studentRepo.GetHashCode() }", $"_r2: { _r2.GetHashCode() }");//both will have different hash code
    }
}
```

> [!example] `DeptRepo`

```csharp
//DeptRepo.cs

interface IDeptRepo
{
    List<Department> GetAll();
    Department GetById(int id);
    void Add(Department dept);
    void Delete(int id);
    void Update(Department dept);
}

public class DeptRepo : IDeptRepo
{
    ITIContext db = new ITIContext();

    //rest of the methods
}

public class DeptMockRepo : IDeptRepo
{
    static List<Department> depts = new List<Department>
    {
        new Department{Id=1,DeptName="IT",Capacity=100,Status=true},
        new Department{Id=2,DeptName="CS",Capacity=100,Status=true},
        new Department{Id=3,DeptName="IS",Capacity=100,Status=true},
        new Department{Id=4,DeptName="SW",Capacity=100,Status=true},
    };
    //rest of the methods
}
```

```csharp
//Program.cs
public class Program
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        //Add services to the container.
        builder.Services.AddControllersWithViews();

        //register the repository in the injector
        builder.Services.AddScoped<IStudentRepo, StudentRepo>();

        builder.Services.AddScoped<IDeptRepo, DeptRepo>();//when someone asks for IDeptRepo return object of DeptRepo

        //we can change DeptRepo to DeptMockRepo and it will work as it is using the interface and DeptMockRepo is implementing the interface
    }

}
```

> [!bug] on adding department while using the mock repository and `AddScoped`
>
> - the object will be added to the list but in the next request the list will be the same as the first request
> - as it will create new instance every time the request is created
> - so we has to use `AddSingleton` no need to make list `static`
> - or make the list `static` so it will be shared between all the instances no we can use `AddScoped` or `AddTransient`

---

> [!done] instead of creating object from `ITIContext` in the repository we can use `ITIContext` in the injector
>
> - so we can use the same `ITIContext` in all the repositories

```csharp
//DeptRepo.cs
public class DeptRepo : IDeptRepo
{
    ITIContext db;

    public DeptRepo(ITIContext _db)
    {
        db = _db;
    }

    //rest of the methods
}
```

```csharp
//Program.cs
public class Program
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        //Add services to the container.
        builder.Services.AddControllersWithViews();

        //register the repository in the injector
        builder.Services.AddScoped<IStudentRepo, StudentRepo>();

        builder.Services.AddScoped<IDeptRepo, DeptRepo>();//when someone asks for IDeptRepo return object of DeptRepo

        builder.Services.AddDbContext<ITIContext>();//register the ITIContext in the injector
        //AddDbContext: register the ITIContext in the injector
        //now we can use the same ITIContext in all the repositories
    }

}
```

> [!done] we can use multiple Repositories in the same controller

```csharp
//StudentController.cs
public class StudentController : Controller
{
    IStudentRepo studentRepo;
    IDeptRepo deptRepo;

    public StudentController(IStudentRepo _studentRepo, IDeptRepo _deptRepo)
    {
        studentRepo = _studentRepo;
        deptRepo = _deptRepo;
    }
}
```

> [!example] `ITIContext` send configuration to `ITIContext` in the injector


```csharp
//ITIContext.cs

//constructors


public ITIContext(){

}
public ITIContext(DbContextOptions<ITIContext> options) : base(options)
{
}
```

```csharp
//Program.cs
builder.Services.AddDbContext<ITIContext>(options => options.UseSqlServer("Server=.;Database=ITI;integrated security=true; trustServerCertificate=true;"));//but it is better to get it from the appsettings.json
```

```json
//appsettings.json
{
  "ConnectionStrings": {
    "ITI": "Server=.;Database=ITI;integrated security=true; trustServerCertificate=true;",
    "ITI2": "Server=.;Database=ITI2;integrated security=true; trustServerCertificate=true;"
  }
}
```

```csharp
//Program.cs
builder.Services.AddDbContext<ITIContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("ITI")),
serviceLifetime: ServiceLifetime.Scoped);//get the connection string from the appsettings.json

//default is scoped
//other options: Singleton, Transient
```

>[!error] 
> change in lifetime  cause error
 ```cs
builder.Services.AddDbContext<ITIContext>();
    builder.Services.AddSingleton<ICourseRepo, CourseRepo>(); 
```
 >[!done]
 >
 ```cs
       builder.Services.AddDbContext<ITIContext>();

      builder.Services.AddTransient<ICourseRepo, CourseRepo>();
```


---

# Lab

> [!done] #lab
>
> - all lecture
