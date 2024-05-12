> [!warning] Outline
>
> - Client-side Validation
> - Connect Web API "Web Service"

---

# Client-side Validation

> [!tip] `<editform>`
>
> - The EditForm component is Blazor's way of handling forms.
> - has Model property that is bound to the form's data used for validation.
> - UX: green and red feedback for valid and invalid fields.

> [!tip] Data Annotations
>
> - add validation to Model properties
> - add feature Form Validation to EditForm
> - by default, EditForm will not validate using Data Annotations
> - we have to add the `DataAnnotationsValidator` component to the form to enable this feature.

```csharp
public class Employee
{
    public int Id { get; set; }
    [MinLength(3, ErrorMessage = "Name Must be at least 3 characters long.")]
    [MaxLength(20, ErrorMessage = "Name Must be at most 20 characters long.")]
    public string Name { get; set; }
    [Range(6000, 25000, ErrorMessage = "Salary must be between 6000 and 25000")]
    public int Salary { get; set; }
    // . vs \. in regex
    //. is reserved for any character except newline
    //\. is the literal period character
    [RegularExpression(@"\w+\.+(jpg|jpeg|png)$", ErrorMessage = "Invalid Image should be jpg, jpeg or png")]
    public string ImageUrl { get; set; }
    public string DeptId { get; set; }
}
```

> [!tip] can make custom validator (inherit ValidationAttribute)

> [!tip] `DataAnnotationsValidator`
>
> - Add this component to the form to enable validation using Data Annotations.
> - This component will validate the form
> - we have to add `ValidationMessage` components to display error messages for invalid fields.

> [!danger] `ValidationSummary`
>
> - Display a summary of all validation errors in the form.
> - Add this component to the form to display a summary of all validation errors.

> [!warning] `onSubmit` event
>
> - this will be called when the form is submitted whether it is valid or not.
> - we can validation manually in the event handler (SaveData Method)
>   > [!done] `OnInValidSubmit` event
>   >
>   > - this will be called when the form is submitted and it is not valid.

```html
<EditForm
  Model="@employee"
  OnValidSubmit="@SaveData"
  OnInvalidSubmit="@HandleInvalidSubmit"
>
  <!-- to support validation -->
  <DataAnnotationsValidator></DataAnnotationsValidator>
  <!-- to display validation error messages -->
  <ValidationSummary></ValidationSummary>

  <div class="form-group">
    <label for="Name">Name</label>

    <InputText id="Name" class="form-control" @bind-Value="employee.Name" />
    <ValidationMessage For="() => employee.Name" />
  </div>
  <div class="form-group">
    <label for="Salary">Salary</label>
    <InputNumber
      id="Salary"
      class="form-control"
      @bind-Value="employee.Salary"
    />
    <ValidationMessage For="() => employee.Salary" />
  </div>
  <div class="form-group">
    <label for="ImageUrl">Image URL</label>
    <InputText
      id="ImageUrl"
      class="form-control"
      @bind-Value="employee.ImageUrl"
    />
    <ValidationMessage For="() => employee.ImageUrl" />
  </div>
  <button type="submit" class="btn btn-primary">Save</button>
</EditForm>
```

> [!done] `HandleInvalidSubmit` event

```csharp
//EditEmployeeComponent.razor.cs
public void saveData()
{
    //can validate manually here
    Console.WriteLine("Save Data");
    NavigationManager.NavigateTo("/employees");
}

private void HandleInvalidSubmit()
{
    Console.WriteLine("Invalid Submit");
    // we can use bool to show/hide error message
    IsInvalidSubmit = true;
}
```

> [!done] check if form is valid

```html
if (IsInvalidSubmit) {
<div class="alert alert-danger">Please fix the errors and try again.</div>
}
```

> [!tip] Custom Validation `UniqueIdAttribute`
>
> - how to check unique id

```csharp
public class UniqueIdAttribute : ValidationAttribute
{
    protected override bool IsValid(object? value, )
    {
      // call web service to check if id is unique (API)
        if (employee != null)
        {
            return false;
        }
        return true;
    }
}
```

---

### Connect Web API "Web Service"

> [!warning] Blazor can not communicate with database directly
>
> - Blazor will communicate with Web API and Web API will communicate with the database.
> - Blazor is a client-side technology (same as JS)
> - Sandboxed environment

> [!done] Swagger
>
> - check the API documentation to see the endpoints and the request/response format.
> - use request/response format to create the model classes in the Blazor application.

> [!warning] Standalone
>
> - Blazor standalone application can be installed on the client machine.

> [!done] Create Models based on API documentation
>
> - in Models folder
> - Employee.cs
> - Department.cs

```csharp
//Models/Employee.cs

public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Salary { get; set; }
    public string Address { get; set; }
    public string DeptId { get; set; }
}
```

```csharp
//Models/Department.cs

public class Department
{
    public string Id { get; set; }
    public string Name { get; set; }
}
```

> [!done] Create Service to call API
>
> - in Services folder
> - IService.cs (generic interface) for CRUD operations
> - EmployeeService.cs (implementation of IService) for Employee
> - DepartmentService.cs (implementation of IService) for Department

```csharp
//Services/IService.cs

public interface IService<T>
{
//    List<T> GetAll();
// use Task to make it async
    Task<List<T>> GetAll();
    Task<T> GetById(int id);
    Task Add(T model);
    Task Update(int id, T model);
    Task Delete(int id);
  }
```

> [!bug] Service consumer
>
> - API consumer: is the client that will consume the API.
> - the language of the client has to provide tool to build HTTP request.
> - JavaScript => ajax, fetch
> - .NET (wpf - console - MVC - Blazor) => consumer => HttpClient class
> - HttpClient class is used to send HTTP requests and receive HTTP responses from a web service.
> - HttpClient is a class in .NET so it can be used in any .NET application.
> - even Web API can consume another Web API.
> - angular has similar class to HttpClient in .NET

```csharp
//Services/EmployeeService.cs

public class EmployeeService : IService<Employee>
{
    HttpClient client;

    public EmployeeService()
    {
        client = new HttpClient();
        client.BaseAddress = new Uri("https://localhost:44300");
    }
     public async Task<List<Employee>> GetAll()
    {
        list<Employee> employees = await client.GetFromJsonAsync<List<Employee>>("/api/employees");//we add just the endpoint
        //be careful with the URI (not to add double slashes (//) in the URI)
        //or use all URI but without the base address above
        //client.GetFromJsonAsync<List<Employee>>("https://localhost:44300/api/employees");

    }

    public async Task<Employee> GetById(int id)
    {
        Employee employee = await client.GetFromJsonAsync<Employee>($"/api/employees/{id}");
        return employee;
    }


    public async Task Add(Employee model)
    {
        HttpResponseMessage response = await client.PostAsJsonAsync("/api/employees", model);
        if (response.IsSuccessStatusCode)
        {
            Console.WriteLine("Employee Added Successfully");
        }else {
            // to check on return data
            response.Content.ReadAsStringAsync();// to read the response
        }
    }

    public async Task Update(int id, Employee model)
    {
        HttpResponseMessage response = await client.PutAsJsonAsync($"/api/employees/{id}", model);
        if (response.IsSuccessStatusCode)
        {
            Console.WriteLine("Employee Updated Successfully");
        }
    }
    public async Task Delete(int id)
    {
        HttpResponseMessage response = await client.DeleteAsync($"/api/employees/{id}");
        if (response.IsSuccessStatusCode)
        {
            Console.WriteLine("Employee Deleted Successfully");
        }
    }
}
```

> [!done] we can create multiple models when needed in request/response


----
# Break 
----

>[!tip] All Employee Page   
> - inside Pages folder
> - create Employee folder
> - AllEmployeeComponent.razor
> - AllEmployeeComponent.razor.cs

>[!warning] Register Service
> - in Program.cs
```csharp
builder.Services.AddScoped<IService<Employee>, EmployeeService>();
```



>[!danger] `OnInitializedAsync` vs `OnInitialized`
> - `OnInitializedAsync` is used when we need to call async methods

```csharp
//Pages/Employee/AllEmployeeComponent.razor.cs

namespace BlazorApp.Pages.Employee
{
    public partial class AllEmployeeComponent
    {
        public List<Employee> Employees { get; set; } 
        [Inject]
        public IService<Employee> EmployeeService { get; set; }

        protected override async Task OnInitializedAsync()
        {
            Employees = await EmployeeService.GetAll();
            base.OnInitializedAsync();
        }
    }
}
```


```html
<!-- //Pages/Employee/AllEmployeeComponent.razor -->

@page "/employees"

<PageTitle>Employees</PageTitle>

@if (employees == null)
{
    <p>Loading...</p>
}
else
{
    <table class="table table-striped">
        <thead>
            <tr>
                <th>Id</th>
                <th>Name</th>
                <th>Salary</th>
                <th>Address</th>
                <th>DeptId</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var employee in employees)
            {
                <tr>
                    <td>@employee.Id</td>
                    <td>@employee.Name</td>
                    <td>@employee.Salary</td>
                    <td>@employee.Address</td>
                    <td>@employee.DeptId</td>
                </tr>
            }
        </tbody>
    </table>
}
```
>[!bug] `NullReferenceException`
> - it throws an exception when the list is null (before the data is loaded)
> - we can use `if` statement to check if the list is null or not
---

>[!tip] Employee Details Page
```csharp
//Pages/Employee/EmployeeDetailsComponent.razor.cs

namespace BlazorApp.Pages.Employee
{
    public partial class EmployeeDetailsComponent
    {
        [Parameter]
        public int Id { get; set; }
        public Employee Employee { get; set; }

        [Inject]
        public IService<Employee> EmployeeService { get; set; }

        protected override async Task OnInitializedAsync()
        {
            Employee = await EmployeeService.GetById(Id);
            base.OnInitializedAsync();
        }
    }
}
```

```html
<!-- //Pages/Employee/EmployeeDetailsComponent.razor -->

@page "/employee/{Id}"

<PageTitle>Employee Details</PageTitle>

@if (employee == null)
{
    <p>Loading...</p>
}
else
{
    <div class="card" style="width: 18rem;">
        <img class="card-img-top" src="@employee.ImageUrl" alt="Card image cap" />
        <div class="card-body">
            <h5 class="card-title">@employee.Name</h5>
            <p class="card-text">Salary: @employee.Salary</p>
            <p class="card-text">Address: @employee.Address</p>
            <p class="card-text">DeptId: @employee.DeptId</p>
        </div>
    </div>
}
```


---
>[!tip] edit Employee Page
```csharp
//Pages/Employee/EditEmployeeComponent.razor.cs

[Prameter]
public int Id { get; set; }
public Employee Employee { get; set; }

[Inject]
public IService<Employee> EmployeeService { get; set; }

[]

protected override async Task OnInitializedAsync()
{
    Employee = await EmployeeService.GetById(Id);
    base.OnInitializedAsync();
}

private Task SaveData()
{
  await  EmployeeService.Update(Id, Employee);//await to make sure the data is updated before navigating
    NavigationManager.NavigateTo("/employees");
}
```

```html
<!-- //Pages/Employee/EditEmployeeComponent.razor -->

@page "/employee/edit/{Id}"

<PageTitle>Edit Employee</PageTitle>

@if (employee == null)
{
    <p>Loading...</p>
}
else
{
    <EditForm Model="Employee" OnSubmit="SaveData">
        <div class="form-group">
            <label for="Name">Name</label>
            <InputText id="Name" class="form-control" @bind-Value="Employee.Name" />
            <ValidationMessage For="() => Employee.Name" />
        </div>
        <div class="form-group
            <label for="Salary">Salary</label>
            <InputNumber id="Salary" class="form-control" @bind-Value="Employee.Salary" />
            <ValidationMessage For="() => Employee.Salary" />
        </div>
        <div class="form-group">
            <label for="ImageUrl">Image URL</label>
            <InputText id="ImageUrl" class="form-control" @bind-Value="Employee.ImageUrl" />
            <ValidationMessage For="() => Employee.ImageUrl" />
        </div>
        <!-- 
            department should be a dropdown list
         -->
        <button type="submit" class="btn btn-primary">Save</button>
    </EditForm>
}
```

>[!bug] It is better to make department as a dropdown list
> - make sure to add the department service
> - add the department service to the DI container in Program.cs
> - add the department service to the EditEmployeeComponent.razor.cs

```csharp
//Services/DepartmentService.cs

public class DepartmentService : IService<Department>
{
    HttpClient client;

    public DepartmentService()
    {
        client = new HttpClient();
        client.BaseAddress = new Uri("https://localhost:44300");
    }
    public async Task<List<Department>> GetAll()
    {
        List<Department> departments = await client.GetFromJsonAsync<List<Department>>("/api/departments");
        return departments;
    }
//rest of the methods like EmployeeService
}
```

>[!danger] Register Department Service
```csharp
//Program.cs
builder.Services.AddScoped<IService<Department>, DepartmentService>();
```

```cs
// <!-- //Pages/Employee/EditEmployeeComponent.razor.cs -->

// rest of the code above
[Inject]
public IService<Department> DepartmentService { get; set; }

public List<Department> Departments { get; set; }

protected override async Task OnInitializedAsync()
{
    Employee = await EmployeeService.GetById(Id);
    Departments = await DepartmentService.GetAll();
    base.OnInitializedAsync();
}
```

```html
<!-- //Pages/Employee/EditEmployeeComponent.razor -->

<!-- 
    department should be a dropdown list
 -->
<div class="form-group">
    <label for="DeptId">Department</label>
    <select id="DeptId" class="form-control" @bind="Employee.DeptId">
        @foreach (var department in Departments)
        {
            <option value="@department.Id">@department.Name</option>
        }
    </select>
    <ValidationMessage For="() => Employee.DeptId" />
```

>[!warning]  API Base URL 
> - we can use appsettings.json to store the API URL instead of hardcoding it in the service
> - in wwwroot folder
> - create appsettings.json from the template 

>[!danger] inject HttpClient
> - instead of creating a new instance of HttpClient in the service
> - we can inject it in the constructor
> - it is in program.cs by default (replace only the base address)
```csharp
//Services/EmployeeService.cs

public class EmployeeService : IService<Employee>
{
    HttpClient client;

    public EmployeeService(HttpClient client)
    {
        this.client = client;
    }
    //rest of the code
}
```
```csharp
//appsettings.json
{
  "ApiBaseUrl": "https://localhost:44300"
}
```

```csharp
//Program.cs
builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.Configuration.GetValue<string>("ApiBaseUrl")) });
```


>[!faq] How to use multiple API URLs using HttpClient
> - like localhost API and google API
> - Cannot inject multiple instances of HttpClient for the with different base addresses
> - to connect to multiple providers, we install package `Microsoft.Extensions.Http`
> - instead of injecting only one and writing the rest in the constructor
> - we can use `IHttpClientFactory` to create named instances of HttpClient
> - now we can use `AddHttpClient` to register the named instances

```csharp
//Program.cs

// builder.Services.AddScoped<IService<Employee>, EmployeeService>();
builder.Services.AddHttpClient<IService<Employee>, EmployeeService>(client =>
{
    client.BaseAddress = new Uri(builder.Configuration.GetValue<string>("ApiBaseUrl"));
});

// builder.Services.AddScoped<IService<Department>, DepartmentService>();
builder.Services.AddHttpClient<IService<Department>, DepartmentService>(client =>
{
    client.BaseAddress = new Uri(builder.Configuration.GetValue<string>("ApiBaseUrl"));
});
```

>[!tip] to use another API URL
> - we can add another URL in appsettings.json 
> - and use it in another service

----
# Lab

- Consume any Web API => all, details, edit