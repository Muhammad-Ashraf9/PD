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
> - install Microsoft.Extensions.Configuration.Json , Microsoft.Extensions.Configuration
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

