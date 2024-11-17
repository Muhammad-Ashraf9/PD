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
> - The application is divided into components.
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

> [!tip] to run the commands we have to be in the project folder.

> [!note] `Angular.json`
>
> - Angular.json is the configuration file for the Angular project.
> - `styles` array in `angular.json` is used to add global styles to the project. (like bootstrap, or import bootstrap in styles.css)
> - `scripts` array in `angular.json` is used to add global scripts to the project.
> - `index.html` is the main HTML file for the project.
> - `main.ts` is the entry point for the project.

> [!danger] `ng serve`
>
> - `ng serve` to run the project.
> - `ng serve --open` to run the project and open the browser.
>   -app is loaded in the browser with the URL `http://localhost:4200/`.
> - HTML page will have scripts used to change the DOM in `<app-root>` tag.

> [!bug] `main.ts`
>
> - `main.ts` is the entry point for the project.
> - `main.ts` has the code to bootstrap the Angular application with app component and configuration in `appConfig`

> [!warning] `app.component.ts`
>
> - `app.component.ts` is the root component for the project. (bootstrap component)
> - has the code to define the component.
> - has the code to define the template, styles, and data for the component.
>   > [!bug] not preferred to change `index.html` file. but the component specified `<app-root>`

```typescript
import { Component } from "@angular/core";

@Component({
  //used to define the class as a component
  selector: "app-root",
  standalone: true,
  imports: [RouterModule], //this like using in C# => import to use
  templateUrl: "./app.component.html", //is HTML file used to render the view.
  styleUrls: ["./app.component.css"], //is CSS file used to style the view.
})
export class AppComponent {
  title = "angular-day1";
}
```

> [!done] `ng generate component componentName`
>
> - used to generate a new component.
> - will create a folder with the component name.(Kebeb case - lowercase with hyphen between words)
> - will create 4 files (componentName.component.ts, componentName.component.html, componentName.component.css, componentName.component.spec.ts) (pascal case `ComponentName`)

```bash
ng generate component home
```

```typescript
// home.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-home", //prefix app is used to avoid conflicts,
  //selector is used to specify the custom html tag used to render the component in the view. (like <app-home></app-home>)
  standalone: true, //doest not need to be in a module. - false => error as it is not in a module.
  imports: [],
  templateUrl: "./home.component.html",
  styleUrls: ["./home.component.css"],
})
export class HomeComponent {
  //postfix Component is used to specify the class as a component.
}
```

> [!done] to display the component in the view, we add it to the `index.html` file.

```html
<!-- app.component.html -->
<app-home></app-home>
<!-- error as it is not in a module.
we have to add it to the imports array in the app.component.ts file. -->
```

> [!done] `app.component.ts`

```typescript
import { Component } from "@angular/core";
import { HomeComponent } from "./home/home.component";

@Component({
  selector: "app-root",
  standalone: true,
  imports: [RouterModule, HomeComponent], //add the component to the imports array.
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"],
})
export class AppComponent {
  title = "angular-day1";
}
```

> [!tip] selector can be class, attribute, or element.
>
> - element: `app-root` => `<app-root></app-root>`
> - class: `.app-root` => `<div class="app-root"></div>`
> - attribute: `[app-root]` => `<div app-root></div>`

> [!tip] `templateUrl` vs `template`
>
> - `templateUrl` is used to specify the HTML file used to render the view.
> - `template` is used to specify the HTML code directly in the component file. (not recommended unless the HTML is small)

```typescript
// home.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-home",
  standalone: true,
  imports: [],
  template: `<h1>Home Component</h1>`, //HTML code directly in the component file.
  styleUrls: ["./home.component.css"],
})
export class HomeComponent {}
```

> [!done] `stylesUrls` vs `styles` vs `style` vs `styleUrl`
>
> - local style will only be applied to the component.
> - `stylesUrls` is used to specify the CSS files used to style the view could be multiple files.
> - `styleUrl` is used to specify the CSS file used to style the view.
> - `styles` is used to specify the CSS code directly in the component file. (not recommended unless the CSS is small)
> - `style` is used to specify the CSS code directly in the component file. (not recommended unless the CSS is small)
> - for style to be global, we use `styles.css`
> - ==search== for how to write global styles in components.

> [!tip] create a new component without CLI
>
> - create a new folder with the component name.
> - create `componentName.component.ts` file.

> [!danger] install `Angular essentials` extension in VSCode for Angular snippets.

```typescript
// about.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "iti-about",
  standalone: true,
  imports: [],
  template: `<h1>About Component</h1>`,
})
export class AboutComponent {}
```

```typescript
// app.component.ts
import { Component } from "@angular/core";
import { HomeComponent } from "./home/home.component";
import { AboutComponent } from "./about/about.component";

@Component({
  selector: "app-root",
  standalone: true,
  imports: [RouterModule, HomeComponent, AboutComponent],
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"],
})
export class AppComponent {
  title = "angular-day1";
}
```

```html
<!-- app.component.html -->
<app-home></app-home>
<iti-about></iti-about>
```

> [!bug] to add it in the Home component, we have to add it to the `home.component.ts` file.

```typescript
import { Component } from "@angular/core";
import { AboutComponent } from "../about/about.component";

@Component({
  selector: "app-home",
  standalone: true,
  imports: [AboutComponent],
  template: `<h1>Home Component</h1>`,
  styleUrls: ["./home.component.css"],
})
export class HomeComponent {}
```

```html
<!-- home.component.html -->
<iti-about></iti-about>
```

> [!tip] Check
>  [Angular documentation](https://angular.io/docs) 
>   [Angular dev](https://angular.dev)

---

# Lab

- Create component (manully + CLI)
- make styles global + local
