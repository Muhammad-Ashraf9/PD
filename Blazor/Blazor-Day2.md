> [!warning] Outline
>
> - 1.Pass Parameter from Component to another Component
> - 2.  Component Life Cycle
> - 3.  Dependency Injection
> - 4.  Form Tag
> - 5.  Validation

> [!tip] Input Parameter
>
> - how to send parameter to component to display data based on parameter
> - Route `employee/{id}`
> - from another component

> [!warning]
>
> - use placeholder `{id}`
> - this will be replaced by actual value
> - use same case for property name and placeholder

```csharp
@page "/employee/{id}"

<h3>
    Employee Details for @id
</h3>

@code {
    public string id { get; set; }
}
```

> [!bug] this will throw error
>
> - `has a property matching the name 'Id', but it does not have [ParameterAttribute]`
> - does not have access to set value of id from route
>
>   > [!done] Add `[Parameter]` attribute to property
>   >
>   > - this will allow to set value from Route segment
>
> - Decorator: used to add feature to property

```csharp
@page "/employee/{id}"

<h3>
    Employee Details for @id
</h3>

@code {
    [Parameter]
    public string id { get; set; }
}

```

> [!bug] this will throw another error
>
> - as `id` is string, it will not be able to convert to `int` for the property
>   > [!done] Change property type to `int`

```csharp
@page "/employee/{id:int}"
//this will only accept integer value

<h3>
    Employee Details for @id
</h3>

@code {
    [Parameter]
    // decorator used to add features to property
    public int id { get; set; }
}
```

---

> [!tip] types of Components
>
> - Page Component
> - Non-Page Component (shared )

> [!warning] Reusable Component
>
> - can be used in multiple places
> - by using name of component as tag `<ComponentName></ComponentName>`
> - now the component is child of the parent component (the component where it is used)
> - HTML relations not OOP

```csharp
// Home.razor
@page "/"

<PageTitle> Home Page </PageTitle>

<Counter></Counter>
//counter is a child component of Home

```

> [!tip] Namspace
>
> - creating classes in any folder will take that folder as namespace `namespace ProjectName.FolderName`

> [!warning] How to pass data from parent to child component

```csharp
// Child.razor
<h3>
    Child Component
</h3>

<p> @Message </p>

@code {
    [Parameter]
    //no it will accept value from parent
    public string Message { get; set; } = "Hello from Child Component";//has to be
}
```

```csharp
// Parent.razor

@page "/parent"

<h3>
    Parent Component
</h3>

<Child></Child>
// Child is a child component of Parent
<child></child>
// can use multiple times

//to make different message for each child
<Child Message="Hello from Parent"></Child>


<Child Message="@LocalMessage"></Child>
//passing local message to child component

<input type="text" @bind-value="LocalMessage" />
//binding input to local message to change message passed to child component
// now any change in input will change message in child component

<p> After Child Component </p>
// this will be displayed after child component

@code {
    public string LocalMessage { get; set; } = "Local Message";
}
```

---

### Grand Parent to Child Component

> [!done] creating another folder to use same component names with different namespaces

> [!tip] How to pass data from Grand Parent to Child Component
>
> - make a property in Child Component with `[CascadingParameter]` attribute
> - this will accept value from any component that is up in hierarchy in the component tree (not only parent)
> - send value from Grand Parent to Child Component using `CascadingValue` component

```csharp
// Child.razor
<h3>
    Child Component
</h3>

@code {
    [CascadingParameter]
    //to make it accept value from any component that is up in hierarchy in the component tree (not only parent)
    //can be used for authentication, etc
    [CascadingParameter(Name = "Msg")]
    //to give a name to the parameter
    public string Message { get; set; } = "Hello from Child Component";//has to be a property with get and set

    [CascadingParameter]

    public int Age { get; set; }

}
```

```csharp
// Parent.razor
<h3>
    Parent Component
</h3>

<Child></Child>
```

```csharp
// GrandParent.razor
@page "/grandparent"
<h3>
    Grand Parent Component
</h3>

<Parent></Parent>
//now grand parent component contains parent component which contains child component

<CascadingValue Name="Msg" Value="LocalMessage">
// Value expects a Property not a string as it does not know if LocalMessage is a string or a property
//to send string value to child component
// <CascadingValue Name="Msg" Value="@("Hello from Grand Parent")">
    <CascadingValue Name="Age" Value="25">
//to send integer value to child component
        <Parent></Parent>
    </CascadingValue>
</CascadingValue>

@code {

    public string LocalMessage { get; set; } = "Local Message from Grand Parent";
}
```

---

### From Child to Parent Component

> [!tip] Using Event
>
> - Parent component subscribes to an event in child component
> - Create an event in child component
> - Subscribe to event in parent component
> - Fire event in child component

```csharp
// Child.razor
<h3>
    Child Component
</h3>

<p> @Counter </p>

<input type="button" @onclick="IncreaseCounter" />
//on click of button, IncreaseCounter method will be called

@code{
    public int Counter { get; set; } = 0;
    //we use event to notify parent component

    [Parameter]//now it is available to parent component

    public EventCallback<int> CounterChangedEvent { get; set; }
    // use type of EventCallback<T> where T is the type of value to be passed to parent component

    private void IncreaseCounter(){
        Counter++;
        CounterChanged.InvokeAsync(Counter);//this accepts int value as we have defined EventCallback<int>
        //will fire
    }

}
```

```csharp
// Parent.razor
@page "/parent"
<h3>
    Parent Component
</h3>

<h4> Counter in Parent: @LocalCounter </h4>

<Child CounterChangedEvent="GetCounterHandler"></Child>
//subscribe to event in child component

@code{
    public int LocalCounter { get; set; }

    public void GetCounterHandler(int counter){
        LocalCounter = counter;
    }
}
```

---

### Component Life Cycle

> [!tip] Class & Object
>
> - class has default constructor
> - when creating a constructor, default constructor is not available
> - constructor is called when object is created - initialization
> - destructor is called when object is destroyed - cleanup
> - same with components
> - but between initialization and cleanup, components have different stages

> [!warning] Component Life Cycle
>
> - Component inherits from `ComponentBase` class
> - inherits virtual methods Like `SetParameters`, `OnInitialized`, `OnParametersSet`
> - `SetParameters` searches for `[Parameter]` , `[CascadingParameter]` and set values
> - `OnInitialized` - called when component is initialized and use parameters from `SetParameters`
> - `OnParametersSet` - called when parameters are changed instead of calling destructor and constructor to create new object (called several times)
> - all has async versions (OnInitializedAsync, SetParametersAsync, etc)


![](Pasted%20image%2020240508121346.png)

```csharp
// LifeCycle.razor

@page "/lifecycle/{id:int}"

<h3>
    Component Life Cycle
</h3>

@code{

    [Parameter]
    public int Id { get; set; }
    //localhost:5000/lifecycle/11
    public LifeCycle(){
        Console.WriteLine($"Constructor Called Id:{Id}");
        //constructor Called Id:0
    }

    public override void SetParametersAsync(ParameterView parameters){
        Console.WriteLine($"SetParameters Called Id:{Id}");
        //SetParameters Called Id:0
        base.SetParametersAsync(parameters);//after this line all parameters are loaded
    }


    protected override void OnInitialized(){
        Console.WriteLine($"OnInitialized Called Id:{Id}");
        //OnInitialized Called Id:11

        base.OnInitialized();
    }

    protected override void OnParametersSet(){
        Console.WriteLine($"OnParametersSet Called Id:{Id}");
        //OnParametersSet Called Id:11
        //any change will call this method again

        // we can add any logic here
        // id = 100;//this will make id 100 every time id is changed
        //to add logic when parameters are changed (get emp with id)
        base.OnParametersSet();
    }
}
```

> [!warning] `OnParametersSet` vs `OnInitialized`
>
> - to make anything in component based on parameter, use `OnParametersSet` if it is going to change
> - `OnInitialized` is called only once when component is initialized
> - if parameter is not going to change, use `OnInitialized`

> [!tip] `virtual` keyword
>
> - used to allow derived class to override
> - if not overridden, base class method will be called

---

## Break

---

### Dependency Injection

> [!tip] Dependency Inverson Principle (DIP)
>
> - High level modules should not depend on low level modules (both should depend on abstractions)
> - like in MVC, we used to say not to make object of context or repository in controller.

> [!warning] Make a Service
>
> - create a folder `Services`
> - create generic interface `IService` with basic CRUD operations
> - create a class `EmployeeService` that inherits from `IService<Employee>`

> [!tip] add department to Employee
>
> - using Employee Model from previous day
> - add Department Class
> - add Department property to Employee `int DeptId`

```csharp
// Services/IService.cs
public interface IService<T>{
    List<T> GetAll();
   T GetById(int id);
}
```

```csharp
// Services/EmployeeService.cs
public interface EmployeeService : IService<Employee>
{
    List<Employee> employees = new List<Employee>();
    public EmployeeService(){
        employees.Add(new Employee{Id = 1, Name = "Omar Elemam", DeptId = 1});
        employees.Add(new Employee{Id = 2, Name = "Ali", DeptId = 2});
        employees.Add(new Employee{Id = 3, Name = "Ali tany", DeptId = 1});
    }

    public List<Employee> GetAll(){
        return employees;
    }

    public Employee GetById(int id){
        return employees.FirstOrDefault(e => e.Id == id);
    }

}
```

> [!tip] Department Service
>
> - create a new class `DepartmentService` that inherits from `IService<Department>`

> [!tip] Employee Pages
>
> - create a new folder `EmployeePages`
> - create a new component `EmployeeList.razor`

```csharp
// EmployeeList.razor
@page "/employees"

<h3>
    Employee List
</h3>

@code{
    public List<Employee> Employees { get; set; }
    //won't use constructor but we will use OnInitialized

    public EmployeeService employeeService { get; set; } = new EmployeeService();//but this is not recommended as we depend on concrete class EmployeeService

    public IService<Employee> employeeService { get; set; }= new EmployeeService();//this is better as we depend on interface IService<Employee> but still we are creating object of EmployeeService

    //dont create but ask => IOC Container (Service Provider) to create object of EmployeeService
    //better way is to use Dependency Injection


    [Inject]//to inject service
    public EmployeeService employeeService { get; set; }

    protected override void OnInitialized(){
        Employees = employeeService.GetAll();
        base.OnInitialized();
    }
}
```

> [!bug] using service globally
>
> - `global using BlazorApp.Services;`
> - to be available in all components + classes

> [!error] `There is no registered service of type 'BlazorApp.Services.EmployeeService'`
>
> > [!done] register service in `Program.cs`
> >
> > - `builder.Services.AddScoped<IService<Employee>, EmployeeService>();`
> > - register happens after paramters are set in components not the constructor

```csharp
// Program.cs
builder.Services.AddScoped<IService<Employee>, EmployeeService>();
```

> [!tip] `EmployeeDetails.razor`
>
> - place holder for employee details based on id `employees/{id:int}`

```csharp
@page "/employee/{id:int}"

<h3>
    Employee Details
</h3>

@if(EmployeeObj != null){//have to add this as EmployeeObj is null
    <p> Id: @EmployeeObj.Id </p>
    <p> Name: @EmployeeObj.Name </p>
    <p> Department: @EmployeeObj.DeptId </p>
}
else{
    <p> Employee Not Found </p>
}
<a href="/employees"> Back to Employees </a>

@code{
    [Parameter]
    public int id { get; set; }

    public Employee EmployeeObj { get; set; }

    [Inject]//ask

    public IService<Employee> EmployeeService { get; set; }

    protected override void OnInitialized(){
        EmployeeObj = EmployeeService.GetById(Id);
        base.OnInitialized();
    }
}
```

> [!danger] DO NOT USE injected service in constructor
>
> - as it is not available in constructor (not set yet)

---

## Form

> [!tip] Edit page
>
> - create a new component `EmployeeEdit.razor`
> - to edit employee details
> - add validation highlight

> [!warning] `EditForm`
>
> - built in component to handle form submission (create, update)
> - `Model` Object to bind form fields,valiation, etc

> [!tip] Input components
>
> - built in components to handle input fields
> - `InputText`, `InputNumber`, `InputSelect`, `InputDate`, etc

```csharp
@page "/employee/edit/{id:int}"

<h3>
    Edit Employee
</h3>

@if(EmployeeObj != null){
   <EditForm Model="EmployeeObj" OnSubmit="SaveData">
   //onSubmit will be called when form is submitted
   //onValidSubmit will be called when form is submitted and all fields are valid
   //onInvalidSubmit will be called when form is submitted and any field is invalid
   <div>
   <label> Name </label>
        <InputText @bind-Value="EmployeeObj.Name" />
        //any change in input will change EmployeeObj.Name
   </div>
    <div>
        <label> Salary </label>
        <InputNumber @bind-Value="EmployeeObj.Salary" />
        //we have to use InputNumber as Salary is of type int
    </div>
    <div>
        <label> Department </label>
        <InputNumber @bind-Value="EmployeeObj.DeptId" />
        //we have to use InputNumber as DeptId is of type int
    </div>
    <input type="submit" value="Save" />
    //this will submit the form when clicked

    </EditForm>
}
else{
    <p> Employee Not Found </p>
}

<a href="/employees"> Back to Employees </a>

@code{
    [Parameter]
    public int id { get; set; }


    public Employee EmployeeObj { get; set; }

    [Inject]
    public IService<Employee> EmployeeService { get; set; }

    [Inject]
    public NavigationManager NavigationManager { get; set; }//built in service to navigate and already registered
    //used to navigate to another component using c# not href

    //to link department to employee


    protected override void OnInitialized(){
        EmployeeObj = EmployeeService.GetById(Id);
        base.OnInitialized();
    }

    public void SaveData(){
        Console.WriteLine("Save Data Called");
        NavigationManager.NavigateTo("/employees");//navigate to employees page after saving data
    }
}
```

> [!faq] How data is saved across components?
>
> - `AddTransient` - new object is created every time the service is used or injected even in the same request
> - `AddSingleton` - same object is used across all users (multiple requests)
> - `AddScoped` - object is created once per request
> - first request - object is created and will be used for other injections in the same request
> - SPA - same request for all components
> - same object is used across all components in the same request
> - when going back to list it is the same request we see the same object

> [!tip] Link Employee to Department
>
> - Employee service should be of single responsibility so we create a new service `DepartmentService`
> - `EmployeeService` should not be responsible for getting departments

```csharp
// Services/DepartmentService.cs
public class DepartmentService : IService<Department>
{
    List<Department> departments = new List<Department>();
    public DepartmentService(){
        departments.Add(new Department{Id = 1, Name = "IT"});
        departments.Add(new Department{Id = 2, Name = "HR"});
        departments.Add(new Department{Id = 3, Name = "Finance"});
    }

    public List<Department> GetAll(){
        return departments;
    }

    public Department GetById(int id){
        return departments.FirstOrDefault(d => d.Id == id);
    }
}
```

```csharp
// Program.cs
builder.Services.AddScoped<IService<Department>, DepartmentService>();
```

```csharp
// EmployeeEdit.razor
@page "/employee/edit/{id:int}"

//injection can be done using @inject
// @inject IService<Department> DepartmentService
// is same as below

<h3>
    Edit Employee
</h3>

@if(EmployeeObj != null){
   <EditForm Model="EmployeeObj" OnSubmit="SaveData">
   <div>
   <label> Name </label>
        <InputText @bind-Value="EmployeeObj.Name" />
   </div>
    <div>
        <label> Salary </label>
        <InputNumber @bind-Value="EmployeeObj.Salary" />
    </div>
    <div>
        <label> Department </label>
        <InputSelect @bind-Value="EmployeeObj.DeptId">
        //to bind select to EmployeeObj.DeptId
            @foreach(var dept in Departments){
                <option value="@dept.Id"> @dept.Name </option>
            }
        </InputSelect>
    </div>
    <input type="submit" value="Save" />
    </EditForm>
}
else{
    <p> Employee Not Found </p>
}

<a href="/employees"> Back to Employees </a>

@code{
    [Parameter]
    public int id { get; set; }


    public Employee EmployeeObj { get; set; }

    [Inject]
    public IService<Employee> EmployeeService { get; set; }

    [Inject]
    public NavigationManager NavigationManager { get; set; }//built in service to navigate and already registered
    //used to navigate to another component using c# not href

    //to link department to employee
    [Inject]
    public IService<Department> DepartmentService { get; set; }

    public List<Department> Departments { get; set; }


    protected override void OnInitialized(){
        EmployeeObj = EmployeeService.GetById(Id);
        Departments = DepartmentService.GetAll();

        base.OnInitialized();
    }

    public void SaveData(){
        Console.WriteLine("Save Data Called");
        NavigationManager.NavigateTo("/employees");//navigate to employees page after saving data
    }
}
```

> [!done] we can split the code into separate file
>
> - `EmployeeEdit.razor` and `EmployeeEdit.razor.cs`

---

# Lab

- Product (id,name, price, catId)
- Category (id, name)

- interface IService<T>
- ProductService : IService<Product>
- CategoryService : IService<Category>

- all products
- product details
- edit product
- all using injection
