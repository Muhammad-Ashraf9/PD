### ADO.NET

> [!tip] ADO.NET
>
> - ADO.NET is a set of classes that expose data access services for .NET Framework
> - ADO.NET provides 2 main categories of classes: Data Providers and DataSets
> - Data Providers: ADO.NET uses data providers to connect to a data source and retrieve data(based on the data source)
> - DataSets: ADO.NET uses DataSets to store data in memory and work with it

> [!bug] Data Providers
>
> - Connection: SqlConnection=> used to connect to a SQL Server database
> - Command: SqlCommand=> used to execute a SQL query against a SQL Server database
> - DataReader: SqlDataReader=> used to read a forward-only stream of rows from a SQL Server database

> [!warning] Nugget
>
> - is a package manager for .NET
> - used to install different drivers (sql client, mysql client, etc) to connect to different databases

> [!example] disconnected mode
>
> - DataAdapter: SqlDataAdapter=> used to retrieve data from a SQL Server database and populate a DataSet in memory
> - DataSet: consists of DataTables=> collection of rows and columns, Relations=> collection of relationships between DataTables.

---

> -using TestADO database

> [!done] steps
>
> - Create a new console application
> - using NuGet Package Manager(like npm)
> - install Microsoft.Data.SqlClient (with `core` but with .NET: System.Data.SqlClient) Latest stable version
> - now we can use provided classes: SqlConnection, SqlCommand, sqlException,...

```csharp
using System;
using Microsoft.Data.SqlClient;
namespace Demo3
{
    class Program
    {
        static void Main(string[] args)
        {
            //create a sql connection object using the connection string
            SqlConnection sqlConnection = new SqlConnection("server=.;database=TestADO;integrated security=true;trustservercertificate=true");
            //we can use data source instead of server
            //integrated security=true means windows authentication

            //open the connection
            sqlConnection.Open();

            //close the connection
            sqlConnection.Close();

            //we use using statement to automatically close the connection (GC lec)
            using (SqlConnection sqlConnection = new SqlConnection("server=.;database=TestADO;integrated security=true;trustservercertificate=true"))
            {
                sqlConnection.Open();
            }

            //create a sql command object
            SqlCommand sqlCommand = new SqlCommand("select * from department", sqlConnection);
            //paramters: commandText: sql query, connection: sql connection object

            sqlCommand.CommandType = System.Data.CommandType.Text;//default
            sqlCommand.CommandType = System.Data.CommandType.StoredProcedure;//if we are calling a stored procedure

            //we need to open the connection before executing the command
            sqlConnection.Open();

            // sqlDataReader is used to read the data from the database
            SqlDataReader reader = sqlCommand.ExecuteReader();//this won't execute the query, but when we call read method, it will execute the query and read the data
            //executeReader returns a stream of rows from a SQL Server database


            //read the data
            while (reader.Read())//returns true if there are more rows to read
            {
                //using index
                Console.WriteLine(reader[0] + " " + reader[1] + " " + reader[2]);
                //using column name
                Console.WriteLine(reader["deptid"] + " " + reader["dname"] + " " + reader["loc"]);

                //if we close the connection here, we can't read the data
                sqlConnection.Close();//will throw an exception: ExecuteReader requires an open and available Connection. The connection's current state is closed.
            }

            //close the connection
            sqlConnection.Close();
//////////////////////////////////////////
            sqlConnection.Open();
            sqlConnection.Open();//open the connection again, will throw an exception:

            //we can check if the connection is already open or not
            if (sqlConnection.State == System.Data.ConnectionState.Closed)
            {
                sqlConnection.Open();
            }


        }
    }
}
```

> [!done] sql server profiler
>
> - used to trace the sql queries executed by the application

> [!bug] connection string
>
> - should be stored in a config file so it can be changed without changing the code and recompiling the application
> - we can create appSettings.json file
> - add the connection string to the appSettings.json file
>
> ```json
> {
>   "ConnectionString": "server=.;database=TestADO;integrated security=true;trustservercertificate=true"
> }
> ```
>
> - make sure to set the `Copy to Output Directory` property to `Copy always` for the appSettings.json file so that it is copied to the output directory when the application is built
> - read the connection string from the appSettings.json file
> - install `Microsoft.Extensions.Configuration.Json `, `Microsoft.Extensions.Configuration`
> - use the IConfiguration interface to read the connection string from the appSettings.json file

```csharp
using System;
using Microsoft.Data.SqlClient;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.Json;

namespace Demo3
{
    class Program
    {
        static void Main(string[] args)
        {
            //read the connection string from the appSettings.json file
           // or var configuration
            var connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;

            //create a sql connection object using the connection string
            SqlConnection sqlConnection = new SqlConnection(connectionString);
            //open the connection
            sqlConnection.Open();

            //create a sql command object
            SqlCommand sqlCommand = new SqlCommand("select * from department", sqlConnection);

            //we need to open the connection before executing the command
            sqlConnection.Open();

            // sqlDataReader is used to read the data from the database
            SqlDataReader reader = sqlCommand.ExecuteReader();

            //read the data
            while (reader.Read())
            {
                Console.WriteLine(reader[0] + " " + reader[1] + " " + reader[2]);
            }

            //close the connection
            sqlConnection.Close();
        }
    }
}
```

> [!example] publish the application
>
> - right click on the project
> - click on publish
> - select folder
> - click on publish
> - the application will be published to the specified folder
>   > [!done] now we can easily change the connection string in the appSettings.json file without changing the code and recompiling the application

---

# Break

---

```csharp
using System;
using Microsoft.Data.SqlClient;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.Json;

namespace Demo3
{
    class Program
    {
        static void Main(string[] args)
        {
            //read the connection string from the appSettings.json file
            var connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;

            //create a sql connection object using the connection string
            SqlConnection sqlConnection = new SqlConnection(connectionString);
            //open the connection
            sqlConnection.Open();

            //create a sql command object
            SqlCommand sqlCommand = new SqlCommand("insert into department(deptid, deptname, capacity,status) values(5, 'OS', 100, true)", sqlConnection);
            //but this is not a good practice, as it is vulnerable to sql injection
            //we should use parameters

            SqlCommand sqlCommand = new SqlCommand("insert into department(deptid, deptname, capacity,status) values(@deptid, @deptname, @capacity, @status)", sqlConnection);

            SqlParameter parameter1 = new SqlParameter(){ParameterName = "@deptid", Value = 6, SqlDbType = System.Data.SqlDbType.Int, Direction = System.Data.ParameterDirection.Input};
            //has to be the same name as in the query

            SqlParameter parameter2 = new SqlParameter(){ParameterName = "@deptname", Value = "OS", SqlDbType = System.Data.SqlDbType.NVarChar, Direction = System.Data.ParameterDirection.Input};
            SqlParameter parameter3 = new SqlParameter(){ParameterName = "@capacity", Value = 100, SqlDbType = System.Data.SqlDbType.Int, Direction = System.Data.ParameterDirection.Input};
            SqlParameter parameter4 = new SqlParameter(){ParameterName = "@status", Value = true, SqlDbType = System.Data.SqlDbType.Bit, Direction = System.Data.ParameterDirection.Input};

            //add the parameters to the command
            sqlCommand.Parameters.Add(parameter1);
            sqlCommand.Parameters.Add(parameter2);
            sqlCommand.Parameters.Add(parameter3);
            sqlCommand.Parameters.Add(parameter4);



            // execute the command insert
             int rowsAffected = sqlCommand.ExecuteNonQuery();//returns the number of rows affected

            //close the connection
            sqlConnection.Close();
        }
    }
}
```

> [!done] stored procedure
>
> ```sql
> create procedure sp_insert_department
> @deptid int, @deptname nvarchar(50), @capacity int, @status bit --name of the parameters should be the same as in the query
> as
> begin
> insert into department(deptid, deptname, capacity,status) values(@deptid, @deptname, @capacity, @status)
> end
> ```

```csharp
using System;
using Microsoft.Data.SqlClient;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.Json;

namespace Demo3
{
    class Program
    {
        static void Main(string[] args)
        {
            //read the connection string from the appSettings.json file
            var connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;

            //create a sql connection object using the connection string
            SqlConnection sqlConnection = new SqlConnection(connectionString);
            //open the connection
            sqlConnection.Open();

            //create a sql command object
            SqlCommand sqlCommand = new SqlCommand("sp_insert_department", sqlConnection);
            //change the command type to stored procedure
            sqlCommand.CommandType = System.Data.CommandType.StoredProcedure;

            SqlParameter parameter1 = new SqlParameter(){ParameterName = "@deptid", Value = 7, SqlDbType = System.Data.SqlDbType.Int, Direction = System.Data.ParameterDirection.Input};
            SqlParameter parameter2 = new SqlParameter(){ParameterName = "@deptname", Value = "OS", SqlDbType = System.Data.SqlDbType.NVarChar, Direction = System.Data.ParameterDirection.Input};
            SqlParameter parameter3 = new SqlParameter(){ParameterName = "@capacity", Value = 100, SqlDbType = System.Data.SqlDbType.Int, Direction = System.Data.ParameterDirection.Input};
            SqlParameter parameter4 = new SqlParameter(){ParameterName = "@status", Value = 1, SqlDbType = System.Data.SqlDbType.Bit, Direction = System.Data.ParameterDirection.Input};

            sqlCommand.Parameters.Add(parameter1);
            sqlCommand.Parameters.Add(parameter2);
            sqlCommand.Parameters.Add(parameter3);
            sqlCommand.Parameters.Add(parameter4);

            //we need to open the connection before executing the command
            sqlConnection.Open();

            // execute the command insert
            int rowsAffected = sqlCommand.ExecuteNonQuery();

            //close the connection
            sqlConnection.Close();
        }
    }
}
```

> [!bug] we want to return the identity value of the inserted row
>
> - if we have a column with identity property, we can use `SCOPE_IDENTITY()` to return the identity value of the inserted row

```csharp
using System;
using Microsoft.Data.SqlClient;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.Json;

namespace Demo3
{
    class Program
    {
        static void Main(string[] args)
        {
            //read the connection string from the appSettings.json file
            var connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;

            //create a sql connection object using the connection string
            SqlConnection sqlConnection = new SqlConnection(connectionString);

            //create a sql command object
            SqlCommand sqlCommand = new SqlCommand("insert into department(deptid, deptname, capacity,status) values(44, 'OS', 100, true); select CAST(scope_identity() AS int)", sqlConnection);



            //we need to open the connection before executing the command
            sqlConnection.Open();

            int x = (int)sqlCommand.ExecuteScalar();//returns the first column of the first row in the result set returned by the query

            // execute the command insert
            int rowsAffected = sqlCommand.ExecuteNonQuery();

            //return the identity value of the inserted row
            sqlCommand.CommandText = "select SCOPE_IDENTITY()";
            int identityValue = Convert.ToInt32(sqlCommand.ExecuteScalar());

            //close the connection
            sqlConnection.Close();
        }
    }
}
```

> [!tip] update
>
> ```csharp
> SqlCommand sqlCommand = new SqlCommand("update department set deptname = 'OS' where deptid = 1", sqlConnection);
> sqlConnection.Open();
> int rowsAffected = sqlCommand.ExecuteNonQuery();
> sqlConnection.Close();
> ```
>
> > [!warning] always use parameters to avoid sql injection

> [!done] delete
> take input from the user
>
> ```csharp
> int id = int.Parse(Console.ReadLine());
> SqlCommand sqlCommand = new SqlCommand("delete from department where deptid=@id", sqlConnection);
> SqlParameter parameter = new SqlParameter(){ParameterName = "@id", Value = id, SqlDbType = System.Data.SqlDbType.Int, Direction = System.Data.ParameterDirection.Input};
> sqlConnection.Open();
> int rowsAffected = sqlCommand.ExecuteNonQuery();
> sqlConnection.Close();
> ```

>[!danger] all the above examples are in connected mode
>
> - we can use disconnected mode to work with data in memory
> - we can use DataAdapter to retrieve data from a SQL Server database and populate a DataSet in memory

```csharp
using System;
using Microsoft.Data.SqlClient;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.Json;

namespace Demo3
{
    class Program
    {
        static void Main(string[] args)
        {
            //read the connection string from the appSettings.json file
            var connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;

            //create a sql connection object using the connection string
            SqlConnection sqlConnection = new SqlConnection(connectionString);

            //create a sql command object
            SqlCommand sqlCommand = new SqlCommand("select * from department", sqlConnection);

            //create a sql data adapter object taking the command object as a parameter
            SqlDataAdapter sqlDataAdapter = new SqlDataAdapter(sqlCommand);

            //create update, insert and delete commands for the data adapter
            SqlCommandBuilder sqlCommandBuilder = new SqlCommandBuilder(sqlDataAdapter);
     
sqlDataAdapter.UpdateCommand = sqlCommandBuilder.GetUpdateCommand();//returns the update command
            Console.WriteLine(sqlCommandBuilder.GetUpdateCommand().CommandText);//update department set deptname = @p1, capacity = @p2, status = @p3 where deptid = @p4

            //create a data table object
            DataTable dataTable = new DataTable();

            //fill the data table with the data from the database
            sqlDataAdapter.Fill(dataTable);//this will open  the connection, execute the query, read the data and close the connection

            foreach (DataRow row in dataTable.Rows)
            {
                //print row
                Console.WriteLine(row);//System.Data.DataRow
                //print columns using index
                Console.WriteLine(row[0] + " " + row[1] + " " + row[2] + " " + row[3]);
                //print columns using column name
                Console.WriteLine(row["deptid"] + " " + row["deptname"] + " " + row["capacity"] + " " + row["status"]);
            }
            //we can read the data again without executing the query again as the data is already in memory unlike connected mode
            foreach (DataRow row in dataTable.Rows)
            {
                Console.WriteLine(row["deptid"] + " " + row["deptname"] + " " + row["capacity"] + " " + row["status"]);
                //display state of the row
                Console.WriteLine(row.RowState);//Unchanged as the data is not updated in memory

                //on update the state will be modified
                //on delete the state will be deleted
                //on insert the state will be added
            }

            //update the data in the data table
            dataTable.Rows[0][1] = "OS";//change the deptname of the first row to OS in memory
            dataTable.Rows[0][2] = 200;//change the capacity of the first row to 200 in memory
            //data is not updated in the database yet
            
            //delete the first row
            dataTable.Rows[0].Delete();//mark the first row as deleted in memory
            //data is not deleted in the database yet

            //insert a new row
            DataRow newRow = dataTable.NewRow();//create a new row
            newRow["deptid"] = 6;
            newRow["deptname"] = "OS";
            newRow["capacity"] = 100;
            newRow["status"] = true;
            //Before
            Console.WriteLine(newRow.RowState);//Detached

            dataTable.Rows.Add(newRow);//add the new row to the data table  
            //After
            Console.WriteLine(newRow.RowState);//Added


            sqlDataAdapter.Update(dataTable);//this will open the connection, execute the query, read the data and close the connection
            //now the data is updated in the database
        }
    }
}
```
>[!tip] SqlCommandBuilder
>   
>   - we can use SqlCommandBuilder to generate the insert, update and delete commands for the data adapter based on the select command

---
### Lab

>[!done] lab
> - sqlConnection, sqlCommand, sqlDataReader
> - connected mode
> - create a new database called `TestADO`
> - read department
> - insert (text, identity)
> - update (stored procedure)
> - delete (stored procedure)
> - class student => List`<student>`
> >[!example] disconnected mode
> - SqlDataAdapter, DataTable, SqlCommandBuilder
> - same with student table

---


> [!danger] to get All notes (`.md` files)
>
> - link: [https://github.com/Muhammad-Ashraf9/PD](https://github.com/Muhammad-Ashraf9/PD)