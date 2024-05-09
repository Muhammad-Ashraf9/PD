> [!Danger] Course Evaluation
>
> - Labs 100%

---

> [!faq] What is Blazor used for?
>
> - Blazor is a web framework designed to run client-side in the browser on a WebAssembly-based .NET runtime (Blazor WebAssembly) or server-side on an ASP.NET Core host (Blazor Server). It allows you to build interactive web UIs using C# and Razor syntax instead of JavaScript.
> - Blazor is used for building interactive web UIs using C# instead of JavaScript.
> - instead of using C# to write server-side only,
> - Blazor used to create SPA (Single Page Application) - client side web application using C#.

> [!warning] Browser only understands HTML, CSS, and JavaScript
>
> - in Razor components, you can write C# code that runs on the server and is rendered to HTML, CSS, and JavaScript that the browser can understand.

> [!bug] JavaScript
>
> - JavaScript is an interpreted language, not a compiled language.
> -
> - Execute - interprator
> - Browser execute JavaScript code using JavaScript runtime.
> - Browser can understand not only JavaScript but all other server-side languages like C#, Java, Python, etc.

> [!tip] WASM (WebAssembly)
>
> - WASM is cross-platform language - near to binary code.(native)
> - in 2015, WASM was introduced.
> - in 2016, w3c (World Wide Web Consortium) made standard for WASM and called it WebAssembly.
> - Browser vendors like Google, Mozilla, Microsoft, and Apple started supporting WASM.
> - now browser can understand WASM code in addition to HTML, CSS, and JavaScript.
> - so if you write C# code and compile it to WASM, then browser can understand it.(or any other language that can be compiled to WASM)
> - but still there is limitations of the client-side (can't access local file system, can't connect to database, etc.)
>   > [!danger] It wouldn't be safe to allow client-side code to access the file system
>   >
>   > - because it would be a security risk that developers could exploit to access sensitive information on the user's computer.
>
> > [!done] JavaScript Sandbox
> >
> > - Browser vendors have created a sandbox for JavaScript to run in, which limits the code's access to the user's computer.
> > - Sandbox allows only limited APIs (Cookie, LocalStorage, Web socket, DOM, etc.)
> > - so any other language that can be compiled to WASM will also run in the same sandbox.(with limited APIs)

> [!tip] C++ & Web Applications
>
> - to make Desktop applications, compile C++ => exe (to make windows => compile on windows, to make mac => compile on mac)
> - to make Web applications, compile C++ => WASM

> [!warning] C#
>
> - to make c# croos-platform applications, compile C# => (dll/exe) - intermediate language (IL)
> - SDK (Development Kit + Runtime)
> - to be able to run this IL code on any platform, we need a runtime that can understand IL code and execute it.
> - .NET runtime is available for Windows, Mac, and Linux.(chose a compatible runtime for the platform)
> - this will translate IL code to native code (binary code) of the platform and execute it.
> - so Make C# run on Browser => use .NET runtime in the browser.
> - this transfer of IL code to WASM is done by .NET runtime in the browser.
> - but started from .NET 8 this transfer happens in the server when requesting the page. (as it will converted to WASM only once and cached in the server and sent to the browser)
> - previously dll/exe was sent instead of WASM.
> - with every request the server sends (file.WASM + .NET runtime) to the browser.
> - browser responsible to understand WASM and execute it.
> - but server (using different languages like C#, Java, Python, etc.) is responsible to generate WASM code.

> [!tip] Blazor web assembly
>
> - WebAssembly is much faster than javascript.
> - as it is near to binary code => runtime execute it directly without the need to be interpreted.

---

> [!tip] Create new Blazor project
>
> - we choose `Blazor WebAssembly Standalone app` template.
> - authentication type: `none`: otherwise alot of code will be generated for us.
> - ==uncheck== `Configure for HTTPS`: to make the connection secure.
> - check the other 3 options:
> - `Progressive Web Application (PWA)`: to make the app work offline.

> [!warning] Run Blazor project
>
> - it will be compiled to dll files.
> - when request the page, the server will send the following files:
> - dotnet runtime (written in js) + WASM files instead of dll files.
> - it will be cached in the browser and for other requests, will get only modified files.

> [!danger] Blazor is Component-based
>
> Package: `Microsoft.AspNetCore.Components.WebAssembly`
>
> - this package helps creating components in Blazor.
>
> - components are part of the UI.
> - contains HTML, CSS, and C# code.
> - components types:
>   1. Built-in components (like button, input, etc.)
>   2. Custom components (like header, footer, etc.) - build by developers.
>      - Page components - URL (like Home, About, etc.)
>      - non-page components - reusable components (like header, footer, etc.) - don't have URL associated with them.
> - for every component, class will be created for this component ( inheriting from `ComponentBase` class)

> [!tip] `LaunchSettings.json`
>
> - this only in development.
> - `profiles` section: contains the configurations for the project.
> - when run the project, it will run the configuration with the name `http` /`IISEXPRESS`

> [!warning] `wwwroot` folder
>
> - contains static files (like images, css, js, etc.)
> - `index.html` file is the entry point for the application. (SPA - Single Page Application)
> - to add `appsettings.json` file, add it to the `wwwroot` folder as all other folders will be compiled to dll files.
> - but won't use it to add connection string as it is like javascript we have only limited APIs.

> [!danger] `index.html`
>
> - contains the following:
>   - id => `app` tag: where the components will be rendered.
>   - id => `blazor-error-ui`: will be displayed if there is an error in the application.(more user-friendly error message than Angular)
> - contains the following scripts:
> - `blazor.webassembly.js`: responsible for loading the .NET runtime in the browser.(most important script)
> - from this script will request the .NET runtime and WASM files essential for the application to run.

> [!danger] `Program.cs`
>
> - contains the following:
>   - `Main` method: entry point for the application.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        //WebAssemblyHostBuilder: responsible for building the application.

        //component-based
        builder.RootComponents.Add<App>("#app");//root component
        //this will render the App component in the div with id app in the index.html file.
        //will replace the svg Loading... with the App component.
        //Single Page Application (SPA) - with one title

        //to make components change the title of the page
        builder.RootComponents.Add<HeadOutlet>("head::after");
        //HeadOutlet: is built-in component to change the title of the page.

        await builder.Build().RunAsync();//used to run the application.
    }
}
```

```razor
<!--
    App.razor
 -->
<Router AppAssembly="@typeof(Program).Assembly">
//Router: built-in component to handle the routing in the application.
//contains the routes of the pages in the application.
    <Found Context="routeData">
    //in case the route is found
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
        //RouteView: built-in component to render the component associated with the route.
        <FocusOnNavigate RouteData="@routeData" Selector="h1" />

    </Found>
    <NotFound>
    //in case the route is not found
    //will generate a page with the title Not found and a message.

    <PageTitle>Not found</PageTitle>
    //built-in component to change the title of the page (HeadOutlet)
        <LayoutView Layout="@typeof(MainLayout)">
        //will load the MainLayout component.
            <p>Sorry, there's nothing at this address.     :(</p>
        </LayoutView>
    </NotFound>
</Router>
```

```razor
<!--
    MainLayout.razor
 -->
<div class="page">
    <div class="sidebar">
        <NavMenu />
        <!--
            NavMenu: non-page custom component.
            as nav bar could be long
         -->
    </div>

    <div class="main">
        <div class="top-row px-4">
            <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
        </div>

        <div class="content px-4">
            @Body
            //this will render the component associated with the route or the NotFound component.
        </div>
    </div>
</div>
```

```razor
<!--
    NavMenu.razor
 -->
<ul class="nav flex-column">
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="" Match="NavLinkMatch.All">
            <span class="oi oi-home" aria-hidden="true"></span> Home
        </NavLink>
        <!--
            NavLink: built-in component to handle the navigation.
          will give the link the active class if the link href match the current page.
         -->
    </li>
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="counter">
            <span class="oi oi-plus" aria-hidden="true"></span> Counter
        </NavLink>
    </li>
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="fetchdata">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Fetch data
        </NavLink>
    </li>
</ul>
```

> [!danger] `_Imports.razor`
>
> - to import the namespaces that will be used in the application.
> - to avoid writing the namespaces in every file.
> - shared file between all the components in the application.

> [!warning] `GlobalUsings.cs`
>
> - good practice to use global usings.
> - to share the namespaces between not only the components but also the services and other classes in the application.
> - this works in all .NET projects not only Blazor.

```csharp
global using BlazorDay1.Shared;
global using System.Net.Http;
```

> [!tip] Shared folder vs Pages folder
>
> - shared folder:
> - contains the shared components between the pages.
> - Pages folder:
> - contains the page components (like Home, Counter, FetchData, etc.) has URL associated with them.

> [!info] create new page
>
> - create new component in the Pages folder.
> - to add routing for this page, `@page "/newpage"` at the top of the component.
> - to change the title of the page, add `<PageTitle>New Page</PageTitle>` in the component.
> - add a new Link in the NavMenu component to navigate to this page.
> - to add a message in the page, add a property in the component and use it in the component.
> - add button to change the message using a method in the component and `@onclick` attribute in the button.
> - client

```razor
<!--
    NewPage.razor
 -->
@page "/newpage"
<PageTitle>New Page</PageTitle>
<h3>New Page</h3>
<h3>@Message</h3>
<button @onclick="ChangeMessage">Change Message</button>

@code {
    public string Message { get; set; } = "Hello, world!";
    private void ChangeMessage()
    {
        Console.WriteLine("Button clicked");
    }
}
```

---

# Break

---

> [!tip] 1 way data binding
>
> - source (c# ) => target (html)

```razor
<!--
    NewPage.razor
 -->

<button @onclick="ChangeMessage">Change Message</button>
<input type="text" value="@Message" />

```

> [!warning] 2 way data binding
>
> - source (html) => target (c#)
> - instead of using `value` attribute, use `@bind-value` => this will change the value of the c# property when focus out of the input.(blur)
>   > [!done] to make it onInput event, use `@bind-value:event="oninput"`
>   >
>   > - default is `onchange` event.

```razor
<!--
    NewPage.razor
 -->

<input type="text" @bind-value="@Message"  @bind-value:event="oninput" />
```

> [!danger]

```cs
//Employee.cs
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Salary { get; set; }
    public string ImageUrl { get; set; }

}
```

> [!warning] prefer to use postfix `Component` for the components
>
> - not to confuse between the components and the classes.
> - put the images in the `wwwroot` folder.
> - class will be created for the component with the same name as the component.

```razor
//EmployeeComponent.razor
<!-- @using BlazorDay1.Models -->
<!--
    instead we can use _Imports.razor file to import the namespace.
 -->
 <!--
    or use global usings in GlobalUsings.cs file.
  -->

@page "/employee"
<PageTitle>Employee</PageTitle>
<h3>Employee</h3>

<p>Id: @employee.Id</p>
//will throw an error as employee is null.
// we need to initialize the employee object.
<img src="/Images/@employee.ImageUrl" alt="Employee Image" style="width: 100px;" />

@code {
    private Employee employee { get; set; }

    //we can initialize the employee object here.
    public EmployeeComponent()
    {
        employee = new Employee(){Id = 1, Name = "Ahmed", Salary = "1000", ImageUrl = "Man.png"};
    }
}
```

> [!done] to achieve separation of concerns
>
> - create class with the same name as the component and put the logic in it with same extension + `.razor.cs`
>   > [!danger] compile will throw error as the class is not partial

```csharp
//EmployeeComponent.razor.cs
public partial class EmployeeComponent
{
    // public Employee employee { get; set; }

    // public Employee employee { get; set; } = new Employee(){Id = 1, Name = "Ahmed", Salary = "1000", ImageUrl="Man.png"};
    // is the same as the following


    //we can initialize the employee object here.
    public EmployeeComponent()
    {
        employee = new Employee(){Id = 1, Name = "Ahmed", Salary = "1000", ImageUrl= "Man.png"};
    }
}
```

```razor
//EmployeeComponent.razor
@page "/employee"
<PageTitle>Employee</PageTitle>
<h3>Employee</h3>
<h3>@employee.Name</h3>

<img src="/Images/@employee.ImageUrl" alt="Employee Image" style="width: 100px;" />

//we can bind the employee name to the input field.
<input type="text" @bind-value="@employee.Name" />

```

---

### All Employees component

```razor
//AllEmployeesComponent.razor

@page "/allemployees"
<PageTitle>All Employees List</PageTitle>
<h3>All Employees List</h3>

<select @bind="selectedEmployeeId">
//to bind the selected value to the selectedEmployeeId property.
    @foreach (var emp in Employees)
    {
        <option value="@emp.Id">@emp.Name</option>
    }
</select>
<h3>@selectedEmployeeId</h3>

<button @onclick="GetEmployee">Get Employee</button>

@if(EmpObj != null)
{
    <p>Id: @EmpObj.Id</p>
    <p>Name: @EmpObj.Name</p>
    <p>Salary: @EmpObj.Salary</p>
    <img src="/Images/@EmpObj.ImageUrl" alt="Employee Image" style="width: 100px;" />
}else
{
    <p>No Employee Selected</p>

}

@code {
    public List<Employee> Employees { get; set; }
    public int selectedEmployeeId { get; set; }
    public Employee EmpObj { get; set; }
    public AllEmployeesComponent()
    {
        Employees = new List<Employee>()
        {
            Employees = new List<Employee>();
            Employees.Add(new Employee(){Id = 1, Name = "Ahmed", Salary = "1000", ImageUrl="Man.png"});
            Employees.Add(new Employee(){Id = 2, Name = "Ahmed", Salary = "1000", ImageUrl="Man.png"});
            Employees.Add(new Employee(){Id = 3, Name = "Ahmed", Salary = "1000", ImageUrl="Man.png"});
            Employees.Add(new Employee(){Id = 4, Name = "Ahmed", Salary = "1000", ImageUrl="Man.png"});
        };

        private void GetEmployee()
        {
             EmpObj = Employees.FirstOrDefault(e => e.Id == selectedEmployeeId);

        }


    }
}
```

> [!warning] to send parameters to method in the component
>
> - use anonymous method to send the parameter to the method.

```razor
<button @onclick="()=>GetEmployee(1)">Get Employee</button>
```

---

## Lab

- do the same with Product(id, name, price, image,catId)
- seperate the logic in the component to another file.
