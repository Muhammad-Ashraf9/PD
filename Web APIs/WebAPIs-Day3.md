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
    public StudentController()
    {
        this.studentRepository = new StudentRepository();
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

> [!done] now we can use the generic repository in the controller

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

> [!done] now we need to register the generic repository in the `Program.cs`

```csharp
//Program.cs
builder.Services.AddScoped<GenericRepository<Student>>();//this will inject the GenericRepository object of type Student in the controller when it needs an object of type GenericRepository of type Student
builder.Services.AddScoped<GenericRepository<Department>>();//this will inject the GenericRepository object of type Department in the controller when it needs an object of type GenericRepository of type Department

```

> [!tip] we can create a new repository to extend the generic repository
>
> - or create create a new repository with different methods
> - and the controller can be dependent on multiple repositories for the same entity

---

# Break

---

> [!warning] onion pattern = repository pattern + dependency injection

```csharp
//StudentDepartmentController.cs
[Route("api/[controller]")]
[ApiController]
public class StudentDepartmentController : ControllerBase
{
    GenericRepository<Student> studentRepository;
    GenericRepository<Department> departmentRepository;
    public StudentDepartmentController(GenericRepository<Student> studentRepository, GenericRepository<Department> departmentRepository)
    {
        this.studentRepository = studentRepository;
        this.departmentRepository = departmentRepository;
    }

    [HttpPost]
    public ActionResult add(Student student)
    {
        departmentRepository.Add(student.Dept);
        studentRepository.Add(student);
        //will this work?
        //do they share the same dbcontext? => no they don't (every repository has its own dbcontext)
        // so we need to save the changes for each repository using multiple transactions and we have to consider the order of the transactions
        departmentRepository.Add(student.Dept);
        departmentRepository.Save();
        studentRepository.Add(student);
        studentRepository.Save();
        //this is the problem of having multiple repositories  in the controller
        //solution: use unit of work pattern
        return Ok();
    }
}
```

> [!tip] Unit of Work Pattern
>
> - adding extra layer between the controller and the repositories
> - this ensures that all the repositories share the same dbcontext
> - this is done by creating a new class that will handle all the repositories and the dbcontext (contains objects of all the repositories )
> - and we can save all the changes at once as any change in any repository will be saved in the same transaction
>   > [!warning] not recommended to make the dbcontext singleton

![](Pasted%20image%2020240423122622.png)

```csharp
//UnitOfWork.cs

public class UnitOfWork
{
    ITIContext db;
    GenericRepository<Student> studentRepository;
    GenericRepository<Department> departmentRepository;
    public UnitOfWork(ITIContext db)
    {
        this.db = db;
        studentRepository = new GenericRepository<Student>(db);
        departmentRepository = new GenericRepository<Department>(db);
    }

}
```

> [!tip] now we can use the unit of work in the controller instead of the repositories

```csharp
//StudentDepartmentController.cs
[Route("api/[controller]")]
[ApiController]
public class StudentDepartmentController : ControllerBase
{
    UnitOfWork unitOfWork;
    public StudentDepartmentController(UnitOfWork unitOfWork)
    {
        this.unitOfWork = unitOfWork;
    }

public ActionResult add(Student student)
{
    unitOfWork.departmentRepository.Add(student.Dept);
    unitOfWork.studentRepository.Add(student);
    //do they share the same dbcontext? => yes they do
    unitOfWork.Save();//now we can save all the changes at once
    return Ok();
}
}
```

> [!warning] we can use it in the student controller and the department controller as well

```csharp
//StudentController.cs
[Route("api/[controller]")]
[ApiController]
public class StudentController : ControllerBase
{
    UnitOfWork unitOfWork;
    public StudentController(UnitOfWork unitOfWork)
    {
        this.unitOfWork = unitOfWork;
    }
    //rest of the code
}
```

> [!warning] what of we have many repositories
>
> - why use the unit of work in student controller and create object of all the repositories in the unit of work
> - we just need the student repository in the student controller
> - how to solve this problem
> - we remove object creation from the unit of work constructor and make a property for each repository in the unit of work
> - make getter for each repository and only create the object when it is needed and if it is not null return it

```csharp
//UnitOfWork.cs
public class UnitOfWork
{
    ITIContext db;
    //private fields for the repositories
    GenericRepository<Student> studentRepository;
    GenericRepository<Department> departmentRepository;
    public UnitOfWork(ITIContext db)
    {
        this.db = db;
    }
    //public properties for the repositories (Getter only)
    //can't set the value of the repository from outside the class
    public GenericRepository<Student> StudentRepository
    {
        get
        {
            if (studentRepository == null)
                studentRepository = new GenericRepository<Student>(db);
            return studentRepository;
        }
    }
    public GenericRepository<Department> DepartmentRepository
    {
        get
        {
            if (departmentRepository == null)
                departmentRepository = new GenericRepository<Department>(db);
            return departmentRepository;
        }
    }

}

```

> [!done] remove registration of the repositories in the `Program.cs` and register the unit of work instead

```csharp
//Program.cs
builder.Services.AddScoped<UnitOfWork>();//this will inject the UnitOfWork object in the controller when it needs an object of type UnitOfWork
```

> [!done] unit of work pattern
>
> - we have only dependency in the controller => the unit of work
> - all repositories share the same dbcontext
> - we can have multiple unit of works and make interface for the unit of work (IUnitOfWork) and use it in the controller to change the unit of work easily
> - we dont use the dependency injection with the repositories as it will have the same problem as before not having the same dbcontext
> - using dependency injection is an option we don't have to use it all the time

> [!tip] we can have multiple constructors in the controller one using dependency injection and the other one using the unit of work object directly

```csharp
//StudentController.cs
[Route("api/[controller]")]
[ApiController]
public class StudentController : ControllerBase
{
    UnitOfWork unitOfWork;
    public StudentController(UnitOfWork unitOfWork)
    {
        this.unitOfWork = unitOfWork;
    }
    public StudentController()
    {
        unitOfWork = new UnitOfWork(new ITIContext());
    }
    //rest of the code
}
```

> [!example] Dependency Injection Types
>
> - constructor injection
> - property injection
> - method injection
> - ==Search==

---

# Break

---

## Authentication and Authorization

> [!tip] MVC vs Web API
>
> - Identity, ...
> - in MVC: using cookies or sessions to authenticate the user
> - those are not used with web APIs as they require alot of workarounds to make them work
> - as there will be different clients
> - it is better to respond with a token that the client will send with every request and client will be responsible for storing the token

> [!tip] JWT (JSON Web Token)
>
> - used to authenticate the user in web APIs
> - when user logs in the server will generate a token and send it to the client
> - the client will send the token with every request
> - this token consists of 3 parts: header, payload, signature
> - header: type of the token and the hashing algorithm - using secret key to generate the token - the server will use the same secret key to validate the token
>
>   > [!warning] hashing vs encryption
>   >
>   > - hashing: one way function (can't be decrypted) - same input will have the same output - that's how we can compare them
>   > - encryption: two way function (can be decrypted)
>
> - payload: contains claims (Key value pairs) + expiration date + issuer + audience + ...
> - signature: is HASH value computed using the header, payload, and is passed to algorithm with the secret key
> - the client is free to store the token however it wants (local storage, session storage, cookie, ...) depending on the type of the client (web, mobile, desktop, ...)
> - but the client should send the token with every request to validate the user
> - the server doesn't store the token it just validates it
> - secret key is only known and saved by the server
> - to create a valid token we need to know 3 things: secret key, hashing algorithm, and the payload
> - this is called stateless authentication as the server doesn't store the token (http is stateless)

![](Pasted%20image%2020240423124239.png)
![](Pasted%20image%2020240423124243.png)
![](Pasted%20image%2020240423124248.png)

---

> [!tip] create `AccountController` to handle the login and register requests

```csharp
//userdataDTO.cs

public record userdata(string username, string password);
//this like a class but it is immutable (can't be changed)
//with properties that can be accessed directly
```

> [!example] JWT
>
> - install the `Microsoft.AspNetCore.Authentication.JwtBearer` package
> - install the `System.IdentityModel.Tokens.Jwt` package

```csharp
//AccountController.cs
[Route("api/[controller]")]
[ApiController]
public class AccountController : ControllerBase
{
    [HttpPost]
    public ActionResult Login(userdata user)
    {
        //check the username and password
        if (user.username == "admin" && user.password == "123")
        {
            //generate the token

            //header => type + algorithm
            //payload => claims + expiration date
            //signature => hash secret key + ...

            //define claims
            List<Claim> userData = new List<Claim>();
            userData.Add(new Claim("name", user.username));//we can put any data we want
            //we can use ClaimTypes
            userData.Add(new Claim(ClaimTypes.MobilePhone, "01000000000"));//it will add schema

            //signing credentials used to sign the token (secret key + hashing algorithm)
            //secret key should't be string
            string secretKey = "Nodejs JWT is better and easier than this";//we need strong secret key that is more than 256  as we are using HmacSha256 unless it will throw an exception

            // var key = new SecretKey());//we have shouldn't use SecretKey directly
            // we should use SymmetricSecurityKey which
            var key = new SymmetricSecurityKey(Encoding.ASCII.GetBytes(secretKey));//to send the secret key as bytes

            var signCred = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);
            //we can choose any hashing algorithm but we need to use the same algorithm to validate the token

            var token = new JwtSecurityToken(
                claims:userData,
                expires:DateTime.Now.AddDays(1),
                signingCredentials: signCred
            );
            var tokenString = new JwtSecurityTokenHandler().WriteToken(token);//to convert the token to string
            //we need the secret key to validate the token
            return Ok(tokenString);

        }
        else
        {
            return Unauthorized();
        }
    }

}
```

```json
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

```

> [!tip] generated token
>
> - it has 3 parts
> - header: `{"alg":"HS256","typ":"JWT"}`
> - payload: `{"name":"admin","mobilephone":"01000000000","exp":1620000000}` + Schema
> - can not validate the token without the secret key

> [!done] `[Authorize]` attribute to restrict access to the action

```csharp
//StudentController.cs
[Route("api/[controller]")]
[ApiController]

public class StudentController : ControllerBase
{
    [Authorize]
    [HttpGet]
    public List<Student> Get()
    {
        return studentRepository.SelectAll();
    }
}
```

> [!tip] define the authentication scheme in the `Program.cs`

```csharp
//Program.cs

//they have to be the same schema
builder.Services.AddAuthentication(options =>
options.DefaultAuthenticateScheme = "myschema")
.AddJwtBearer("myschema", options =>
{
    //we have to use the same secret key and hashing algorithm used to generate the token
    string secret = "Nodejs JWT is better and easier than this";
    var key = new SymmetricSecurityKey(Encoding.ASCII.GetBytes(secret));
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //those are used if we are using server for the authentication
        IssuerSigningKey = key,
        ValidateIssuer = false,
        ValidateAudience = false
    };
});


```

> [!done] we send the token in the header of the request
>
> - not the body of the request as there are some requests that don't have a body
> - we can use the `Authorization` header to send the token
> - `Bearer` is the type of the token
> - javascript client:
> - `Authorization: Bearer token` there is a space between the type and the token
> - c# client: `client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);`
> - we can validate in the action or we can validate in the middleware (outside the action)
>   ![](Pasted%20image%2020240423133520.png)

---

### Lab

> [!warning] Lab
>
> - part1 : apply the generic repository pattern + unit of work
> - part2 : config jwt => UI - web "login" and use the token to access the action
