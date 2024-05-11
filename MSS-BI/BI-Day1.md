# Microsoft SQL Server Business Intelligence (BI)

## Day 1 - SSIS (SQL Server Integration Services)

> [!tip] BI
>
> - it is a set of tools and techniques used to transform raw data into meaningful and useful information to help business users make better decisions
> - Dashboard
> - Analysis
> - Data mining
> - Machine learning
> - Reporting

> [!example] source system (OLTP)
>
> - system that is used to run the business
> - it saves the business data into the database like information about ITI mansoura branch (students, courses, instructors, ...)
> - every branch could have different backend and different database
> - how to make analysis on the data of all branches
> - problem: they could send data in different formats (excel, csv, ...)
>
>   > [!done] Data Warehouse (OLAP)
>   >
>   > - it is a centralized database that is used to store the data from different sources (OLTP)
>   > - to make analysis and reports on the data
>
> - transaction is done on the source system (OLTP) and then the data is transferred to the data warehouse (OLAP)

> [!example] ETL (Extract, Transform, Load)
>
> - tools that is used to make ETL
> - SSIS
> - Informatica
> - DataStage

> [!warning] Staging Area
>
> - it is a temporary storage area that is used to store the data before loading it into the data warehouse
>
> 1. ==extract== the data from the source systems (OLTP) - not a backup but transferring the tables as they are to the staging area
>
> - the tables could have different structures and different columns (like: date(yyyy-mm-dd), date(dd-mm-yyyy), ...)
> - so we need to handle different formats of the data
> - and merge related tables together (students from different branches) into one table (students) with the same structure
>
> 2. this is the ==transformation== process
>
> - clean the data (remove the duplicates, replace null values, edit data types, concatenate columns, ...)
>
> 3. ==Load== the data into the data warehouse (OLAP)
>
> - schedule the ETL process (daily, weekly, monthly, ...)
> - it is like jobs that run on the server
> - it depends on the business requirements
> - in our tool => XML file
> - in other tools => database => in this case it is not temporary storage area

> [!warning] even if all databases are using the same database management system (like SQL Server)
>
> - we would still need the Data Warehouse (OLAP) to make analysis on the data
> - as Transactional databases (OLTP) are not designed for analysis but for transactions (insert, update, delete, ...) to run the business
> - Data Warehouse only supports Structured data (tables, columns, rows, ...) sql databases
> - no support for unstructured data (like images, videos, ...)

![](Pasted%20image%2020240425164521.png)

> [!done] SSIS
>
> - used to create packages that are used to make ETL process
> - it is like jobs that run on the server
> - control flow:
> - data flow:
> - precendece constraints:
> - variables: to store values that are used in the package
> - data viewer watch
> - connection manager

> [!example] Data Flow
>
> - it a task from control flow tasks
> - source: the source of the data (like a table, a file, ...)
> - transformation: the process of cleaning the data (like remove duplicates, replace null values, ...)
> - destination: the destination of the data (like a table, a file, ...)

> [!tip] Control Flow
>
> - it is the tasks that are used to control the flow of the package (data flow is part of the control flow)
> - anything outside the data flow (before or after the data flow)
> - like in case of success backup, in case of failure make message box, ...

> [!note] steps
>
> - open visual studio as administrator
> - search for SQL Server Integration Services Project
> - create a new project
> - in SSIS packages folder (has Packages.dtsx)
> - right click => new SSIS package
> - XML file => to be independent of the database
> - from the toolbox
> - drag and drop the data flow task from the toolbox to the control flow
> - double click on the data flow task
> - drag and drop the source (like Excel Source) from the toolbox to the data flow
> - drag and drop the transformation (like Sort) from the toolbox to the data flow
> - drag and drop the destination (like OLE DB Destination) from the toolbox to the data flow

> [!tip] Control Flow
>
> - it is all the tasks that are used to control the flow of the package
> - and the data flow is part of the control flow
> - drag and drop send mail task from the toolbox to the control flow
> - right click on the line between the task to make it green (success) or red (failure) or blue (completion)
> - drag and drop the execute script task from the toolbox to the control flow
> - right click on the arrow between the tasks to make it green (success) or red (failure) or blue (completion)

---

# Break

---

> [!tip] How to add student to the database using SSIS
>
> - ==Excute SQL Task== from toolbox to control flow
> - right click on the task => edit => connection => new connection => Microsoft OLE DB Provider for SQL Server => server name (if you have multiple instances) or (.) localhost => database name (ITI)=> test connection => ok
> - SQlSourceType: Direct input means we will write the query directly in the task
> - SQLStatement: `insert into students (st_id, st_fname) values (21, 'ahmed')`
> - ok
> - connection manager: now has the connection string to the server.database
> - to execute package => right click on the package => execute package

> [!done] add tasks based on success or failure
>
> - in case of success => Backup Database
> - backup database task from the toolbox to the control flow
> - right click on the arrow between the tasks => success => backup database task
> - Backup Database Task => connection => new connection => Backup_ITI_DB => ok
> - Backup type: full
> - backup database task => destination => file system => create a new folder => next => next => finish
> - in case of failure => open the message box (Script Task)
> - script task from the toolbox to the control flow
> - right click on the arrow between the tasks => failure => script task
> - edit script
>
> ```csharp
> MessageBox.Show("Error in the package");
> ```
>
> - run => first time will backup the database => second time will show the message box `Error: Violation of primary key constraint`

> [!warning] Loops & Variables
>
> - From the toolbox => For Loop Container => drag and drop it to the control flow
> - drag and drop script task to the for loop container
> - to pass the value of the loop to the script task we need to use variables
> - right click on the package => variables => add variable => name: `counter` => data type: int32 => value: 0 (default value) => ok
> - for loop editor :
> - `InitExpression: @counter = 1` to start from 1
> - `EvalExpression: @counter <= 5` to loop 5 times
> - `AssignExpression: @counter = @counter + 1` to increment the counter
> - ok
> - double click on the script task => we need to read the value of the counter
> - 2 types of variables: system variables and user variables
> - we choose user variable we created `counter`
> - `ReadOnlyVariables: User::counter` to read the value of the counter
> - edit script
> - to show the value of the counter in the message box
> - we use DTS (Data Transference Service) to access the variables
>
> ```csharp
> MessageBox.Show(Dts.Variables["counter"].Value.ToString());// as message box only accepts string
> ```

> [!example] Bulk Insert from text file to database
>
>
> - `Excute SQL Task` from toolbox to truncate the table
> - `SqlStatement: truncate table students` : to delete all the data from the table
> - `Bulk Insert Task` from toolbox to control flow
> - specify the destination table
> - specify the source file `SourceConnection: Flat File Connection Manager` => new connection => browse => file name => ok
> - specify the format
> - `Column delimiter: comma (,)`
> - `Row delimiter: {CR}{LF}`
> - if the data in the file is not in right format it will give an error

> [!done] Transfer data of students to excel file but ==sorted==
>
> - from the toolbox => drag and drop the data flow task to the control flow
> - double click on the data flow task
> - toolbox will change to data flow items
> - drag and drop the source ( OLE DB Source) to the data flow
> - drag and drop the transformation (Sort) to the data flow
> - drag and drop the destination (Excel Destination) to the data flow
> - double click on the source => connection manager => choose the server.database => table or view => table name: students => columns => preview => ok
> - we can specify the columns we want to transfer
> - double click on the sort => choose the column to sort by => and the order (ascending or descending)
> - double click on the destination => Excel Connection Manager => new connection => browse => write the file name => ok
> - choose the sheet name => ok
> - we have to get mapping => map the columns from the source to the destination => ok
>   > [!error] to avoid the error
>   >
>   > - use `.csv` file instead of `.xlsx` file
>   >   or from solution explorer => right click on the project => properties => configuration properties => debug => run64bitruntime => false

> [!faq] transfer table from one database to another with transformation
>
> - from the toolbox => drag and drop the data flow task to the control flow
> - double click on the data flow task
> - Source: OLE DB Source
> - Destination: OLE DB Destination
> - Transformation: Concatenate (fname + lname) => Derived Column
> - `Character Map` to change the case of the column
> - `Data Conversion` to change the data type of the column we have to be careful with the data types to avoid errors (like converting string to int)
> - `Copy Column` to copy the column to another column
> - `Audit` this is related to the tool (will be explained later)
> - configure the source then link it to the transformation then concatenate
> - specify how to concatenate the columns (like `[fname] + " " + [lname]`)
> - can yous functions like `ISNULL` to replace null values,...
> - link it to `Character Map` to change the case of the column => can choose to replace the column or add a new column
> - link it to `Copy Column` to copy the column to another column => choose the column to copy and the column to copy to
> - link it to `Data Conversion` to change the data type of the column => choose the data type
> - link it to `Audit` to use system variables to get `PackageID`, `MachineName`, `UserName`,....
> - configure the destination
> - we can create a new table or use an existing table
> - the tool can get the columns with data types
> - link the transformation to the destination
> - map the columns from the source to the destination to remove the warning

> [!tip] export poeple than 23 years old to excel file
>
> - `Flat File Destination`
> - `Excel Destination` => `.xlsx` file
> - `Conditional Split` to split the data based on the condition
> - `Multicast` to send the data to multiple destinations without condition
> - configure the OLE DB Source to get the data from the database
> - configure the conditional split to split the data based on the condition
> - case1: Greater than 23 => `[st_age] > 23`
> - case2: Less than 23 => `![st_age] > 23`
> - to deal with the null values we have to use `ReplaceNull` to replace the null values with a specific value
> - `REPLACENULL([st_age], 0)` to replace the null values with 0
> - default output: `Equal`
> - then we link and specify the output to the destination `Greater`, `Less`, `Equal`
> - configure the 3 destinations
> - map the columns from the source to the destination to remove the warning
> - run the package

> [!warning] UNION data from 2 sources
>
> - `Union All` to union the data from 2 sources (without removing the duplicates)
> - `Merge` to union the data from 2 sources (with removing the duplicates) like Union - but the data should be ==sorted==
> - `Merge Join` like: inner join, left join, right join, full join the data but the data should be ==sorted==

---

## Lab 1


