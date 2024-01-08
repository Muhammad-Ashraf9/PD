# Bootstrap

> -  [Bootstrap](https://getbootstrap.com/)

> [!info] Bootstrap is a free and open-source CSS framework directed at responsive, mobile-first front-end web development. It contains CSS- and (optionally) JavaScript-based design templates for typography, forms, buttons, navigation, and other interface components.

> [!tip] css and js
> css files:Bs.css, Bs.min.css, Bs-grid.css, Bs-grid.min.css
> js files:Bs.js, Bs.min.js, Bs.bundle.js, Bs.bundle.min.js

---

> [!success] Bootstrap
>
> - offline
> - cdn link: cdnlink for css | cdnlink for js
> - npm install bootstrap
> - ...

> [!warning] read documentation

> [!danger] compiled css and js vs source files
>
> - compiled and minified: Bs.css, Bs.min.css, Bs.js, Bs.min.js
>   bs.min.map : map used to map css to the corresponding scss file
> - source: need => sass compiler - autoprefixer

> [!tip] Gulp
>
> - gulp: task runner
>   [gulpjs](https://gulpjs.com/)

> [!tip] we can get the grid system from bootstrap
> bootstrap.grid.css
> use right to left
> bootstrap.rtl.css

---

> [!tip] bootstrap.css
> `:root` : css variable in root( html in this case)

```css
:root {
  --blue: #007bff;
  --indigo: #6610f2;
  --purple: #6f42c1;
}
```

> [!tip] documentation: contents
>
> - `bootstrap.css` layout, content, components, utilities
> - `bootstrap-grid.css` only grid system , only flex utilities

> [!warning] browsers & devices

> [!tip] webpack: bundler
>
> - use webpack to bundle files into one file

> [!danger] Read documentation

> [!info] $grid-breakpoints: map
>
> - map: key-value pair

```scss
$grid-breakpoints: (
  xs: 0,
  sm: 576px,
  md: 768px,
  lg: 992px,
  xl: 1200px,
) !default;
```

> [!tip] container vs container-sm vs container-md vs container-lg vs container-xl
>
> - container: width: 100% (max-width: 540px)
> - container-sm: width: 100% (max-width: 540px)
> - container-md: width: 100% (max-width: 720px)
> - container-lg: width: 100% (max-width: 960px)
> - container-xl: width: 100% (max-width: 1140px)
> - container-xxl: width: 100% (max-width: 1320px)
> - container-fluid: width: 100% (no max-width)

---

> [!tip] link bootstrap
>
> - `<link rel="stylesheet" href="css/bootstrap.css">`
>   > [!danger] need to use this meta tag
>   > `<meta name="viewport" content="width=device-width, initial-scale=1">`
>   > MUST for responsive design
>   > other required meta tags is charset
>   > `<meta charset="utf-8">`

> basicstructure.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <!-- required meta tags -->
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="css/bootstrap.css" />
    <title>Bootstrap</title>
  </head>
  <body>
    <div class="container border border-2 border-danger">
      <!-- container with red border -->
      <div class="row">
        <div class="col-6">col-6</div>
        <div class="col-6">col-6</div>
      </div>
    </div>
  </body>
</html>
```

---

> [!tip] `class="col"` to let bootstrap decide the width give them equal width

> [!tip] `class="col-auto"` to let bootstrap decide the width depends on the content

> [!tip] `class="row"` is a flex container we can use flex utilities
> `justify-content-center` to center the content
> `align-items-center` to center the content vertically

> [!info] `class="row row-cols-2"` to make the row have 2 columns
> `class="row row-cols-sm-2 row-cols-lg-3"` to make the row have 2 columns in small screen and 3 columns in large screen

---

### typography classes

```html
<div class="container">
  <p class="text-center">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!-- no left and right => start and end -->
  <p class="text-start">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <p class="text-uppercase">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!-- appear as h3 -->
  <p class="h3">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!--display-3 make the font size bigger -->
  <p class="display-3">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!-- margin depends on $spacer => intial value 1rem -->
  <!-- $spacer: 1rem ; 
        $spacers: (
            0: 0,
            1: ($spacer * .25),
            2: ($spacer * .5),
            3: $spacer,
            4: ($spacer * 1.5),
            5: ($spacer * 3)
        ); -->

  <!-- my-2 => margin top and bottom 2rem -->
  <p class="my-2">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!--lead => font size 1.25rem -->
  <p class="lead">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!-- small => font size 0.875rem -->
  <p class="small">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!-- special html5 tag like: mark... kbd: keyboard..., abbr: abbreviation... -->
  <p class="small">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <p>
    lorem ipsum dolor sit amet consectetur <kbd>kbd</kbd> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <!-- abbr => abbreviation -->
  <p>
    lorem ipsum dolor sit amet consectetur
    <abbr title="abbreviation">abbr</abbr> adipisicing elit. Quisquam,
    voluptatum.
  </p>
  <!-- mark => highlight -->
  <p>
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <!-- contexual classes error : red, warning: yellow, success: green, info: blue -->
  <p class="text-danger">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <p class="text-warning">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <p class="text-success">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <p class="text-info">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <!-- bg-classes -->
  <p class="bg-danger">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <p class="bg-warning">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <p class="bg-success">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <p class="bg-info">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>
  <!--padding classes-->
  <p class="py-3">
    lorem ipsum dolor sit amet consectetur <mark>mark</mark> adipisicing elit.
    Quisquam, voluptatum.
  </p>

  <!-- list-unstyled => remove the default style -->
  <ul class="list-unstyled">
    <li>item1</li>
    <li>item2</li>
    <li>item3</li>
  </ul>
  <!-- list-inline => make the list inline -->
  <ul class="list-inline">
    <li class="list-inline-item">item1</li>
    <li class="list-inline-item">item2</li>
    <li class="list-inline-item">item3</li>
  </ul>
  <!--to change the style of the list: use css-->

  <!-- breadcrumb class on list-->
  <ul class="breadcrumb">
    <li class="breadcrumb-item"><a href="#">Home</a></li>
    <li class="breadcrumb-item"><a href="#">Library</a></li>
    <li class="breadcrumb-item">Data</li>
  </ul>
</div>
```

> [!danger] SEARCH IN DOCUMENTATION

---

### Icons

> [!tip] [fontawesome](https://fontawesome.com/)

```html
<!-- Font Awesome -->
<!-- bootstrap support font awesome -->
<!--cdn link for font awesome-->
<!-- fontawesome.com -->

<h4>Facebook : <i class="fa-brands fa-facebook text-primary fa-2x"></i></h4>
<!-- can be customized -->
<!--badge-->
<p>Inbox: <span class="badge bg-primary">99+</span></p>

<!--progress bar-->
<div class="progress">
  <div class="progress-bar bg-danger" style="width: 25%">25%</div>
</div>
<!-- striped / striped animated progress bar -->
<div class="progress">
  <div
    class="progress-bar bg-danger progress-bar-striped progress-bar-animated"
    style="width: 25%"
  >
    25%
  </div>
</div>
```

---

### Buttons

```html
<!-- button -->
<!-- we have to add btn class -->
<button class="btn">Primary</button>
<!-- change the button style -->
<button class="btn btn-primary">Primary</button>
<button class="btn btn-secondary">Secondary</button>
<button class="btn btn-success">Success</button>
<button class="btn btn-danger">Danger</button>
<button class="btn btn-warning">Warning</button>

<button class="btn btn-link">Link</button>

<!-- change the button size -->
<button class="btn btn-primary btn-lg">Primary</button>
<button class="btn btn-primary btn-sm">Primary</button>
<!-- button group -->
<!-- remove space between buttons we can use to change group style default : horizontal -->
<!-- <div class="btn-group"> -->
<!-- <div class="btn-group-vertical"> -->
<div class="btn-group btn-group-lg">
  <button class="btn btn-primary">Primary</button>
  <button class="btn btn-primary">Primary</button>
  <button class="btn btn-primary">Primary</button>
</div>
```

---

### images

```html
<!-- image -->
<!--rounded-circle => make the image circle-->
<img src="https://picsum.photos/200/300" alt="image" class="rounded-circle" />
```

---

### tables

> [!tip] we have to use standard structure for the selector to work

```html
<!-- table -->
<!-- using class table change the style of the table entirely -->
<!-- table-bordered => add border to the table -->
<!-- table-hover => add hover effect -->
<!-- table-striped => add striped effect -->
<table class="table table-bordered text-center">
  <thead class="bg-primary text-white">
    <tr>
      <th scope="col">#</th>
      <th scope="col">First</th>
      <th scope="col">Last</th>
      <th scope="col">Handle</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">1</th>
      <td>Mark</td>
      <td>Otto</td>
      <td>@mdo</td>
    </tr>
    <tr>
      <th scope="row">2</th>
      <td>Jacob</td>
      <td>Thornton</td>
      <td>@fat</td>
    </tr>
    <tr>
      <th scope="row">3</th>
      <td colspan="2">Larry the Bird</td>
      <td>@twitter</td>
    </tr>
  </tbody>
</table>
```

> [!danger] u have to link your css file after bootstrap.css to override the default style

```css
/* use its selector from dev tools */
.table-striped tbody tr:nth-of-type(odd) {
  background-color: #e6e6e6;
}
```

---

### Alerts
    
```html
<!-- alert -->

<!--alert contexual classes-->
<!-- alert-primary => change the color of the alert -->

<!-- we have to add alert class -->
<p class="alert alert-primary">Lorem ipsum dolor sit amet.</p>
<p class="alert alert-secondary">Lorem ipsum dolor sit amet.</p>
<p class="alert alert-success">Lorem ipsum dolor sit amet.</p>
<p class="alert alert-danger">Lorem ipsum dolor sit amet.</p>
<p class="alert alert-warning">Lorem ipsum dolor sit amet.</p>

<!-- alert link change the color of the link according to the alert
-->
<p class="alert alert-primary">
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Quisquam, voluptatum.
  <a href="#" class="alert-link">alert link</a>
</p>

<!--to close the alert-->
<button class="btn btn-primary" data-bs-dismiss="alert">Close</button>

<!-- JJJJJJJJJJJJJJJJJJSSSSSSSSSSSSSSSSSSSSSS -->



```

---
# BREAK
---

### Bootstrap JS

>[!warning] Bootstrap version 4
>
> - jQuery
> problem with positioning
> - we use popper.js to solve the problem : drop down menu(written in JQ)
>load popper.js before bootstrap.js

>[!tip] Bootstrap version 5
> same as version 4
> proper.js
> BS.js
> or BS.bundle.js (BS.js + popper.js)

>[!tip] Bootstrap link options
> option 1: Bootstrap Bundle with Popper 
> option 2: separate 


>[!tip] Components
> 3 parts
> - HTML
> - CSS
> - JS

>[!example] SPA
> - Single Page Application


---

>[!tip] attributes: to use JS
> - data-attributename="value"
> - example: Data-toggle="modal"

```html
<!-- i want to dismess this alert using js -->
<p id="#alert1" class="alert alert-success">iti mansoura <span class="close" data-dismess="alert">&times;</span></p>

<!-- if i want to dismess another alert using  -->
<p class="alert alert-success">iti mansoura <span class="close" data-dismess="alert" data-target="#alert1">&times;</span></p>
```

---

### drop down menu
```html
<!-- drop down menu -->
<div>
    <button
        class="btn btn-primary dropdown-toggle"
        data-bs-toggle="dropdown"
        aria-expanded="false"
        aria-haspopup="true"
        id="dropdownMenuButton1"
    >
    <ul class="dropdown-menu">
        <li class="dropdown-header"></li> 
        <li class="dropdown-item">item1</li>
        <li class="dropdown-divider"></li>
        <li class="dropdown-item">item2</li>
        <li class="dropdown-item">item3</li>
    </ul>
</div>
```


---
### forms
```html
<!-- form -->
<div class="container">
  <form>
    <div class="form-group">
      <!-- label for="name" => id="name" label and input made for eachother so we wrap them into form-group
        form-group => no visual appearance just for grouping -->
      <label for="name">Name</label>
      <input type="text" id="name" class="form-control" />
      <!-- form-control => make the input full width Block
      make focus blue border 
      -->
    </div>
    <!-- select we have to add form-control class 
    to make it full width
  custom-select => remove the default style
  custom-select-sm => make the select small
  custom-select-lg => make the select large

    -->
    <select class="form-control custom-select">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
    </select>
  </form>
</div>











