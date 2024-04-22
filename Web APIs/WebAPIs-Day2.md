# WebAPIs Day2

> [!tip] create new web api project
>
> - template: `ASP.NET Core Web API`
> - default configuration
> - install package: `Microsoft.EntityFrameworkCore.SqlServer`, tools: `Microsoft.EntityFrameworkCore.Tools`
> - database first=> reverse engineering using `EF Core power tools`
> - move connection string to `appsettings.json`
> - in `program.cs` add dbcontext and connection string

```csharp
//program.cs
        builder.Services.AddDbContext<webapiContext>(
            options => options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"))
            );
```

> [!error] change `<InvariantGlobalization>` to false in `webapi.csproj` project file

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <InvariantGlobalization>false</InvariantGlobalization>
  </PropertyGroup>
```

> [!tip] add controller `StudentsController`
>
> - choose `API Controller` with CRUD actions

---

## Serilaization and Deserialization

> [!tip] serialization
>
> - converting object to json/xml (common format) between client and server (c# server to java client-UI)
> - this happens before sending data to client
> - not all clients understand c# objects that's why we use common format like json/xml
>   > [!warning] Deserialization
>   >
>   > - some clients cannot deal with json/xml directly like Java so it needs to be converted to object
>   > - deserialization is converting json/xml to object of client language ( java client-UI)

> [!example] `[JsonIgnore]` attribute
>
> - used to ignore property during serialization
> - not recommended to ignore related data like department of student
> - used to ignore sensitive data like password
>   > [!tip] `JsonIgnoreCondition`
>   >
>   > - we can use `JsonIgnoreCondition` to ignore property based on condition
>   > - `JsonIgnoreCondition.WhenWritingDefault`=> ignore property if it has default value
>   > - `JsonIgnoreCondition.WhenWritingNull`=> ignore property if it is null
>   > - if not specified=> ignore property ==always==

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    [JsonIgnore]//this property will be ignored during serialization
    public string Password { get; set; }
    [JsonIgnore]//not recommended to ignore related data like department of student
    public string DeptId { get; set; }
    [ForeignKey("DeptId")]
    public virtual Department Dept { get; set; }


    [JsonIgnore(Condition = JsonIgnoreCondition.WhenWritingDefault)]
    //ignore property if it has default value
    public string Address { get; set; }

    [JsonIgnore(Condition = JsonIgnoreCondition.WhenWritingNull)]
    //ignore property if it is null
    public string Email { get; set; }


}
```

> [!tip] loading related data
>
> - lazy loading: related data is loaded when it is accessed
> - eager loading:
> - explicit loading

> [!example] lazy loading
>
> - install package: `Microsoft.EntityFrameworkCore.Proxies`
> - in `webapiContext` class add `UseLazyLoadingProxies()`

```csharp
//webapiContext.cs
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            if (!optionsBuilder.IsConfigured)
            {
                optionsBuilder.UseLazyLoadingProxies().UseSqlServer("name=DefaultConnection");
            }
        }
```

![](Pasted%20image%2020240422094006.png)

> [!error] a possible object cycle was detected
>
> - infinite loop when serilaizer tries to convert student object to json
> - serialization is easier than deserialization as maping from strong type to weak type (C# to json) is easier than weak type to strong type (json to C#)
> - when serilaizer tries to convert student object to json it will get department object using lazy loading as it is being accessed => department object will try to get student objects in the list of students using lazy loading=> infinite loop
>   > [!done] solution
>   >
>   > - when circular reference is detected=> ignore the reference => and get only the accessed data until now
>   > - other just continue with the serialization

> [!tip]
>
> - install package: `Microsoft.AspNetCore.Mvc.NewtonsoftJson`
> - in `startup.cs` add `AddNewtonsoftJson()`
> - when trying to get studentById=> will get 3 levels of data
> - student + department + student
> - we can specify the depth of data to be loaded ( levels of data to be loaded)
> - `options.SerializerSettings.MaxDepth = 2;` => will load 2 levels of data
> - this not recommended to use more levels as the more levels of data the more time it will take to load data and it is more complex for the serilaizer to convert data to json
> - and the client may not need all the data
> - and it is overhead on the network to send more data
> - this approach is not used but DTO (Data Transfer Object) is used

```csharp
//startup.cs
builder.Services.AddControllers().AddNewtonsoftJson(options =>{
options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore;//this will ignore circular reference => get only the accessed data until now
//default is error
});

```

> [!done] DTO (Data Transfer Object)
>
> - It is like a view model in MVC
> - specifying only the data that is needed by the client to be sent (not the model with all the data and related data) and serialized
> - `return Ok(student);`=> serialize student object with all the data and related data
> - better to use DTO


```csharp
//StudentsDTO.cs
public class StudentsDTO
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string DeptName { get; set; }
}
```

```csharp
//StudentsController.cs
public ActionResult GetById(int id){
    var student =  db.Students.Find(id);
    if(student==null){
        return NotFound();
    }
    else{
        var studentDTO = new StudentsDTO(){
            Id = student.Id,
            Name = student.Name,
            DeptName = student.Dept.Name
        };
        return Ok(studentDTO);
        //this will serialize studentDTO object only with the data that is needed by the client
    }
}
```

![](Pasted%20image%2020240422100251.png)

```csharp
//StudentsController.cs
public ActionResult getall()
{
    List<Student> students = db.Students.ToList();
    List<StudentDTO> studentsDTO = new List<StudentDTO>();
    foreach (var student in students)
    {
        StudentDTO studentDTO = new StudentDTO()
        {
            Id = student.Id,
            Name = student.Name,
            DeptName = student.Dept.Name
        };
        studentsDTO.Add(studentDTO);
    }
    return Ok(studentsDTO);
    //this will serialize studentsDTO list only with the data that is needed by the client
}
```

> [!done] StudentDTO is editable
>
> - can add or remove properties as needed

```csharp
//DepartmentDTO.cs
public class DepartmentDTO
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public List<string> StudentsNames { get; set; } = new List<string>();
}
```

```csharp
//DepartmentsController.cs
[Rout("api/[controller]")]
[ApiController]
public class DepartmentsController : ControllerBase
{
    private readonly webapiContext db;
    public DepartmentsController(webapiContext db)
    {
        this.db = db;
    }

    [HttpGet]
    public ActionResult getall()
    {
        List<Department> depts = db.Departments.ToList();
        List<DepartmentDTO> deptsDTO = new List<DepartmentDTO>();
        foreach (var dept in depts)
        {
            DepartmentDTO deptDTO = new DepartmentDTO()
            {
                Id = dept.Id,
                Name = dept.Name,
                Location = dept.Location,
                StudentsNames = dept.Students.Select(s => s.Name).ToList()
            };
            deptsDTO.Add(deptDTO);
        }
        return Ok(deptsDTO);
    }

    [HttpGet("{id}")]
    public ActionResult getbyid(int id)
    {
        Department dept = db.Departments.Find(id);
        if (dept == null)
        {
            return NotFound();
        }
        else
        {
            DepartmentDTO deptDTO = new DepartmentDTO()
            {
                Id = dept.Id,
                Name = dept.Name,
                Location = dept.Location,
                 StudentsNames = dept.Students.Select(s => s.Name).ToList()
               
            };
            //or

            // foreach (var student in dept.Students)
            // {
            //     deptDTO.StudentsNames.Add(student.Name);
            // }

            return Ok(deptDTO);
        }
    }   
}
```

>[!tip] using DTO
> - we can create multiple DTOs for the same model each with different data
> - no need to use `JsonIgnore` with DTO unless we want to ignore some data when it is null or default



>[!example] Pagination
>
> - used when we have a lot of data
> - to get data based on page number and page size
> - having multiple actions one for getting all data and one for getting paginated data

![](Pasted%20image%2020240422102653.png)

>[!example] Attributes
> - `[Consumes]`=> specify the content type that the action can accept
> - `[Produces]`=> specify the content type that the action can produce
> - `[HttpGet]`=> specify the action to be called when the request is GET

>[!warning] `[Consumes]` 
> - without `[Consumes]`=> the action can accept any content type (json/xml, text, bson,..)
> - with `[Consumes]`=> the action can accept only the specified content type
> -  this can for the controller or the action or the whole application
> - client can specify the accept header in the request to specify the content type that it can accept
> - but with `[Produces]`=> the action can produce only the specified content type

![](Pasted%20image%2020240422103731.png)
```csharp
//StudentsController.cs

[HttpPost]
// [Consumes("application/json")]//now the action can accept only json content type
[Cosumes("text/xml")]//now the action can accept only xml content type


[Produces("application/json")]//now the action can produce only json content type

//this appears in the swagger documentation
public ActionResult add(Student student)
{
    db.Students.Add(student);
    db.SaveChanges();
    return CreatedAtAction(nameof(getbyid), new { id = student.Id }, student);
}
```


![](Pasted%20image%2020240422103809.png)

>[!tip] multiple routes
> - we can have multiple routes for the same action
> - we can specify the route in the action and in the controller


![](Pasted%20image%2020240422103826.png)

>[!done] 

![](Pasted%20image%2020240422104248.png)

>[!warning] Scaffolding Controllers 
> - we can add controllers using scaffolding specifying the model and the context with all the actions