# Responsive Web Design

### CSS preprocessors

> [!tip] Sass, Less, Stylus
>
> > [!example] CSS preprocessors are scripting languages that extend the default capabilities of CSS. They enable us to use logic in our CSS code, such as variables, nesting, inheritance, mixins, functions, and mathematical operations. They also allow us to split our code into multiple files, which makes it easier to maintain larger codebases and share code between projects.
>
> ---
>
> > [!success] Transpilers: In the case of CSS preprocessors, they convert our code to regular CSS. This is necessary because browsers don't understand Sass, Less, or Stylus code. They only understand CSS. So, we need to convert our Sass, Less, or Stylus code to CSS before we can use it in our web pages.

> [!info] types of transpilers
>
> compass transpiler
> vscode live sass compiler

> [!warning] Edit only in the scss file, not in the css file. The css file is generated from the scss file. If you edit the css file, your changes will be overwritten when the scss file is compiled.

> [!example] Sass file extension:
>
> > [!bug]`.sass`:old syntax (indentation based)
> > no semicolons or curly braces
> > [!success] `.scss` :scoping
> > variables, mixins, nesting, inheritance, etc.

---

> [!tip] compass-app
>
> watch for changes to the scss file and automatically compile it to css
> [!success] steps:
> create compass project: create project folder structure
> sass folder - stylesheets folder
> compass watch: watch for changes to the scss file and automatically compile it to corresponding css file
> creating new scss file: will automatically create a corresponding css file and compile scss to css
> added scss code will automatically be compiled to css

> [!success] any valid css is valid scss
>
> > [!danger] any valid scss is not valid css

---

> [!info] Sass :
>
> - variables: `$primary-color: #333;`
>   -scoping=> nesting: `nav { ul { li { a { color: $primary-color; } } } }`
> - placeholder
> - mixins
> - @if @else
> - @for @each @while
> - Architecture: 7-1 pattern

> [!danger] single line comment in sass `//` won't be compiled to css

> [!success] multi line comment in sass `/* */` will be compiled to css

---

> [!info] sass variables

```scss
$bgred: #ff0000;
$redborder: 1px solid #ff0000;
p {
  background-color: $bgred;
  border: $redborder;
}
```

---

> [!tip] scoping

```scss
#mainwrapper {
  background-color: #000;
}
#mainwrapper #innerwrapper {
  border: 1px solid #fff;
}
#mainwrapper #innerwrapper p {
  text-decoration: underline;
}
#mainwrapper #innerwrapper p:hover {
  color: #fff;
}
#mainwrapper #innerwrapper p:active {
  color: #fff;
}
// same as css code

// we can use scoping in sass
//this is not valid css
#mainwrapper {
  background-color: #000;
  #innerwrapper {
    border: 1px solid #fff;
    p {
      text-decoration: underline;
      //   p:hover {
      //     color: #fff;
      //   }
      // &  is used to refer to the parent selector
      // & here refers to p
      &:hover {
        color: #fff;
      }
    }
  }
}
```

---

> [!info] placeholder: container for styles that we want to reuse
>
> - like inheritance

```scss
.main {
  color: red;
  border: 1px solid red;
}
.mainwrapper {
  //add main styles to mainwrapper
  //inheritance
  @extend .main;
  text-decoration: underline;
  border-radius: 5px;
}
.sidemain {
  //add main styles to sidemain
  //inheritance
  @extend .main;
  background-color: #000;
}
//compiles to group selector
//css
.main,
.mainwrapper,
.sidemain {
  color: red;
  border: 1px solid red;
}
// we don't want to compile .main
// turn it into a placeholder
// container for styles not to be compiled
// we add % to the name
%main {
  color: red;
  border: 1px solid red;
}
//this won't be compiled
//we have to extend it with same name

.mainwrapper {
  @extend %main;
  text-decoration: underline;
  border-radius: 5px;
}
.sidemain {
  @extend %main;
  background-color: #000;
}
```

---

> [!info] mixins:

```scss
$bgred: red;
// now we can write @if @else

@if ($bgred==red) {
  p {
    color: red;
  }
} @else {
  p {
    color: blue;
  }
}

//iterators:for to (ending excluded), through(ending included) , each
//if we want to create large number of  classes

// @for $i from 1 to 10 {//9classes 10 not included
@for $i from 1 through 10 {
  //10classes
  //   .iti-$i {//Error
  .iti-#{$i} {
    font-size: 3px; //3px,3px,3px,3px,3px,3px,3px,3px,3px
    font-size: 3 * $i * 1px; //3px,6px,9px,12px,15px,18px,21px,24px,27px
    font-size: 4 * $i * 1px; //4px,8px,12px,16px,20px,24px,28px,32px,36px
  }
}
```

---

> [!tip] images paths

```scss
//in css
.gmail {
  background-image: url("images/social/gmail.png");
}
.outlook {
  background-image: url("images/social/outlook.png");
}
.yahoo {
  background-image: url("images/social/yahoo.png");
}

//in sass
//images paths
$path: "images/social";
.gmail {
  background-image: url($path + "/gmail.png");
  //image name is the same as class name
}
.outlook {
  background-image: url($path + "/outlook.png");
}
.yahoo {
  background-image: url($path + "/yahoo.png");
}

//we can use lists
$emails: gmail outlook yahoo;
//create classes for each email in the list $emails

@each $email in $emails {
  .#{$email} {
    background-image: url($path + "/" + $email + ".png");
  }
}
```

```scss
$path: "images/social";
$networks: fackbook twitter instagram;
//link every social network to a color

$networks: (fackbook, blue) (twitter, lightblue) (instagram, red); //map
// we iterate over the map
@each $social, $color in $networks {
  .#{$social} {
    background-image: url($path + "/" + $social + ".png");
    background-color: $color;
  }
}
```

---

> [!tip] maximum

> [!danger] difference between browsers compatibility with properties of css3
> transform : scale(1) //scale the element by 1.5
> width: 100px;
> every browser has its own way to use transform
> [!success] prefixes: -webkit- -moz- -ms- -o-
> repeat the same code with different prefixes to guarantee compatibility
> -webkit-transform: scale(1);
> -moz-transform: scale(1);
> -ms-transform: scale(1);
> -o-transform: scale(1);
> transform: scale(1);
> last line is the standard

> [!warning] we can avoid writing the same code with different prefixes using mixins

> [!info] mixins
>
> > [!success] mixins are like functions
> > [!tip] steps:
> > 1- declare mixin
> > 2- use mixin `@include`

```scss
//prefixes mixin
@mixin prefix($property, $value) {
  -webkit-#{$property}: $value;
  -moz-#{$property}: $value;
  -ms-#{$property}: $value;
  -o-#{$property}: $value;
  #{$property}: $value;
}
```

> use mixin

```scss
p {
  //not return => this like replacing this the code with the mixin code
  @include prefix(transform, scale(1));
  @include prefix(transiton, all 0.5s ease-in-out);
}
```

---

### Grid

> [!info] Bootstrap grid system
> every row has 12 columns

> [!success] screen breakpoints:
> screen infixes: xs, sm, md, lg, xl
> xs: 0px - 575.999px => extra small screen
> sm: 576px - 767.999px => small screen
> md: 768px - 991.999px => medium screen
> lg: 992px - 1199.999px => large screen
> xl: 1200px - 1399.999px => extra large screen
> xxl: 1400px and up => extra extra large screen

> [!tip] grid classes
> we need 72 classes to cover all the screen sizes (6\*12)
> `col-xs-1{ width: 8.33%}` `col-xs-2{ width: 16.66%}` ... `col-xs-12{ width: 100%}` > `col-` + `screen infix` + `-` + `number of columns`
> in version 4,5 no xs infix we use `col-1` `col-2` ... `col-12` for extra small screen

---

> extra small screen => 12 columns classes
> small screen => 12 columns classes
> ...
> `col-(1 : 12)` `col-sm-(1 : 12)` `col-md-(1 : 12)` `col-lg-(1 : 12)` `col-xl-(1 : 12)` `col-xxl-(1 : 12)`

```scss
//
@mixin gridgenerator($columnscount, $infix, $breakpoint) {
  //we can make our own grid system to work with 15 columns
  @for $i from 1 through $columnscount {
    @if ($infix== "") {
      // if no infix => extra small screen
      @media screen and (min-width: $breakpoint) {
        .col-#{$i} {
          width: 100 / ($columnscount / $i) * 1%;
        }
      }
    }
  } @else {
    @media screen and (min-width: $breakpoint) {
      .col-#{$infix}-#{$i} {
        width: 100 / ($columnscount / $i) * 1%;
      }
    }
  }
}
```

---

> [!info] grid system generation

```scss
@include gridgenerator(12, "", 0px);
@include gridgenerator(12, "sm", 576px);
@include gridgenerator(12, "md", 768px);
@include gridgenerator(12, "lg", 992px);
@include gridgenerator(12, "xl", 1200px);
@include gridgenerator(12, "xxl", 1400px);
```

---

> [!info] container vs container-fluid
> container: xs screen => 100% width || otherwise => screen breakpoint width with margin auto (centered)
> container-fluid: full width {width:100%}

```scss
//reset
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.container-fluid {
  width: 100%;
}

.row {
  display: flex;
  flex-wrap: wrap;
}

@mixin gridgenerator($columnscount, $infix, $breakpoint) {
  //we can make our own grid system to work with 15 columns
  @for $i from 1 through $columnscount {
    @if ($infix== "") {
      // if no infix => extra small screen
      .container {
        width: 100%;
      }
      @media screen and (min-width: $breakpoint) {
        .d-none {
          display: none !important;
        }
        .d-block {
          display: block !important;
        }
        .col-#{$i} {
          width: 100 / ($columnscount / $i) * 1%;
        }
        .offset-#{$i} {
          margin-left: 100 / ($columnscount / $i) * 1%;
        }
      }
    }
  } @else {
    .container {
      width: $breakpoint;
      margin: auto;
    }
    @media screen and (min-width: $breakpoint) {
      .d-#{$infix}-none {
        display: none !important;
      }
      .d-#{$infix}-block {
        display: block !important;
      }
      .col-#{$infix}-#{$i} {
        width: 100 / ($columnscount / $i) * 1%;
      }
      .offset-#{$infix}-#{$i} {
        margin-left: 100 / ($columnscount / $i) * 1%;
      }
    }
  }
}
```

---

#### using grid

> testgrid.html

```html
<div class="container">
  <div class="row">
    <div class="col-6 col-sm-9">
        <!-- extra small screen and more 6 columns -->
        <p>1</p>
    </div>
    <div class="col-12 col-sm-6 col-md-4 col-lg-3 col-xl-2 col-xxl-1">
        <!-- extra small screen 12 columns and small screen 6 columns and medium screen 4 columns and large screen 3 columns and extra large screen 2 columns and extra extra large screen 1 column -->
      <p>1</p>
    </div>
    <div class="col-6 col-lg-8 ">
        <!-- 6 columns from extra small screen to large screen and 8 columns from extra large screen and more -->
      <p>1</p>
    </div>
    <div class="col-6 col-lg-4 ">
        <!-- 6 columns from extra small screen to large screen and 4 columns from extra large screen and more -->
      <p>1</p>
    </div>
    <div class="col-6  ">

      <p>1</p>
    </div>
    <div class="col-6 d-md-none d-lg-block col-lg-8 ">
        <!-- 6 columns from extra small screen to medium screen and h

      <p>1</p>
    </div>
  </div>
</div>
```

---

### 7-1 Architecture Pattern

> [!info] 7 folders and 1 file
> https://sass-guidelin.es/#architecture
> 7 folders:(placeholder files:start with _) base, components, layout, pages, themes, abstracts, vendors
> 1 file: `main.scss` => import all the files in the folders
> placeholders files: files starting with `_`and ending with`.scss`

> [!example] create folders and file main.scss
> abstract: \_variables.scss, \_mixins.scss, \_functions.scss, \_placeholders.scss

> [!tip] \_variables.scss

```scss
//_variables.scss
// classes
//btn btn-primary , btn-danger, btn-secondary , btn-lg ..
$btn-primary-color: blue;
$btn-danger-color: red;
$btn-info-color: green;

$btnfontlg: 20px;
$btnfontmd: 15px;
$btnfontsm: 10px;

$btnlight: #fff;
$btndark: #000;
```

> [!example] components:
>
> components: \_button.scss, \_card.scss, \_form.scss, \_input.scss, \_navbar.scss, \_footer.scss, \_header.scss, \_sidebar.scss, \_slider.scss, \_table.scss, \_typography.scss

```scss
//_button.scss
//load variables but better to load them in main.scss in sequence(ordered)
.btn {
  // width: 100px;
  // margin: 10px;
  //we can create place holder and extend it
  border-radius: $btn-corner-radius;
}
.btn-primary {
  background-color: $btn-primary-color;
}
.btn-danger {
  background-color: $btn-danger-color;
}
.btn-sm {
  font-size: $btnfontsm;
}
.btn-lg {
  font-size: $btnfontlg;
}
```

```scss
//abstract/_placeholders.scss
//placeholders
//placeholders are not compiled to css
%btn {
  width: 100px;
  margin: 10px;
}
```

> [!tip] base/_reset.scss

```scss
//base/_reset.scss
//reset
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

> [!tip] layout/_grid.scss

```scss
//layout/_grid.scss
//grid
// put the same code we wrote in the grid generator mixin
```

> [!warning] we can use only the grid from bootstrap extracting it from bootstrap source code

> [!example] main.scss

```scss
//main.scss
//import all the files in the folders
@import "base/reset";
@import "abstract/variables";
@import "abstract/placeholders";
@import "components/button";
@import "layout/grid";
//we can make it in one line
@import "abstract/variables", "abstract/placeholders", "components/button",
  "layout/grid";
```

---
>[!info] link main.css to index.html
>   >[!danger] link main.css not main.scss
>  >[!success] main.css is generated from main.scss
```html
<link rel="stylesheet" href="css/main.css" />
```
---
>[!info] test in testgrid.html

>[!warning] compass change output style 
>expanded: human readable
>compressed: minified
>compact: one line
>nested: nested


---
# #lab-RWD1