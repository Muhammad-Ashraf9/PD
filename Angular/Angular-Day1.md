## Angular

> [!tip] Angular
>
> - Angular is a client-side framework which is used to build single-page applications(SPA).
> - SPA is a web application that loads a single HTML page and dynamically updates that page (DOM) as the user interacts with the app.

> [!warning] Server Side Rendering
>
> - Browser sends a request to the server.
> - Server sends a response with HTML, CSS, and JavaScript + Data.
> - Page is rendered on the server and sent to the browser.
> - Browser displays the page.

> [!danger] Client Side Rendering
>
> - Browser sends a request to the server.
> - Server sends a response with HTML, CSS, and JavaScript.
> - Browser displays the page.
> - on user interaction, the app sends requests to the server for data to update the page with the new data.
> - Web API is responsible for sending and receiving data from front-end.
> - Web API is responsible for communicating with databases.

> [!tip] TypeScript
>
> - Angular is written in TypeScript.
> - TypeScript is a superset of JavaScript.(any valid JavaScript code is also a valid TypeScript code)

> [!note] Angular Features
>
> - can change the dom easily.
> - can create custom html tags. (extend html)
> - MVVM(Model-View-ViewModel) architecture.
> - Angular is a Framework has a lot of built-in features (like routing, http client, etc).
> - react, vue are libraries.

> [!note] Angular Versions
>
> - AngularJS (1) - 2010
> - Angular 2 - 2016 (completely different from AngularJS - rewritten)
> - Angular 3 - skipped
> - Angular decided to release new versions every 6 months with backward compatibility.
> - Angular 4 - 2017
> - ... Angular 17

> [!tip] Components
>
> -The application is divided into components.
>
> - Components are the basic building blocks of Angular applications.
> - Any Component has its own HTML, CSS, and TypeScript file.
> - Components should be reusable.
> - Components could be a whole page or a part of a page.(like a header, footer, button, etc)

> [!warning] Angular Component
>
> - class
> - Angular Component is a TypeScript class.
> - Component Template is an HTML used to render the view.
> - Component Styles CSS are used to style the view.
> - to achieve separation of concerns, we should keep the template and styles in separate files. (HTML, CSS)
> - Component Data is the properties of the TypeScript class.

> [!danger] Directives
>
> - class
> - Directives are TypeScript classes that are used to manipulate the DOM easily.
> - Structural Directives: used to add or remove elements from the DOM based on a condition.
> - like `*ngFor`: add or remove elements from the DOM based on data in component class.
> - like `*ngIf`: add or remove elements from the DOM based on a condition.
> - like `*ngSwitch`:
> - Attribute Directives: used to change the appearance or behavior of an element, component, or another directive.
> - like `ngStyle`: to change the style of an element.
> - like `ngClass`: to add or remove classes from an element.

> [!tip] Angular 17
>
> - `if`, `for`, `switch` are the new directives in Angular 17.

> [!tip] Pipes
>
> - class
> - Pipes are used to format data before displaying it in the view.

> [!bug] Services
>
> - class
> - Having a common functionality or data that used in multiple components.
> - it is better to create a service (class) that has that functionality or data.
> - to use a service in a component, we create an instance of the service in the component class.
>   > [!done] Dependency Injection Container (DIC)
>   >
>   > - Angular has a built-in dependency injection system.
>   > - Angular creates an instance of the service and injects it into the component class.

> [!warning] Decorators
>
> - to differentiate the class as a component, directive, or service, we use decorators.
> - `@Component`, `@Directive`, `@Pipe`

> [!bug] Before Angular 16
>
> - the following was ==Mandatory==:
> - Any class has to be in a module.(Component, Directive, Pipe, Service)
> - Module is a class with `@NgModule` decorator.
> - like AdminModule has all the components, directives, pipes, and services related to the admin.
> - like namespace in C#.
> - to use a component, directive, pipe, or service in another module, we have to export it from the module.

> [!tip] Angular 17 & Standalone Components
>
> - Default to use Standalone Components.
> - this is recommended by Angular Team.
> - we will be using Standalone Components.

> [!note] Angular CLI
>
> - we have to install Angular CLI to create an Angular project.
> - first, we have to install Node.js.
> - `npm install -g @angular/cli` to install Angular CLI globally.
> - to specify the version of Angular CLI, we can use `npm install -g @angular/cli@version`.
> - `ng new project-name` to create a new Angular project.
> - choose the CSS preprocessor (like CSS, SCSS, SASS, LESS, Stylus). - we will be using CSS.
> - Server-side rendering (SSR) - we will be using Client-side rendering (CSR). write (No)

---

# Break

---
