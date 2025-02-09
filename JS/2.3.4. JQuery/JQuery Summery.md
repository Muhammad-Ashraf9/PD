

# Day 1

> [!tip] **jQuery** is a lightweight, cross-browser JavaScript library that simplifies DOM manipulation, event handling, animations, and AJAX.  
> _Write less, do more!_

---

## Table of Contents

- [[#jQuery Overview]]
- [[#How to Use jQuery]]
- [[#jQuery Setup and Alias]]
- [[#jQuery Selectors and Traversal]]
- [[#jQuery Manipulation Methods]]
- [[#jQuery Effects and Animations]]
- [[#jQuery AJAX Methods]]
- [[#Utility Methods]]
- [[#Exception Handling]]

---

## jQuery Overview

> [!tip] **jQuery Advantages:**
> 
> - **Easy to use**
> - **Cross-browser support**
> - **Lightweight**
> - **CSS3 support**
> - **Rich plugin ecosystem**

> [!info] **jQuery Versions:**
> 
> - **jQuery Core** (the fundamental library)
> - **jQuery Mobile**
> - **jQuery UI**

---

## How to Use jQuery

1. **Download the Library**
    - Choose between production (`.min.js`) for performance or development (`.js`) for easier debugging.
2. **Use a CDN**
    - **Advantages:**
        - Leverages browser caching if the user has previously loaded the library.
        - Automatic updates if the CDN serves a new version.
3. **Install via npm/yarn**
    - Use package managers for modern development workflows.

---

## jQuery Setup and Alias

- **Global Access:**  
    jQuery is available as both `jQuery` and the alias `$`:
    
    ```js
    window.jQuery = window.$ = jQuery;
    ```
    
- **Document Ready:**  
    Ensure the DOM is fully loaded before running your code:
    
    ```js
    $(document).ready(function () {
      console.log("DOM is ready");
    });
    // or simply:
    $(function () {
      console.log("DOM is ready");
    });
    ```
    

---

## jQuery Selectors and Traversal

- **Selectors:**  
    Use CSS-like selectors to target elements:
    
    ```js
    $("p");        // selects all paragraph elements
    $(".myClass"); // selects elements with class "myClass"
    $("#myId");    // selects the element with id "myId"
    $("[attribute]"); // selects elements with the specified attribute
    ```
    
- **Advanced Filters & Pseudo-selectors:**  
    Examples include `:first`, `:gt(index)`, `:lt(index)`, `:has()`, and `:contains()`.
- **DOM Traversal:**  
    Methods such as `.parent()`, `.children()`, `.siblings()`, `.next()`, and `.prev()` help navigate the DOM.

---

## jQuery Manipulation Methods

- **Content Manipulation:**
    - `.html()` to get or set HTML content.
    - `.text()` for text content.
    - `.val()` for input values.
- **CSS Manipulation:**
    - `.css()` can be used to get or set style properties:
        
        ```js
        $("div:first").css("border", "1px solid black");
        $("div:first").css({
          width: "500px",
          color: "red",
          backgroundColor: "green",
        });
        ```
        
- **Class Manipulation:**
    - `.addClass()`, `.removeClass()`, and `.toggleClass()` simplify handling CSS classes.

---

## jQuery Effects and Animations

- **Built-in Effects:**  
    Methods like `.hide()`, `.show()`, `.fadeIn()`, `.fadeOut()`, `.slideUp()`, and `.slideDown()` allow for smooth transitions.
    
    Example with a callback:
    
    ```js
    $("p").slideUp(2000, function () {
      console.log("Transition completed");
    });
    ```
    
- **Custom Animations:**  
    Use `.animate()` to change CSS properties over time:
    
    ```js
    $("div").animate({
      width: "500px",
      height: "500px",
      "border-radius": "50%",
      "background-color": "red",
    }, 3000);
    ```
    
- **Animation Queue:**  
    jQuery automatically queues animations so they run sequentially.
    

---

## jQuery AJAX Methods

- **AJAX Simplification:**  
    jQuery abstracts the complexities of `XMLHttpRequest` with methods such as:
    - `.load()`
    - `.get()`
    - `.post()`
    - `.ajax()`
- These methods enable seamless asynchronous data fetching and updates.

---

## Utility Methods

- **Filtering Collections:**  
    Use `.filter()` with callback functions or pseudo-selectors (e.g., `:gt()`, `:lt()`) to refine element selections.
- **Iteration:**  
    Since jQuery collections are not plain arrays, use `.each()` to iterate:
    
    ```js
    $("div").each(function (index, element) {
      console.log(index, element);
    });
    ```
    

---

## Exception Handling

> [!warning] While not directly part of jQuery, understanding exception handling is important. Below is an example in C# that demonstrates handling different types of exceptions using try/catch/finally:

```cs
try {
    // Code that might throw exceptions
} catch (DivideByZeroException ex) {
    Console.WriteLine("Divide by zero error: " + ex.Message);
} catch (FormatException ex) {
    Console.WriteLine("Format error: " + ex.Message);
} catch (Exception ex) {
    Console.WriteLine("General error: " + ex.Message);
} finally {
    Console.WriteLine("This block always runs.");
}
```

> [!info] **Note:** Always include the jQuery script before your custom script to ensure that `$` is defined.

---


# Day 2

> [!tip] **Day 2 Overview:**  
> This session dives into advanced jQuery techniques including hover events, attribute vs property handling, DOM traversing, element manipulation, comprehensive event handling, AJAX interactions, and plugin integration.

---

## Table of Contents

- [[#Hover & Events]]
- [[#attr vs prop]]
- [[#Traversing]]
- [[#Manipulation]]
- [[#Events]]
- [[#AJAX]]
- [[#Plugins]]

---

## Hover & Events

> [!tip] **Hover & Events:**  
> jQuery makes handling mouse events straightforward. You can use:
> 
> - `.mouseover()` and `.mouseout()` to change element styles.
> - Chaining methods to apply multiple events in one go.
> - `.hover()`, which accepts two functions: one for mouse enter and one for mouse leave.
> - `.toggleClass()` to automatically add/remove classes on hover.
> 
> **Example:**
> 
> ```js
> $("li").hover(
>   function () { $(this).css("color", "yellow"); },
>   function () { $(this).css("color", "black"); }
> );
> ```

---

## attr vs prop

> [!info] **attr vs prop:**  
> Understand the difference:
> 
> - **.attr():** Retrieves or sets the HTML attribute (as defined in the tag).
> - **.prop():** Retrieves or sets the DOM property (reflecting the current state).
> 
> **Usage Highlights:**
> 
> - For checkboxes, use `.prop("checked")` to get a boolean value.
> - For images, `.prop("width")` returns a number, while `.attr("width")` returns a string.
> 
> **Example:**
> 
> ```js
> $(":checkbox").prop("checked"); // returns true or false
> $("img:first").prop("width");     // returns a number
> ```

---

## Traversing

> [!tip] **DOM Traversing with jQuery:**  
> Navigate your document easily using methods such as:
> 
> - **Parent/Children:** `.parent()`, `.children()`, and `.parents()` for upward/downward navigation.
> - **Siblings and Adjacent Elements:** `.siblings()`, `.next()`, `.prev()`, `.nextAll()`, and `.prevAll()`.
> - **Filtering:** Use `.filter()`, `.not()`, and pseudo-selectors like `:eq()`, `:first`, or `:gt()`.
> - **Conversion:** `.get()` converts a jQuery collection into a standard array.
> 
> **Example:**
> 
> ```js
> $("ol:first li").nextAll().css("color", "red");
> ```

---

## Manipulation

> [!info] **DOM Manipulation:**  
> jQuery provides powerful methods to insert, wrap, or remove elements:
> 
> - **Insertion:**
>     - `.append()` and `.prepend()` (or their counterparts `.appendTo()` and `.prependTo()`) add content.
> - **Wrapping:**
>     - `.wrap()`, `.wrapInner()`, and `.wrapAll()` to encapsulate elements.
> - **Removal:**
>     - `.empty()` (removes inner content), `.remove()`, and `.detach()` (preserves events).
> - **Cloning:**
>     - `.clone()` (shallow) or `.clone(true)` (deep, including events).
> - **One-Time Events:**
>     - Use `.one()` to bind an event handler that executes only once.
> 
> **Example:**
> 
> ```js
> $("li").append("<p>Text.</p>");
> ```

---

## Events

> [!tip] **Event Handling and Delegation:**  
> Register events easily with:
> 
> - **Direct Binding:** Using methods like `.click()`, `.dblclick()`, `.mouseover()`, and `.mouseout()`.
> - **.on() and .off():** For binding and unbinding multiple events or dynamically added elements.
> - **Event Delegation:** Attach a handler to a parent (e.g., `<ul>`) to handle events on current and future child `<li>` elements.
> 
> **Example:**
> 
> ```js
> $("ul").on("click", "li", function () {
>   $(this).css("color", "red");
> });
> ```
> 
> Additionally, using `.off()` allows you to remove specific event handlers.

---

## AJAX

> [!tip] **AJAX with jQuery:**  
> jQuery simplifies asynchronous HTTP requests:
> 
> - **$.ajax():** The core method with options like `url`, `method`, `dataType`, and callbacks.
> - **Convenience Methods:** Such as `.get()`, `.post()`, and `.load()`.
> - Handles responses gracefully using `success` and `error` callbacks.
> 
> **Example:**
> 
> ```js
> $.ajax({
>   url: "https://jsonplaceholder.typicode.com/posts",
>   method: "GET",
>   dataType: "json",
>   success: function (data) {
>     $(data).each(function (index, element) {
>       console.log(element.title);
>       document.write(`<h1>${element.title}</h1>`);
>     });
>   },
>   error: function (error) {
>     console.log(error);
>   }
> });
> ```

---

## Plugins

> [!success] **Plugins:**  
> Extend jQuery functionality effortlessly with plugins.  
> For example, the Selmy plugin (brought to you by Selmy) can add extra features without reinventing the wheel.

---
