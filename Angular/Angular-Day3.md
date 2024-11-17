## Pipes


> [!tip] Pipes
> create pipe `ng generate pipe <pipe-name>`
>
> - `ng generate pipe pipes/power`
> - will create a file `power.pipe.ts` and a test file `power.pipe.spec.ts`

```typescript
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({
  name: "power",
  standalone: true, //don't need to be in a module
})
export class PowerPipe implements PipeTransform {
  //has to implement PipeTransform and its transform method

  //   transform(value: unknown, ...args: unknown[]): unknown {
  //     return null;
  //   }
  transform(x: number, y: number = 1): number {
    return Math.pow(x, y);
  }
}
```

```html
<h1>{{ 2 | power }}</h1>

<!-- 
    2
    has to import the pipe in the component
-->
<h1>{{ 2 | power: 3 }}</h1>
<!-- 
    8
    2^3
 -->
```

```typescript
// app.component.ts
import { PowerPipe } from "./pipes/power.pipe";

@Component({
    imports: [PowerPipe]
})


```

> [!example] Pure and Impure Pipes
>
> - Search for ==pure== and ==impure== pipes
> >[!done]  [Change detection with pipes](https://angular.dev/guide/templates/pipes#change-detection-with-pipes)
> >- By default, all pipes are considered `pure`, which means that it only executes when a primitive input value (such as a `String`, `Number`, `Boolean`, or `Symbol`) or a changed object reference (such as `Array`, `Object`, `Function`, or `Date`). Pure pipes offer a performance advantage because Angular can avoid calling the transformation function if the passed value has not changed.
> >- As a result, this means that mutations to object properties or array items are not detected unless the entire object or array reference is replaced with a different instance. If you want this level of change detection, refer to [detecting changes within arrays or objects](https://angular.dev/guide/templates/pipes#detecting-change-within-arrays-or-objects).

>[!warning]  [Pipes - Detecting change within arrays or objects](https://angular.dev/guide/templates/pipes#detecting-change-within-arrays-or-objects)
> - When you want a pipe to detect changes within arrays or objects, it must be marked as an impure function by passing the `pure` flag with a value of `false`.
> - Avoid creating impure pipes unless absolutely necessary, as they can incur a significant performance penalty if used without care.
> - Angular developers often adopt the convention of including `Impure` in the pipe `name` and class name to indicate the potential performance pitfall to other developers.


---
## parent-child communication

> [!tip] parent to child
>
> - make components reusable send data from parent to child
> - `@Input()`: decorator to receive data from parent

```html
<!-- parent.component.html -->
<app-child></app-child>
```

```typescript
// child.component.ts
import { Component, Input } from "@angular/core";

@Component({
  selector: "app-child",
  templateUrl: "./child.component.html",
  styleUrls: ["./child.component.css"],
})
export class ChildComponent {
  @Input() data: string = ""; //now child can receive data from parent from attribute named data
}
```

```html
<!-- child.component.html -->
<h1>{{data}}</h1>
```

```html
<!-- parent.component.html -->
<app-child data="Hello"></app-child>
<!-- 
    Hello
    child component will display Hello
 -->
<app-child></app-child>
<!-- 
    empty
    child component will display empty as default value is empty "" in child component
 -->

<app-child [data]="parentData"></app-child>
<!-- 
    parentData
    child component will display parentData as parentData is a variable in parent component and is binded to child component
    -->
```

```typescript
// parent.component.ts
import { Component } from "@angular/core";
import { ChildComponent } from "./child/child.component";

@Component({
  selector: "app-parent",
  templateUrl: "./parent.component.html",
  import: [ChildComponent], //have to import child component
  styleUrls: ["./parent.component.css"],
})
export class ParentComponent {
  parentData = "parentData";
}
```

---

> [!danger] change in property name in child component will throw error as it is binded to parent component with the same name
>
> - if we change `@Input() data: string = "";` to `@Input() data1: string = "";` in child component
>   > [!done] solution
>   >
>   > - change `<app-child [data]="parentData"></app-child>` to `<app-child [data1]="parentData"></app-child>`
>   > - Better to use `@Input("data") data1: string = "";` in child component this will allow us to use `data` in parent component and `data1` in child component

---

> [!example] change name locally in child component

```typescript
// child.component.ts

import { Component, Input } from "@angular/core";

@Component({
  selector: "app-child",
  templateUrl: "./child.component.html",
  styleUrls: ["./child.component.css"],
})
export class ChildComponent {
  @Input("data") data1: string = ""; //now child can receive data from parent from attribute named data
  lname: string = "lname";

  saveLname(value: string) {
    this.lname = value;
  }
}
```

```html
<!-- child.component.html -->

<h1>{{data1}}</h1>
<input #lnameInput type="text" />
<button (click)="saveLname(lnameInput.value)">Save</button>
<h1>{{lname}}</h1>
```

> [!faq] How to pass data from child to parent component when data is changed in child component?
>
> - to notify parent component about the change in child component we define an event in child component and emit it to parent component 
> - `EventEmitter<string>`: will send string data to parent component
> - bind the event to the parent component and call a function in parent component to update the data 
> - `<app-child (onNameChange)="updateData($event)"></app-child>` will receive the data in `$event` and call `updateData` function in parent component
> - use `@Output()` on the event and can use alias name for the event 
> - `@Output('onNameChange') nameChange = new EventEmitter<string>();`
> - `@Output() onNameChange = new EventEmitter<string>();`

```typescript
// child.component.ts
import { Component, Input, Output, EventEmitter } from "@angular/core";

@Component({
  selector: "app-child",
  templateUrl: "./child.component.html",
  styleUrls: ["./child.component.css"],
})
export class ChildComponent {
  @Input("data") data1: string = ""; //now child can receive data from parent from attribute named data
  lname: string = "lname";
  @Output() onNameChange: EventEmitter<string> = new EventEmitter<string>(); //the type of data to be sent to parent component is string and can be changed to any type (number, object, etc)

  saveLname(value: string) {
    this.lname = value;
    this.onNameChange.emit(this.lname); //fire the event and send the data to parent component
    //parent component can subscribe to this event and get the data
  }
}
```

```typescript
// parent.component.ts
import { Component } from "@angular/core";
import { ChildComponent } from "./child/child.component";

@Component({
  selector: "app-parent",
  templateUrl: "./parent.component.html",
  import: [ChildComponent], //have to import child component
  styleUrls: ["./parent.component.css"],
})
export class ParentComponent {
  parentData = "parentData";

  updateData(data: string) {
    this.parentData = data;
  }
}
```

```html
<!-- parent.component.html -->
<app-child (onNameChange)="parentData = $event"></app-child>
<!-- 
    this will update the parentData in parent component when the data is changed in child component
 -->

<app-child (onNameChange)="updateData($event)"></app-child>
<!-- 
    this will update the parentData in parent component when the data is changed in child component
 -->
```

> [!done] Steps
>
> 1. Define an event in child component `@Output() onNameChange: EventEmitter<string> = new EventEmitter<string>();`
> 2. Emit the event in child component `this.onNameChange.emit(this.lname);`
> 3. subscribe to the event in parent component `<app-child (onNameChange)="updateData($event)"></app-child>`
> 4. define a function in parent component to update the data `updateData(data: string) { this.parentData = data; }`

---

> [!tip] child-parent communication
>
> - can be used to split components and make them reusable
> - like CRUD in Lab can be split into components (add, list, edit, )
> - we can use `@Output()` & `@Input()` to communicate between different components
> - or send data to app component and then send it to other components
>   > [!warning] This is complicated
>
> > [!done] Services


>[!done] Signals Approach
>-  [Angular Signal Components: input, output (Complete Guide) (angular-university.io)](https://blog.angular-university.io/angular-signal-components/)


---

# Break

---

## Services

> [!tip] Logging
>
> - instead of having different logging functions in different components we can have a service for logging

```typescript
// component1.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-component1",
  templateUrl: "./component1.component.html",
  styleUrls: ["./component1.component.css"],
})
export class Component1Component {
  log() {
    console.log("Component1");
  }
}
```

```html
<!-- component1.component.html -->
<button (click)="log()">Log</button>
```

```typescript
// component2.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-component2",
  templateUrl: "./component2.component.html",
  styleUrls: ["./component2.component.css"],
})
export class Component2Component {
  log() {
    console.log("Component2");
  }
}
```

```html
<!-- component2.component.html -->
<button (click)="log()">Log</button>
```

> [!warning] This is not a good practice as we are repeating the code in different components
>
> - any change in the log function will have to be done in all the components

> [!tip] generate service
>
> - `ng generate service services/logger`
> - will create a file `logger.service.ts` and a test file `logger.service.spec.ts`

```typescript
// logger.service.ts
// import { Injectable } from "@angular/core"; //will be explained later
// @Injectable({ providedIn: "root" }) //will be explained later
export class LoggerService {
  log(message: string) {
    console.log(message);
  }
}
```

> [!tip] to use the service in a component
>
> - create an instance of the service in the component

```typescript
// component1.component.ts
import { Component } from "@angular/core";
import { LoggerService } from "../services/logger.service";

@Component({
  selector: "app-component1",
  templateUrl: "./component1.component.html",
  styleUrls: ["./component1.component.css"],
})
export class Component1Component {
  loggerService: LoggerService = new LoggerService(); //create an instance of the service
  add() {
    //code
    this.loggerService.log("Component1");
  }
}
```

> [!danger] This is not a good practice as we are creating a new instance of the service in the component
>
> - as components are dependent on the service (dependency)
> - components needs to create an instance of the service
> - bad maintainability => if we change the service we have to change all the components
> - Testing => testing creation of the service with the functionality of the component
> - components using different instances of the service
> 
>   > [!done] IOC (Inversion of Control)
>   >
>   > - is a design principle in which the control of object creation is transferred to outside of the class (factory, )
>   > - then ask the factory to create the object for us or return the object that is already created
>
> > [!done] DI (Dependency Inversion)
> >
> > - High-level modules should not depend on low-level modules. Both should depend on abstractions.
> > - Abstractions should not depend on details. Details should depend on abstractions.
>
> > [!done] open-closed principle
> >
> > - a class should be open for extension but closed for modification
>
> > [!done] DIC (Dependency Injection Container)
> >
> > - used to register the services and provide them to the components
> > - Angular has a built-in DIC
>
> > [!done] `providers: [LoggerService]`
> >
> > - in the component we add the service to the providers array to tell Angular to create an instance of the service
> > - this will be available to the component and its child components
> > - if it is not added to the providers array it will search for the service in the parent component and up the hierarchy if not will search in the root if not found will throw an error

```typescript
// component1.component.ts
import { Component } from "@angular/core";
import { LoggerService } from "../services/logger.service";

@Component({
  selector: "app-component1",
  templateUrl: "./component1.component.html",
  styleUrls: ["./component1.component.css"],
  providers: [LoggerService], //add the service to the providers array other wise it will throw an error (injector error)
})
export class Component1Component {
  constructor(public loggerService: LoggerService) {} //DI
  add() {
    //code
    this.loggerService.log("Component1");
  }
}
```

> [!danger] This will get the instance of the service from different containers
>
> > [!done] to use the same instance of the service in the components
> >
> > - add the service to the providers array in the parent component like `app.component.ts`

> [!tip] Dependency Injection Container Provider
>
> - has a table
> - row => (token: name of the class, class: instance of the class)
> - `providers: [{provide: LoggerService, useClass: LoggerService}]` => if the token and class are the same we can use shorthand `providers: [LoggerService]`
> - `useClass`: don't have to be of type `provide` = no inheritance needed
> - [Defining dependency providers](https://v17.angular.io/guide/dependency-injection-providers "A provider factory function is a plain function that Angular can call to create a dependency.")
> 

> [!done] to register the service in the root module
>
> - `appConfig` => Not recommended
> - as it if it is not used it will be rendered anyway
> - better to use `@Injectable({ providedIn: "root" })` in the service
> - `@Injectable({ providedIn: "platform" })` => to make the service available to the multiple applications
> - `@Injectable()` 
> -  [providedIn](https://v17.angular.io/api/core/Injectable#providedin)


> [!warning] register on app component vs root
>
> - if the service is registered in the app.component.ts it will be available to the app component and its child components not (the pipes, directives, etc)
> - if the service is registered in the root it will be available to the whole application

---

> [!tip] create components (add, list )
>
> - create a service to store the data
> - `ng generate service services/student`
> - will create a file `student.service.ts` and a test file `student.service.spec.ts`

```typescript
// student.service.ts
import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root", //register the service in the root will be available to the whole application
})
export class StudentService {
  students: Student[] = [
    new Student(1, "student1", 22),
    new Student(2, "student2", 23),
    new Student(3, "student3", 24),
  ];

  getStudents() {
    return this.students;
  }

  addStudent(student: Student) {
    this.students.push(student);
  }
}
```

```typescript
// list.component.ts
import { Component } from "@angular/core";
import { StudentService } from "../services/student.service";

@Component({
  selector: "app-list",
  templateUrl: "./list.component.html",
  styleUrls: ["./list.component.css"],
})
export class ListComponent {
  students: Student[] = [];
  constructor(private studentService: StudentService) {}

  loadStudents() {
    this.students = this.studentService.getStudents();
  }
}
```

```html
<!-- list.component.html -->

<button (click)="loadStudents()">Load Students</button>

<table>
  <tr>
    <th>Id</th>
    <th>Name</th>
    <th>Age</th>
  </tr>
  @for(student of students) {

  <tr>
    <td>{{student.id}}</td>
    <td>{{student.name}}</td>
    <td>{{student.age}}</td>
  </tr>

  }@empty{
  <tr>
    <td colspan="3">No Students</td>
  </tr>
  }
</table>
```

```typescript
// add.component.ts
import { Component } from "@angular/core";
import { StudentService } from "../services/student.service";
import { Student } from "../models/student";
import { FormsModule } from "@angular/forms";

@Component({
  selector: "app-add",
  templateUrl: "./add.component.html",
  styleUrls: ["./add.component.css"],
})
export class AddComponent {
  student: Student = new Student(0, "", 0);

  constructor(public studentService: StudentService) {} //we just ask for the service and it will be provided by the DIC

  addStudent() {
    this.studentService.addStudent(new Student(this.id, this.name, this.age));
  }
}
```

```html
<!-- add.component.html -->

<input type="text" [(ngModel)]="student.id" />
<input type="text" [(ngModel)]="student.name" />
<input type="text" [(ngModel)]="student.age" />
<button (click)="addStudent()">Add</button>
```

> [!warning] for this to work both the components has to use the same instance of the service
>
> - this can be done by adding the service to the providers array in the app.component.ts (containig both the components)
> - or better to use the `@Injectable({ providedIn: "root" })` in the service.

> [!example] change injected class
>
> - class2 extends class1 & class3 extends class1
> - in the providers array in the component we can use `providers: [{provide: class1, useClass: class2}]` to use class2 instead of class1
> - or `providers: [{provide: class1, useClass: class3}]` to use class3 instead of class1
> - this will change the class to be used in the component and its child components without changing the class itself


---
## Lab

> [!tip] Lab
>
> - CRUD operations on students & Departments using services

