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
>
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
>[!example] `sql server profiler`
>
>```sql
>SELECT TOP(1) [s].[Id], [s].[Age], [s].[DeptId], [s].[Grade], [s].[Name]
>FROM [Students] AS [s]
>WHERE [s].[Id] = 1
>```

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
>[!example] `sql server profiler`
>
>```sql
>SELECT TOP(1) [s].[Id], [s].[Name], [d].[DeptName]
>FROM [Students] AS [s]
>INNER JOIN [Departments] AS [d] ON [s].[DeptId] = [d].[Id]
>WHERE [s].[Id] = 1
>```

>[!done] Eagar loading
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

>[!example] `sql server profiler`
>
>```sql
>SELECT TOP(1) [s].[Id], [s].[Age], [s].[DeptId], [s].[Grade], [s].[Name], [d].[Id], [d].[DeptName]
>FROM [Students] AS [s]
>INNER JOIN [Departments] AS [d] ON [s].[DeptId] = [d].[Id]
>WHERE [s].[Id] = 1
>```

>[!done] we can include multiple navigation properties
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