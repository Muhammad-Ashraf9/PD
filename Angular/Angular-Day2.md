## Recap on Previous Lecture

[Angular-Day1.md](Angular-Day1.md)

---

## binding

> [!tip] **Binding**
>
> - is a way to define communication between the component and the view. It is a technique to output data from a component and to accept user inputs.
> - ==one-way== data binding: data flows from the component to the template (view) or from the template to the component.
> - from component to template:
>   - Interpolation: {{expression}}
>   - Property binding: [property]="expression"
>   - Attribute binding: [attr.attributeName]="expression"
> - from template to component:
>   - Event binding: (event)="expression"
>   - Two-way binding: [(ngModel)]="expression"

> [!tip] Create test component using Angular CLI
>
> ```bash
> ng generate component test
> ```
>
> - will create a folder named `test` with the following files:
>   - `test.component.ts`
>   - `test.component.html`
>   - `test.component.css`
>   - `test.component.spec.ts`
> - add it to the app component import `app.component.ts`

```typescript
import { Component } from "@angular/core";

@Component({
  selector: "app-test",
  templateUrl: "./test.component.html",
  styleUrls: ["./test.component.css"],
})
export class TestComponent {
  // name = "Angular";//  type will be inferred as string
  // property has to be initialized in declaration or in constructor

  name: number = "Angular"; //will throw an error as the type is not matching

  // default access  modifier is public
  // to change the access modifier use private

  //can use any to assign any type of value
  // name: any = "Angular";

  // to make it accecpt multiple types
  age: string | number | null = 10;

  // array of strings
  hobbies: string[] = ["coding", "reading", "gaming"];

  // array can contain string or number
  // hobbies: (string | number)[] = ["coding", "reading", "gaming", 10];

  // array can contain string or array of numbers
  // hobbies: (string | number[])[] = ["coding", "reading", "gaming", [10, 20, 30]];
  constructor() {
    //if it was string it will autocomplete with string methods
    this.name = "Angular";

    // or we can cast using type assertion
    // this.name = "Angular" as string;
    //or
    // <type>variable

    this.age = false; // will throw an error as the type is not matching

    this.hobbies.forEach((hobby) => {
      console.log(hobby);
    });
  }
}
```

> [!warning] we can use the property in the template
>
> - string interpolation `{{ expression }}`
> - expression can be a variable, a method, or a property of the component anything that returns a value - not a statement like `if` or `for` loop
> - used to display the value of a property in the template not to change it - will throw an error if we try to change it
> - used to display the content

```html
<p>{{name}}</p>
<!-- 
    string interpolation
-->

<p>{{age + 1}}</p>

<!-- 
    <p>{{age++}}</p> will throw an error 
 -->
```

```typescript

//  function to return a value and we can specify the return type
// myfunction() : void {} this will not return anything

// myfunction() : string {} this will return a string

// we can specify parameters types
// myfunction(a: number, b: number) : number {}
myfunction(a: number, b: number): number {
  return a + b;
}
```

> [!done] use function in the template

```html
<p>{{myfunction(10, 20)}}</p>
```

> [!tip] Property binding
>
> - used to set the property of an element
> - used to set the attribute of an element

```html
<!-- 
    property binding
-->
<img [alt]="name" src="" />
<!-- 
    this will set the alt attribute of the image to the value of the name property
 -->

<img alt="{{name}}" src="" />
<!-- 
    this will set the alt attribute of the image to the value of the name property but it is recommended to use property binding
  -->

<img alt="name" src="" />
<!-- 
    this will set the alt attribute of the image to the string name not the value of the name property
   -->
```

> [!tip] make input disabled based on a property value

```typescript
export class TestComponent {
  isActivated: boolean = false;
  name: string = "Angular";
}
```

<input [disabled]="!isActivated" type="text" [value]="name" />

<!--
    this will disable the input if the isActivated property is false
    and set the value of the input to the value of the name property
 -->

 <img src="assets/{{name}}.png" />
 <!-- 
    this will set the src attribute of the image to the value of the name property 
  -->
```

> [!warning] Attribute binding vs property binding
>
> - attribute binding is used to set the attribute of an element like: `disabled`, `src`, `href`, `title`, `class`, ...
> - property binding is used to set the property of an element like: `value`, `innerHTML`, `textContent`, `innerText`, `textContent`, `style`,...

```html
<!-- 
    attribute binding
-->

<a [href]="url">link</a>
<!-- 
    this will set the href attribute of the anchor to the value of the url property
 -->

<!-- 
    property binding
-->
<p [innerText]="name"></p>
<!-- 
    this will set the innerText property of the paragraph to the value of the name property
 -->

<td [colspan]="n"></td>
<!-- 
    will throw an error
    it is colSpan not colspan
    becareful with the property name 
    use properties tab in the browser to check the properties of the element
    
 -->

<!-- 
   we can use attr.attributeName to set the attribute
  -->

<td [attr.colspan]="n"></td>
<!-- 
        <p [attr.innerText]="name"></p>
        no attribute called innerText
     -->
```

> [!tip] Event binding
>
> - used to listen to events like click, mouseover, mouseout, keyup, keydown, ...
> - same name as the event but without the `on` prefix

```typescript
// test.component.ts
export class TestComponent {
  n = 0;
  show() {
    n++;
    console.log(`clicked ${n} times`);
  }
}
```

```html
<!-- 
    event binding
 -->

<button (click)="show()">show</button>

<!-- 
    use keyup event
 -->
<input (keyup)="show()" type="text" value="show" />

<!-- 
    using event object
    pass the event object to the method
 -->
<input (click)="show($event)" value="show" />

<!-- 
    we can send only the event target or value on the event target
 -->
```

<input (click)="show($event.target)" value="show" />
<input (click)="show($event.target.value)" value="show" />

```typescript
export class TestComponent {
  n = 0;
  show(event) {
    n++;
    console.log(`clicked ${n} times`);
    console.log(event); //KeyboardEvent {target: }
    console.log(event.target); //<input>
    console.log(event.target.value); //show
  }
}
```

> [!tip] reference variable
>
> - used to get a reference to an element in the template
> - use `#` to define a reference variable
> - used to get all the properties and methods of the element

```typescript

export class TestComponent {
    this.name = "Angular";
  show(value) {
    this.name = value;//this will change the value of the name property and will be displayed in the template automatically without the need to refresh the page
    console.log(value);
  }
}

```

```html
<!-- 
    reference variable
 -->

<input #t1 type="text" value="show" (keyup)="show(t1.value)" />
<!-- 
    send the value of the input on click to the show method
    reference variable t1 can be used anywhere in the template 
 -->

<button #b1 (click)="show(t1.value)">show</button>
<!-- 
    will send the value of the input to the show method when the button is clicked
 -->

<!-- 
    to specify only the enter key to trigger the event 

  -->
<input #t1 type="text" value="show" (keyup.enter)="show(t1.value)" />

<!-- 
    to make it on blur
    -->
<input #t1 type="text" value="show" (blur)="show(t1.value)" />

<!-- 
    if the method is one line we can use the following syntax
    but it is not recommended
 -->
<input #t1 type="text" value="show" (keyup)="name = t1.value" />
```

> [!tip] Two-way binding
>
> - element has to have `change` event and `value` property

```html
<!-- 
    two-way binding
 -->

<input type="text" [value]="name" #t1 (keyup)="name = t1.value" /> {{name}}

<!-- 
    better way to use two-way binding
    using ngModel
    banana in a box
    [(ngModel)]="name" 🍌
 -->

<input type="text" [(ngModel)]="name" /> {{name}}
<!-- 
    we have to import FormsModule in the test component
 -->
```

```typescript
import { FormsModule } from "@angular/forms";

@NgModule({
  imports: [FormsModule],
})
```

---

## Break

---

## Models

> [!tip] Create a model
> `ng generate class models/student --skip-tests`

```typescript
// student.ts
export class Student {
  protected id: number = 0;
  name: string = "";
  age: number = 0;
  //   constructor(id: number, name: string, age: number) {
  constructor(id: number, name: string, age?: number) {
    //this will make the age optional so we have to check if it is undefined or not
    this.id = 1;
    this.name = "Hamada💍";
    // this.age = 20;
    this.age = age ?? 0; //this will check if the age is undefined or not if it is undefined it will assign 0 to it
  }
}

//to create an object of the class
let std: Student = new Student();

//to access the properties
std.name = "Ahmed";
std.age = 20;
//cannot access the id property as it is protected
```

```typescript
// we can create add properties with constructor by adding access modifier to the constructor parameters

export class Student {
  constructor(public id: number, public name: string, public age: number) {
    this.id = id;
    this.name = name;
    this.age = age;
  }
}

//to create an object of the class
let std: Student = new Student(1, "Ahmed", 20);
std.name = "Ali";
```

```typescript
// private access modifier

export class Student {
  //   constructor(private id: number, private name: string, private age: number) {
  constructor(public id: number, public name: string, public age: number = 0) {
    // we can assign a default value to the parameter
    this.id = id;
    this.name = name;
    this.age = age;
  }
  //we can add getters and setters to access the private properties
  getId() {
    return this.id;
  }
  setId(id: number) {
    this.id = id;
  }

  // or we can use the following syntax
  get Id() {
    return this.id;
    //has to return a value or will throw an error
    // unlike the previous syntax getId() {} we can use it without return
  }
  set Id(id: number) {
    this.id = id;
  }
}

let std: Student = new Student(1, "Ahmed", 20);
// we cannot access the properties directly

std.getId();
std.setId(2);

std.Id;
std.Id = 3;
```

> [!done] use the model in the component

```typescript
// test.component.ts
import { Student } from "../models/student";

export class TestComponent {
  student: Student = new Student(1, "Ahmed", 20);
  students: Student[] = [
    new Student(1, "Ahmed", 20),
    new Student(2, "Ali", 25),
    new Student(3, "Omar", 30),
  ];
}
```

```html
<!-- 
    test.component.html
 -->
<p>{{student}}</p>
<!-- 
    [object Object]

  -->

<p>{{student.name}} :: {{student.age}}</p>

<!-- 
    to display the array of students
 -->
<p>{{ students }}</p>
<!-- 
    [object Object],[object Object],[object Object]
  -->

<table class="table">
  <thead>
    <tr>
      <th>Id</th>
      <th>Name</th>
      <th>Age</th>
    </tr>
  </thead>
  <tbody>
    <!-- 
        *ngFor directive to loop through the array
     -->
    <tr *ngFor="let std of students">
      <!-- 
            structural directive
            we have to import CommonModule in the test component
         -->
      <!-- <td>{{std}}</td> -->
      <!-- 
            [object Object]
          -->
      <td>{{std.id}}</td>
      <td>{{std.name}}</td>
      <td>{{std.age}}</td>
    </tr>
  </tbody>
</table>
```

```typescript
// test.component.ts
import { CommonModule } from "@angular/common";

@NgModule({
  imports: [CommonModule],
})
```

```html
<!-- 
    test.component.html
 -->

<!-- <tr *ngFor="let std of students; let i = index"> -->
<!-- 
         to get the index of the element
      -->
<tr
  *ngFor="let std of students; let i = index; let odd = odd; let even = even; let first = first; let last = last"
>
  <!-- 
        to get the odd and even elements in the array (based on the index)
        <td>{{odd}}</td>
        <td>{{even}}</td>
     -->
  <!--
        to get the first and last elements in the array
        <td>{{first}}</td> true
        <td>{{last}}</td> false
        -->
  <td>{{i }}</td>
  <!-- 
            0, 1, 2
       -->
  <td>{{std.id}}</td>
  <td>{{std.name}}</td>
  <td>{{std.age}}</td>
</tr>
```

> [!tip] `[ngClass]` directive
>
> - used to add or remove classes based on a condition

```css
// test.component.css
.bg-red {
  background-color: red;
}
.bg-green {
  background-color: green;
}
```

```html
<!-- 
    test.component.html
 -->
<tr
  *ngFor="let std of students; let i = index; let odd = odd; let even = even; let first = first; let last = last"
  [ngClass]="{ 'bg-red': odd, 'bg-green': !odd }"
>
  <td>{{i}}</td>
  <td>{{std.id}}</td>
  <td>{{std.name}}</td>
  <td>{{std.age}}</td>
</tr>
```

> [!tip] `[ngStyle]` directive
>
> - used to add or remove styles based on a condition

```html
<!-- 
    test.component.html
 -->
<tr
  *ngFor="let std of students; let i = index; let odd = odd; let even = even; let first = first; let last = last"
  [ngStyle]="{ 'background-color': odd ? 'red' : 'green' }"
>
  <td>{{i}}</td>
  <td>{{std.id}}</td>
  <td>{{std.name}}</td>
  <td>{{std.age}}</td>
</tr>
```

> [!tip] `[ngIf]` directive
>
> - used to show or hide an element based on a condition

```typescript
// test.component.ts
export class TestComponent {
  x: number = 10;
}
```

```html
<!-- 
    test.component.html
 -->
<p *ngIf="x > 10">x is greater than 10</p>
<!-- 
    will not be displayed as x is not greater than 10
  -->

<input type="text" [(ngModel)]="x" />
<!-- 
    two-way binding to change the value of x using the input
   -->

<!-- 
    new syntax @if
   -->
@if(x > 10) {
<p>x is greater than 10</p>
}@else{
<p>x is less than 10</p>
}
```

> [!tip] `@for`

```html
<!-- 
    test.component.html
 -->
<tbody>
  <!-- @for(item of students; track $index) -->
  <!-- 
        track is mandatory in @for not in *ngFor (it is optional in *ngFor)
     -->
  @for(item of students; track item.id)
  <!-- 
            to track the changes based on the id of the item so it will change only the changed item not all the items

            will throw an error if no track is specified
         -->
  {
  <tr>
    <td>{{item.id}}</td>
    <td>{{item.name}}</td>
    <td>{{item.age}}</td>
  </tr>
  }@empty{
  <!-- 
            if the array is empty
         -->
  <tr>
    <td colspan="3">No data</td>
  </tr>
  }
</tbody>
```

> [!example] Pipes
>
> - used to format the data
> - has to import `CommonModule` in imports array
> - can be used with interpolation `{{ expression | pipeName }}`

```html
<!-- 
    test.component.html
 -->
<p>{{name | uppercase}}</p>
<!-- 
    to convert the name to uppercase
  -->
<p>{{name | lowercase}}</p>
<!-- 
        to convert the name to lowercase
     -->
<p>{{name | titlecase}}</p>
<!-- 
        to convert the name to titlecase
     -->
```

```typescript
// test.component.ts

export class TestComponent {
  today: Date = new Date();
}
```

```html
<!-- 
    test.component.html
 -->
<p>{{today}}</p>
<!-- 
    will dispaly the date in the following format
    Mon Jul 26 2021 11:00:00 GMT+0200 (Eastern European Standard Time)
  -->
<p>{{today | date: "dd/MM/yyyy"}}</p>
<!-- 
        can send data to the pipe
        send date format to the date pipe

     -->
<p>{{today | date: "short"}}</p>
<!-- 
        short date format: 7/26/21, 11:00 AM
      -->
<p>{{today | date: "shortDate"}}</p>
<!-- 
            short date format: 7/26/21
            other formats: medium, long, full, shortTime, mediumTime, longTime, fullTime,...
         -->
```

> [!tip] Angular API Reference (https://angular.io/api)[https://angular.io/api]
>
> - to check the available pipes and their options
> - has everything (decorators, directives, pipes, ...) in Angular
> - `https://angular.io/api/common/DatePipe`
> - `{{ value_expression | date [ : format [ : timezone [ : locale ] ] ] }}`

> [!done] can use multiple pipes

```html
<!-- 
    test.component.html
 -->
<p>{{today | date: "dd/MM/yyyy" | uppercase}}</p>
<!-- 
    will dispaly the date in the following format
    26/07/2021
  -->
```

> [!example] add new student

```typescript
// test.component.ts
export class TestComponent {
  student: Student = new Student(1, "Ahmed", 20);
  students: Student[] = [
    new Student(1, "Ahmed", 20),
    new Student(2, "Ali", 25),
    new Student(3, "Omar", 30),
  ];

  addStudent() {
    this.students.push(this.student);
  }
}
```

```html
<!-- 
    test.component.html
 -->

<input type="text" [(ngModel)]="student.id" />
<input type="text" [(ngModel)]="student.name" />
<input type="text" [(ngModel)]="student.age" />
<!-- 
    these inputs will change the value of the student object and will be displayed in the template 2-way binding
 -->

<button (click)="addStudent()">Add Student</button>
<!-- 
    to add a new student to the array
 -->
```

> [!bug] any change in input will change the value of the student object in the array
>
> > [!done] to fix this issue we have to create a new object of the student class

```typescript
// test.component.ts

export class TestComponent {
  student: Student = new Student(1, "Ahmed", 20);
  students: Student[] = [
    new Student(1, "Ahmed", 20),
    new Student(2, "Ali", 25),
    new Student(3, "Omar", 30),
  ];

  addStudent() {
    this.students.push(
      new Student(this.student.id, this.student.name, this.student.age)
    );
  }
}
```

> [!tip] to show student details when clicked on the row

```typescript
// test.component.ts
export class TestComponent {
  student: Student = new Student(1, "Ahmed", 20);
  stdDetails: Student | null = null;
  students: Student[] = [
    new Student(1, "Ahmed", 20),
    new Student(2, "Ali", 25),
    new Student(3, "Omar", 30),
  ];

  selectedStudent: Student = new Student(0, "", 0);

  showStudent(std: Student) {
    this.selectedStudent = std;
  }
}
```

```html
<!-- 
    test.component.html
 -->
<tr
  *ngFor="let std of students; let i = index; let odd = odd; let even = even; let first = first; let last = last"
  [ngClass]="{ 'bg-red': odd, 'bg-green': !odd }"
>
  <td>{{i}}</td>
  <td>{{std.id}}</td>
  <td>{{std.name}}</td>
  <td>{{std.age}}</td>
  <td>
    <button (click)="stdDetails = std">Show</button>
    <!-- <button (click)="showStudent(std)">Show</button> -->
  </td>
</tr>

<!-- 
    to show the selected student details
 -->
<p *ngIf="selectedStudent.id != 0">
  {{selectedStudent.id}} :: {{selectedStudent.name}} :: {{selectedStudent.age}}
</p>
```

> [!bug] to install bootstrap
>
> ```bash
> npm install bootstrap
> ```
>
> - add bootstrap to `styles.css`
>   using `@import` syntax and the path to the bootstrap css file

> [!done] or better use tailwindcss
>
> - (https://tailwindcss.com/docs/installation)[https://tailwindcss.com/docs/installation]

---

# Lab

> - CRUD operations on the student - Department (one component for each)
