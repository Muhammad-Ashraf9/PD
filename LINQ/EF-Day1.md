### Transactions

```csharp
using System.Data.SqlClient;

namespace Demo1
{
    class Program
    {
        static void Main(string[] args)
        {
                //read the connection string from the appSettings.json file
            var connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;

            //create a sql connection object using the connection string
            SqlConnection connection = new SqlConnection(connectionString);
            //open the connection
            connection.Open();

            {
                SqlTransaction transaction = connection.BeginTransaction();
                try
                {
                    SqlCommand command = new SqlCommand("INSERT INTO Employees VALUES(1, 'John', 1000)", connection, transaction);
                    command.ExecuteNonQuery();
                    command = new SqlCommand("INSERT INTO Employees VALUES(2, 'Mike', 2000)", connection, transaction);
                    command.ExecuteNonQuery();
                    transaction.Commit();
                }
                catch (SqlException ex)
                {
                    transaction.Rollback();
                }
            }
        }
    }
}
```

---

> [!tip] steps
>
> - create new windows form application
> - add list box and name it `listBoxDepartment`
> - install `Microsoft.Data.SqlClient` package using NuGet package manager=> this will add to item group in `.csproj file`
> - install `Microsoft.Extensions.Configuration`
> - install `Microsoft.Extensions.Configuration.Json`
> - disable nullable in `.csproj file` for warnings
> - add `appSettings.json` file
> - add connection string in `appSettings.json` file
>
> ```json
> {
>   "ConnectionString": "Data Source=.;Initial Catalog=testADO;Integrated Security=True"
> }
> ```
>
> is the same as
>
> ```json
> {
>   "ConnectionString": "Server=.;Database=testADO;Trusted_Connection=True;"
> }
> ```

```csharp
using Microsoft.Extensions.Configuration;
using System;
using System.Data.SqlClient;
using System.Windows.Forms;

public partial class Form1 : Form
{
    string connectionString = string.Empty;
    SqlConnection connection;
    SqlCommand command;
    public Form1()
    {
        connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;
        connection = new SqlConnection(connectionString);
        command = new SqlCommand("SELECT * FROM Department", connection);

        InitializeComponent();
    }

  //on load event of the form add the department names to the list box
    private void Form1_Load(object sender, EventArgs e)
    {
        connection.Open();
        SqlDataReader reader = command.ExecuteReader();
        while (reader.Read())
        {
            listBoxDepartment.Items.Clear();//clear the list box
            listBoxDepartment.Items.Add(reader["deptName"]);//add the department name to the list box

            // reader.GetInt16(0);//instead of casting manually

            // add anonymous type
            listBoxDepartment.Items.Add(new { Id = reader[0], Name = reader[1] });
        }
        connection.Close();
    }
}
```

> [!bug] we can define inner class in the form class to hold the data and bind it to the list box
>
> - department class
> - NOT RECOMMENDED

```csharp
public partial class Form1 : Form
{
    public class Department// can be defined with any access modifier like public, private, protected, internal, protected internal
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public override string ToString()
        {
            return $"{Id} - {Name}";
        }
    }
    string connectionString = string.Empty;
    SqlConnection connection;
    SqlCommand command;
    public Form1()
    {
        connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;
        connection = new SqlConnection(connectionString);
        command = new SqlCommand("SELECT * FROM Department", connection);

        InitializeComponent();
    }

  //on load event of the form add the department names to the list box
    private void Form1_Load(object sender, EventArgs e)
    {
        connection.Open();
        SqlDataReader reader = command.ExecuteReader();
        // now it will show the name of the class in the list box instead of the department name

            // we want to show the name of the department store the id of the department in the list box
        listBoxDepartment.DisplayMember = "Name";//show the name of the department
        listBoxDepartment.ValueMember = "Id";//store the id of the department
        while (reader.Read())
        {
            listBoxDepartment.Items.Clear();//clear the list box
            //we use GetInt16  as reader[0] returns an object
            //same for GetString
            listBoxDepartment.Items.Add(new Department { Id = reader.GetInt16(0), Name = reader.GetString(1) });//add the department name to the list box

        }

        connection.Close();
    }
    //on selected index change event of the list box
    private void listBoxDepartment_SelectedIndexChanged(object sender, EventArgs e)
    {
       Text = (listBoxDepartment.SelectedItem as Department).Id.ToString();//show the id of the selected department in the title bar
    }
    // on click event of the button save update the department name in the database
    private void buttonSave_Click(object sender, EventArgs e)
    {
        string deptName = textBoxDepartmentName.Text;
        int deptId = (listBoxDepartment.SelectedItem as Department).Id;
        command.CommandText = $"UPDATE Department SET deptName = '{deptName}' WHERE deptId = {deptId}";
        //we have to clear parameters of a command previously used
        command.Parameters.Clear();
        connection.Open();//make sure the connection is open before executing the command
        int rowsAffected = command.ExecuteNonQuery();
        connection.Close();
        if (rowsAffected > 0)
        {
            MessageBox.Show("Department updated successfully");
        }
        else
        {
            MessageBox.Show("Department not updated");
        }
    }
}
```

> [!warning] selectedItem vs selectedValue vs selectedIndex
>
> - SelectedIndex returns the index of the selected item in the list box
> - SelectedText returns the text of the selected item in the list box
> - SelectedValue returns the value of the selected item in the list box
> - SelectedItem returns the selected item in the list box

---
>[!warning] `disconnected` mode


```csharp
public partial class Form2 : Form
{
    string connectionString = string.Empty;
    SqlConnection connection;
    SqlCommand command;
    DataTable dataTable;
    SqlDataAdapter adapter;
    public Form2()
    {
        connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;
        connection = new SqlConnection(connectionString);
        command = new SqlCommand("SELECT * FROM Department", connection);

        InitializeComponent();
    }

    private void Form2_Load(object sender, EventArgs e)
    {
        dataTable = new DataTable();
        adapter = new SqlDataAdapter(command);
        adapter.Fill(dataTable);

        //complex binding  => Data Source
        listBoxDepartment.DataSource = dataTable;//Data Source of the list box now has all the rows of the department table
        //this will display DataRowView in the list box
        
        listBoxDepartment.DisplayMember = "deptName";//show the name of the department
        listBoxDepartment.ValueMember = "deptId";//store the id of the department
        
       
    }

    private void listBoxDepartment_SelectedIndexChanged(object sender, EventArgs e)
    {
      Text = listBoxDepartment.SelectedValue.ToString();//show the id of the selected department in the title bar

    }

    //show department data in controls: label, numericUpDown, textBox, checkBox
    // deptId, deptName, capacity, status


}
```

> [!tip] on load event of the form show the department data in the controls

```csharp
private void Form2_Load(object sender, EventArgs e)
{
    dataTable = new DataTable();
    adapter = new SqlDataAdapter(command);
    adapter.Fill(dataTable);

    SqlCommandBuilder builder = new SqlCommandBuilder(adapter);//this will build the insert, update, delete commands for the adapter

    //we have to set the insert, update, delete commands for the adapter otherwise the adapter will not know how to update the data table in the database and will throw an exception
    adapter.InsertCommand = builder.GetInsertCommand();
    adapter.UpdateCommand = builder.GetUpdateCommand();
    adapter.DeleteCommand = builder.GetDeleteCommand();


    //complex binding  => Data Source => collection
    listBoxDepartment.DataSource = dataTable;

    listBoxDepartment.DisplayMember = "deptName";//show the name of the department
    listBoxDepartment.ValueMember = "deptId";//store the id of the department

    //show department data in controls: label, numericUpDown, textBox, checkBox
    // deptId, deptName, capacity, status

    //simle binding: property => field
    labelDeptId.DataBindings.Add("Text", dataTable, "deptId");
    textBoxDeptName.DataBindings.Add("Text", dataTable, "deptName");
    numericUpDownCapacity.DataBindings.Add("Value", dataTable, "capacity");
    checkBoxStatus.DataBindings.Add("Checked", dataTable, "status");
    
    //this done automatically by the binding source in both the list box and the controls
}
```

>[!warning] 2-way binding
> - the changes in the controls will be reflected in the data table
> - on save button click event update the data table in the database
```csharp
private void buttonSave_Click(object sender, EventArgs e)
{
    foreach (DataRow row in dataTable.Rows)
    {
        Debug.WriteLine(row.RowState);//this will show the state of the row in the output window

        // won't see any changes in the output window 
        //but when select other row and press save button it will show the changes in the output window
    }
    adapter.Update(dataTable);//but we want to build the command object to update the data table in load event of the form
}
```

>[!tip] `BindingSource`
> - we can use `BindingSource` to bind the data table to the list box and the controls
> - now we can use it to move between the rows of the data table (next, previous, first, last)



>[!tip] `BindingNavigator`
> - we can use `BindingNavigator` to move between the rows of the data table

```csharp
public partial class Form3 : Form
{
    string connectionString = string.Empty;
    SqlConnection connection;
    SqlCommand command;
    DataTable dataTable;
    SqlDataAdapter adapter;
    BindingSource bindingSource;
    BindingNavigator navigator;
    public Form3()
    {
        connectionString = new ConfigurationBuilder().AddJsonFile("appSettings.json").Build().GetSection("ConnectionString").Value;
        connection = new SqlConnection(connectionString);
        command = new SqlCommand("SELECT * FROM Department", connection);

        InitializeComponent();
    }

    private void Form3_Load(object sender, EventArgs e)
    {
        dataTable = new DataTable();
        adapter = new SqlDataAdapter(command);
        adapter.Fill(dataTable);

        SqlCommandBuilder builder = new SqlCommandBuilder(adapter);
        adapter.InsertCommand = builder.GetInsertCommand();
        adapter.UpdateCommand = builder.GetUpdateCommand();
        adapter.DeleteCommand = builder.GetDeleteCommand();

        bindingSource = new BindingSource(dataTable, "");//this will bind the data table to the binding source

        navigator = new BindingNavigator(true);//this will create a binding navigator
        // true => show the items in the navigator like add, delete, save, next, previous, first, last

        navigator.BindingSource = bindingSource;//this will bind the binding source to the navigator

        Controls.Add(navigator);//add the navigator to the form
        //any control we want to show in the form we have to add it to the Controls collection of the form

        
        listBoxDepartment.DataSource = bindingSource;//this will bind the binding source to the list box
        listBoxDepartment.DisplayMember = "deptName";//show the name of the department
        listBoxDepartment.ValueMember = "deptId";//store the id of the department

        //bind the controls to the binding source
        labelDeptId.DataBindings.Add("Text", bindingSource, "deptId");
        textBoxDeptName.DataBindings.Add("Text", bindingSource, "deptName");
        numericUpDownCapacity.DataBindings.Add("Value", bindingSource, "capacity");
        checkBoxStatus.DataBindings.Add("Checked", bindingSource, "status");
    }

    private void buttonSave_Click(object sender, EventArgs e)
    {
        adapter.Update(dataTable);
    }

    //on click event of the button next move to the next row in the data table
    private void buttonNext_Click(object sender, EventArgs e)
    {
        bindingSource.MoveNext();
    }

    //on click event of the button previous move to the previous row in the data table
    private void buttonPrevious_Click(object sender, EventArgs e)
    {
        bindingSource.MovePrevious();
    }

    //on click event of the button first move to the first row in the data table
    private void buttonFirst_Click(object sender, EventArgs e)
    {
        bindingSource.MoveFirst();
    }

    //on click event of the button last move to the last row in the data table
    private void buttonLast_Click(object sender, EventArgs e)
    {
        bindingSource.MoveLast();
    }
}
```
