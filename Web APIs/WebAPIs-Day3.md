## Web APIs Best Practices

![](Pasted%20image%2020240423091426.png)

> [!tip] Repository
>
> - we need to separate the logic of the application from the queries to the database using the repository pattern.
> - instead of accessing the database directly using the entity framework (dbcontext) in the controller, we can create a repository class that will handle all the database operations.
> - generic repository
> - generic repository with unit of work

> [!tip] create a repository for each entity (student, Department, ...)

```csharp
// StudentRepository.cs
public class StudentRepository
{
    ITIContext db;
    public StudentRepository(ITIContext db)
    {
        this.db = db;
    }
    //we need to make the methods for all the operations we need to do on the student entity
    public List<Student> SelectAll()
    {
        return db.Students.ToList();
    }
    public Student SelectById(int id)
    {
        return db.Students.Find(id);
    }
    public void Add(Student student)
    {
        db.Students.Add(student);
        db.SaveChanges();
    }
    public void Edit(Student student)
    {
        db.Entry(student).State = EntityState.Modified;//this will mark the student object as modified
        db.SaveChanges();
    }
    public void Remove(int id)
    {
        Student student = db.Students.Find(id);
        db.Students.Remove(student);
        db.SaveChanges();
    }
}
```

> [!example] now any query we need we will do it in the repository class and we can use it the controller
>
> - replace all queries in the controller with the repository methods
>   > [!done] these patterns make it easier to test the application and make it more maintainable
>   >
>   > - specialy in big applications

```csharp
//StudentController.cs
[Route("api/[controller]")]
[ApiController]
public class StudentController : ControllerBase
{
    StudentRepository studentRepository;
    public StudentController(StudentRepository studentRepository)
    {
        this.studentRepository = studentRepository;
    }
[HttpGet]
public List<Student> Get()
{
    return studentRepository.SelectAll();
}
[HttpGet("{id}")]
public Student Get(int id)
{
    return studentRepository.SelectById(id);
}
[HttpPost]
public void Post( Student student)
{
    studentRepository.Add(student);
}

[HttpPut]
public void Put(Student student)
{
    studentRepository.Edit(student);
}
[HttpDelete("{id}")]
public void Delete(int id)
{
    studentRepository.Remove(id);
}

}
```

> [!warning] now the controller is dependent on the repository class
>
> - we need to inject the repository class in the controller
> - we can use the dependency injection in the Program class to inject the repository class in the controller

```csharp
//Program.cs
builder.Services.AddScoped<StudentRepository>();
```

> [!example] scoped vs transient vs singleton
>
> - scoped: the object will be created once per request
> - transient: the object will be created every time it is requested (request + subrequest)
> - singleton: the object will be created once and will be used for all the requests

> [!warning] what if we want to add without `db.SaveChanges()` in the controller so we can add multiple objects and save them all at once
>
> - connected: (add,update,delete) the object and save it directly
> - disconnected: (add,update,delete) the object and save it later
> - we need to separate the (add,update,delete) logic from the save logic

```csharp
//StudentRepository.cs
public void Add(Student student)
{
    db.Students.Add(student);
}
public void Save()
{
    db.SaveChanges();
}
```

> [!done] now we can add multiple objects and save them all at once in the controller

```csharp
//StudentController.cs
[HttpPost]
public void Post( Student student)
{
    // studentRepository.Add(student);
    // studentRepository.Add(student);
    studentRepository.Add(student);
    studentRepository.Save();//this will save all the objects at once
    return Ok();
}
```

---

> [!example] Dependency Injection
>
> - instead of creating the repository object in the controller we can use the dependency injection to inject the repository object in the controller
> - ```csharp
>     //StudentController.cs
>     StudentRepository studentRepository;
>     public StudentController(StudentRepository studentRepository)
>     {
>         this.studentRepository = studentRepository;
>      //this.studentRepository = new StudentRepository();///XXXXXXX
>     }
>   ```
>
>   > [!warning]
>   >
>   > - problem with testing the controller as it is dependent on the repository object
>   > - problem with maintaining the code as the controller is dependent on the repository object any change in the repository object will affect the controller
>
>   ```
>
>   ```

> [!tip] Dependency Inverson
>
> - using more generic interfaces instead of the concrete classes
> - so the controller will be dependent on the interface not the concrete class
> - now it will be easier to user other student repository classes that implement the `IStudentRepository` interface

```csharp
//IStudentRepository.cs
public interface IStudentRepository
{
    List<Student> SelectAll();
    Student SelectById(int id);
    void Add(Student student);
    void Edit(Student student);
    void Remove(int id);
}
```

```csharp
//StudentRepository.cs
public class StudentRepository : IStudentRepository
{
    ITIContext db;
    public StudentRepository(ITIContext db)
    {
        this.db = db;
    }
    //rest of the code

}
```

> [!tip] add the object of the repository class in the Program class

```csharp
//Program.cs
builder.Services.AddScoped<IStudentRepository, StudentRepository>();//this will inject the StudentRepository object in the controller when it needs an object of type IStudentRepository
```

> [!done] now to change the repository class we change it in the `Program.cs` not in the controller
>
> - make student repository with static list instead of the database

```csharp
//StudentListRepository.cs
public class StudentListRepository : IStudentRepository
{
   static List<Student> students = new List<Student>(){
        new Student{Id=1,Name="Ahmed",Age=20},
        new Student{Id=2,Name="Ali",Age=22},
        new Student{Id=3,Name="Mohamed",Age=21},
    };
    public void Add(Student student)
    {
        students.Add(student);
    }
    public void Edit(Student student)
    {
        Student s = students.Find(s => s.Id == student.Id);
        s.Name = student.Name;
        s.Age = student.Age;
    }
    public void Remove(int id)
    {
        Student s = students.Find(s => s.Id == id);
        students.Remove(s);
    }
    public List<Student> SelectAll()
    {
        return students;
    }
    public Student SelectById(int id)
    {
        return students.Find(s => s.Id == id);
    }
}
```

> [!error] but it is still using the student repository with the database
>
> > [!done] we need to change the repository object in the `Program.cs` to use the `StudentListRepository` instead of the `StudentRepository`
> >
> > - the controller won't be affected by this change

```csharp
//Program.cs
builder.Services.AddScoped<IStudentRepository, StudentListRepository>();//this will inject the StudentListRepository object in the controller when it needs an object of type IStudentRepository
```

> [!example] Interface is better used than abstract class
>
> - as it is the highest level of abstraction
> - we can implement multiple interfaces but we can't inherit from multiple abstract classes
> - it offers more flexibility than abstract classes

---

## Generic Repository

> [!tip] Generic Repository
>
> - for each entity/table we need to create a repository class
>   > [!faq] what if we have many entities/tables
>
> > [!done] we need to create a generic repository class that will handle all the entities/tables

```csharp
//GenericRepository.cs
public class GenericRepository<TEntity> where TEntity : class
//this will make the class generic and restrict the TEntity to be a class
{
    ITIContext db;
    public GenericRepository(ITIContext db)
    {
        this.db = db;
    }
    public List<TEntity> SelectAll()
    {
        //return db.Students.ToList();//students is dbset of student
        // this is the same as
        // db.Set<Student>().ToList();//this will get the dbset of the student entity
        return db.Set<TEntity>().ToList();//to get the dbset of the TEntity(student,department,..)
    }
    public TEntity SelectById(int id)
    {
        return db.Set<TEntity>().Find(id);
    }
    public void Add(TEntity entity)
    {
        db.Set<TEntity>().Add(entity);
    }
    public void Edit(TEntity entity)
    {
        db.Entry(entity).State = EntityState.Modified;
    }
    public void Remove(int id)
    {
        TEntity entity = db.Set<TEntity>().Find(id);
        db.Set<TEntity>().Remove(entity);
    }
    public void Save()
    {
        db.SaveChanges();
    }
}
```

>[!done] now we can use the generic repository in the controller

```csharp
//StudentController.cs
[Route("api/[controller]")]
[ApiController]
public class StudentController : ControllerBase
{
    GenericRepository<Student> studentRepository;
    public StudentController(GenericRepository<Student> studentRepository)
    {
        this.studentRepository = studentRepository;
    }
    //rest of the code

}
```

```csharp
//DepartmentController.cs
[Route("api/[controller]")]
[ApiController]
public class DepartmentController : ControllerBase
{
    GenericRepository<Department> departmentRepository;
    public DepartmentController(GenericRepository<Department> departmentRepository)
    {
        this.departmentRepository = departmentRepository;
    }
    //rest of the code

}
```

>[!done] now we need to register the generic repository in the `Program.cs`

```csharp
//Program.cs
builder.Services.AddScoped<GenericRepository<Student>>();//this will inject the GenericRepository object of type Student in the controller when it needs an object of type GenericRepository of type Student
builder.Services.AddScoped<GenericRepository<Department>>();//this will inject the GenericRepository object of type Department in the controller when it needs an object of type GenericRepository of type Department

```

>[!tip] we can create a new repository to extend the generic repository
> - or create create a new repository with different methods
> - and the controller can be dependent on multiple repositories for the same entity
