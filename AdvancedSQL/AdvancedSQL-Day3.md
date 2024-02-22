## Indexing and Performance

> [!tip] Index
>
> - clustered index
> - non-clustered index

> [!example] table saved in hard disk as data pages (8KB each)
>
> > [!warning] table without index (no primary key)
> >
> > - data is not sorted in any way

> [!warning] table without primary key
>
> - data is not sorted
> - when searching for a record, the entire table is scanned(even if the wanted record is the first one)

> [!done] table with primary key
>
> - data is sorted (clustered) based on the primary key
> - primary key constraint creates a clustered index
> - B+ tree is used to store the data (created automatically by the database engine)
> - turns the table scan into a page scan (much faster)
>   > [!tip] that's why searching with primary key is much faster
>   >
>   > ```sql
>   > select * from table where primary_key = 1
>   > ```

![[binaryTree.png]]

> [!warning] when searching for a record without primary key (or any other column)
>
> - the entire table is scanned (same problem as before)
>   > [!tip] to solve this problem
>   >
>   > ```sql
>   > create nonclustered index i2 on table(column)
>   > ```
>   >
>   > - create a non-clustered index on the column
>   > - this will save the column data sorted in a separate tree (but the data pages contain pointers to the actual data)
>   > - that's searching for a record with the primary key is faster than searching for a record with a non-clustered index
>   > - but searching for a record with a non-clustered index is still faster than searching for a record without any index
>   >   -we can create multiple non-clustered indexes on the same table
>
> > [!danger] only one clustered index can be created on a table

![[non-clustersd-index.png]]

> [!warning] indexes is good for `select` statements but bad for `insert`, `update`, and `delete` statements

> [!example] if we had clusterd index on column `A` and then we add primary key on column `B`
>
> - then `B` will be non-clustered index
> - so primary key not always clustered index

---

```sql
-- create clustered index on st_f_name column
create clustered index i1 on student(st_fname) --Error: clusered index already exists

-- create non-clustered index on st_f_name column
create nonclustered index i2 on student(st_fname)

-- create non-clustered index on st_address column
create nonclustered index i3 on student(st_address)

--
select * from student where st_fname = 'Ahmed'

--
select

-- primary key -> clustered index
-- unique constraint -> non-clustered index

create table ITIemps(
    eid int identity,
    SSN int primary key,
    fname varchar(50),
    salary int unique,
    overtime int unique,
    constraint c1 check(salary > 0),
)

create  unique index i7 on Student(st_age) --constraint(old + new data) + non-clustered index

create  index i7 on Student(st_age) --non-clustered index
```

> [!done] need to know which columns are used the most in the `where` clause to create the non-clustered index on them
>
> - `sql server profiler` can be used to monitor the queries and know which columns are used the most
> - creates trace files that can be analyzed to know which columns are used the most
> - `database engine tuning advisor` can be used to suggest the best indexes for the database
> - get the trace file `.trc` and open it in the `database engine tuning advisor` to get the suggestions
> - the suggestions can be applied to the database to improve the performance

---

### Pivot And Group

> [!done] `pivot and group.sql` file
>
> - check file for examples

#### rollup

```sql
select ProductID,SalesmanName,quantity
from sales

select ProductID as X,sum(quantity) as "Quantities"
	from sales
	group by ProductID
    --this will give us the total quantity for each product
    -- add row with all the quantities
    --we can use union to get the same result

select ProductID as X,sum(quantity) as "Quantities"
	from sales
	group by ProductID
    union
    select 'Total' as X,sum(quantity) as "Quantities"
    from sales
-- used hard disk 2 times
-- we use rollup to get the same result with only 1 hard disk usage
-- rollup:  (use the aggregate function on the result of the group by)
select ProductID as X,sum(quantity) as "Quantities"
	from sales
	group by rollup(ProductID)
    --this will give us the total quantity for each product and the total quantity for all the products


select SalesmanName as Name, sum(quantity) as TotalQuantity
from Sales
group by rollup(SalesmanName)
-- this will give us the total quantity for each salesman
-- and the total quantity for all the salesmen


select ProductID,SalesmanName,sum(quantity) as "Quantities"
from sales
group by ProductID,SalesmanName
-- this will give us the total quantity for each product for each salesman
-- 10 rows

select ProductID,SalesmanName,sum(quantity) as "Quantities"
from sales
group by rollup(ProductID,SalesmanName)
-- this will give us the total quantity for each product for each salesman
-- 15 rows : rollup works on the first column only(product id)
-- +1: is sum of all the quantities
-- +3: is the sum of the quantities for each product
-- +1: is the sum of the quantities for each product for each salesman


select SalesmanName,ProductID,sum(quantity) as "Quantities"
from sales
group by rollup(SalesmanName,ProductID)


-- to make rollup on both columns we use cube
-- cube: it is like result of 4 queries
select ProductID,SalesmanName,sum(quantity) as "Quantities"
from sales
group by cube(ProductID,SalesmanName)
-- 1. total for each product for each salesman
--2. total for each product
--3. total for each salesman
-- 4. total for all

--order by ProductID,SalesmanName


select ProductID,SalesmanName,sum(quantity) as "Quantities"
from sales
group by cube(ProductID,SalesmanName)
--order by ProductID,SalesmanName

```

> [!done] `cube` is like multiple `rollup` on the same result

```sql
--grouping sets
-- this  will cancel the group by 
-- only rollup on the specified columns
select ProductID,SalesmanName,sum(quantity) as "Quantities"
from sales
group by grouping sets(ProductID,SalesmanName)
order by SalesmanName

---

---
```

#### pivot

> [!done] `pivot` is used to change how the data is displayed
>
> - used to group by 2 columns and display the aggregate of a third column (X,Y, aggregate)

![[pivot.png]]

```sql
--if u have the result of the previous query
select ProductID,SalesmanName,sum(quantity) as "Quantities"
from sales
group by SalesmanName,ProductID
-- this will give us the total quantity for each product for each salesman

-- is the same as but with different display
SELECT *
FROM sales
PIVOT (SUM(Quantity) FOR SalesmanName IN ([Ahmed],[Khalid])) as PVT
-- product id not mentioned in the pivot (it's the y-axis)

SELECT *
FROM sales
PIVOT (SUM(Quantity) FOR productID IN ([1],[2],[3])) as PVT
-- same as the previous query but with different display

-- we can make variable to get all the product ids and use it in the pivot (while loop)

```

> [!example] `batch` vs `script`
>
> - `batch` is a group of `sql` statements that are executed together
> - ==indpendent== as they are executed one by one even if one of them fails (the rest will be executed)
> - ddl statements are not allowed in the batch(seperate between them by `go`)
> - this is considered script
>   > [!done] we can backup the database script: ddl queries that create the database and dml queries that insert the data
>   >
>   > - `tasks` -> `generate scripts`

```sql
-- batch
select * from student
select * from employee
select * from department
```

```sql
-- script
create database school
go
use school
go
create table student
(
    id int,
    name varchar(50)
)
go
```

#### Transactions

> [!done] Transaction
>
> - transaction: a set of independent sql statements that are executed together as a single unit of work
> - if one of the statements fails, the entire transaction is rolled back
> - if all the statements succeed, the entire transaction is committed
> - used to ensure that all the statements are executed together

> [!warning] query(SSMS,VS) vs `.LDF` log file vs `.MDF` data file
>
> - query: `sql` statements (any query is implicitly a transaction)
> - `.LDF` log file: contains the log of the transactions (what happened in the database)
> - `.MDF` data file: contains the data of the database
> - Explicit transaction: `begin transaction` -> `commit` or `rollback`
>   > [!danger] if no `commit` or `rollback` is used, the transaction will be implicitly rolled back when Error occurs

![[transaction.png]]

```sql
create table parent(
    pid int primary key
)
create table child(
    cid int foreign key references parent(pid)
)

insert into parent values(1)
insert into parent values(2)
insert into parent values(3)
insert into parent values(4)
--this is a batch no dependency between the statements (

insert into child values(1) --1 row affected
insert into child values(22) --Error: foreign key violation
insert into child values(3) -- 1 row affected

-- transaction
begin transaction
insert into child values(1) --1 row affected
insert into child values(2) -- 1 row affected
insert into child values(3) -- 1 row affected
rollback -- this will rollback the entire transaction

begin transaction
insert into child values(1) --1 row affected
insert into child values(22) -- Error: foreign key violation
insert into child values(3) -- 1 row affected
commit -- this will commit the data even if error occurs

-- begin try and catch
begin try
    begin transaction
    insert into child values(1) --1 row affected
    insert into child values(22) -- this will go straight to the catch block
    insert into child values(3)
end try
begin catch
    rollback
    Select ERROR_MESSAGE() , ERROR_LINE(), ERROR_NUMBER() -- to display the error information
end catch
-- 1 row affected
-- 0 rows affected


```

### Transaction properties ACID

> [!done] Search for `ACID` properties of the transaction

---

#### Break

---

### Views

![[views.png]]

> [!done] View
>
> - view: is a virtual table that is created from a `select` statement
> - specify user view of the data
> - hide DB Objects
> - simplify complex queries
> - limit access to the data
> - has no parameter
> - No DML queries inside view body
>   > [!bug] but can run DML queries on the view with some restrictions
>   >
>   > ```sql
>   > create view v1 as select * from student
>   > insert into v1 values(1,'Ahmed')
>   > ```

> [!tip] types of views
>
> - standard view
> - indexed view: has physical storage
> - partitioned view: can use multiple tables from multiple databases from multiple servers (union all)

> [!done] can make join between view and table , view and view

```sql
--- create view
create view Vstuds
as
select * from student

--calling the view
select * from Vstuds

create view Vcairo
as
    select st_id,st_name ,st_address
    from student
    where st_address = 'Cairo'

select * from Vcairo

alter schema HR transfer Vcairo

select * from HR.Vcairo

alter schema dbo transfer HR.Vcairo

-- this will drop the view(query) not the table
drop view Vcairo

-- alter view
alter view Vcairo(sid,sname,saddress)
as
    select st_id,st_name ,st_address
    from student
    where st_address = 'Cairo'
    --more safe as it hide the columns names

    create view Valex
    as
        select st_id,st_name ,st_address
        from student
        where st_address = 'Alex'

    select * from Valex

    --create view with union of both views
    create view VcairoAlex
    as
        select * from Vcairo
        union all
        select * from Valex

    select * from VcairoAlex

    -- complex view
    -- complex joins that is used frequently
    create view Vjoin(sid,sname,dname,did)
    as
        select s.st_id,s.st_name ,d.dept_name,d.dept_id
        from student s
        join department d
        on s.dept_id = d.dept_id

    select * from Vjoin
    select sname,dname from Vjoin

    --join view with table
    select sname,dname,grade from
    Vjoin V inner join Stud_course SC
    on V.sid = SC.st_id

    --sp_helpText : to get the view definition (code)
    sp_helpText 'Vjoin'

    --with encryption: to hide the view definition

    alter view Vjoin(sid,sname,dname,did)
    with encryption
    as
        select s.st_id,s.st_name ,d.dept_name,d.dept_id
        from student s
        join department d
        on s.dept_id = d.dept_id

    sp_helpText 'Vjoin' -- Error: The text for object 'Vjoin' is encrypted.


    --DML Views
    ------View  one table
    create view Valex
    as
        select st_id,st_name ,st_address
        from student
        where st_address = 'Alex'
    --insert
    insert into Valex values(1,'Ahmed','Alex') -- 1 row affected : this will insert the data into the student table
    -- can only update only data that is in the view

    --we only can insert data that is in the view
    --other columns has to for the insert statement to work
    ---allow null
    ---default value
    ---drived column
    ---identity column

    insert into Valex values(1,'Ahmed','Mansoura') -- this will work
    -- but we can not see this data in the view (as alex is the only address in the view)

    -- so we need to make constraints on the view
    -- with check option
    create view Valex
    as
        select st_id,st_name ,st_address
        from student
        where st_address = 'Alex'
        with check option
    insert into Valex values(1,'Ahmed','Mansoura') -- Error: The attempted insert or update failed because the target view either specifies WITH CHECK OPTION

    ------View  multiple tables
    --- can't DELETE from the view XXXXX
    --- insert and update are not allowed if the view has more than one table
     insert into Vjoin values(1,'Ahmed','HR',1) -- Error: multiple base tables

     -- we only insert data for the student table and the department table (only one talbe at a time)
        insert into Vjoin values(1,'Ahmed')
        insert into Vjoin values('Ahmed',5)
---

```

### Merge

> [!warning] last transaction vs Daily transaction
> need to compare the last transaction with the daily transaction
>
> - update the last transaction with the daily transaction
>   > [!done] `Merge`
>   >
>   > - we can use it to compare 2 tables and update the target table based on the source table
>   > - can compare between table and subquery

```sql
MERGE INTO last_transaction AS Target -- target table (has to be a TABLE)
USING daily_transaction AS Source -- source table
-- source can be subquery
ON Target.lid = Source.did
WHEN MATCHED THEN
    UPDATE SET Target.lvalue = Source.dvalue
    -- no need to specify the updated column it is always target column
WHEN NOT MATCHED THEN
    INSERT
    VALUES (Source.did,Source.dname,Source.dvalue)
    -- no need to specify the inserted column it is always target column
```

> [!tip] other ways to write `merge`

![[merge.png]]

```sql
-- create table last
create table lastt(
    xid int ,
    xname varchar(50),
    xvalue int
)

-- create table daily
create table daily(
    yid int ,
    yname varchar(50),
    yvalue int
)

Merge into Lastt as T
using daily as S
on  T.xid = s.yid
when Matched then
update
    set T.xvalue = S.yvalue
    when not matched then
    insert values(s.yid,s.yname,s.yvalue)
output $action; --to dispalay actions done
-- insert
-- update
-- update

output -- trigger (next lec)





merge into cairoStuds as T
using mansouraStuds as S
on T.SSN = S.SSN
when matched then
-- decide what action to make
when not matched then
-- decide what action to make
```

---

> [!danger] to get All notes (`.md` files)
>
> - link: [https://github.com/Muhammad-Ashraf9/PD](https://github.com/Muhammad-Ashraf9/PD)