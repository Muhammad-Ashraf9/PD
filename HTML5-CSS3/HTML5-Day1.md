## Semantic HTML

> [!tip] semantic tags
> are tags that have meaning. They are not just for styling, but they have a meaning to the browser and the developer. They are used to describe the content of the page, and not just the look of the page.

### Semantic tags

> [!tip]
>
> - `<header>` - Defines a header for a document or a section
> - `<nav>` - Defines a set of navigation links
> - `<section>` - Defines a section in a document
> - `<article>` - Defines an independent, self-contained content
> - `<aside>` - Defines content aside from the content (like a sidebar)
> - `<footer>` - Defines a footer for a document or a section

---

#### HTML 5 Structure

> [!tip] Doctype is not an HTML tag. It is an instruction to the web browser about what version of HTML the page is written in.

```html
<!DOCTYPE html> <!-- HTML5 doctype -->
<!-- node not element  -->
<!-- used to validate html structure  through predefined dtd file -->
<!-- html 5 not 4 -->
<!-- similar to <!DOCTYPE root-element
```

---

### DTD and xml

> [!tip] DTD stands for Document Type Definition
> It defines the document structure with a list of legal elements and attributes.

---

### meta tags

> [!info] SEO

> [!tip] used to inform the browser about the page

> [!tip] Meta tags always go inside the head element, and are typically used to specify character set, page description, keywords, author of the document, and viewport settings.

```html
<meta charset="utf-8" />
<!--  character encoding for the document used to support all languages -->
<!-- use utf-8 for all languages -->
<!-- unicode.org -->
<meta name="description" content="Free Web tutorials" />
<!--  description of the document -->
<meta name="keywords" content="HTML, CSS, JavaScript" />
<!--  keywords for search engines -->
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<!--  viewport settings  used to control the page's dimensions and scaling  important for responsive design -->
```

---

> slectors.html

```css
/* any p within any container  div*/
div p {
  color: red;
}
/* any p within any container  */
/* asks every container if the first element is p element  */
/* if it was the first p in the container but not the first CHILD then won't be  */
p:nth-child(1) {
  /* start with 1 */
  color: red;
}
div > p:nth-child(1) {
  /* asks only divs if the first element is p element  */
  color: red;
}
/* first-of-type: used to select the first element of its type in a container*/
div > p:first-of-type {
  /* this selects the first p in the div */
}
/* last-of-type: used to select the last element of its type in a container*/
div > p:last-of-type {
  /* this selects the last p in the div */
}

/* first-child: used to select the first element in a container*/
div > p:first-child {
}

/* last-child: used to select the last element in a container*/
div > p:last-child {
}
/* only-child: used to select the only element in a container */
div > p:only-child {
}

/* only-of-type: used to select the only element of its type in a container */
/* if it  was the only p in the container */
div > p:only-of-type {
}

P:nth-child(2n + 1) {
  /* P:nth-child(odd) { */
  /* n = 0,1,2,3,4,5,6,7,8,9,10 */
  /* selects all odd elements */
  color: red;
}
P:nth-child(2n) {
  /* P:nth-child(even) { */
  /* n = 0,1,2,3,4,5,6,7,8,9,10 */
  /* selects all even elements */
  color: red;
}
/* p thats that from second to last
 */
p:nth-last-child(n + 2) {
  color: red;
}
/* p thats that from 8 to first
 */
p:nth-last-child(-n + 8) {
  color: red;
}
/* range selection */
p:nth-child(n + 2):nth-child(-n + 8) {
  /* from 2 to 8 */
  color: red;
}
```

---

### layout

> [!tip] layout is the way we arrange the elements in the page

> [!tip] Responsive : the page should be displayed well on different widths with only horizontal scrolling

> [Info] Fixed : the page display is fixed and does not change according to the width of the screen

> [!tip] Adaptive : the page display changes according to the width of the screen
> media queries : used to change the style according to the width of the screen

---

> [!example] fwd.html
> used semantic tags like header, nav, section, article, aside, footer
> helps in SEO
> helps and developers to understand the structure of the page
> nav is used to contain the navigation links

> [!info]negative space is the space between the elements
> margins and paddings
> reset negative space to 0
> margin: 0;
> padding: 0;

```css
/* 1. fixed layout */
* {
  margin: 0;
  padding: 0;
}
#mainwrapper {
  width: 960px;
  margin: 0 auto;
  /* center the page */
}
/* without box sizing you have to calculate element dimesion by the adding the width and padding and margin */
/* fixed layout can't be resized on different screens when the screen width is less than 960px the page will be cut and there will be a horizontal scroll bar */
/* we need to convert the fixed layout to be more flexible */
```

---

### flexible layout

```css
/* 2. flexible layout */
* {
  margin: 0;
  padding: 0;
}
/* % used to calculate the width of the element relative to the parent element */
/* body is part of the whole page not all of the page explained later */
/* using %  will resize the page according to the width of the screen to certain limit */
/* if the screen width is less than 600px remove  the aside element */
/* using media queries */
/* device in portrait or landscape mode or screen size */

/* Media queries */
```

---

### media queries

> mediaqurytest.html

```css
/* 3. media queries */

/* @media + medianame(type) */
/* @media tv  =>when the page is displayed on tv */
/* @media print{
  css code
} */ /* when the page is printed */

/* @media screen  
*/
/* @media all{
  like having no media queries
} */

@media all and (max-width: 600px) {
  /* when the screen width is less than 600px */
  /* remove the aside element */
  aside {
    display: none;
  }
}
@media all and (min-width: 1000px) {
  /* when the screen width is more than 1000px */
  section {
    width: 70%;
  }
}
/* portraite and landscape */
@media all and (orientation: landscape) {
  /* when the screen is in landscape mode */
}

@media all and (orientation: portrait) {
  /* when the screen is in portraite mode */
}
/* wrong to put all the code in landscape and change it in portrait */
/* Duplicate code */

/* we remove the same code from portrait as it is already applied */

/* even better to write the code and put only changes in portrait instead of putting all the code in landscape */
```

---

> we can add media in html based on media condition

```html
<link rel="stylesheet" ref="style.css" />
<link
  rel="stylesheet"
  media="screen and (max-width: 600px)"
  href="mobile.css"
/>
```

---

# Break

---

### Shadows

> [!tip] box-shadow: h-shadow v-shadow blur spread color inset;

> [!tip] text-shadow: h-shadow v-shadow blur color;

```css
/* 1. box-shadow */
box-shadow: red -10px -10px;
/* can display more than one shadow
 */
box-shadow: red -10px -10px, blue 10px 10px;
/* inset: makes the shadow inside the element */
box-shadow: red 10px 10px 50px inset;

box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75), -10px -10px 5px 0px rgba(0, 0, 0, 0.75);

/* text-shadow: h-shadow v-shadow blur color; */
text-shadow: 2px 2px red;
text-shadow: 2px 2px red, -2px -2px black;
/* /small values only for text */
```

---

### multiple coloumns

```css
/* 2. multiple coloumns */
/* overflow problem happens when the content is more than the height or width of the element */
div {
  overflow-x: hidden;
  /* hide the horizontal scroll bar  and the content will be cut */
  overflow-y: hidden;
  /* hide the vertical scroll bar and the content will be cut */
  overflow: hidden auto;
  /* hide the horizontal scroll bar and show the vertical scroll bar */
  overflow: auto;
  /* show the horizontal scroll bar and show the vertical scroll bar */
  overflow: scroll;
  /* show the horizontal scroll bar and show the vertical scroll bar even if the content is less than the height or width of the element */

  /* auto : show scroll bar only if needed */

  resize: horizontal;

  resize: both;
  /* resize the element from all directions */

  /* 3. multiple coloumns */
  height: 100px;
  column-count: 3;
  /* divide the content into 3 coloumns */
  column-gap: 20px;
  /* gap between the coloumns */
  column-rule: 1px solid red;
  /* border between the coloumns */

  /* using <mark> tag  to highlight text */
}
```

---

### transformation

> transform.html

```css
section {
  transform: rotate(45deg);
  /* deg: degrees */
  /* +ve : clockwise, -ve : anti-clockwise */
  transform: scale(2, 2);
  /* double width and height */
  transform: scale(2);
  /* same  */
  /* -ve mirror  */
  transform: scaleX(-1);
  /* -ve mirror  x,y */
  /* fraction */
  transform: scale(0.5);
  /* half width and height */

  skew: skew(45deg);
  /* skew makes the element ma3oog*/

  /* translate (x,y) */
  transform: translate(100px, 100px);
  /* move the element 100px to the right and 100px down */
  /* -ve : move to the left and up */
  transform: translate(-100px, -100px);
  /* -ve : move to the left and up */

  /* transition */
  /* transition: property duration timing-function delay; */

  transition: opacity 2s;
  /* transition opacity for 2s */
  transition: opacity 2s, trnsform 1s;
  /* transition opacity for 2s and transform for 1s */
  transition: all 2s;
  /* all properties will have transition */
  transition: all 2s ease-in-out;
  /* try in lab */
}
```

---

# background-gradient

> backgroundgradient.html

```css
/* background: linear-gradient(direction, color-stop1, color-stop2, ...); */
section {
  background-color: red;
  background-color: yellow;
  /* overwrites the previous background color */

  background-image: linear-gradient(to right, red, blue, yellow, green);
  background-image: linear-gradient(to bottom, red, blue, yellow, green);
  background-image: linear-gradient(45deg, red, blue, yellow, green);
  background-image: radial-gradient(circle, red, blue, yellow, green);
  background-image: repeating-linear-gradient(45deg, red 10px, blue 15px);
  background-image: repeating-radial-gradient(circle, red 10px, blue 15px);

  /* background-image: url("image.jpg"); */
  background-image: url("image.jpg");
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;

  /* background image and linear gradient  */
  background-image: linear-gradient(top bottom, transparent, lightblue),
    url("image.jpg");
  /* not to overwrite the background image */
  background-image: linear-gradient(to top, transparent, lightblue),
    url("image.jpg");
  /* order is important so the image won't be on top of the gradient */
}
```

> [!info] search
> `repeating-linear-gradient` vs `repeating-radial-gradient`
