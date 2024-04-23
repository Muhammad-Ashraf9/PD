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
>   > - deserialization is more difficult as you are converting from loosely typed to strictly typed you have to add data like data types...

> [!example] `[JsonIgnore]` attribute
>
> - used to ignore property during serialization
> - not recommended to ignore related data like department of student
> - used to ignore sensitive data like password
>   > [!tip] `JsonIgnoreCondition`
>   >
>   > - we can use `JsonIgnoreCondition` to ignore property based on condition
>   > - `JsonIgnoreCondition.WhenWritingDefault`=> ignore property if it has default value or Null.
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
builder.Services.AddDbContext<ITIContext>(op=>op.UseLazyLoadingProxies().UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

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
[Route("api/[controller]")]
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

> [!tip] using DTO
>
> - we can create multiple DTOs for the same model each with different data
> - no need to use `JsonIgnore` with DTO unless we want to ignore some data when it is null or default

> [!example] Pagination
>
> - used when we have a lot of data
> - to get data based on page number and page size
> - having multiple actions one for getting all data and one for getting paginated data

![](Pasted%20image%2020240422102653.png)

> [!example] Attributes
>
> - `[Consumes]`=> specify the content type that the action can accept
> - `[Produces]`=> specify the content type that the action can produce
> - `[HttpGet]`=> specify the action to be called when the request is GET

> [!warning] `[Consumes]`
>
> - without `[Consumes]`=> the action can accept any content type (json/xml, text, bson,..)
> - with `[Consumes]`=> the action can accept only the specified content type
> - this can for the controller or the action or the whole application
> - client can specify the accept header in the request to specify the content type that it can accept
> - but with `[Produces]`=> the action can produce only the specified content type

![](Pasted%20image%2020240422103731.png)

```csharp
//StudentsController.cs

[HttpPost]
// [Consumes("application/json")]
//now the action can accept only json content type

[Cosumes("text/xml")]
//now the action can accept only xml content type


[Produces("application/json")]
//now the action can produce only json content type

//this appears in the swagger documentation
public ActionResult add(Student student)
{
    db.Students.Add(student);
    db.SaveChanges();
    return CreatedAtAction(nameof(getbyid), new { id = student.Id }, student);
}
```

![](Pasted%20image%2020240422103809.png)

> [!tip] multiple routes
>
> - we can have multiple routes for the same action
> - we can specify the route in the action and in the controller

![](Pasted%20image%2020240422103826.png)

> [!done]

![](Pasted%20image%2020240422104248.png)

> [!warning] Scaffolding Controllers
>
> - we can add controllers using scaffolding specifying the model and the context with all the actions

---

# Break

---

> [!tip] creating windwo form application to consume web api
>
> - api is hosted on iiS express (visual studio) it has to be running
> - create new windows form application
> - `Data Grid View` to display data
> - `HTTP Client` class used to
> - we need to install web api client package to use ReadAsAsync `Microsoft.AspNet.WebApi.Client`

```csharp
//Form1.cs
//form load
private void Form1_Load(object sender, EventArgs e)
{
    HttpClient client = new HttpClient();//create object of http client
    HttpResponseMessage response = client.GetAsync("https://localhost:7280/api/students").Result;//get data from the api
    // if (response.StatusCode == System.Net.HttpStatusCode.OK || response.StatusCode == System.Net.HttpStatusCode.NoContent)
    //to check if the response is ok or no content

    //or we can use response.IsSuccessStatusCode to check if the response is ok
    if (response.IsSuccessStatusCode)
    {
        //Deserialization
        List<StudentData> students = response.Content.ReadAsAsync<List<StudentData>>().Result;//deserialize the data (array of students) to list of student data

        //add data to the data grid view
        dataGridView1.DataSource = students;


    }

}
```

> [!warning] Deserialization
>
> - we need to deserialize the data to turn it to c# object
> - deserialization problem => is adding info to json to be strong type
> - we need a schema to use when deserializing
> - we have to use the same properties and property names in the schema as in the json
> - we can use help documentations (swagger) to get the schema
> - install deserialization package: 

```csharp
//StudentData.cs
public class StudentData
{
    public int id { get; set; }
    public string name { get; set; }
    public string deptName { get; set; }
}
```

> [!example] post data to the api
>
> - `PostAsJsonAsync` to post data to the api
> - `PostAsJsonAsync("url",data)`=> post data to the api
> - `client.PostAsJsonAsync("https://localhost:7280/api/students/{id}", student).Result;`=> post student object to the api
> - we can send id in student object or in the url

> [!tip] delete data from the api
>
> - `DeleteAsync` to delete data from the api

---

> [!tip] consuming web api using javascript
>
> - using `jQuery` to consume web api

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <!-- 
        add jquery library to use ajax
     -->
    <script
      src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.7.1/jquery.js"
      integrity="sha512-+k1pnlgt4F1H8L7t3z95o3/KO+o78INEcXTbnoJQ/F2VqDVhWoaiVml/OEHv9HsVgxUaVW+IbiZPUJQfF/YxZw=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    ></script>
    <!-- 
        use ajax to get data from the api
     -->
    <script>
      $(function () {
        $.ajax({
          url: "https://localhost:7280/api/students",
          type: "GET",
          success: function (data) {
            console.log(data);
          },
          error: function (error) {
            console.log(error);
          },
        });
      });
    </script>
  </head>
  <body>
    <h1>Students</h1>
  </body>
</html>
```

> [!error] ==CORS== (Cross-Origin Resource Sharing)
>
> - error happens due to accessing resources from different origins (different domains)
> - this is client side problem (ajax)
> - browser blocks the request if the origin of the request is different from the origin of the resource
> - this is used for security reasons to prevent accessing resources from different origins/domains unless it is allowed by this origin/domain
> - to allow the request we have to enable CORS in the server side
> - we can enable CORS for all the requests or for specific requests (methods, origins, headers,..)
> - browser sends a preflight request (OPTIONS) to check if the request is allowed
> - the server responds with the allowed methods, origins, headers,..
> - depending on the response the browser will allow or block the request
> - [MDN-CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#what_requests_use_cors)

```csharp
//startup.cs
string txt = "";
builder.Services.AddCors(options =>
{
    options.AddPolicy(txt,
    //this will add cors policy with the configuration in txt
        builder =>
        {
            builder
            // .AllowAnyOrigin()//to allow requests from any origin
            .WithOrigins("http://localhost:8080")//to allow requests from this origin
            .AllowAnyHeader()
            .AllowAnyMethod();
        });
});



//add middleware with the configuration in text
app.UseCors(txt);
```

> [!done] now we can access the api from the client side and get the data

> [!tip] using `ajax` to post data to the api

```html
<script>
  $(function () {
    $.ajax({
      url: "https://localhost:7280/api/students",
      type: "POST",
      contentType: "application/json",
      data: JSON.stringify({
        name: "Ahmed",
        deptId: 1,
      }),
      success: function (data) {
        console.log(data);
      },
      error: function (error) {
        console.log(error);
      },
    });
  });
</script>
```

> [!done] jQuery Revision on Github
>
> - Like, Share, Comment, Follow, subscribe
> - [Github - PD Files](https://github.com/Muhammad-Ashraf9/PD)

---

# Documentation on ASP.NET Core Web API

> [!tip] Swagger
>
> - used to document the web api
> - OpenAPI specification: is a language-agnostic specification for describing REST APIs
> - `NSwag` , `Swashbuckle` are used to generate swagger documentation
> - we can install any of them
> - we can edit swagger documentation

![](Pasted%20image%2020240422124650.png)

```csharp
//startup.cs
// builder.Services.AddSwaggerGen();
//we can add options to the swagger documentation
builder.Services.AddSwaggerGen(options =>
options.SwaggerDoc("v1", new OpenApiInfo()
{
    Version = "v2",//version of the documentation
    Title = "Web API Ash",//the title of the documentation
    Description = "This is a web api for students and departments ",//description of the documentation
    Contact = new OpenApiContact()
    {
        Name = "Ash",
        Email = "muhammad.ashraf.tahaa@gmail.com",
    },
    TermsOfService = new Uri("https://www.google.com"),
}));
```

> [!tip] we can add xml comments to the code to be added to the swagger documentation
>
> - to generate xml documentation file
> - from project properties => build => output => check the xml documentation file
> - specify the path of the xml file or just name of the file if it is in the same directory
> - we have to make swagger to use the xml file

![](Pasted%20image%2020240422124151.png)

```csharp

//StudentsController.cs

/// <summary>
/// get student by id
/// </summary>
/// <param name="id">id of the student</param>
/// <returns>student object</returns>
/// <remarks>
/// request example:
/// GET /api/students/1
/// </remarks>
[HttpGet("{id}")]
public ActionResult getbyid(int id)
{
    //code
}
```

```csharp
//startup.cs
builder.Services.AddSwaggerGen(options =>
{
    //to use the xml file
    options.SwaggerDoc("v1", new OpenApiInfo()
    {
        Version = "v2",
        Title = "Web API Ash",
        Description = "This is a web api for students and departments ",
        Contact = new OpenApiContact()
        {
            Name = "Ash",
            Email = "
        },
    }
    
    options.IncludeXmlComments("WebAPIs.xml");
    //it is better to make url cros platform by using Path.Combine
    });
});
```

> [!example] `[ProducesResponseType]`
>
> - we all possible responses for the action

```csharp
//StudentsController.cs
[HttpGet("{id}")]
[ProducesResponseType<List<Student>>(200)]
[ProducesResponseType(404, Type = typeof(void))]//not found
public ActionResult getbyid(int id)
{
    //code
}
```

> [!tip] Swachbuckle Annotations
>
> - install package: `Swashbuckle.AspNetCore.Annotations`
> - add `options.EnableAnnotations()` to the swagger documentation

![](Pasted%20image%2020240422124043.png)

```csharp
//startup.cs
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo()
    {
        Version = "v2",
        Title = "Web API Ash",
        Description = "This is a web api for students and departments ",
        Contact = new OpenApiContact()
        {
            Name = "Ash",
            Email = "
        },
        }
    options.IncludeXmlComments("WebAPIs.xml");
    options.EnableAnnotations();
    });
});
```

```csharp
//StudentsController.cs
[HttpGet("{id}")]
[SwaggerOperation(Summary = "get student by id", Description = "get student by id")]
//to add summary and description to the action
[SwaggerResponse(200, "student object", typeof(Student))]
// to add response type to the action

```

![](Pasted%20image%2020240422124012.png)

> [!warning] Securing Swagger
>
> - instead of using `app.UseSwagger();` use `app.MapSwagger().RequireAuthorization();`
> - we can add authorization to the swagger documentation

```csharp
//startup.cs
// app.MapSwagger().RequireAuthorization();
//to make it for admin only
app.MapSwagger().RequireAuthorization(options =>
{
    options.RequireRole("Admin");
});
```





---
## Lab

![](Pasted%20image%2020240422124534.png)

![](Pasted%20image%2020240422124538.png)
