#### Recap

[EF-Day1](EF-Day1.md)

---

> [!tip] outline
>
> 1. inheritance
> 1. config file
> 1. server side evaluation & client side evaluation
> 1. tracking and no tracking
> 1. stored procedure

> [!note] steps
>
> - create a new project
> - install both `Microsoft.EntityFrameworkCore.Tools` and `Microsoft.EntityFrameworkCore.SqlServer`
> - set nullable to `disable` in `csproj` file
> - create a new class `Person` with `Id`, `Age` and `Name` properties inside `Models` folder
> - create a new class `Employee` that inherits from `Person` class with `Salary` property
> - create a new class `Student` that inherits from `Person` class with `Grade` property
> - create a new class `ITIContext` that inherits from `DbContext` class

> [!warning] POCO class
>
> - Plain Old CLR Object
> - class without any data annotation

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

}
```

```csharp
public class Employee : Person
{
    public int Salary { get; set; }
}
```

```csharp
public class Student : Person
{
    public string Grade { get; set; }
}
```

```csharp
public class ITIContext : DbContext
{
    //create public dbset
    public DbSet<Person> People { get; set; }
    //override onConfiguring method
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        //it is better to use appsettings.json file and read the connection string from it
        optionsBuilder.UseSqlServer("Server=.;Database=ITI-EF-Day2;integrated security=true;trust server certificate=true");
        //to connect to visual studio server
        //optionsBuilder.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=ITI-EF-Day2;integrated security=true;trust server certificate=true");

        base.OnConfiguring(optionsBuilder);
    }
}
```

> [!done] `add-migration`
>
> - `add-migration` command is used to create a migration file
>   > [!warning] it has only people table despite the fact that we have 3 classes and 2 inherites from the person class


> [!done] `Remove-Migration` command
>
> - `Remove-Migration` command is used to remove the last migration file

> [!done] we add `DbSet` for each class

```csharp
   public DbSet<Employee> Employees { get; set; }
   public DbSet<Student> Students { get; set; }
```

> [!bug] we `add-migration`commands again
>
> - migration file is created table people but with `Discriminator` column
> - `update-database` command is used to create the database

```csharp
//migrations file
migrationBuilder.CreateTable(
    name: "People",
    columns: table => new
    {
        Id = table.Column<int>(type: "int", nullable: false)
            .Annotation("SqlServer:Identity", "1, 1"),
        Age = table.Column<int>(type: "int", nullable: false),
        Discriminator = table.Column<string>(type: "nvarchar(max)", nullable: false),//Discriminator column: used to differentiate between the classes
        Name = table.Column<string>(type: "nvarchar(max)", nullable: true),
        Salary = table.Column<int>(type: "int", nullable: true),
        Grade = table.Column<string>(type: "nvarchar(max)", nullable: true)
    },
    constraints: table =>
    {
        table.PrimaryKey("PK_People", x => x.Id);
    });
```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        Student s1 = new Student { Name = "Sharkawy", Age = 20, Grade = "A" };
        Employee e1 = new Employee { Name = "Ash", Age = 30, Salary = 10000 };
        Console.WriteLine(db.Entry(s1).State);//Detached
        Console.WriteLine(db.Entry(e1).State);//Detached

        db.Students.Add(s1);
        db.Employees.Add(e1);

        Console.WriteLine(db.Entry(s1).State);//Added
        Console.WriteLine(db.Entry(e1).State);//Added

        //this all the changes in the memory
        db.SaveChanges();//to save the changes in the database
    }

}
```

> [!warning] `null` problem
>
> - we have a problem with the `null` values in the `People` table
> - when adding a new `Employee` student data will be `null` and vice versa

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
       db.People.OfType<Person>().Count();//2
        db.People.OfType<Employee>().Count();//1
        db.People.OfType<Student>().Count();//1

        var students = db.People.OfType<Student>().ToList();
        foreach (var student in students)
        {
            Console.WriteLine(student.Name);
            //Sharkawy
        }
        var employees = db.People.OfType<Employee>().ToList();
        foreach (var employee in employees)
        {
            Console.WriteLine(employee.Name);
            //Ash
        }

        var students = db.Students.ToList();
        foreach (var student in students)
        {
            Console.WriteLine(student.Name);
            //Sharkawy
        }
    }

}
```

> [!example] `Discriminator`
>
> - `Discriminator`: is used to differentiate between the classes.

> [!example] `db.Database.EnsureDeleted();` vs `db.Database.EnsureCreated();`
>
> - `db.Database.EnsureDeleted();`: will delete the database
> - `db.Database.EnsureCreated();`: will create the database

> [!danger] instead of creating one table for all the classes.
>
> - we can create a table for each class
> - delete Migration Folder

> [!tip] `UseTpc` vs `UseTph` vs `UseTpt`
>
> - `UseTph`: Table Per Hierarchy (default) we have one table for all the classes
> - `UseTpc`: Table Per Concrete Type no table for the abstract class
> - `UseTpt`: Table Per Type (we have a table for each class)

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(p=>{
            //UseTpt: Table Per Type
            p.UseTptMappingStrategy();
    })
}
```

> [!tip] this will create 3 tables
>
> - `Person`: `Id`, `Name`, `Age`
> - `Employee`: `Id`: will be the primary key, and foreign key to `Person` table + `Salary`
> - `Student`: `Id`: will be the primary key, and foreign key to `Person` table + `Grade`

```csharp
//Migration file

migrationBuilder.CreateTable(
    name: "People",
    columns: table => new
    {
        Id = table.Column<int>(type: "int", nullable: false)
            .Annotation("SqlServer:Identity", "1, 1"),
        Name = table.Column<string>(type: "nvarchar(max)", nullable: true),
        Age = table.Column<int>(type: "int", nullable: false),
        Discriminator = table.Column<string>(type: "nvarchar(max)", nullable: false)
    },
    constraints: table =>
    {
        table.PrimaryKey("PK_People", x => x.Id);
    });


migrationBuilder.CreateTable(
    name: "Students",
    columns: table => new
    {
        Id = table.Column<int>(type: "int", nullable: false)
            .Annotation("SqlServer:Identity", "1, 1"),
        Grade = table.Column<string>(type: "nvarchar(max)", nullable: true)
    },
    constraints: table =>
    {
        table.PrimaryKey("PK_Students", x => x.Id);
    });
migrationBuilder.CreateTable(
    name: "Employees",
    columns: table => new
    {
        Id = table.Column<int>(type: "int", nullable: false)
            .Annotation("SqlServer:Identity", "1, 1"),
        Salary = table.Column<int>(type: "int", nullable: false)
    },
    constraints: table =>
    {
        table.PrimaryKey("PK_Employees", x => x.Id);
    });

```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        db.Database.EnsureDeleted();
        db.Database.EnsureCreated();
        Student s1 = new Student { Name = "Sharkawy", Age = 20, Grade = "A" };
        Employee e1 = new Employee { Name = "Ash", Age = 30, Salary = 10000 };
        db.Students.Add(s1);
        db.Employees.Add(e1);
        db.SaveChanges();

    }
}
```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
       Console.WriteLine(db.People.OfType<Person>().Count());//2
        Console.WriteLine(db.People.OfType<Employee>().Count());//1
        Console.WriteLine(db.People.OfType<Student>().Count());//1

        var students = db.People.OfType<Student>().ToList();
        foreach (var student in students)
        {
            Console.WriteLine(student.Name);
            //Sharkawy
        }
        var employees = db.People.OfType<Employee>().ToList();
        foreach (var employee in employees)
        {
            Console.WriteLine(employee.Name);
            //Ash
        }

        var students = db.Students.ToList();
        foreach (var student in students)
        {
            Console.WriteLine(student.Name);
            //Sharkawy
        }

    }
}
```

> [!warning] but this will affect the performance
>
> - inserting will insert in 2 tables
> - deleting will delete from 2 tables
>   > [!done] so we prefer nulls over performance issues

> [!example] making `Person` class `abstract`
>
> - `add-migration`
> - this will create a migration file with 3 tables
> - `Remove-Migration` to remove the last migration file
>   > [!done] `UseTpc` to create a table for only `Employee` and `Student` classes
>   >
>   > - `Employee`: `Id`, `Name`, `Age`, `Salary`
>   > - `Student`: `Id`, `Name`, `Age`, `Grade`

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(p=>{
            //UseTpc: Table Per Concrete Type
            p.UseTpcMappingStrategy();
    })
}
```

> [!warning] In `Main` method
>
> - same as before

---

> [!done] working with database created last lab [[EF-Day1]](EF-Day1.md)

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {

       var res =  db.Students.FirstOrDefault(s => s.Id == 1);

    }
}
```

> [!done] to fill database with data on build
>
> - `HasData` method is used to fill the database with data on build
> - `HasData` takes a list of objects or a single object

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Student>().HasData(new Student { Id = 1, Name = "Sharkawy", Age = 20, Grade = "A" });
    // list of students
    modelBuilder.Entity<Student>().HasData(new List<Student>
    {
        new Student { Id = 2, Name = "Super", Age = 20, Grade = "B" },
        new Student { Id = 3, Name = "Ash", Age = 20, Grade = "C" }
    });

}
```

> [!done] `add-migration` and `update-database` commands

> [!bug] `AsNoTracking` method
>
> - `AsNoTracking` method is used to stop tracking the object

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {

       var res =  db.Students.FirstOrDefault(s => s.Id == 1);
        Console.WriteLine(db.Entry(res).State);//Unchanged
       Console.WriteLine(res.Name);//Sharkawy
       res.Name = "Sharkawy2";
         Console.WriteLine(db.Entry(res).State);//Modified

       //to stop tracking this object
       //AsNoTracking

       var res2 =  db.Students.AsNoTracking().FirstOrDefault(s => s.Id == 1);
        Console.WriteLine(db.Entry(res2).State);//Detached
         res2.Name = "Sharkawy2";
        Console.WriteLine(db.Entry(res2).State);//

        db.SaveChanges();//will not update the database

    }
}
```

> [!bug] to stop tracking all the objects from the database
>
> - we configure ITIContext to stop tracking all the objects from Entity
> - `UseQueryTrackingBehavior` method is used to stop tracking all the objects from database
> - ```csharp
>   optionsBuilder.UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
>   ```

```csharp
//ITIContext.cs
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseSqlServer("Server=.;Database=ITI-EF-Day2;integrated security=true;trust server certificate=true");
    optionsBuilder.UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
    base.OnConfiguring(optionsBuilder);
}
```

> [!example] evaluating client side vs server side

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Students.Select(s => new { Name = s.Name + " " + s.Age });
        Console.WriteLine(res.ToQueryString());//SELECT [s].[Name] + N' ' + CAST([s].[Age] AS nvarchar(max)) AS [Name] FROM [Students] AS [s]

        //server side evaluation: Concatenation is done in the database
        foreach (var item in res)
        {
            Console.WriteLine(item);//{ Name = Sharkawy 26 }
        }

    }
}
```

```csharp
//MyFunc
public static string MyFunc(String name, int age)
{
    return name + " " + age;
}

```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        // MyFunc is not supported in the database
        var res = db.Students.Select(s => MyFunc(s.Name, s.Age));
        Console.WriteLine(res.ToQueryString());// SELECT [s].[Name] , [s].[Age] FROM [Students] AS [s]

        //client side evaluation: Concatenation is done in the client
        foreach (var item in res)
        {
            Console.WriteLine(item);//Sharkawy 26
        }
        // var res2 = db.Students.Select(s => MyFunc(s.Name, s.Age).contains("a"));//error

        //we have to get all data from the database and then apply the function
        // ToList
        var res2 = db.Students.ToList().Where(s => MyFunc(s.Name, s.Age).Contains("a"));
    }
}
```

> [!done] it is better to use ==server== side evaluation

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Students.FirstOrDefault(s => s.Id == 1);
        //this will get only the student data without department data or course data
        Console.WriteLine(res.Name);//Sharkawy
        Console.WriteLine(res.DeptId);//1
        Console.WriteLine(res.Department.DeptName);//Exception: System.NullReferenceException: 'Object reference not set to an instance of an object.'


    }
}
```

> [!example] `sql server profiler`
>
> ```sql
> SELECT TOP(1) [s].[Id], [s].[Age], [s].[DeptId], [s].[Grade], [s].[Name]
> FROM [Students] AS [s]
> WHERE [s].[Id] = 1
> ```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Students.Select(s => new { StdId = s.Id, Name = s.StdName, DeptName = s.Department.DeptName });
        .FirstOrDefault(s => s.Id == 1);
        Console.WriteLine(res.Name);//Sharkawy
        Console.WriteLine(res.DeptName);//PD
    }
}
```

> [!example] `sql server profiler`
>
> ```sql
> SELECT TOP(1) [s].[Id], [s].[Name], [d].[DeptName]
> FROM [Students] AS [s]
> INNER JOIN [Departments] AS [d] ON [s].[DeptId] = [d].[Id]
> WHERE [s].[Id] = 1
> ```

> [!done] Eagar loading
>
> - `Include` method is used to load the related data

```csharp
//main method
//we need to use EntityFrameworkCore to auto complete the Include method
using Microsoft.EntityFrameworkCore;
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Students.Include(s => s.Department).FirstOrDefault(s => s.Id == 1);
        //this will get department data and set it to navigation property of the student Department
        Console.WriteLine(res.Name);//Sharkawy
        Console.WriteLine(res.DeptId);//1
        Console.WriteLine(res.Department.DeptName);//PD
    }
}
```

> [!example] `sql server profiler`
>
> ```sql
> SELECT TOP(1) [s].[Id], [s].[Age], [s].[DeptId], [s].[Grade], [s].[Name], [d].[Id], [d].[DeptName]
> FROM [Students] AS [s]
> INNER JOIN [Departments] AS [d] ON [s].[DeptId] = [d].[Id]
> WHERE [s].[Id] = 1
> ```

> [!done] we can include multiple navigation properties

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Students
        .Include(s => s.Department)
        .Include(s => s.Courses)
        .FirstOrDefault(s => s.Id == 1);
        //this will get department data and set it to navigation property of the student Department
        Console.WriteLine(res.Name);//Sharkawy
        Console.WriteLine(res.DeptId);//1
        Console.WriteLine(res.Department.DeptName);//PD
        Console.WriteLine(res.Courses.Count);//2

        //to include some properties related to department we use ThenInclude
        var res2 = db.Students
        .Include(s => s.Department)
        .ThenInclude(d => d.Courses)//this will include courses related to the department
        // .ThenInclude(c => c.Students)//this will include students related to the course
        .Include(s => s.Courses)//this will include courses related to the student
        .FirstOrDefault(s => s.Id == 1);
    }
}
```

---

# Break

---

> [!done] `Reference` method
>
> - `Reference` method is used to load the related data

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Students.FirstOrDefault(s => s.Id == 1);
        //res.Department; //null
        //this will get department data and set it to navigation property of the student Department
        db.Entry<Student>(res).Reference(s => s.Department).Load();
        //res.Department; //{Demo.Department} department data
        Console.WriteLine(res.Name);//Sharkawy
        Console.WriteLine(res.DeptId);//1
        Console.WriteLine(res.Department.DeptName);//PD

        //if it was list => we use Collection
        var res2 = db.Departments.FirstOrDefault(d => d.Id == 1);//only department data
        Console.WriteLine(res2.DeptName);//PD

        //res2.Students.Count;//0 as it is not loaded we use Eagar loading

        var res2 = db.Departments.Include(d => d.Students).FirstOrDefault(d => d.Id == 1);//1 query to get department data and students data (inner join)
        //
        //or we can use Collection
        db.Entry<Department>(res2).Collection(d => d.Students).Load();//2 queries
        foreach (var student in res2.Students)
        {
            Console.WriteLine(student.Name);
        }



    }
}
```

> [!example] `sql server profiler`
>
> - `db.Entry<Student>(res).Reference(s => s.Department).Load();`
> - 2 requests to the database
>
> ```sql
> SELECT TOP(1) [s].[Id], [s].[Age], [s].[DeptId], [s].[Grade], [s].[Name]
> FROM [Students] AS [s]
> WHERE [s].[Id] = 1
> ```
>
> ```sql
> SELECT [d].[Id], [d].[DeptName]
> FROM [Departments] AS [d]
> WHERE [d].[Id] = 1
> ```

> [!example] Lazy loading
>
> - to load the related data when it is needed without using `Include` or `Reference` methods
> - to enable lazy loading we need to install `Microsoft.EntityFrameworkCore.Proxies` package
> - navigation properties ==MUST== be `virtual`

```csharp
//ITIContext.cs
//has to include
using Microsoft.EntityFrameworkCore.Proxies;


/// OnConfiguring
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseSqlServer("Server=.;Database=ITI-EF-Day2;integrated security=true;trust server certificate=true");
    optionsBuilder.UseLazyLoadingProxies();//to enable lazy loading
    base.OnConfiguring(optionsBuilder);
}
```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Departments.FirstOrDefault(d => d.Id == 1);
        Console.WriteLine(res.DeptName);//PD
        Console.WriteLine(res.Students.Count);//2
        foreach (var student in res.Students)
        {
            Console.WriteLine(student.Name);//Sharkawy  Super
        }
    }
}
```

> [!done] Eagar loading vs Lazy loading
>
> - in web applications we use Eagar loading to get all the data in one request
> - in desktop applications we use Lazy loading to get the data when it is needed

> [!example] `sql server profiler`
>
> - `var res = db.Departments.FirstOrDefault(d => d.Id == 1);`
> - 1 request to the database
>
> ```sql
> SELECT TOP(1) [d].[Id], [d].[DeptName]
> FROM [Departments] AS [d]
> WHERE [d].[Id] = 1
> ```
>
> - `foreach (var student in res.Students)`
> - 1 request to the database
>
> ```sql
> SELECT [s].[Id], [s].[Age], [s].[DeptId], [s].[Grade], [s].[Name]
> FROM [Students] AS [s]
> WHERE [s].[DeptId] = 1
> ```

> [!error] Navigation properties MUST be `virtual` with `UseLazyLoadingProxies`
>
> - if we have a navigation property without `virtual` keyword we will get an error
> - as proxy class will not be able to override the navigation property

> [!danger] configuration file
>
> - doing configuration for all entities in `OnModelCreating` method is not a good practice
> - better to create a configuration file for each entity
>   > [!done] `StudentConfiguration` class
>   >
>   > - inherit from `IEntityTypeConfiguration<Student>` interface
>   > - override `Configure` method

```csharp
//StudentConfiguration.cs
public class StudentConfiguration : IEntityTypeConfiguration<Student>
{
    public void Configure(EntityTypeBuilder<Student> builder)
    {
        builder.HasKey(s => s.Id);
        builder.Property(s => s.Id).ValueGeneratedNever();//to prevent auto increment
        builder.Property(s => s.Name).HasMaxLength(50).IsRequired();
        builder.Property(s => s.Age).IsRequired();
        builder.Property(s => s.Grade).HasMaxLength(1).IsRequired();

        //relationship
        builder.HasOne(s => s.Department)
        .WithMany(d => d.Students)
        .HasForeignKey(s => s.DeptId)
        .IsRequired();//default is true => used to prevent null values

    }
}
```

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.ApplyConfiguration(new StudentConfiguration());
}
```

> [!done] `DepartmentConfiguration` class
>
> - same as `StudentConfiguration` class

```csharp
//DepartmentConfiguration.cs
public class DepartmentConfiguration : IEntityTypeConfiguration<Department>
{
    public void Configure(EntityTypeBuilder<Department> builder)
    {
        builder.HasKey(d => d.DeptId);
        builder.Property(d => d.DeptName).HasMaxLength(50).IsRequired();

        //no need to configure the relationship as it is one to many relationship and it is configured in the StudentConfiguration
    }
}
```

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{//if order matters
    modelBuilder.ApplyConfiguration(new StudentConfiguration());
    modelBuilder.ApplyConfiguration(new DepartmentConfiguration());
}
```

> [!done] `ApplyingConfigurationFromAssembly` method
>
> - to apply all the configurations from the assembly

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{ // use this if order of config does not matter
    //we use any one of the following  (any class from the assembly ,only one is enough)
    modelBuilder.ApplyConfigurationsFromAssembly(typeof(StudentConfiguration).Assembly);//this will apply all the configurations from the assembly
    // modelBuilder.ApplyConfigurationsFromAssembly(typeof(Program).Assembly);
    // modelBuilder.ApplyConfigurationsFromAssembly(typeof(DepartmentConfiguration).Assembly);
}
```

```csharp
//main method
public static void Main(string[] args)
{
  Console.WriteLine(typeof(StudentConfiguration).Assembly.FullName);//ITI-EF-Day2, Version=
    Console.WriteLine(typeof(Program).Assembly.FullName);//ITI-EF-Day2, Version=
    Console.WriteLine(typeof(DepartmentConfiguration).Assembly.FullName);//ITI-EF-Day2, Version=

    //all the configurations are in the same assembly
}
```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        //ew have to use $  or string.Format
        var res = db.Students.FromSql($"select * from students");//to execute raw sql query
        foreach (var student in res)
        {
            Console.WriteLine(student.Name);//Sharkawy Super Ash
        }
        //we can use  where clause
        var res2 = db.Students.FromSql($"select * from students where age > 20").ToList();
        // we can use parameters
        int age = 20;
        var res3 = db.Students.FromSql($"select * from students where age > {age}").ToList();
        Console.WriteLine(res3.ToQueryString());//SELECT * FROM students WHERE age > 20
        foreach (var student in res3)
        {
            Console.WriteLine(student.Name);//Super Ash
        }

        //we can't specify the return columns
        // var res2 = db.Students.FromSql($"select Name, Age from students").ToList();Error
        //as Student class has more than 2 properties
        //we  can use  db.Database.SqlQuery<StudentView>
        //we can create StudentView class with Name and Age properties
    }
}
```

```csharp
//StudentView.cs
public class StudentView
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res2 = db.Database.SqlQuery<StudentView>($"select Name, Age from students").ToList();
        foreach (var student in res2)
        {
            Console.WriteLine(student.Name);//Sharkawy Super Ash
        }
    }
}
```

> [!done] Stored Procedure
>
> - `FromSql` method is used to execute stored procedure
> - `GetStudents` stored procedure
>
> - to get all the students

```sql
CREATE PROCEDURE GetStudents
AS
BEGIN
    SELECT * FROM Students
END
```

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Students.FromSql("exec GetStudents").ToList();
        foreach (var student in res)
        {
            Console.WriteLine(student.Name);//Sharkawy Super Ash
        }

        //if GetStudents returns part of the columns
        //we use db.Database.SqlQuery<StudentView>
        var res2 = db.Database.SqlQuery<StudentView>("exec GetStudents").ToList();
    }
}
```

> [!example] creating a stored procedure
>
> - `add-migration` to create a migration file
> - in migration file

```csharp
//migration file
protected override void Up(MigrationBuilder migrationBuilder)
{
    String sp = @"CREATE PROCEDURE GetStudents
    AS
    BEGIN
        SELECT * FROM Students
    END";
    migrationBuilder.Sql(sp);//this will execute the command in sp variable and create the stored procedure
}

//we can use down method to drop the stored procedure
protected override void Down(MigrationBuilder migrationBuilder)
{
    String sp = @"DROP PROCEDURE GetStudents";
    migrationBuilder.Sql(sp);//this will execute the command in sp variable and drop the stored procedure
}
```

> [!done] `update-database` to update the database and create the stored procedure

> [!example] we can use `db.Database.SqlQuery` when all the columns are returned from the stored procedure
>
> - and instead of using view `StudentView` we can use `Student` class
> - `db.Database.SqlQuery<Student>("exec GetStudents").ToList();`

```csharp
//main method
public static void Main(string[] args)
{
    using (ITIContext db = new ITIContext())
    {
        var res = db.Database.SqlQuery<Student>("exec GetStudents").ToList();
        foreach (var student in res)
        {
            Console.WriteLine(student.Name);//Sharkawy Super Ash
        }
    }
}
```

---

# Break

---

### Web Application

> [!example] Web application vs Desktop application vs Console application vs Class library
>
> - class library: is a project that contains classes and interfaces that can be used in other projects can't run by itself but can be used by other projects
> - console application: is a project that can run by itself (`Main` method)
> - Desktop application: is a project that can run by itself has a user interface (`Main` method) event driven programming (users has to update the application to get the latest version) (responsive)
>   > [!tip] Web application
>   >
>   > - Web application: has a user interface, event driven programming, different from desktop application as it is hosted on a server and accessed by a client through a web browser (users don't have to update the application to get the latest version)
>   > - not as responsive as desktop application
>   > - on clicking => round trip to the server => get the response
>   > - uses http protocol to communicate between the client and the server
>   > - client sends a request to the server using http protocol
>   > - server has to have web server to host the web application like Apache,...etc
>   > - cross platform => Kestrel web server
>   > - web server: manages the requests and responses to and from the client and the web application
>   > - all of the client and server and application has to be connected to Network and has IP address
>   > - server has to be static IP address
>   > - client can be dynamic IP address(using DHCP)
>   > - Server uses the domain name (instead of using the IP address) `https://www.google.com`
>   > - client uses DNS to resolve the domain name to IP address
>   > - server can be used to host multiple web applications
>   > - every web application has to have a unique port number `https://www.google.com:443`, `https://www.google.com:80`
>   > - we don't have to use the port number in the url as by default http uses port 80 and https uses port 443
>   > - we specify what to do using the url `https://www.facebook.com/login` => login page
>   > - on logging in => it will send a request has the username and password to the server => server will check the username and password => if it is correct => it will send the response to the client => if it is not correct => it will send the response to the client
>   > - we use `#` to specify the section of the page `https://www.facebook.com/login#section1`
>   > - we use `?` to specify the parameters `https://www.facebook.com/login?username=ash&password=123`

> [!example]
>
> - request from client
> - `Content-Length: 35` => the length of the request body
> - `Accept: image/gif, image/jpeg, */*` => the type of the response that the client can accept
> - `Accept-Encoding: gzip, deflate` => the type of the response encoding that the client can accept
> - `Accept-Language: en-US` => the language that the client can accept
> - `Host: www.google.com` => the domain name
> - `User-Agent: Mozilla/4.0` => the browser that the client is using

![](Pasted%20image%2020240309134137.png)

> [!example]
>
> - response from server
> - `HTTP/1.1 200 OK` => the status code of the response: 200 => OK, 404 => Not Found, 500 => Internal Server Error
>   ![](Pasted%20image%2020240309134856.png)
> - `Server: Apache/2.2.14 (Win32)` => the server that the web server is using
> - `Last-Modified: Mon, 22 Feb 2010 13:26:52 GMT` => the last time the page was modified
> - `Connection: close` => the connection between the client and the server will be closed after the response
> - `Content-Type: text/html` => the type of the response : tells the client how to interpret the response
>   ![](Pasted%20image%2020240309134603.png)

> [!warning] HTTP protocol
>
> - URL is case insensitive => `https://www.google.com` is the same as `https://www.GOOGLE.com`
> - stateless protocol: every request is independent from the previous request
> - to maintain the state we use `cookies` and `sessions`

> [!bug] `Netwrok` in chrome dev tools
>
> - when we request `https://www.google.com` => 45 requests
> - 1 request to get the html page
> - 1 request to get the images, css, js, ...

> [!done] the response from the server has to be HTML, CSS, JS, images, ... to be displayed in the browser

> [!warning] making web page dynamic to change the content of the page depending on data
>
> - 1 HTML page for all products for example (only change the data)
>   > [!done] ASP => Active Server Pages
>   >
>   > - build HTML page with data from the server and send it to the client
>   > - ==ASP.NET Core MVC==
>   > - other approaches: to build the View in the client side using JS or JS frameworks like Angular, React, Vue.js
>   > - and get the data from the server using API (REST, GraphQL, ...) as JSON for example

---

> [!warning] Before .NET (2002)
>
> - creating web applications :
> - ASP => 1 page has the HTML and the server side code (VBScript)
> - ASP.NET Web Forms => code behind (C# or VB.NET) and the HTML page separate => OOP can be used - drag and drop controls (server side controls) - transfered to HTML (rendering)
>   > [!error] ASP.NET Web Forms
>   >
>   > - no unit testing
>   > - maintainability is hard
>   > - extensibility is hard
>
> > [!done] ASP.NET MVC
> >
> > - using the MVC design pattern => Model, View, Controller
> > - Model: the data
> > - View: the UI
> > - Controller: the logic (the middle) => get the data from the model and send it to the view
> >   ![](Pasted%20image%2020240309142016.png)

> [!tip] Ruby on Rails
>
> - using the MVC design pattern creating twitter (2003)
> - php laravel uses the MVC design pattern as well
> - Java Spring
> - Microsoft used the MVC design pattern in ASP.NET MVC (2009) => (.NET Framework) => Windows only
> - ==ASP.NET Core MVC (2016)== => (.NET Core) => cross platform

> [!warning] .NET Framework vs .NET Core
>
> - .NET Framework: Windows only, has all the features of .NET
> - .NET Core: cross platform, install the features that we need

# ASP.NET Core MVC

> [!example] `Kestrel` web server
>
> - cross platform web server
> - can be used alone or with another web server
> - to use features in other web server
>   ![](Pasted%20image%2020240309142016.png)
> - Controller: receives the request `/students` or `/students/1`
> - Model: has the data(Entities,validations,...), Controller gets the data from the model and send it to the view
> - View: has the UI (HTML, CSS, JS, Razor, ...)
> - Razor: is a view engine that is used to build the view in ASP.NET Core MVC (C# and HTML)
> - Controller recieves the request then send to the model to get the data then send it to the view to build the UI then send it to the client as a response
> - controllers: are classes like `StudentsController` that manage the requests and responses that inherit from `Controller` class from `Microsoft.AspNetCore.Mvc` namespace

---
### #lab-EF-Day2
> - try eager loading, lazy loading, ...
> - has to be done before tommorow's lab or will fail in EF
