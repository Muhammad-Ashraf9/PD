> [!example] to add different style to router link we use `routerLinkActive` directive. It is used to add a class to the element when the link's route becomes active.

```html
//app.component.html <a routerLink="/home" routerLinkActive="active">Home</a>
```

> [!tip] `title` in route configuration is used to display the title of the page in the browser tab.

```typescript
//app.routes.ts
  { path: 'home', component: HomeComponent, data: { title: 'Home' } },

```

> [!note] `DataSource` => Observable
>
> - emits a stream of data over time (1s => part of the data, 2s => another part of the data, etc.)
> - Components does not have to wait for the data to be fully loaded
> - better to use the data as it comes in
> - data source is observable that emits data over time
> - the component subscribes to the data source and gets the data as it comes in
> - observer is the one who listens to the data source
> - can use operators like skip, take, filter, etc. to manipulate the data

> [!warning] create data source
>
> - import `Observable` from `rxjs`
> - `rxjs` is a library already included in Angular
> - create a new observable using `new Observable()`
> - we can specify the type of data that the observable will emit `<string>` || `new Observable<string>()`
> - we pass a function to the observable that will be called when the observer subscribes to the observable
> - `new Observable<string>(observer => { observer.next('Hello'); })`
> - `observer.next()` is used to emit data when the data is ready
> - `observer.complete()` is used to indicate that the data source has finished emitting data
> - `observer.error()` is used to indicate that an error has occurred
> - `observer.complete()` and `observer.error()` (only one can be called)

```typescript
// data-source.component.ts

import { Component, OnInit } from "@angular/core";
import { Observable } from "rxjs";

@Component({
  selector: "app-data-source",
  templateUrl: "./data-source.component.html",
  styleUrls: ["./data-source.component.css"],
})
export class DataSourceComponent {
  run() {
    let dataSource = new Observable<number>((observer) => {
      setTimeout(() => {
        observer.next(10);
      }, 1000);
      setTimeout(() => {
        observer.next(20);
      }, 2000);
      setTimeout(() => {
        observer.next(30);
      }, 3000);
      setTimeout(() => {
        observer.complete();
      }, 4000); //no more data to emit
      //complete() and error() can not be called together
      // it is either complete() or error()
      setTimeout(() => {
        observer.error(); //error will be undefined
        observer.error(new Error("Something went wrong")); //error will be called with the message "Something went wrong"
      }, 2000); //error() will be called after 2s and complete() will not be called
    });

    ///nothing will happen until we subscribe to the data source
    dataSource.subscribe({
      next: (data) => {
        //data is the data that is emitted by the data source
        console.log(data);
      }, //the function that will be called when the data is ready(observer.next())
      complete: () => {
        console.log("Data source is complete");
      }, //the function that will be called when the data source is complete(observer.complete())
      //we can handle the error
      error: (err) => {
        console.log(err); //
      },
    });

    // we can pass a function to the subscribe method
    //first argument is the data that is emitted by the data source and the second argument is the error and the third argument is the complete
    // dataSource.subscribe((data) => {
    //   console.log(data);
    // });

    //dataSource.subscribe((data) => console.log(data), (err) => console.log(err), () => console.log("Data source is complete"));
    //first one is more readable
  }
}
```

> [!tip] `interval` is an observable that emits a number every second. It is used to create a data source that emits data over time.

```typescript
// data-source.component.ts
import { interval } from "rxjs";// import interval from rxjs

@Component({
  selector: "app-data-source",
  templateUrl: "./data-source.component.html",
  styleUrls: ["./data-source.component.css"],
})

export class DataSourceComponent {

let sub :Subscription | null = null;


run(){

//     interval(1000).subscribe((data) => {
//         console.log(data);
// });// this will emit a number every second starting from 0 to infinity
//to stop the interval we can use the unsubscribe method
sub = interval(1000).subscribe((data) => {
  console.log(data);
});
}

stop(){
this.sub?.unsubscribe();
}
```

> [!danger] we can add filters to the observable to filter the data that is emitted by the data source

```typescript
// data-source.component.ts

run() {
    sub = interval(1000).pipe(
      filter((data) => data > 5), //filter the data that is emitted by the data source (only emit the data that is greater than 5)
      take(5) //take the first 5 data that is emitted by the data source
      //6,7,8,9,10
    ).subscribe((data) => {
      console.log(data);
    });
  }
```

> [!warning] all information about `rxjs` can be found in the [official documentation](https://rxjs.dev/guide/overview)

---

### HTTP Client

> [!note] `HttpClient` is a service that is used to make HTTP requests to the server.
>
> - It is used to get data from the server, post data to the server, etc.
> - `observable` is used to handle the response from the server
> - `httpClient.get()` is used to make a `GET` request to the server
> - `httpClient.post()` is used to make a `POST` request to the server
>   -...

```typescript
//Get http://localhost:3000/departments
httpClient.get("http://localhost:3000/departments");

//Get http://localhost:3000/departments/1

httpClient.get("http://localhost:3000/departments/1");

//Post http://localhost:3000/departments
httpClient.post("http://localhost:3000/departments", department);
```

---

> [!tip] Using Web API department endpoints
>
> - request will be made to get the UI (Angular)
> - when loading the department page, the request will be made to get all the departments
> - using the UI already loaded, we can make a request to get the data and display it using angular

```typescript
////models/department.ts
export class Department {
  constructor(
    public id: number,
    public name: string,
    public location: string,
    public capacity: number
  ) {}
}
```

> [!tip] create a service to get the data from the server
>
> - `ng g s services/department`
> - logic communicating with the server will be in the service

> [!warning] to inject services related to `HttpClient`
>
> - we have to use `provideHttpClient` in `app.config.ts`
> -

```typescript
//app.config.ts
import { provideHttpClient } from "@angular/common/http";

export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes), provideHttpClient()],
};
```

```typescript
//department.service.ts

import { Injectable } from "@angular/core";
import { HttpClient } from "@angular/common/http";

@Injectable({
  providedIn: "root",
})
export class DepartmentService {
  private baseUrl: string = "http://localhost:3000/api/departmentTemps"; //as it is used in all methods

  constructor(private httpClient: HttpClient) {}

  getAll() {
    return this.httpClient.get<Department[]>(this.baseUrl);
    //this will return an observable that emits an array of departments `Observable<Department[]>` as we are getting `Department[]`
    //we have to subscribe to the observable to get the data

    //subcribing to the observable should be done in the component as each component will have its own way of handling the data
  }
}
```

> [!danger] to use the service in the component

```typescript
//department-list.component.ts

import { Component, OnInit } from "@angular/core";

import { DepartmentService } from "../services/department.service";
import { Department } from "../models/department";

@Component({
  selector: "app-department-list",
  templateUrl: "./department-list.component.html",
  styleUrls: ["./department-list.component.css"],
})
export class DepartmentListComponent implements OnInit {
  departments: Department[] = [];

  constructor(private departmentService: DepartmentService) {}

  ngOnInit() {
    //we have to subscribe to the observable to get the data
    this.departmentService.getAll().subscribe({
      next: (data) => {//data is the array of departments
        this.departments = data;
      },
      error: (err) => {
        console.log(err);//
      },
    });
}
```

> [!note] to display the data in the component

```html
<!-- //department-list.component.html -->
<div class="table-responsive">
  <table class="table table-striped table-bordered table-hover">
    <thead>
      <tr>
        <th>department ID</th>
        <th>Department Name</th>
        <th>Count</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody>
      @for(department of departments; track department.id) {
      <tr>
        <td>{{ department.id }}</td>
        <td>{{ department.name }}</td>
        <td></td>
      </tr>

      }@empty {
      <tr class="text-center">
        <td colspan="4" class="text-muted">No departments found</td>
      </tr>
      }
    </tbody>
  </table>
</div>
```

> [!danger] `providers` in `component`
>
> - to inject value (base url) to the service/component...
> - we can use sugar syntax `useValue` to provide a value to the component
> - `providers: [{ provide: "baseUrl", useValue: "http://localhost:3000/api/departmentTemps" }]`
> - now we have the value in the component and we can use it
>   > [!warning] `useClass` vs `useValue` vs `useFactory` vs `useExisting`
>   >
>   > - we can use `useValue` to provide a value to the component (like a string, number, etc.) - url, etc.
>   >
>   > - ==Search for the difference between `useClass`, `useValue`, and `useFactory` in Angular==

---

# Break

---

> [!tip] add a new department

```typescript
//department.service.ts

add(department: Department) {
    return this.httpClient.post<Department>(this.baseUrl, department);
    //this will return an observable that emits a department `Observable<Department>` as we are getting `Department`
  }

```

```typescript
//department.component.ts

import { Component, OnInit } from "@angular/core";
import { FormsModule } from "@angular/forms";

import { DepartmentService } from "../services/department.service";
import { Department } from "../models/department";

@Component({
  selector: "app-department",
  templateUrl: "./department.component.html",
  imports: [FormsModule],
  styleUrls: ["./department.component.css"],
})
export class DepartmentComponent implements OnDestroy {
  department: Department = new Department(0, "", "", 0);
  sub: Subscription | null = null;

  constructor(
    private departmentService: DepartmentService,
    private router: Router
  ) {}

  save() {
    this.sub = this.departmentService.add(this.department).subscribe({
      //when we subscribe to the observable we get a subscription and it has to be stored in a variable to unsubscribe
      next: (data) => {
        console.log(data);
        this.router.navigate(["/departments"]);
      },
      error: (err) => {
        console.log(err);
      },
    });
  }
  ngOnDestroy() {
    this.sub?.unsubscribe();
  }
}
```

```html
<!-- department.component.html -->

<input
  type="text"
  class="form-control"
  placeholder="Department Name"
  [(ngModel)]="department.name"
  name="name"
/>
<select class="form-control" [(ngModel)]="department.location" name="location">
  <!-- 
        options could be added from database 
     -->
  <option value="Cairo">Cairo</option>
  <option value="Alexandria">Alexandria</option>
  <option value="Giza">Giza</option>
  <option value="Aswan">Aswan</option>
</select>
<input
  type="number"
  class="form-control"
  placeholder="Capacity"
  [(ngModel)]="department.capacity"
  name="capacity"
/>

<button class="btn btn-primary" (click)="save()">Save</button>
```

> [!tip] Department details
>
> - get the department details by id

```typescript
//department.service.ts

getById(id: number) {
    return this.httpClient.get<Department>(`${this.baseUrl}/${id}`);
  }
```

```typescript
//department-details.component.ts
import { Component, OnInit } from "@angular/core";
import { ActivatedRoute } from "@angular/router";
import { DepartmentService } from "../services/department.service";
import { Department } from "../models/department";

@Component({
  selector: "app-department-details",
  templateUrl: "./department-details.component.html",
  styleUrls: ["./department-details.component.css"],
})
export class DepartmentDetailsComponent implements OnInit {
  department: Department = new Department(0, "", "", 0);

  constructor(
    private route: ActivatedRoute,
    private departmentService: DepartmentService
  ) {}

  ngOnInit() {
    this.route.params.subscribe((params) => {
      //we have to unsubscribe from the observables (both params and data)  - not only the data
      // every subscription is a reference to different observable and we have to unsubscribe from all of them
      this.departmentService.getById(params["id"]).subscribe((data) => {
        this.department = data;
      });
    });
  }
}
```

> [!tip] update department

```typescript
//department.service.ts

update(department: Department) {
    return this.httpClient.put<Department>(`${this.baseUrl}/${department.id}`, department);
  }

delete(id: number) {
    return this.httpClient.delete(`${this.baseUrl}/${id}`);//won't call the server until we subscribe to the observable
  }
```

> [!warning] Observables vs Promises
>
> - ==Search for the difference between Observables and Promises==

---

> [!tip] API uses `JWTBearer` token for authentication
>
> - we need to send the token with every request to the server (in the header)

```typescript
//account.service.ts

import { Injectable } from "@angular/core";
import { HttpClient, HttpHeaders } from "@angular/common/http";

@Injectable({
  providedIn: "root",
})
export class AccountService {
  private baseUrl: string = "http://localhost:3000/api/account";

  isLoggedIn: boolean = false;

  constructor(private httpClient: HttpClient) {}

  login(username: string, password: string) {
    // return this.httpClient.post(`${this.baseUrl}/login`, { username, password });

    let user = `username=${username}&password=${password}`;
    this.httpClient
      .get(`${this.baseUrl}/login?${user}`, { responseType: "text" }) //specify the type of the data that is returned by the server to avoid errors as it expects json
      .subscribe((data) => {
        console.log(data);
        localStorage.setItem("token", data);
        this.isLoggedIn = true;
      });
  }
}
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
  username: string = "";
  password: string = "";

  constructor(private accountService: AccountService) {}

  login() {
    this.accountService.login(this.username, this.password);
  }
}
```

```html
<!-- login.component.html -->

<input type="text" class="form-control" placeholder="Username
[(ngModel)]="username" name="username" />
<input
  type="password"
  class="form-control"
  placeholder="Password"
  [(ngModel)]="password"
  name="password"
/>

<button class="btn btn-primary" (click)="login()">Login</button>
```

> [!danger] we have to send the token with every request to the server
>
> - we can use `HttpInterceptor` to intercept the request and add the token to the header
> - we can have multiple interceptors like middlewares
> - `ng g interceptor interceptors/addtoken`
> - this will create a folder named `interceptors` and a file named `addtoken.interceptor.ts`
> - request is immutable and we can't change it so we have to clone it and add the token to the cloned request and return the cloned request

```typescript
//interceptors/addtoken.interceptor.ts

import { HttpInterceptorFn } from "@angular/common/http";

export const AddTokenInterceptor: HttpInterceptorFn = (req, next) => {
  let token = localStorage.getItem("token");
  if (token) {
    let clonedRequest = req.clone({
      headers: req.headers.set("Authorization", `Bearer ${token}`),
    });
    return next(clonedRequest);
  }
  return next(req);
};
```

> [!danger] we have to add the interceptor to the providers in `app.config.ts`

```typescript
//app.config.ts

import { provideHttpClient, withInterceptors } from "@angular/common/http";
import { AddTokenInterceptor } from "./interceptors/addtoken.interceptor";

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient(withInterceptors([AddTokenInterceptor])),
  ],
};
```

> [!faq] How to read data from the token to get the user information?
>
> - so we can display different information based on the user role, etc.
> - use `jwt-decode` library to decode the token

---

# Break

---

```typescript
//account.service.ts
import * as jwtDecode from "jwt-decode";

export class AccountService {
  private baseUrl: string = "http://localhost:3000/api/account";

  isLoggedIn: boolean = false;
  user: { username: string; isAdmin: boolean } = {
    username: "",
    isAdmin: false,
  };

  constructor(private httpClient: HttpClient) {
    let token = localStorage.getItem("token");
    if (token) {
      this.user = jwtDecode(token);
      this.isLoggedIn = true;
    }
  }

  login(username: string, password: string) {
    let user = `username=${username}&password=${password}`;
    this.httpClient
      .get(`${this.baseUrl}/login?${user}`, { responseType: "text" })
      .subscribe((data) => {
        localStorage.setItem("token", data);
        this.user = jwtDecode.jwtDecode(data); //decode the token to get the user information {username, role, etc.}
        this.isLoggedIn = true;
      });
  }
}
```

> [!tip] to display the user information in the component

```html
<!-- navbar.component.html -->

@if(accountService.isLoggedIn) {
<li class="nav-item">
  <a class="nav-link" routerLink="/"
    >Welcome {{ accountService.user.username }}</a
  >
</li>
<li class="nav-item">
  <a class="nav-link" routerLink="#">Logout</a>
</li>
} @else {
<li class="nav-item">
  <a class="nav-link" routerLink="#">Login</a>
</li>
}
```

> [!tip] Web API Authorization
>
> - add claim with role to the token ` new Claim(ClaimTypes.Role, "Admin")`
> - in the API we can use `[Authorize(Roles = "Admin")]` to restrict access to the endpoint to the admin only
> - we can use `[Authorize]` to restrict access to the endpoint to the authenticated users only

---

### Forms

> [!example] Template-driven forms vs Reactive forms
>
> - code that manage forms could be in the template or in the component(ts)
> - in template-driven forms, the code that manages the form is in the template
> - in reactive forms, the code that manages the form is in the component(ts)
> - Reactive forms are more flexible than template-driven forms
> - this could be found in the official documentation of Angular [https://angular.io/guide/forms-overview](https://angular.io/guide/forms-overview)

> [!warning] PrimeNG
>
> - [primeNG](https://www.primeng.org/primeng/)
> - a library that provides a lot of components that could be used in Angular (like Calendar, etc.)
> - how to use it in both template-driven and reactive forms
> - for installation and usage, check the official documentation: (https://primeng.org/installation)[https://primeng.org/installation]
> - there are other libraries like `material UI`

---

### Modules

> [!warning] create a new module
>
> - `ng g m modules/employee`
> - to add component to the module we make `standalone = false` in the component decorator
> - remove `imports: [CommonModule]` array from the component decorator
> - as the imports array is done in the module
> - now add the component to the module declarations array `declarations: [TestComponent]`
> - this component is now part of the module and we can use it in the module
> - any component that is part of the module can use the component (is private to the module)
> - app.component can't use the component as it is not part of the module
> - to use the component in the app.component we have to export the component in the module `exports: [TestComponent]`

```typescript
//test.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-test",
  templateUrl: "./test.component.html",
  styleUrls: ["./test.component.css"],
  standalone: false, //can not have imports array
})
export class TestComponent {}
```

```typescript
//test2.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-test2",
  templateUrl: "./test2.component.html",
  styleUrls: ["./test2.component.css"],
  standalone: false, //can not have imports array
})
export class Test2Component {}
```

```typescript
//test.module.ts
import { NgModule } from "@angular/core";
import { CommonModule } from "@angular/common";
import { TestComponent } from "./test.component";
import { Test2Component } from "./test2.component";

@NgModule({
  declarations: [TestComponent, Test2Component], //can use TestComponent in Test2Component and vice versa as they are part of the same module (local to the module)
  imports: [CommonModule],
  exports: [TestComponent], //can use TestComponent in the app.component when we import the module in the app.component if we tried to use Test2Component it will not work as it is not exported
  //exports: [TestComponent, Test2Component],//can use TestComponent and Test2Component in the app.component when we import the module in the app.component
})
export class TestModule {}
```

```typescript
//app.component.ts
import { Component } from "@angular/core";
import { TestModule } from "./modules/employee/test.module";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"],
  imports: [TestModule],
})
export class AppComponent {}
```

```html
<!-- 
//app.component.html
 -->

<app-test></app-test>

<!-- 
<app-test2></app-test2>
this will not work as Test2Component is not exported in the module
 -->
```

---
### Lab
