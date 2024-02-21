## Variables and Functions

### Variables

> [!bug] we can not declare global variable or assign value to it in SQL Server
>
> - but it changes after executing queries
>   > [!done] we can declare and assign value to local variable

> [!example] batch: group of SQL statements that are executed together

> [!warning] local variable 
>
> - declaration, assignment, and display should be all in the same batch (as it is local to the batch) or function
> - declaration: `declare @variableName dataType`
> - assign value: `set @variableName = value` || `select @variableName = value`
> - display: `select @variableName`

```sql
select @x = age from Employee where EmployeeID = 1
-- this will assign value to @x

--update fname and store age in @x
update Employee set fname = 'John', age = @x where EmployeeID = 1

--display age
select @x
```

> [!danger] global variable
>
> - starts with `@@`
> - we can only display it
> - we can't declare or assign value to it
> - `declare @@variableName` will give error
> - `set @@variableName = value` will give error
> - we can assign its value to local variable and display it
> - `set @variableName = @@variableName`

> [!example] global variable
>
> - `@@servername`: name of the server
> - `@@version`: version of SQL Server
> - `@@identity`: last identity value generated in the current session (Null: insert on normal table, num: if insert on table with identity column)
> - `@@rowcount`: number of rows affected by the last statement
> - `@@error`: error number of the last statement (0: no error, num: if error)

> [!warning] named instance vs default instance
>
> - named instance: `servername\instancename`
> - `./instancename` is the same as `servername\instancename`
> - we can have multiple named instances on the same server
> - default instance: `servername`
> - we can have only one default instance on the same server

```sql
select @@IDENTITY
--

select SCOPE_IDENTITY()
--

```

```sql
--those 2 has to be in the same batch
declare @x int --if excuted alone, will declare @x and remove it from memory immediately
select @x -- display


```

```sql
-- select either display or assign value
declare @x int
select @x = age from Employee where EmployeeID = 1
select @x = age, name from Employee --Error -> display or assign only, not both.
```

```sql
-- if select returned one value it will be assigned to @x
declare @x int
select @x = age from Employee where EmployeeID = 1

--if select didn't return value, @x will be the same (not changed)
select @x = age from Employee where EmployeeID = -1

-- if select returned multiple values, it will give be assigned to @x (last value)
select @x = age from Employee
```

##### Arrays

```sql
-- declare array 1d
declare @t table (col1 int)
insert into @t
select st_age from Student
select * from @t
select count(*) from @t -- now we saved the data in variable @t we can use it in other queries instead of executing the query again

-- declare array 2d
declare @t table (col1 int, col2 nvarchar(50))
insert into @t
select st_age, st_name from Student
select * from @t
```

```sql
--select either display or assign value
declare @x int
select @x = age, @name = name from Employee where EmployeeID = 1
select @x, @name
```

```sql
-- update and store salary in @x
declare @x int
update Employee
set name = 'John', @x = salary
where EmployeeID = 1
select @x
```

```sql
--variables dynamic query
declare @par int = 5
select * from Employee where id = @par

select top (@par) * from Employee
```

```sql
-- make metadata dynamic
declare @col varchar(50)  = '*', @t varchar(50) = 'Employee'
select @col from @t
--- ERROR from @t not table but string
select 'select * from Employee'
--will "display select * from Employee"

--execute
exec('select * from Employee')
--will display the data

exec('select ' + @col + ' from ' + @t)

-- make where dynamic

declare @col varchar(50)  = '*', @t varchar(50) = 'Employee', @where varchar(50) = 'salary > 1000'
exec('select ' + @col + ' from ' + @t + ' where ' + @where)
```

> [!bug] varchar(10) will take only 10 characters, if we assign more than 10 characters it will cut them to length of 10 chars.

---

### global variable

```sql
select @@servername

select @@version

--this will dispaly the row affected
update Instructor set salary += 1000
select @@rowcount

--this will display 2 select statements (data from Instructor and row affected)
select * from Instructor
select @@rowcount


select * from Instructor
select @@rowcount
select @@rowcount -- 1


select  from student
select @@ERROR -- 156 error number

select * from student
go
select @@ERROR -- 0

select @@IDENTITY -- null

```

### control flow

> [!tip] control flow
>
> - `if` statement
> - `case` statement
> - `while` statement
> - `begin...end` block
> - `choose` statement

```sql
--if statement

declare @x int
update Student set st_age += 1
select @x = @@rowcount
if (@x > 0)
    select 'multiple rows affected'
else
    select 'no rows affected'
-- same as ordinary if statement instead of {} we use(optional) begin...end if we have multiple statements (mandatory)
if (@x > 0)
    begin
        select 'multiple rows affected'
        select 'multiple rows affected'
    end
else
    select 'no rows affected'


--if exists if not exists
create table student
(
    sid int ,
    sname nvarchar(50)
)
-- Error object already exists

-- check metadata before creating table
select * from sys.tables -- this will display all tables in the database(info about tables)

select name from sys.tables where name = 'student' -- this will display the name of the table if it exists

if exists (select name from sys.tables where name = 'student')
    select 'table exists'
else
    create table student
    (
        sid int ,
        sname nvarchar(50)
    )
-- this will create the table if it doesn't exist

--not only metadata
delete from Topic where top_id = 1
-- this will give error and stop database and will give details about the error (security issue)

if exists (select top_id from Topic where top_id = 1)
    delete from Topic where top_id = 1
else
    select "Id can't be found"

-- begin try...end try   begin catch...end catch
-- if i dont know where the error will occur
-- like try catch in c#

begin try
    delete from Topic where top_id = 1
end try
begin catch
    select 'table has relation with other tables'
end catch

```

###### while

```sql
--while
declare @x int = 10
while @x <= 20
    begin
     set @x += 1
     if @x = 14
        continue
        if @x = 16
            break
        select @x
    end
    -- 11 12 13 15
```

```sql
-- case  IIF

--criteria of salary

select ins_name,
    case
        when salary <= 3000 then 'low'
        else 'high'
    end as new_crt
from Instructor

--IIF (ternary operator)
select ins_name,
    IIF(salary <= 3000, 'low', 'high') as new_crt
from Instructor

```

```sql
-- update salary 20%
update Instructor
set salary = salary * 1.2

-- update who has salary less than 3000 increase by 20%  and who has more than 3000 increase by 10%
update Instructor
set salary =
    case
        when salary <= 3000 then salary * 1.2
        else salary * 1.1
    end
```

###### windowing function

> lead and lag .sql file

```sql
-- to get the next value, the previous value
--- lead and lag
select ins_name, salary,
    lead(salary) over (order by salary) as next_salary,
    lag(salary) over (order by salary) as prev_salary
    from Instructor

    -- not necessary to order by salary
    select ins_name, salary,
    lead(ins_name) over (order by salary) as
    next_ins_name,
    lag(ins_name) over (order by salary) as
    prev_ins_name
    from Instructor


    -- but it could be in other subject

    -- partition by to get the next value, the previous value for each subject for each student
    select st_name, grade, c_name,
    lead(st_name) over (partition by c_name order by grade) as  next_st_name,
    lag(st_name) over (partition by c_name order by grade) as  prev_st_name
    from Student

-- partition by don't have to be the same as order by and the same  with lead and lag

-- First_Value and Last_Value (first row  (max or min) and last row (max or min) in the partition)
   select st_name, grade,
   first_value(grade) over (order by grade) as first_grade,
   last_value(grade) over (order by grade) as last_grade
   from Student
   -- but it could be in other subject


   -- 4 combined in one query
   select st_name, grade,
   Prod_next = lead(grade) over (partition by c_name order by grade),
    Prod_prev = lag(grade) over (partition by c_name order by grade),
    Prod_first = first_value(grade) over (partition by c_name order by grade),
    Prod_last = last_value(grade) over (partition by c_name order by grade)
    from grades

--- try queries in the file
```

---

# Break

---

### Functions

> [!tip] Built-in functions
>
> - scalar functions: return single value
> - NULL values: `ISNULL(value, replacement)`
> - data conversion: `CONVERT(dataType, value)`
> - system function: `host_name()`, `db_name()`,
> - aggregate functions: `SUM(column)`, `AVG(column)`, `COUNT(column)`, `MIN(column)`, `MAX(column)`
> - Date and Time functions: `GETDATE()`, `DATEADD(day, 1, GETDATE())`
> - String functions: `concat()`
> - Math functions: `sin()`
> - Ranking functions: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()`
> - Windowing functions: `LEAD()`, `LAG()`, `FIRST_VALUE()`, `LAST_VALUE()`

> [!tip] user-defined functions
>
> - scalar functions: return single value (can use if, while, ... but return single value)
> - inline table-valued functions: return table (depend on the body(only select statement)=> view)
> - multi-statement table-valued functions: return table (depend on the body(select statement, declare, set, if, while, ...)) [ insert based on select ]
> - only select statement: no DML inside the function
> - can use built-in functions inside the user-defined functions

> [!bug] All functions must return a value
>
> - both built-in and user-defined functions

```sql
--NULL values
select ISNULL(null, 0) -- 0

-- this will replace the null values with 'no name'
select ISNULL(st_name,'no name') from Student

--this will remove the null values
select st_name from Student where st_name is not null

--array of replacement coalesce
select coalesce(st_name, st_lname, 'no name') from Student

--nullif (if the 2 values are the same it will return null, if not it will return the first value)
select nullif(5,5) -- null
select nullif(5,6) -- 5

--data conversion (convert || cast)
select CONVERT(nvarchar(50), getdate()) -- convert date to string

select cast(getdate() as nvarchar(50)) -- convert date to string

-- convert vs cast
-- convert is preferred when we want to convert date to string as it has more options (third parameter to specify the format)

select CONVERT(nvarchar(50), getdate(), 101) -- convert date to string with format mm/dd/yyyy
select CONVERT(nvarchar(50), getdate(), 104) -- convert date to string with format dd.mm.yyyy

-- format (use it instead of convert when we want to convert date to string as format is more readable)
select format(getdate(), 'dd.mm.yyyy') -- convert date to string with format dd.mm.yyyy
select format(getdate(), 'dddd MMMM yyyy') -- convert date to string with format day month year
select format(getdate(), 'dddd') -- convert date to string with format day name
select format(getdate(), 'MMMM') -- convert date to string with format month name
select format(getdate(), 'hh:mm:ss') -- convert date to string with format hour minute second

-- format vs day
-- format returns string
-- day returns int
select day(getdate()) -- 1
select format(getdate(), 'dd') -- 01

-- eomonth (end of month)
select eomonth(getdate()) -- last day of the month
select FORMAT(eomonth(getdate()), 'dd') -- 31 (month has 31 days)
select FORMAT(eomonth(getdate()), 'dddd') -- Monday (last day of the month)
select eomonth(getdate(), +1) -- last day of the next month
select eomonth(getdate(), -1) -- last day of the previous month


```

### Aggregate functions

```sql
-- aggregate functions
-- count(*) vs count(column)
-- count(*) will count all rows including null values
-- count(column) will count all rows except null values
select count(*) , count(st_name) from Student


--

```

##### system function

```sql
-- system function
select db_name() -- database name
select suser_name() -- user name
select host_name() -- server name
select OBJECT_ID('Student') -- object id of the table

```

##### string functions

```sql
-- string functions
--upper lower (convert string to upper or lower case)
select upper(st_name), lower(st_name) from Student

-- len (length of the string)
select len(st_name) from Student

-- substring(string, start, length) (get part of the string)
select substring(st_name, 1, 3) from Student -- get the first 3 characters of the string

select substring(st_name, 3,3) from Student -- get 3 characters starting from the 3rd character

-- not only with select statement
select *
from Student
where len(st_name) > 3

-- concat (concatenate strings)
select concat('ahmed','112',NULL,'ali') -- ahmed112ali

-- concat_ws (concatenate strings with separator)
select concat_ws(',', 'ahmed','112',NULL,'ali') -- ahmed,112,ali

--trim (remove spaces from the beginning and the end of the string)
select trim('  ahmed  ') -- ahmed

--charindex (get the position of the substring in the string)
select charindex('m', 'ahmed') -- 3
select charindex('x', 'ahmed') -- 0 (not found)

--replace (replace substring with another substring)
select replace('ahmed$gmail.com', '$', '@') -- ahmed@gmail.com

--reverse (reverse the string)
select reverse('ahmed') -- demha

--replicate (repeat the string)
select replicate('ahmed', 3) -- ahmedahmedahmed



```

##### Math functions

```sql
-- Math functions
-- sin cos tan log power sqrt abs

select sqrt(16) -- 4

select abs(-5) -- 5

select power(2,3) -- 8

select rand() -- random number between 0 and 1

select round(5.51354654, 2) -- 5.51 (round to 2 decimal places)
```

#### Date functions

```sql
-- Date functions
-- getdate() (current date and time)

select DATEDIFF(day, '1/1/2021', '1/1/2022') -- 365 (difference between 2 dates in days)
 select DATEDIFF(month, '1/1/2021', '1/1/2022') -- 12 (difference between 2 dates in months)
    select DATEDIFF(year, '1/1/2021', '1/1/2022') -- 1 (difference between 2 dates in years)

--- logical functions
select isdate('1/1/2021') -- 1 (valid date)
select isnumeric('1') -- 1 (valid number)

-- Ranking functions
-- Windowing functions
```

---

### User-defined functions

> [!tip] user-defined functions
>
> - any function must return a value
> - scalar functions: return single value
> - inline functions: return table [ select ]
> - multi-statement functions: return table [ select + if, while, ...]

```sql
-- scalar function
--function takes id and return name of the student

--fun prototype[signature] (function name, input parameters, body,return type)

create function get_sname(@sid int)
returns nvarchar(50) -- returns (with s) part of the signature (return type)
begin
    declare @sname nvarchar(50) --has to be same type (varchar(50) )
    select @sname = st_name from Student where st_id = @sid
    return @sname -- return: return the value of the function(which has the same type)
end

--calling
select get_sname(1) -- Error: get_sname is not a recognized built-in function name.

select dbo.get_sname(1) -- 'ahmed' need to use dbo. to call the function (use the schema name)

--as only built-in functions can be called without schema name

--we have to use schema if we are using the full path of the object (database.schema.object)
-- if we are using objects from different schemas in the same database, we have to use the schema name

-- we can change the schema name of the function
alter schema HR transfer dbo.get_sname


-- to edit the body of the function
alter function get_sname(@sid int)
returns varchar(20)
begin
    declare @sname nvarchar(20)
    select @sname = st_name from Student where st_id = @sid
    return @sname
end

-- we can use if, while, ... inside the function but we have to return a single value
```

##### inline functions

```sql
-- inline function (return table)
--function takes did and return table of instructors in the department

create function getinst(@did int)
returns table
as
return
(
    select ins_name,
    salary*12 as annual_salary -- we have to make alias name in inline function (drived column)
    from Instructor where dept_id = @did
)

--- calling
select getinst(1)--Error: getinst is not a recognized built-in function name.

select * from getinst(1) -- we don't have to use schema as no built-in function returns table

select ins_name from getinst(1) -- we can use the returned table as a normal table

select sum(annual_salary) from getinst(1) -- we can use the returned table in aggregate functions

-- string_split  returns table
-- c# SQLCLRfunction (not considered as built-in function) Search

```

##### multi-statement functions

```sql
--
create function getstuds(@format varchar(50))
returns @t table --to differentiate between the inline and multi-statement functions
(
    id int,
    sname varchar(50),
)
as
begin
    if @format = 'first'
        insert into @t
        select st_id, st_fname from Student
    else if @format = 'last'
        insert into @t
        select st_id, st_lname from Student
    else if @format = 'fullname'
        insert into @t
        select st_id, concat(st_fname, ' ', st_lname) from Student
    -- return @t -- Error: A RETURN statement with a return value cannot be used in this context.
    return -- to force @t to be returned
end


--- calling

select * from getstuds('sdfdsf')-

select * from getstuds('first')

select * from getstuds('last')

select * from getstuds('fullname')


--why DML query in the function (insert into @t)
-- as we are using it on variable (in memory) not on the table (in the database)


```

> [!danger] we can't make dynamic function
>
> ```sql
> create function fun(@col varchar(50), @t varchar(50))
> returns @t table
> as
> begin
>     excute('insert into @t select ' + @col + ' from ' + @t)
>     return
> end
> ```
>
> this will give error (can't use dynamic query in the function)
>
> > [!done] but can be used inside stored procedure

> [!example] functions parameters can have default values
>
> - instead of overloading
>
> ```sql
> create function fun(@id int = 1)
> ```

---

> [!danger] to get All notes (`.md` files)
>
> - link: [https://github.com/Muhammad-Ashraf9/PD](https://github.com/Muhammad-Ashraf9/PD)
