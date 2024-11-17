### Component Lifecycle

> [!tip] [Component lifecycle](https://v17.angular.io/guide/lifecycle-hooks "Angular calls lifecycle hook methods on directives and components as it creates, changes, and destroys them.")
>
> - we can use the lifecycle ==hooks== to do some actions when the component is created, updated, or destroyed.
> - component is class so it has a constructor.
> - `ngOnChanges`: run every time the data decorated with `@Input` is changed after the constructor (if exists)
> - can be used for validation.
> - `ngOnInit`: run after the constructor and ngOnChanges( if exists)
> - run once.
> - used to initialize the component.
> - convention: Constructor -> Dependency Injection & and initialization properties with simple values
> - `ngDoCheck`: Called immediately after `ngOnChanges()` on every change detection run, and immediately after `ngOnInit()` on the first run.
> - used to detect and act upon changes that Angular can't or won't detect on its own. See details and example in [Defining custom change detection](https://v17.angular.io/guide/lifecycle-hooks#docheck) in this document.
> - not recommended to use as it will affect the performance.
> - `ngOnDestroy`: run when the component is destroyed. 
> - component is removed from the DOM. 
> - can be used to confirm the user before leaving the page.

```typescript
//test.component.ts
import {
  Component,
  OnInit,
  OnChanges,
  DoCheck,
  OnDestroy,
} from "@angular/core";

@Component({
  selector: "app-test",
  templateUrl: "./test.component.html",
  styleUrls: ["./test.component.css"],
})
export class TestComponent {
  name: string;
  constructor() {
    this.name = "Ahmed";
    console.log("constructor called");
  }
  ngOnInit() {
    this.name = "Ali";
    console.log("ngOnInit called");
  }
}
```

```html
<!-- test.component.html -->
<p>{{name}}</p>
```

> [!tip] The output will be `Ali` as the `ngOnInit` will be called after the constructor and will change the value of the name to `Ali`.
>
> - `constructor called` 
> - `ngOnInit called`

> [!bug] Implement the `ngOnInit` lifecycle hook in the `test.component.ts`

```typescript
//test.component.ts
import { Component, OnInit } from "@angular/core";

@Component({
  selector: "app-test",
  templateUrl: "./test.component.html",
  styleUrls: ["./test.component.css"],
})
export class TestComponent implements OnInit {
  name: string;
  constructor() {
    this.name = "Ahmed";
    console.log("constructor called");
  }
  ngOnInit() {
    this.name = "Ali";
    console.log("ngOnInit called");
  }
}
```

> [!bug] Implement the `ngOnChanges` lifecycle hook in the `test.component.ts`
>
> - used with properties decorated with `@Input` decorator.

```typescript
//test.component.ts
import { Component, OnInit, OnChanges } from "@angular/core";

@Component({
  selector: "app-test",
  templateUrl: "./test.component.html",
  styleUrls: ["./test.component.css"],
})
export class TestComponent implements OnInit, OnChanges, OnDestroy {
  name: string; //won't be called as it is not decorated with @Input
  @Input() age: number; //will be called as it is decorated with @Input
  constructor() {
    this.name = "Ahmed";
    console.log("constructor called");
  }
  ngOnInit() {
    this.name = "Ali";
    console.log("ngOnInit called");
  }
  ngOnChanges(changes: SimpleChanges): void {
    //
    console.log(changes); //will show the changes in the console
    //{previousValue: undefined, currentValue: 25, firstChange: true, isFirstChange: ƒ}
    //when the age is changed it will be called again.
    //{previousValue: 25, currentValue: 30, firstChange: false, isFirstChange: ƒ}
    console.log("ngOnChanges called"); //will be called after the constructor and before ngOnInit
    // when the age is changed it will be called again.
  }

  ngOnDestroy() {
    console.log("ngOnDestroy called"); //will be called when the component is destroyed - removed from the DOM.
  }
}
```


>[!done] - [Lifecycle event sequence](https://v17.angular.io/guide/lifecycle-hooks#lifecycle-event-sequence)

>[!faq] [Call it in `ngOnInit()`](https://v17.angular.io/tutorial/tour-of-heroes/toh-pt4#call-it-in-ngoninit "Link to this heading")
>- While you could call `getHeroes()` in the constructor, that's not the best practice.
>- Reserve the constructor for minimal initialization such as wiring constructor parameters to properties. 
>- The constructor shouldn't _do anything_. It certainly shouldn't call a function that makes HTTP requests to a remote server as a _real_ data service would.
>- Instead, call `getHeroes()` inside the [_ngOnInit lifecycle hook_](https://v17.angular.io/guide/lifecycle-hooks) and let Angular call `ngOnInit()` at an appropriate time _after_ constructing a `HeroesComponent` instance.
>- `ngOnInit(): void {   this.getHeroes(); }`

>[!error] `ngOnChanges`
> - if `@Input` is present but not used by parent `ngOnChanges` won't be called 
> - if 2 are present but only one is used it will be the only one in `SimpleChanges` hash table



>[!faq] order
> - parent constructor
> - child constructor
> - parent `ngOnInit`
> - parent `ngDoCheck`
> - child `ngOnChanges`
> - Child `ngOnInit` 
> - Child `ngDoCheck` 
> - Parent `ngDoCheck `
> - Child `ngDoCheck`

---

## Routing

> [!tip] Routing
>
> - based on the path the router will load the component.
> - `/home` => `HomeComponent`
> - `/about` => `AboutComponent`
> - `/students/add` => `AddStudentComponent`
> - `/students` => `StudentListComponent`
> - `/students/3` => `StudentDetailsComponent` 
> - show the details of the student with id 3 and this will be dynamic based on the id.
> - client: the routing is done on the client-side.
> - partial refresh - no need to refresh the whole page.
> - history - save the history of the pages visited.
>   > [!done] `RouterModule`

> [!tip]
>
> - application is bootstrapped in `main.ts` with `appConfig` from `app.config.ts`.
> - in `app.config.ts`:
> - it provides the configuration for the application including the routes.
> - in `app.routes.ts`: the routes are defined.
> - app component is already known to the router and it is the main component that will be loaded.
> - `RouterOutlet` is the placeholder for the component that will be loaded based on the path.
> - app component is importing the `RouterOutlet` by default.
> - changing the path in browser manually will reload the page.
> - we use links to navigate between the pages.
>   > [!done] instead of using `href` we use `routerLink` directive to navigate between the pages.
>   >
>   > - has to be imported from `@angular/router`.
>   > - add it to the `imports` array in the component it will be used in.

```typescript
//app.routes.ts
import { Routes } from "@angular/router";
import { HomeComponent } from "./home/home.component";

export const appRoutes: Routes = [
  { path: "home", component: HomeComponent },
  { path: "about", component: AboutComponent },
  { path: "contact", component: ContactComponent },
];
```

```html
<!-- app.component.html -->
<h1>Header</h1>
<!-- links to navigate between the pages -->
<a href="/home">Home</a>
<a href="/about">About</a>
<a href="/contact">Contact</a>
<!-- 
    this is a normal link that will reload the page.
    it will load the whole page again.
 -->

<a routerLink="/home">Home</a>
<a routerLink="/about">About</a>
<a routerLink="/contact">Contact</a>
<!-- 
    this is a router link that will load the component based on the path.
    it will load the component in the placeholder.
 -->

<h1>App Component</h1>

<router-outlet></router-outlet>
<!-- 
    the component will be loaded here based on the path.
    if the path is /home => HomeComponent will be loaded here.
    if the path is /about => AboutComponent will be loaded here.
    if the path is /contact => ContactComponent will be loaded here.
 -->

<h1>Footer</h1>
```

> [!example] split the app component into header, footer, ... components
>
> - import the components in the app component.

```html
<!-- app.component.html -->
<app-header></app-header>
<router-outlet></router-outlet>
<app-footer></app-footer>
```

> [!bug] we have to import `routerLink` directive from `@angular/router` in the component.

```html
<!-- header.component.html -->
<div class="navbar">
  <ul>
    <li><a routerLink="/home">Home</a></li>
    <li><a routerLink="/about">About</a></li>
    <li><a routerLink="/contact">Contact</a></li>
  </ul>
</div>
```

> [!example] create the `NotFoundComponent` to handle the 404 error.
>
> - configure the routes to handle the 404 error.

```typescript
//app.routes.ts
import { Routes } from "@angular/router";
import { HomeComponent } from "./home/home.component";
import { AboutComponent } from "./about/about.component";
import { ContactComponent } from "./contact/contact.component";

export const appRoutes: Routes = [
  //routes are matched from top to bottom
  // ** is a wildcard that will match any path that is not matched by the previous routes
  // { path: "**", component: NotFoundComponent },//has to be the last route otherwise it will match all the paths
  { path: "home", component: HomeComponent },
  { path: "about", component: AboutComponent },
  { path: "contact", component: ContactComponent },
  //   { path: "", component: HomeComponent },//default route will render the HomeComponent
  //we can use redirectTo to redirect to another route when the path is empty
  // { path: "", redirectTo: "/home"}// will throw an error has to provide pathMatch
  { path: "", redirectTo: "/home", pathMatch: "full" }, //will redirect to /home when the path is empty
  //prefix: the path starts with the provided path
  //full: the path is exactly the provided path we have to use it with empty path as it will match the empty path exactly
  //using the prefix will redirect to /home every time as it will match all the paths (empty path is a prefix for all the paths)

  { path: "**", component: NotFoundComponent }, //has to be the last route
];
```

> [!tip] we can bind the routerLink to an array of urls as url could be dynamic.

```html
<a [routerLink]="['/home']">Home</a>
```

> [!tip] add `/students` route to the appRoutes
>
> - add link to the students page in the header component.

```typescript
//app.routes.ts
{ path: "students", component: StudentListComponent },
```

```html
<!-- header.component.html -->
<li><a routerLink="/students">Students</a></li>
```

> [!example] make students load students list on render not on click load button.

```typescript
//student-list.component.ts
import { Component, OnInit } from "@angular/core";

@Component({
  selector: "app-student-list",
  templateUrl: "./student-list.component.html",
  styleUrls: ["./student-list.component.css"],
})
export class StudentListComponent implements OnInit {
  students: Student[] = [];
  constructor(private studentService: StudentService) {} //constructor is used for dependency injection

  ngOnInit() {
    this.students = this.studentService.getStudents();
  }
}
```

---

# Break

---

> [!tip] public and private access modifiers
>
> - public: can be accessed from outside the class - can be used in the template.
> - private: can't be accessed from outside the class - can't be used in the template.

> [!done] route with code
>
> - using DI to inject the router in the component.


```html
<!-- student-list.component.html -->

<button (click)="goToAddStudent()">Add Student</button>

<!-- 
    is the same as 
 -->

<a routerLink="/students/add">Add Student</a>
```

```typescript
//student-list.component.ts
import { Component, OnInit } from "@angular/core";

@Component({
  selector: "app-student-list",
  templateUrl: "./student-list.component.html",
  styleUrls: ["./student-list.component.css"],
})
export class StudentListComponent implements OnInit {
  students: Student[] = [];
  constructor(private studentService: StudentService, private router: Router) {} //constructor is used for dependency injection

  ngOnInit() {
    this.students = this.studentService.getStudents();
  }

  goToAddStudent() {
    this.router.navigate(["/students", "add"]);
  }
}
```

> [!done] route to student list when the student is added

```typescript
//student-add.component.ts
import { Component, OnInit } from "@angular/core";

@Component({
  selector: "app-student-add",
  templateUrl: "./student-add.component.html",
  styleUrls: ["./student-add.component.css"],
})
export class StudentAddComponent {
  constructor(private studentService: StudentService, private router: Router) {} //constructor is used for dependency injection

  saveStudent() {
    this.studentService.addStudent();
    this.router.navigate(["/students"]);
  }
}
```

> [!example] Student Details
>
> - add route to the student details with the student id.(`/students/details/:id`) => `/students/details` + id parameter.
> - add link to the student details in the student list.

```typescript
//app.routes.ts
{ path: "students/details/:id", component: StudentDetailsComponent },
```

```html
<!-- student-list.component.html -->
<a routerLink="/students/details/{{student.id}}">Details</a>
<!-- 
        is the same as
   -->
<a [routerLink]="['/students', 'details', student.id]">Details</a>
```

> [!done] get the student id from the route
>
> - use the `ActivatedRoute` to get the id from the route.

```typescript
//student-details.component.ts
import { Component, OnInit } from "@angular/core";
import { ActivatedRoute } from "@angular/router";

@Component({
  selector: "app-student-details",
  templateUrl: "./student-details.component.html",
  styleUrls: ["./student-details.component.css"],
})
export class StudentDetailsComponent implements OnInit {
  std: Student = new Student(0, "", 0);
  constructor(
    private route: ActivatedRoute,
    private studentService: StudentService
  ) {} //ask for the ActivatedRoute service to get the current route
  //ask for the studentService to get the student details based on the id

  ngOnInit() {
    const id = this.route.snapshot.params.id; //snapshot is used to get the current value of the route (params, query, ...)
    //id is the name of the parameter in the route (students/details/:id)
    this.std = this.studentService.getStudentById(+id);
  }
}
```

```typescript
//student.service.ts

/// add getStudentById method to get the student by id

  getStudentById(id: number) : Student | null {
    return this.students.find((student) => student.id === id);
  }

```

```html
<!-- student-details.component.html -->

<h1>Student Details</h1>
<p>{{std?.name}}</p>
<p>{{std?.age}}</p>
```

> [!tip] to display the student details beside the list of students
>
> - make the student details component a child of the student list component.

```typescript
//app.routes.ts
{ path: "students", component: StudentListComponent, children: [
    { path: "details/:id", component: StudentDetailsComponent }
  ] },
```

```typescript
//student-list.component.ts

import { Component, OnInit } from "@angular/core"

@Component({
  selector: "app-student-list",
  templateUrl: "./student-list.component.html",
  styleUrls: ["./student-list.component.css"],
})

```

```html
<!-- student-list.component.html -->
<h1>Student List</h1>
<!-- 
    list of students
 -->
<router-outlet></router-outlet>
<!-- 
    has to be added to the student list component imports
 -->
```

> [!done] same with add student component

```typescript
//app.routes.ts
{ path: "students", component: StudentListComponent, children: [
    { path: "details/:id", component: StudentDetailsComponent },
    { path: "add", component: StudentAddComponent }
  ] },
```

> [!bug] when pressing details second time the details won't be updated it will still show the previous student details.
>
> - the component has to be reloaded to get the new student details.
>   > [!done] make the component track the changes in the route to reload the component when the route changes.
>   >
>   > - we use `ActivatedRoute` to subscribe to the changes in the route.
>   > - we use `params` to get the changes in the parameters of the route.

```typescript
//student-details.component.ts
import { Component, OnInit } from "@angular/core";
import { ActivatedRoute } from "@angular/router";
import { StudentService } from "../student.service";
import { Student } from "../student";
import { Subscription } from "rxjs"; //to unsubscribe when the component is destroyed
//rxjs: Reactive programming for JavaScript.

@Component({
  selector: "app-student-details",
  templateUrl: "./student-details.component.html",
  styleUrls: ["./student-details.component.css"],
})
export class StudentDetailsComponent implements OnInit, OnDestroy {
  std: Student = new Student(0, "", 0);
  subscription: Subscription | null = null;
  constructor(
    private route: ActivatedRoute,
    private studentService: StudentService
  ) {} //ask for the ActivatedRoute service to get the current route
  //ask for the studentService to get the student details based on the id

  ngOnInit() {
    this.subscription = this.route.params.subscribe((params) => {
      //subscribe takes a callback function that will be called when the route changes(return object of type subscription)
      //every time we press the details link a new subscription will be created - and the old one will still be there (memory leak)
      //we need to free the resources when the component is destroyed
      const id = params["id"];
      this.std = this.studentService.getStudentById(+id);
    });
  }
  ngOnDestroy() {
    //unsubscribe when the component is destroyed
    this.subscription?.unsubscribe();
  }
}
```

---

## Lazy Loaded Components

> [!tip] Get only the needed components
>
> - only load the components when they are needed.
> - move the paths to separate file `students.routes.ts`. we add it to students folder (students/students.routes.ts).
> - we have to link the routes to the app routes.

```typescript
//students/students.routes.ts

import { Routes } from "@angular/router";
import { StudentListComponent } from "./student-list/student-list.component";
import { StudentDetailsComponent } from "./student-details/student-details.component";
import { StudentAddComponent } from "./student-add/student-add.component";

export const studentsRoutes: Routes = [
  { path: "", component: StudentListComponent },
  { path: "details/:id", component: StudentDetailsComponent },
  { path: "add", component: StudentAddComponent },
];
```

```typescript
//app.routes.ts
export const appRoutes: Routes = [
  { path: "home", component: HomeComponent },
  { path: "about", component: AboutComponent },
  { path: "contact", component: ContactComponent },

  {
    path: "students",
    loadChildren: () =>
      import("./students/students.routes").then((m) => m.studentsRoutes),
  },

  { path: "", redirectTo: "/home", pathMatch: "full" },
  { path: "**", component: NotFoundComponent },
];
```

> [!done] Lazy load the students components
>
> - when requesting the app for the first time it will load all the components in the routes except the students components.
> - when navigating between pages included in the routes won't make new requests to the server.(Home, About, Contact)
> - when navigating to the students page it will make a request to the server to get the students components.
> - it will be loaded once then navigating again won't make a request to the server.

---

## Protecting the Routes

> [!example] login
>
> - `ng g s services/account`

```typescript
//account.service.ts
import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class AccountService {
  isLoggedIn = false;
  constructor() {}

  login() {
    //takes username and password
    //send a request to the server to check the username and password
    //if the username and password are correct set isLoggedIn to true
    //return jwt token => to be used in the requests to the server => saved in the local storage
    this.isLoggedIn = true;
  }
}
```

### Guards

> [!tip] Guards
>
> - used to protect the routes.
>   `ng g guard guards/canLogin --skip-tests`
> - `canActivate`: to protect the route.
> - `canActivateChild`: to protect the child routes.
> - `canDeactivate`: to prevent the user from leaving the page.
> - will create a guard in the guards folder `can-login.guard.ts`.

```typescript
//can-login.guard.ts

import { CanActivate } from "@angular/router";
import { inject } from "@angular/core";

export const CanLoginGuard: CanActivate = (route, state) => {
  //to ask for object of type AccountService we use DI => inject
  const accountService = inject(AccountService);
  const router = inject(Router);
  //check if the user is logged in
  if (accountService.isLoggedIn) {
    return true;
  }
  //if the user is not logged in redirect to the login page
  router.navigateByURL("/login");
  return false;
};
```

> [!tip] use `canActivate` to protect the route
>
> - could be multiple guards for the same route.
> - in the routes we add the guard to the canActivate array.

```typescript
//app.routes.ts

import { CanLoginGuard } from "./guards/can-login.guard";

export const appRoutes: Routes = [
  { path: "home", component: HomeComponent },
  { path: "about", component: AboutComponent },
  {
    path: "contact",
    component: ContactComponent,
    canActivate: [CanLoginGuard],
  },

  {
    path: "students",
    loadChildren: () =>
      import("./students/students.module").then((m) => m.studentsRoutes),
  },

  { path: "login", component: LoginComponent },

  { path: "", redirectTo: "/home", pathMatch: "full" },
  { path: "**", component: NotFoundComponent },
];
```

```typescript
//login.component.ts
import { Component } from "@angular/core";
import { AccountService } from "../services/account.service";

@Component({
  selector: "app-login",
  templateUrl: "./login.component.html",
  styleUrls: ["./login.component.css"],
})
export class LoginComponent {
  constructor(private accountService: AccountService) {}

  login() {
    this.accountService.login();
  }
}
```

```html
<!-- login.component.html -->
<button (click)="login()">Login</button>
```

> [!example] conditionally show the routes in header component
>
> - show contact route only if the user is logged in & display logout button instead of login button.

```typescript
//header.component.ts
import { Component } from "@angular/core";
import { AccountService } from "../services/account.service";

@Component({
  selector: "app-header",
  templateUrl: "./header.component.html",
  styleUrls: ["./header.component.css"],
})
export class HeaderComponent {
  constructor(public accountService: AccountService) {} //public to be used in the template
}
```

```html
<!-- header.component.html -->

@if(accountService.isLoggedIn) {

<li><a routerLink="/contact">Contact</a></li>
<li><a routerLink="/logout">Logout</a></li>
}@else{
<li><a routerLink="/login">Login</a></li>
}
```

---

### Lab

- routes for all the components.(students,departments,home, about, contact)
  - lazy load the components.(students,departments)
