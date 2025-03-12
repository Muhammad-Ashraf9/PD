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
                .HasMaxLength(4);


            entity.Property(e => e.DeptName)
                .HasMaxLength(50);
        });

        modelBuilder.Entity<Student>(entity =>
        {
            entity.HasKey(e => e.StudentNo);

            entity.Property(e => e.StudentNo)
                .HasMaxLength(4)


            entity.Property(e => e.DeptNo)
                .HasMaxLength(4)


            entity.Property(e => e.StudentName)
                .HasMaxLength(50)


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
        var depts = db.Departments;//deferred execution: sql query is executed when we iterate through the collection.
        
        foreach (var dept in depts)
        {

            Console.WriteLine(dept);//we can override ToString() method in Department entity.

            Console.WriteLine(dept.DeptNo + " " + dept.DeptName);//DeptNo and DeptName are properties of Department entity.
        }

        Console.WriteLine(depts.ToQueryString());//SELECT [d].[DeptNo], [d].[DeptName] FROM [Departments] AS [d]

        var depts = db.Departments.Where(d => d.Capacity > 100).ToList();
        Console.WriteLine(depts.ToQueryString());//SELECT [d].[DeptNo], [d].[DeptName] FROM [Departments] AS [d] WHERE [d].[Capacity] > 100


        //projection
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


---

# Break

---

## Code First

> [!done] steps
>
> ```xml
> <Nullable>disable</Nullable>
> ```
>
> - create a new project.
> - disable `nullable` reference types from project properties.
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
> - `Convention`: easy to use, but we can't specify the column names and other properties.
> - `Annotation`: higher priority than conventions. we can specify the column names and other properties.
> - `Fluent API`: higher priority than conventions and annotations, we can do anything with the table.

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
        entity.HasOne(s => s.DeptNoNavigation)
            .WithMany(d => d.Students)
            .HasForeignKey(s => s.DeptNo)

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

> [!tip] create Course Entity
>
> - create file `Course.cs` in Models folder.
> - create properties for `Course` entity.
> - create DbSet for Course in `ITIContext` class.

```csharp
//Course.cs
namespace DemoCodeFirst.Models
{
    public class Course
    {
        public int CrsId { get; set; }
        public string CrsName { get; set; }
    }
}
```

```csharp
//ITIContext.cs

//add DbSet for Course.
public virtual DbSet<Course> Courses { get; set; }

//override OnModelCreating method.
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Course>(entity =>
    {
        entity.HasKey(e => e.CrsId);//this will be primary key. (identity by default)
        entity.Property(e => e.CrsId).ValueGeneratedNever();//this will not be identity.
        entity.Property(e => e.CrsName).IsRequired().HasMaxLength(20);//required and has a length of 20.
    });
    base.OnModelCreating(modelBuilder);
}
```

> [!done] `add-migration` and `update-database`
>
> - ```cmd
>     add-migration addCourseTable
>      update-database
>   ```

> [!warning] relationships
>
> - relationship between entities is unidirectional.
> - but in code ==bidirectional==
> - we use `navigation properties` to specify the relationship between entities.

```csharp
//Department.cs
public class Department
{
    public int Id { get; set; }
    public string DeptName { get; set; }
    public int? Capacity { get; set; }
    public int Status { get; set; }

    //collection of students.
    //we can  use HashSet as object should be unique.
    //we have to initialize it otherwise it will be null and will throw an exception.
    public virtual List<Student> Students { get; set; } = new List<Student>();

    //we will know later why we use virtual.
}
```

```csharp
//Student.cs
public class Student
{
    public int StdId { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    [ForeignKey("DeptNoNavigation")]//used to specify the navigation property.
    //or we can use [ForeignKey("DeptNo")] before the property.
    public int DeptNo { get; set; }//not a must

    [ForeignKey("DeptNo")]//used to specify the foreign key. Must be used with navigation property.
    public virtual Department DeptNoNavigation { get; set; }//navigation property.

    //we will know later why we use virtual.
}
```

> [!done] `add-migration` and `update-database`
>
> - ```cmd
>     add-migration addRelationship
>      update-database
>   ```
> - this will create the foreign key in the database.

```csharp
//3342_addRelationship.cs
public partial class addRelationship : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Students",
            columns: table => new
            {
                StdId = table.Column<int>(type: "int", nullable: false),
                Age = table.Column<int>(type: "int", nullable: false),
                Name = table.Column<string>(type: "nvarchar(max)", nullable: true),
                DeptNo = table.Column<int>(type: "int", nullable: false)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Students", x => x.StdId);
                table.ForeignKey(
                    name: "FK_Students_Departments_DeptNo",//name of the constraint.
                    column: x => x.DeptNo,
                    principalTable: "Departments",
                    principalColumn: "Id",
                    onDelete: ReferentialAction.Cascade);//as DeptNo is required.
                    //will delete the student if the department is deleted.
            });

        migrationBuilder.CreateIndex(
            name: "IX_Students_DeptNo",
            table: "Students",
            column: "DeptNo",
            unique: true);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(
            name: "Students");
    }
}
```

> [!done] main

```csharp
//main
class Program
{
    static void Main(string[] args)
    {
        using (ITIContext db = new ITIContext())
        {
           Student std = new Student(){StdId = 1, Name = "Ahmed", Age = 20, DeptNo = 1};

           //add student to the department.
              db.Students.Add(std);
                db.SaveChanges();

            //add through navigation property.
            Department dept = db.Departments.FirstOrDefualt(d => d.Id == 1);
            Student std = new Student(){StdId = 2, Name = "Ali", Age = 22,DeptNoNavigation = dept};
            db.Students.Add(std);
            db.SaveChanges();

            //add through department students list.
            Department dept = db.Departments.FirstOrDefualt(d => d.Id == 1);
            dept.Students.Add(new Student(){StdId = 3, Name = "Sara", Age = 25});
            db.SaveChanges();
        }
    }
}
```

> [!bug] Many-to-Many relationship
>
> - Course and Department
> - we add list of courses to department and list of departments to course.
> - `virtual` is used for lazy loading.
>   > [!warning] we dont have to create the table manually, only if there are additional attributes.
>   >
>   > - then we have to create a new entity for the table. `StudentCourse` for example.

```csharp
//Department.cs
public class Department
{
    public int Id { get; set; }
    public string DeptName { get; set; }
    public int? Capacity { get; set; }
    public int Status { get; set; }

    public virtual List<Student> Students { get; set; } = new List<Student>();
    public virtual List<Course> Courses { get; set; } = new List<Course>();

    //virtual = lazy loading.
}
```

```csharp
//Course.cs
public class Course
{
    public int CrsId { get; set; }
    public string CrsName { get; set; }

    public virtual List<Department> Departments { get; set; } = new List<Department>();

    //virtual = lazy loading.
}
```

> [!done] `add-migration` and `update-database`
>
> - ```cmd
>     add-migration addManyToManyRelationship
>      update-database
>   ```
> - this will create the many-to-many relationship in the database.
> - will create a new table `DepartmentCourses` with `DeptId` and `CrsId` columns.

```csharp
//7542312312312387_addManyToManyRelationship.cs
public partial class addManyToManyRelationship : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "DepartmentCourses",
            columns: table => new
            {
                DeptId = table.Column<int>(type: "int", nullable: false),
                CrsId = table.Column<int>(type: "int", nullable: false)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_DepartmentCourses", x => new { x.DeptId, x.CrsId });//composite primary key.
                table.ForeignKey(
                    name: "FK_DepartmentCourses_Courses_CrsId",
                    column: x => x.CrsId,
                    principalTable: "Courses",
                    principalColumn: "CrsId",
                    onDelete: ReferentialAction.Cascade);
                table.ForeignKey(
                    name: "FK_DepartmentCourses_Departments_DeptId",
                    column: x => x.DeptId,
                    principalTable: "Departments",
                    principalColumn: "Id",
                    onDelete: ReferentialAction.Cascade);
            });

        migrationBuilder.CreateIndex(
            name: "IX_DepartmentCourses_CrsId",
            table: "DepartmentCourses",
            column: "CrsId",
            unique: true);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(
            name: "DepartmentCourses");
    }
}
```

> [!done] StudentCourse Entity
> Many-to-Many relationship
>
> - create a new file `StudentCourse.cs` in Models folder.
> - create properties for `StudentCourse` entity.
> - will split Many-to-Many relationship into two One-to-Many relationships.

```csharp
//StudentCourse.cs
namespace DemoCodeFirst.Models
{
    public class StudentCourse
    {
        public int StdId { get; set; }
        public int CrsId { get; set; }

        //degree
        public int? Degree { get; set; }

        [ForeignKey("StdId")]
        public virtual Student Student { get; set; }

        [ForeignKey("CrsId")]
        public virtual Course Course { get; set; }
    }
}
```

```csharp
//Student.cs
public class Student
{
    public int StdId { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public int DeptNo { get; set; }
    public Department DeptNoNavigation { get; set; }

    public virtual List<StudentCourse> StudentCourses { get; set; } = new List<StudentCourse>();
}
```

```csharp
//Course.cs
public class Course
{
    public int CrsId { get; set; }
    public string CrsName { get; set; }

    //add list of StudentCourse as it is the side of many. (to the previous code )
    //departmen
    public virtual List<StudentCourse> StudentCourses { get; set; } = new List<StudentCourse>();
}
```

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<StudentCourse>(entity =>
    {
        entity.HasKey(e => new { e.StdId, e.CrsId });
        entity.Property(e => e.Degree).HasColumnName("Degree");
    });

    modelBuilder.Entity<StudentCourse>()
        .HasOne(e => e.Student)
        .WithMany(e => e.StudentCourses)
        .HasForeignKey(e => e.StdId)


    modelBuilder.Entity<StudentCourse>()
        .HasOne(e => e.Course)
        .WithMany(e => e.StudentCourses)
        .HasForeignKey(e => e.CrsId)


    base.OnModelCreating(modelBuilder);
}
```

> [!example] `add-migration` and `update-database`

> [!danger] we didn't add `DbSet` for `StudentCourse` in `ITIContext` class.
>
> - but it in migration file it will create the table.
> - as it has relation with `DbSet` already in the context.
>   > [!done] better to add `DbSet` for `StudentCourse` in `ITIContext` class.
>   >
>   > - so we can use it in the code.
>   >
>   > ```csharp
>   > db.StudentCourses
>   > ```

```csharp
//ITIContext.cs
// add DbSet for StudentCourse.
public virtual DbSet<StudentCourse> StudentCourses { get; set; }
```

> [!tip] one-to-Many relationship
>
> - department and student

> [!tip] ==Fluent API==
>
> - we use either one of the following.
> - from Department to Student or from Student to Department.

```csharp
//ITIContext.cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    //It is either one of the following.

    //1.from Department to Student
    modelBuilder.Entity<Department>(entity =>
    {
        //HasMany: used to specify the one-to-many relationship.
        entity.HasMany(d => d.Students)//one department has many students.
            .WithOne(s => s.DeptNoNavigation)//
            .HasForeignKey(s => s.DeptNo)
            .IsRequired();//required
            // .IsRequired(false);//not required
    });

    //2.from Student to Department
    modelBuilder.Entity<Student>(entity =>
    {
        entity.HasOne(s => s.DeptNoNavigation)//one student has one department. (name of the navigation property)
            .WithMany(d => d.Students)//many students in one department.
            .HasForeignKey(s => s.DeptNo)//foreign key.
            .IsRequired();
    });


    ////////////////

    //Course and Department
    modelBuilder.Entity<Department>(entity =>
    {
        entity.HasMany(d => d.Courses)//one department has many courses.
            .WithMany(c => c.Departments)//many courses in many departments.
    });


    base.OnModelCreating(modelBuilder);
}
```

> [!example] one-to-one relationship
>
> - object on both sides of the relationship.
> - if it is nullable then it is not required.
> - foriegn key in one of the tables.
> - if one end is nullable and the other is not, then the foreign key will be in the not nullable end.

> [!tip] self relationship
>
> - object on same class reference itself.
> - for example, Student and Leader
> - create a new property in the class for the same class.

```csharp
//Student.cs

Public class Student
{
    public int StdId { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }


    [ForeignKey("Leader")]
    public int? LeaderId { get; set; }//nullable as leader doesn't have a leader.
    public virtual Student Leader { get; set; }//navigation property

}
```

> [!info] `server explorer` in visual studio from `view` menu.
>
> - used to view the database and its objects.
> - connction string is in the properties of the database.
>
> ```json
> "conn1":"Server=(localdb)\\mssqllocaldb;Database=ITI;integrated security=true;trustservercertificate=true;"
> ```
>
> - to connect to visual studio sql server
> - delete migration folder and then run `add-migration` and `update-database` commands.
> - this will create the database and its objects.

> [!bug] `ITIContextSnapshot.cs`
>
> - used to track the changes in the code like adding new properties or new entities.
> - this file gets updated when we run `add-migration` command
> - this file is used to compare the changes in the code to specify the changes in the database.

> [!done] to return to the previous migration
>
> - migrations: `m1`, `m2`, `m3`, `m4`, `m5`
> - we can use `update-database` command with the migration name.
> - ```cmd
>   update-database m2
>   ```
> - this will return to the `m2` migration.
> - will call `down` method of `m5` , `m4`, `m3`
> - we can't remove migration as the migration could be dependent on other migrations.
> - good practice as intial migration with no tables and then add the tables in the next migrations.
> - good practice to name migration after biggest change in it.

---

### #Lab-EF-Day1

- database first (department and student)
- CRUD operations
- code first (department, student, course)
- relationships (one-to-many, many-to-many, one-to-one, self)
- CRUD operations
