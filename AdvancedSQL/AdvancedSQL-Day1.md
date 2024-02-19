### Course outline

![Course outline](image%201.png)

---

### DB Creation

> [!tip] physical vs logical representation
>
> - Physical Files: .ldf: log file, .mdf: data file + metadata
> - Logical: pointer [ filegroup ] -> physical file
> - we can make secondary physical files `.ndf` for better performance
> - and make filegroup for secondary files (instead of only primary filegroup)
> - we can create table in specific filegroup

> [!tip] column properties
>
> - identity: auto increment
> - computed: calculated column(formula: `column1 + column2`,is persisted: yes: store in disk, no: calculate on the fly)
>   > [!bug] `age as getdate() - birthdate` is not allowed as getdate() is changing
> - sparse: store only non-null values (useful for columns with many null values) yes: don't store null values, no: store null values

> [!warning] we dont add values in identity column, it is auto incremented or computed column

> [!done] INSERT And DELETE Specifications
>
> - `delete: set null`: set null in foreign key when primary key is deleted (delete department, set null in employee)
> - `update`: `cascade` : update foreign key when primary key is updated (update department, update department in employee)
> - `delete`: `cascade`: delete foreign key when primary key is deleted (delete department, delete employee)
> - `delete`: `no action`: don't delete foreign key when primary key is `deleted` (delete department, Error if employee has department)
> - `delete`: `set default`: set default value in foreign key when primary key is deleted (delete department, set default in employee)
>   > [!bug] we define default value in foreign key column (has to be one of the values in primary key column)
> - if we didn't specify (default will be `null`)

---

> [!example] `dbo` is the default SQL Server schema, if we don't specify schema, it will be `dbo`
> ==logical== group of objects
>
> - `SchemaName.ObjectName` (dbo.Employee)
> - Object Name: Table, View, Procedure, Function,...
>   > [!done] Schema solved 3 problems
>   >
>   > - Security: we can give permission to schema (instead of each object alone)
>   > - Object Naming: we can have same table name in different schema
>   > - Object Grouping: we can group objects in schema

```sql
-- create schema
create schema HR
create schema Sales
-- change schema of table
alter schema HR transfer dbo.Employee
-- default schema dbo (we don't need to specify schema)
alter schema HR transfer  Employee

-- we can create Employee table again
create table Employee
(
    EmployeeID int primary key,
    EmployeeName nvarchar(50),
    DepartmentID int,
    constraint FK_DepartmentID foreign key (DepartmentID) references Department(DepartmentID)
)

-- change schema of table
alter schema HR transfer Employee

-- copy data from dbo.Employee to HR.Employee
select into  HR.Employee from dbo.Employee
```

> [!tip] `synonym` is an alias for table, view, procedure, function, ...
>
> - we can use synonym instead of table name
> - this is useful when the table name is changed or moved to another schema

```sql
-- create synonym
create synonym Emp for HR.Employee
-- use synonym
select * from Emp
-- drop synonym
drop synonym Emp
```

---

> [!done] defult databases
>
> - `master`: system database :this is the database that records the system level information for SQL Server
> - `model`: system database :this is the database that is used as the template when a new database is created
> - `msdb`: system database :this is the database that is used by SQL Server Agent for scheduling alerts and jobs
> - `tempdb`: system database :this is the database that is used to store temporary objects and temporary data

> [!example] sql server agent
>
> - used to schedule jobs
> - we can create jobs to run at specific time

```sql

```
