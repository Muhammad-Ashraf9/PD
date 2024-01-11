## Animation

> [!info] types of animation
> from to
> percentage %

> [!tip] Animation
> change the value of the property over time

```css
/* @keyframes */
/* Declaration */
@keyframes sectionAnimation {
  /* from{
        width: 400px;
        height: 400px;
    } */
  /* we can get rid of it as it is the default value */
  to {
    width: 200px;
    height: 200px;
    background-color: green;
  }
}
section {
  width: 400px;
  height: 400px;
  animation-name: sectionAnimation;
  animation-duration: 5s;
  /* ran only one time so we use iteration count */
  animation-iteration-count: 2;
  animation-iteration-count: infinite; /* will run forever */
  /* not smooth to => from */
  animation-direction: alternate; /*smooth from => to => from */
  animation-direction: reverse; /*smooth but only to from so we use alternate */

  animation-delay: 2s; /* delay before starting */
  animation-fill-mode: forwards;
  animation-timing-function: ease-in-out;
}
section:hover {
  animation-play-state: paused;
  /* default is running so we can pause it on hover */
}
img:hover section {
  /* won't work as section is not a child of img (space => descendent) */
  /* when hover on img  section will s
    animation-play-state: paused;
    /* default is running so we can pause it on hover */
}
img:hover + p {
  display: block;
  /* when hover on img  p will show */
}
p.iti:hover + p {
  background-color: red;
  /* when hover on p.iti  direct following p will change background color */
}
p.iti:hover ~ p {
  background-color: red;
  /* when hover on p.iti  all following p will change background color */
}
body p:first-of-type {
  /* select the first p in body */
}
```

> [!tip] Selectors + vs ~ vs > vs space
>
> `+ => direct following sibling`
>  ` ~ => following sibling`
>  ` > => direct child`
>  ` space => descendent`

```css
/* @keyframes */
/* Percentage of duration */

@keyframes sectionAnimation {
  0% {
    width: 400px;
    height: 400px;
  }
  50% {
    width: 200px;
    height: 200px;
    background-color: green;
  }
  100% {
    width: 400px;
    height: 400px;
  }
}
section {
  width: 400px;
  height: 400px;
  animation-name: sectionAnimation;
  animation-duration: 5s;
  animation-iteration-count: infinite;
}
```

---

### Measure Units

> [!info] Absolute Units
> cm, mm, in, px, pt, pc,pica,inch
>
> > [!bug] disadvantage
> > not responsive
> > won't change with screen size

> [!info] Relative Units
> em, rem, vw, vh, vmin, vmax, %
> rem: relative to root element (html) font-size
> em: relative to parent element font-size
> %: relative to parent element property(width: 50% => 50% of parent width, font-size: 50% => 50% of parent font-size)

```css
div {
  /* width: 600px; */
  width: 50%;
  /* 50% of parent (body) width */
  border: 1px solid black;
  font-size: 18px;
}
p {
  width: 50%;
  /* relate to the width of the parent the same property */
  /* width: 300px; computed value */
  border: 1px solid black;
  font-size: 20px;
  /*we want to make  relation between fontsize and width */
  /* we use em */
  width: 10em;
  /* 10 * 20px = 200px 
   as em relative to fontsize of element 
    if no fontsize it will be relative to parent fontsize  10 * 18px = 180px 
    it goes up to the root element (html) fontsize 10 * 16px = 160px 
    */
  /* if we changed browser fontsize it will change default is 16px
        em won't change 
        as it is related to its to its font-size
        so we use rem
        to make it related to root element font-size
        */
  width: 10rem;
  /* 10 * 16px = 160px */
  /* default font-size is 16px */
}
```

## Viewport Units

> [!info] Viewport Units
> vw, vh, vmin, vmax
> vw: viewport width
> vh: viewport height

```css
/* div takes half of screen */
/* 50% body not the same as 50% screen */
/* relative the area of the screen not the body as body width can be changed  */

* {
  margin: 0;
  padding: 0;
}
body {
  width: 500px;
}
section {
  width: 50%;
  /* 50% of body width = 250px */
  /* won't change if we change screen size */
  width: 50vw;
  /* 50% of screen width */
  /*  will change when we change screen size */
  height: 50vh;

  /* to take full width and height of screen */
  width: 100vw;
  height: 100vh;

  /* horizontal scroll bar will appear */
  /* as we have margin and padding */
  /* to get rid of it */
  box-sizing: border-box; /*  later */

  height: 50 vmax;
  /* 50% of the max of viewport width and height */
  height: 50 vmin;
  /* 50% of the min of viewport width and height */
  background-color: red;
}
```

---

## Box Model

> [!info] Box Model
> any element in html is a box
> `box-sizing: content-box;` default value
> `p{width: 200px; border-width: 10px; padding: 20px;}`
> total width = 200 + 10 + 10 + 20 + 20 = 260px
>
> > [!success] `box-sizing: border-box;`
> > total width = 200px

````css
```css * {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
p {
  border: 1px solid black;
  margin: 10px;
  width: 200px;
  /* all p will have the same width */
}
/* if we change border width it will change the width of the element */
````

---

### variables in css

> [!info] variables in css
>
> > [!danger] care for scoping
> > variables declared in section can't be used in other sections
> > `--name: value;`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  --red-color: red;
  /* variables declared in body can be used in all sections */
  --border: 1px solid black;
}
section {
  width: 200px;
  padding: 10px;
  margin: 10px;
  border: 1px solid black;
}
#first {
  --red-color: red;
  border-color: red;
  border: var(--border);
}
#first > p {
  background-color: var(--red-color);
  /* background-color: red;  will be resolved to red  scope of section */
}
#second > p {
  color: var(--red-color);
  /* can't be resolved to red  scope of section */
}
```

---

###### calc()

> [!info] calc()
> `width: calc(100% - 20px);`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
section {
  width: 200px;
  padding: 10px;
  margin: 10px;
  border: 1px solid black;
}

#mainwrapper {
  width: 50vw;
  border: 1px solid black;
  height: 200px;
}
#internal {
  width: 200px;
  border: 1px solid black;
  background-color: red;
  display: inline-block;
  /*display inline-block will make space between elements */
#second {
  display: inline-block;
  /* we need the second wrapper to take the rest of the space */
  /* 50vw - 200px  */
  /* we can use calc() */
  width: calc(50vw - 200px);
}
```

---

### float

> [!info] float
> `float` :change default behavior of elements
> like moving elements up (floating) and position them
> `clear`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
section {
  width: 50vw;
  height: 50vh;
  border: 1px solid black;
  margin: 10px auto;
}
#left,
#right {
  width: 200px;
  height: 300px;
  display: inline-block;
  /* this makeleft and right in the same line but we want right to be right aligned */
}
#left {
  float: left;
  /*we can chnge left to right easily*/
  float: right;
}
#right {
  float: right;
}
footer {
  /* we need to clear the float */
  clear: left;
  /* to stop floating and intrpreter the rest of the elements as normal */
  clear: both;
  /* clear both left and right */
}
/* float affect the scope so we put floated elements inside a wrapper */
/* left, right is floated but element outside the wrapper is not */
/* no need to clear */
```

---

---

### flexbox

> [!tip] flexbox
> `display: flex;`
>
> > [!example] parent properties
> > `flex-direction: row | row-reverse | column | column-reverse;` default is row =>horizontal
> > `justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;` default is flex-start depends on flex-direction if row => horizontal if column => vertical
> > `align-items: flex-start | flex-end | center | baseline | stretch;` default is stretch depends on flex-direction if row => vertical if column => horizontal
> > `flex-wrap: nowrap | wrap | wrap-reverse;` default is nowrap
>
> > [!tip] child properties
> > `flex-grow: 0;` default is 0
> > `flex-shrink: 1;` default is 1
> > `flex-basis: auto;` default is auto
> > `flex: 0 1 auto;` default is 0 1 auto
> > `order: 0;` default is 0
> > `align-self: auto | flex-start | flex-end | center | baseline | stretch;` default is auto

> [!danger] flexbox
> https://css-tricks.com/snippets/css/a-guide-to-flexbox/

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.mainwrapper {
  width: 100vw;
  height: 90vh;
  display: flex;
  /* easiest way to make children in the same line
    default is row
    justif content is flex-start by default left to right (row) top to bottom (column)

    */
  flex-direction: row; /* default  not necessary */

  justify-content: center;
  /* center the children in the main axis either horizontal or vertical  depends on flex-direction 
  here we center the children horizontally
  */
  align-items: center;
  /* center the children in the cross axis either horizontal or vertical  depends on flex-direction
    here we center the children vertically the opposite of justify-content
    */
  align-items: flex-end;
  /* align the children to the end of the cross axis 
    here we align the children vertically to the bottom
    */

  /* when setting the direction to column
     */
  flex-direction: column;
  /* justify-content will be vertical */
  /* align-items will be horizontal */

  flex-direction: row-reverse;
  /* reverse the direction of the main axis */

  flex-wrap: wrap;
  /* wrap the children to the next line if there is no space 
    default is nowrap
    */
  /* if it is shrinkable it won't wrap until it reachs its flex-basis */
}
.boxes {
  width: 200px;
  height: 200px;
}
.box1 {
  flex-grow: 1;
  /* means take 100% of the space left after taking the space of the other elements */
}
.box2 {
  flex-grow: 2;
  /* left space will be 2:1  
 */
  flex-shrink: 0;
  /* default is 1 */
  min-width: 200px;
  min-height: 200px;
  /* better to use flex-basis

*/
  flex-basis: 200px;
  /* it works either direction row or column */

  /* all of the above can be replaced by flex: 2 0 200px; */
  /* flex: flex-grow flex-shrink flex-basis */
  flex: 2 0 200px;

  align-self: flex-end; /* align the element to the end of the cross axis */

  /* we can reorder the elements by order property
 */
  order: 1; /* default is 0 */
}
```

> [!danger] check this link
> https://css-tricks.com/snippets/css/a-guide-to-flexbox/
>
> > [!success] FLEXBOX FROGGY: Game to learn flexbox
> > https://flexboxfroggy.com/

---

### flex demo

> flexdemo.html

> [!info] `background-size` > `background-size: 200px;` > `background-size: 100%` > `background-size: cover;` cover the whole element

> [!info] Before After
> `::before` `::after`
> is a pseudo element: an element that doesn't exist in the html but we can style it
> `content: "text";`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
ul {
  list-style: none;
}
li {
}
/* if we want to add something after every li */
li::after {
  content: "Hello";
  content: "";
  /* display: block;
    width: 100%; */
  height: 10px;
  background-color: red;
  /* redline under every li */
  /* to make it only appear on hover */
  display: none;
  width: 100%;
  transition: all 0.5s ease-in-out;
}
li:hover ::after {
  display: block;
  width: 100%;
}
```

---

#### Grid

> [!info] Grid
> `display: grid;`
>
> > [!example] parent properties
> > `grid-template-columns: ` : define the number of columns and their width
> > `grid-template-rows: `: define the number of rows and their height
> > `gap:  `: define the gap between columns and rows
>
> > [!tip] child properties
> > `grid-area`
> > `grid-row`
> > `grid-column`

> gridmethodone.html

```css
/* 5rows */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.mainwrapper{
    width: 50vw;
    height: 50vh;
    margin: 10px auto;
    border: 1px solid black;
    display: grid;
    /* spread elements */
    grid-template-rows:20px 20px 20px 20px 20px;
    /* 5 rows each 20px id height not 200px it wont fill parent */
    /* we can use repeat() */
    grid-template-rows: repeat(5, 20px);
    grid-template-rows:10% 10% 20px 20px 20px;
    %: parent h
    grid-template-rows:10% 10% auto 20px 20px;
    /* auto: depend on content */
    /* we can use fr to make it responsive
    fr: fraction of the parent
    */
    grid-template-rows: 1fr 1fr 1fr 1fr 1fr;
    /* 1fr: 1/5 of the parent */
    grid-template-rows: 1fr 1fr 2fr 2fr 1fr;
    /* 1fr: 1/6 of the parent */

    grid-template-coloumns: 4fr 1fr

    gap: 10px;
    /* gap between rows and columns */
    gap: 10px 20px;
    /* gap 10px between rows and 20px between columns */

}
.mainwrapper>div{
    border: 1px solid black;
}
#mainheader{
    grid-row: 1/2;
    /* 1/2: from row 1 to row 2 */
    grid-column: 1/3;
    /* 1/3: from column 1 to column 3 */
}
#mainnav{
    grid-row: 2/3;
    grid-column: 1/3;
}
/* to reorder we change the numbers of the rows and columns */
#asideone{
    grid-row: 3/6;
    grid-column: 1/2;
}
#asidetwo{
    grid-row: 3/6;
    grid-column: 2/3;
}
#footer{
    grid-row: 6/7;
    grid-column: 1/3;
}


```

> [!bug] problem with this method
> maintainability is hard
> we can use `grid-template-areas`

````css

---

> gridmethodtwo.html

```css
/* 5rows */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.mainwrapper{
    width: 50vw;
    height: 50vh;
    margin: 10px auto;
    border: 1px solid black;
    display: grid;
    grid-template-areas:    "header header"
                            "nav nav"
                            "asideone asidetwo"
                            "asideone asidetwo"
                            "asideone asidetwo"
                            "footer footer";
/* we can stil use grid-template-rows and grid-template-columns */
    grid-template-rows: repeat(6, 1fr);
    grid-template-columns: repeat(2, 1fr);



}
.mainwrapper>div{
    border: 1px solid black;
}
#mainheader{
    grid-area: header;
}
#mainnav{
    grid-area: nav;
}
/* to reorder we change the numbers of the rows and columns */
#asideone{
    grid-area: asideone;
}
#asidetwo{
    grid-area: asidetwo;
}
#footer{
    grid-area: footer;
}

````

>[!danger] Chrome DevTools
> layout 
> grid
