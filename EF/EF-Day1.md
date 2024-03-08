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
> - all of them depend on `Microsoft.EntityFrameworkCore` package.

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

> [!tip]

---

# Break

---

## Code First

> [!done] steps
>
> - disable `nullable` reference types from project properties.
>
> ```xml
> <Nullable>disable</Nullable>
> ```
>
> - create a new project.
> - install `Microsoft.EntityFrameworkCore.SqlServer` package. from `NuGet`
> - install `Microsoft.EntityFrameworkCore.Tools` package.
> - create models and Data folders
> - create `ITIContext.cs` file in Data folder.
> - create `Student.cs` and `Department.cs` files in Models folder.

> [!example] `sql server object explorer` in view menu in visual studio.
>
> - used to view the database and its objects.
> - we don't have to download `sql server ` to use it as visual studio has its own sql server.
> - we can get the connection string from the properties of the database.
> - `Data Source` is the server name.
> - `Initial Catalogue` is the database name.

```csharp
//Department.cs
namespace DemoCodeFirst.Models
{
    public class Department
    {
        //using annotations to specify the primary key. if we didn't use conventions.
        [Key] //used to specify the primary key.
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]//used to specify the identity.
        [DatabaseGenerated(DatabaseGeneratedOption.None)]//used to specify that the value is not generated by the database.
        //computed => the value is generated by the database based on the
        public int Id { get; set; }

        [Required]//used to specify that the column is required.
        [StringLength(50)]//used to specify the length of the column instead of using the max length.
        public string DeptName { get; set; }

        // public int Capacity { get; set; }//required by default.
        public int? Capacity { get; set; }//not required.

        public int Status { get; set; }
    }
}
```

```csharp

```

```csharp
//ITIContext.cs
namespace DemoCodeFirst.Data
{
    public class ITIContext : DbContext
    {
        public virtual DbSet<Department> Departments { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            if (!optionsBuilder.IsConfigured)
            {
                optionsBuilder.UseSqlServer("Server=.;Database=ITI;integrated security=true;trustservercertificate=true;");
                //optionsBuilder: used to build the options for the context.
                //UseSqlServer: used to specify the database provider.
            }
        }

        //fluent api
        //onModelCreating: used to specify the model for the context.
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            //used to create the table.
            modelBuilder.Entity<Department>(entity =>
            {
                entity.HasKey(e => e.Id);
                entity.Property(e => e.DeptName).IsRequired().HasMaxLength(50).HasColumnName("DeptName");
                entity.Property(e => e.Capacity).HasColumnName("Capacity");
                entity.Property(e => e.Status).HasColumnName("Status");
            });

            mode

        }


    }
}
```

> [!done] `add-migration`
>
> - any migration has a name.
> - `add-migration` is used to create a new migration.
> - ```cmd
>     add-migration addDepartmentTable
>   ```
>   > [!error] we has to specify primary key for the table and other properties.
>
> > [!done] conventions
> >
> > - we use conventions to specify the primary key and other properties.
> > - `Id` or `ClassNameId` is the primary key. this convention will tell EF that this is the primary key.
> > - now migration will be created and will create file automatically. `20210913120000_addDepartmentTable.cs`

```csharp
//20210913120000_addDepartmentTable.cs
public partial class addDepartmentTable : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(//used to create a new table.
            name: "Departments",//from the DbSet name.
            columns: table => new
            {
                Id = table.Column<int>(type: "int", nullable: false)//Id is the primary key.
                    .Annotation("SqlServer:Identity", "1, 1"),//used to specify the identity.
                    DeptName = table.Column<string>(type: "nvarchar(max)", nullable: true)//DeptName is a column.
            },
        );
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(//used to drop the table.
            name: "Departments");
    }
}

```

> [!example] for this code to be reflected in the database, we have to run `update-database` command.
>
> - ```cmd
>   update-database
>   ```
> - this will create the table in the database.
> - will create a new row in `__EFMigrationsHistory` table.

> [!warning] if we changed the model, we have to create a new migration.
>
> - ```cmd
>   add-migration addCapacityColumn
>   ```
> - this will create a new migration file.
> - but this will not update the database.
> - to remove the last migration, we can use `remove-migration`.
>
> ```cmd
> remove-migration
> ```
>
> - this will remove the last migration.
>   > [!done] to update the database, we have to run `add-migration` and `update-database` commands.
>   >
>   > - `add-migration` is used to create a new migration.
>   > - `update-database` is used to update the database.

```csharp
//main
class Program
{
    static void Main(string[] args)
    {
        ITIContext db = new ITIContext();
        //db is disposable object so we need to dispose it to close the connection to the database.
        db.Dispose();//this will close the connection to the database.

        // or we can use using statement.
        using (ITIContext db = new ITIContext())
        {
            //code here.
        }//will call dispose method automatically after the block.

        using (ITIContext db = new ITIContext())
        {
            var depts = db.Departments.ToList();
            foreach (var dept in depts)
            {
                Console.WriteLine($"{dept.Id} {dept.DeptName} {dept.Capacity}" );
            }
        }
    }
}
```

> [!danger] ways to create tables in the database from code.
>
> - conventions: easy to use, but we can't specify the column names and other properties.
> - annotations: higher priority than conventions. we can specify the column names and other properties.
> - fluent api: higher priority than conventions and annotations, we can do anything with the table.

```csharp
//Student.cs
namespace DemoCodeFirst.Models
{
    [Table("Students")]//to specify the table name in the database.
    public class Student
    {
        [Key]
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int StdId { get; set; }//to use convention we have to use Id or StudentId.

        [Column("StdName")]//to specify the column name in the database.
        [StringLength(50)]//to specify the length of the column.
        [Required]//to specify that the column is required.
        public string Name { get; set; }//nvarchar(max) by default.

        public int Age { get; set; }
        public int DeptNo { get; set; }
        public Department DeptNoNavigation { get; set; }
    }
}
```

> [!error] Fluent API

```csharp
//ITIContext.cs

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Department>(entity =>
    {
        entity.HasKey(e => e.Id);
        entity.Property(e => e.DeptName).IsRequired().HasMaxLength(50).HasColumnName("DeptName");
        entity.Property(e => e.Capacity).HasColumnName("Capacity");
        entity.Property(e => e.Status).HasColumnName("Status");
    });

    modelBuilder.Entity<Student>(entity =>
    {
        entity.HasKey(e => e.StdId);//this will be identity by default.
        //ValueGeneratedNever: used to specify that the value is not generated by the database.
        entity.Property(e => e.StdId).ValueGeneratedNever();//this will not be identity.
        entity.Property(e => e.Name).IsRequired().HasMaxLength(50).HasColumnName("StdName");//stdName is  required and has a length of 50.
        entity.Property(e => e.Age).IsRequired(false);//age is not required.

        entity.Property(e => e.DeptNo).HasColumnName("DeptNo");
        entity.HasOne(d => d.DeptNoNavigation)
            .WithMany(p => p.Students)
            .HasForeignKey(d => d.DeptNo)
            .HasConstraintName("FK__Students__Department");
    });
}
```

> [!warning] `add-migration`
>
> - will create a new migration file but ==empty==
> - we have to add `DbSet` for `Student` in `ITIContext` class.

```csharp
public virtual DbSet<Student> Students { get; set; }
```

> [!done] `add-migration` and `update-database`
>
> - then run `add-migration` command again.
> - then run `update-database` command.
> - this will create the table in the database.
>   > [!done] sql server profiler
>   >
>   > - this will show the sql queries that are executed by the application.
>   > - will create Student table in the database.
>   >   ```sql
>   >   CREATE TABLE [Students] (
>   >   [StdId] int NOT NULL,
>   >   [Age] int NOT NULL,
>   >   [StdName] nvarchar(50) NOT NULL,
>   >   )
>   >   ```

---
# Break
---
