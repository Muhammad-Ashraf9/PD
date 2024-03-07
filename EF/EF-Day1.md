# Entity Framework Core

> [!tip] Entity Framework Core (EF Core)
>
> - ORM (Object-Relational Mapping) framework.
> - EF Core is a modern object-database mapper for `.NET`.
> - creates entities from tables, and vice versa. (Database First, Code First)
> - it supports LINQ queries.(linq to entities) => sql queries.
> - it supports change tracking, and it can track changes in entities.
> - `Microsoft.EntityFrameworkCore.SqlServer` is the package for SQL Server.
>   there are other packages for other databases.(==nosql==, mysql, sqlite, etc.)

> [!warning] code first vs database first
>
> - Code First: create entities and then create database from entities.
> - Database First: create database and then create entities from database.

> [!done] steps
>
> - create a new project.
> - same database (`TestADO`) student and department tables.
> - install `Microsoft.EntityFrameworkCore.SqlServer` package. from `NuGet`
> - install `Microsoft.EntityFrameworkCore.Tools` package. :used for migrations.

> [!example] `DbContext` class
>
> - used to connect to the database. (open session)
> - `DbSet` : used to query and save instances of entities.
> - object of `DbSet` is a collection that has entities of a specific type.

> [!tip] using cli commands
>
> - ```cmd
>   Scaffold-DbContext "Server=.;Database=TestADO;integrated security=true;trustservercertificate=true;" Microsoft.EntityFrameworkCore.SqlServer
>   ```
> - `Scaffold-DbContext` : used to create `DbContext` and entities from the database.
> - `DeptNoNavigation` : used to reference the department entity from the student entity.
> - properties that are not nullable in the database are not nullable in the entity. and vice versa.
>
>   > [!bug] to work properly, the code shouldn't have any errors.
>
>   > [!done] `Scaffold-DbContext` options
>   >
>   > - `-context` : used to specify the name of the `DbContext` class.
>   > - `-contextdir` : used to specify the directory of the `DbContext` class.
>   > - `-outputdir` : used to specify the directory of the entities.
>   > - `-force` : used to overwrite the existing files.
>   >
>   > ```cmd
>   > Scaffold-DbContext "Server=.;Database=TestADO;integrated security=true;trustservercertificate=true;" Microsoft.EntityFrameworkCore.SqlServer -context AppContext -contextdir data -outputdir Models
>   > ```
>
> > [!danger] writing this commands is error-prone.
>
> > [!done] Download `Microsoft.EntityFrameworkCore.PowerTools` then install
> >
> > - easy to use. (right-click on the project, then select `Entity Framework` => `Reverse Engineer`)

````csharp

> [!example] `TestADOContext` class
>
> - `DbContext` class.

```csharp
public class TestADOContext : DbContext
{
    public TestADOContext()
    {
    }

    public TestADOContext(DbContextOptions<TestADOContext> options)
        : base(options)
    {
    }

    //DbSet for each table.
    public virtual DbSet<Department> Departments { get; set; }
    public virtual DbSet<Student> Students { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            optionsBuilder.UseSqlServer("Server=.;Database=TestADO;integrated security=true;trustservercertificate=true;");
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Department>(entity =>
        {
            entity.HasKey(e => e.DeptNo);
            entity.Property(e => e.DeptNo)
                .HasMaxLength(4)
                .IsUnicode(false);

            entity.Property(e => e.DeptName)
                .HasMaxLength(50)
                .IsUnicode(false);
        });

        modelBuilder.Entity<Student>(entity =>
        {
            entity.HasKey(e => e.StudentNo);

            entity.Property(e => e.StudentNo)
                .HasMaxLength(4)
                .IsUnicode(false);

            entity.Property(e => e.DeptNo)
                .HasMaxLength(4)
                .IsUnicode(false);

            entity.Property(e => e.StudentName)
                .HasMaxLength(50)
                .IsUnicode(false);

            entity.HasOne(d => d.DeptNoNavigation)
                .WithMany(p => p.Students)
                .HasForeignKey(d => d.DeptNo)
                .HasConstraintName("FK__Students__Department");
        });

        OnModelCreatingPartial(modelBuilder);
    }

    partial void OnModelCreatingPartial(ModelBuilder modelBuilder);
}
````

```csharp
//main
class Program
{
    static void Main(string[] args)
    {
        TestADOContext db = new TestADOContext();
        var depts = db.Departments.ToList();//immediate execution.
        var depts = db.Departments;//deferred execution.
        //sql query is executed when we iterate through the collection.
        foreach (var dept in depts)
        {

            Console.WriteLine(dept);//we can override ToString() method in Department entity.

            Console.WriteLine(dept.DeptNo + " " + dept.DeptName);//DeptNo and DeptName are properties of Department entity.
        }

        Console.WriteLine(depts.ToQueryString());//SELECT [d].[DeptNo], [d].[DeptName] FROM [Departments] AS [d]

        var depts = db.Departments.Where(d => d.Capacity > 100).ToList();
        Console.WriteLine(depts.ToQueryString());//SELECT [d].[DeptNo], [d].[DeptName] FROM [Departments] AS [d] WHERE [d].[Capacity] > 100


        //projectionj
        var depts = db.Departments.Where(d => d.Capacity > 100).Select(d => new { d.DeptNo, d.DeptName }).ToList();
        Console.WriteLine(depts.ToQueryString());//SELECT [d].[DeptNo], [d].[DeptName] FROM [Departments] AS [d] WHERE [d].[Capacity] > 100

        int x = 100;
        var depts = db.Departments.Where(d => d.Capacity > x).Select(d => new { d.DeptNo, d.DeptName }).ToList();
        Console.WriteLine(depts.ToQueryString());//SELECT [d].[DeptNo], [d].[DeptName] FROM [Departments] AS [d] WHERE [d].[Capacity] > @__x_0
        //parameterized query.

        var dept = db.Departments.Single(d => d.DeptNo == 50);

        //single object
        Console.WriteLine(dept);// {DeptNo = 50, DeptName = "IT", Capacity = 100, Status  = 1}

    }
}
```

> [!example] sql server profiler
>
> - `Single`
> - ```csharp
>         var dept = db.Departments.Single(d => d.DeptNo == 50);
>   ```
> - ```sql
>   SELECT TOP(2) [d].[DeptNo], [d].[DeptName], [d].[Capacity], [d].[Status]
>   FROM [Departments] AS [d]
>   WHERE [d].[DeptNo] = 50
>   ```
> - `TOP(2)` : used to get the first row. (if there are more than one row with the same `DeptNo` value, it will throw an exception.)
> - `First`
>
> ```sql
>   SELECT TOP(1) [d].[DeptNo], [d].[DeptName], [d].[Capacity], [d].[Status]
>   FROM [Departments] AS [d]
>   WHERE [d].[DeptNo] = 50
> ```

```csharp
//main
class Program
{
    static void Main(string[] args)
    {
        TestADOContext db = new TestADOContext();
        var dept = db.Departments.FirstOrDefualt(d => d.DeptNo == 50);
        dept.DeptName = "IT";//this will update the department name in the memory.

        db.SaveChanges();//this will update the department name in the database.

        db.Departments.Add(new Department { DeptNo = 60, DeptName = "HR", Capacity = 100, Status = 1 });//this will add a new department in memory.

        db.SaveChanges();//this will save the changes in the database.

        //EF replaced ADO.NET
        //all mapping functionality is done by EF.
        //with similar performance.

        var dept = db.Departments.FirstOrDefualt(d => d.DeptNo == 50);

        //check status before updating.
        Console.WriteLine(db.Entry<Department>(dept).State);//to get the state of the entity. (Unchanged)
        Console.WriteLine(db.Entry<Department>(dept).OriginalValues["DeptName"]);//to get the original value of the property.

        dept.DeptName = "IT";
        Console.WriteLine(db.Entry<Department>(dept).State);//(Modified)

        db.SaveChanges();//this will update the department name in the database.

        //add a new department.
        Department dept = new Department(){DeptNo = 60, DeptName = "HR", Capacity = 100, Status = 1};
        db.Departments.Add(dept);
        Console.WriteLine(db.Entry<Department>(dept).State);//(Added)
        //but still not saved in the database.

        db.SaveChanges();//this will save the changes in the database.


        //delete a department.
        //first get the department from the database.
        var dept = db.Departments.FirstOrDefualt(d => d.DeptNo == 60);//this returns object
        Console.WriteLine(db.Entry<Department>(dept).State);//(Unchanged)
        db.Departments.Remove(dept);
        Console.WriteLine(db.Entry<Department>(dept).State);//(Deleted)
        db.SaveChanges();//this will delete the department from the database.



    }
}
```

> [!danger] `SaveChanges` method
>
> - `SaveChanges` method is used to save the changes in the database.
> - it will save all the changes in the database.

> [!warning] if we changed in the generated code, like override `ToString` method, and then run `Scaffold-DbContext` again, it will overwrite the changes.
>
> > [!done] to avoid this, we can use `partial` keyword.
> >
> > - `partial` keyword is used to split the class into multiple files.
> > - we can create a new file and then use `partial` keyword to add new methods or properties.
> > - then, the generated code will not overwrite the new file. only the generated file.
>
> > [!error] but changes to columns names will change properties names in the entity.
> >
> > - then we have to change the properties names in the new file.

>[!tip]
