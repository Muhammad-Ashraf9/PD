# JQuery

> [!tip] JQuery is a JavaScript library that makes it easier to manipulate the DOM and handle events.
> write less, do more

> [!warning] JQuery advantages:
>
> - Easy to use
> - Cross-browser support
> - Lightweight
> - css3 support
> - Plugins

> [!info] JQuery versions:
>
> - Jquery mobile
> - Jquery UI
> - Jquery core <=====

> [!example] How to use JQuery:
>
> 1. Download the JQuery library from the JQuery website.(js file)
>
> - versions(production(.min.js), development(.js))
>   > [!faq] why use development version over production version in development?
>   >
>   > - production version is minified and hard to debug
>   > - development version is not minified and easy to debug
>
> 2. CDN (Content Delivery Network) link: a server that contains a copy of the JQuery library
>
>    > [!fail] disadvantages of using normal link:
>    >
>    > - request to the server every time the page loads
>
>    > [!success] advantages of using CDN:
>    >
>    > - Cache: if the user has already downloaded the JQuery library from the same CDN, it won't be downloaded again.
>    > - Updates: if the user has already downloaded the JQuery library from the same CDN, and the CDN has been updated, the browser will download the updated version.
>
> 3. Install JQuery using npm / yarn
>
>    > [!info] npm: Node Package Manager

> [!tip] [[CDN]](https://cdnjs.com) links:
>
> used to get links for different libraries

---

> loadjq.html

> [!tip] `jQuery` is the same as `$`
>
> `window.jQuery = window.$ = jQuery`
>
> > [!danger]
> > used to avoid conflicts with other libraries

```js
// jQuery;
//$;
console.log(jQuery); //function f(e,t){return new x.fn.init(e,t)}

// write less, do more

window.addEventListener("load", function () {
  let allPs = document.getElementsByTagName("p");
  this.document
    .getElementByTagName("button")[0]
    .addEventListener("click", function () {
      for (let i = 0; i < allPs.length; i++) {
        allPs[i].style.display = "none";
      }
    });
  this.document
    .getElementByTagName("button")[1]
    .addEventListener("click", function () {
      for (let i = 0; i < allPs.length; i++) {
        allPs[i].style.display = "block";
      }
    });
});

/////////////////////////
```

```js
//jQuery
$("input:first").click(function () {
  $("p").hide();
  // $("p").hide(2000);//2s transition
  // $("p").hide(2000).show(3000);//2s transition hide then show in 3s
});
$("input:last").click(function () {
  $("p").show();
});
```

> [!bug] remember to add jquery script before your script

```js
//load in jquery
console.log($); //function f(selector,context){return new x.fn.init(selector,context)}

$(document).ready(function () {
  console.log("loaded");
  console.log($("p")); //jquery object (array of p elements)  x.fn.init(3) [p, p, p, prevObject: x.fn.init(1)]
});
//another way
$(function () {
  //less readable
  console.log("loaded");
}); //is the same as $(document).ready(function () {});
```

> [!info] JQuery selectors:
> selectors like css selectors (id, class, tag, attribute, pseudo)

> [!info] JQuery events:
>
> `.click()` `.dblclick()` `.mouseenter()` `.mouseleave()` `.hover()` `.focus()` `.blur()` `.change()` `.submit()` `.keydown()` `.keyup()` `.keypress()` `.on()`

> [!info] JQuery traversing:
>
> `.parent()` `.parents()` `.children()` `.siblings()` `.next()` `.prev()` `.first()` `.last()` `.eq()` `.filter()` `.not()`

> [!info] JQuery manipulation:
>
> `.html()` `.text()` `.val()` `.attr()` `.removeAttr()` `.addClass()` `.removeClass()` `.toggleClass()` `.css()` `.hide()` `.show()` `.toggle()` `.fadeIn()` `.fadeOut()`.....

> [!info] JQuery effects:
>
> `.hide()` `.show()` `.toggle()` `.fadeIn()` `.fadeOut()` `.fadeToggle()` `.fadeTo()` `.slideUp()` `.slideDown()` `.slideToggle()` `.animate()` `.stop()` `.delay()` `.queue()` `.clearQueue()` `.dequeue()` `.promise()`....

> [!info] JQuery ajax:
>
> `.load()` `.get()` `.post()` `.ajax()` `.getJSON()` `.getScript()`
> xmlhttprequest behind the scenes

---

> html selectors.html

```js
//select paragraph and add style
$(function () {
  $("p"); //is the same as document.querySelectorAll("p")
  //collection of p elements always collection even if one element
  console.log($("p")); //x.fn.init(3) [p, p, p, prevObject: x.fn.init(1)]

  //one function many uses
  //css() get or set css properties
  $("p").css("border", "1px solid black"); //set border to 1px solid black
  //select all p with class attriute iti
  $(".iti"); //select all elements with class iti
  $("p.iti"); //select all p elements with class iti
  //select all p with class attriute (check for attriute)
  //attribute selector
  $("[class]"); //select all elements with class attribute
  $("p[class]"); //select all p elements with class attribute
  $("p.iti"); //select all p elements with class iti

  $("p[class='iti']"); //select all p elements with class attribute iti
  //using regex starts with ^ , ends with $ , contains *
  $("p[class*='iti']"); //select all p elements with class attribute contains iti

  $("p[class^='iti']"); //select all p elements with class attribute starts with iti

  $("p[class$='iti']"); //select all p elements with class attribute ends with iti

  //CSS selector => p[class$='iti']

  //FILTERS
  $("P:first"); //select first p element this gets the first p in general not in every container

  $("P:nth-child(1)"); //select first p element
  //asks every container if  its first child is a p element

  //last li in first ul
  $("ul:first li:last"); //select last li in first ul

  //first li in second ul
  /////////////////// eq() select element by index
  //not like nth-child starts from 0
  $("ul:eq(1) li:first"); //select first li in second ul

  $("ul")[0].css("border", "1px solid black"); //ERROR
  $("ul")[0]; //html element not jquery object no css function
  $($("ul")[0]).css("border", "1px solid black");
  // we need to make sure that we are using jquery object
});

///////////gt() greater than
////////////lt() less than

$("p:gt(1)"); //select all p elements with index greater than 1
$("p:lt(1)"); //select all p elements with index less than 1

/////////////////Input data
$("input"); //select all input elements
//exclude text area , checkbox , radio ,select
console.log($(":input")); //select all input elements
//include text area , checkbox , radio ,select

//select input with type text
$(":text"); //select all input elements with type text
//select input with type password
$(":password"); //select all input elements with type password
//select enabled  or disabled
$(":enabled"); //select all input elements that are enabled

$("tr"); //tagName selector
//to get tr after the first one (header)
$("tr:gt(0)"); //select all tr elements with index greater than 0

//style even and odd rows
$("tr:gt(0):even").css("background-color", "red");
$("tr:gt(0):odd").css("background-color", "green");

////div empty , div with p, div with span
$("div"); //select all div elements
$("div:parent"); //select all div elements that are parents empty excluded
//////////:has() =>elements
$("div:parent:has(p)"); //select all div elements that are parents and have p element
//we can use :has() only
$("div:has(p)"); //select all div elements that have p element

//:contains = > content
// case sensitive
$("p:contains(iti)"); //select all p elements that contain iti

//select all headers
$("h1,h2,h3,h4,h5,h6"); //select all header elements
////////////////////////:header filter
$(":header"); //select all header elements
```

---

```js
//////////// text() get or set text

$("div:first").text(); //get text of first div
$("div:first").text("new text"); //set text of first div

//////////////////// html() get or set html

$("div:first").html(); //get html of first div
$("div:first").html("<p>new html</p>"); //set html of first div

////////////////// val() get or set value
$(":text:first").val(); //get value of first text input
$(":text:first").val("new value"); //set value of first text input

///////////////// width() get or set width
$("div:first").width(); //get width of first div
$("div:first").width(500); //set width of first div

///////////////// css() get or set css properties
$("div:first").css("border", "1px solid black"); //set border to 1px solid black
$("div:first").css("border"); //get border of first div

///////////////// method chaining
$("div:first").css("border", "1px solid black").width(500).text("new text");

///////////////// pass object to css()
$("div:first").css({
  border: "1px solid black",
  width: 500,
  color: "red",
  padding: "10px",
}); //set border to 1px solid black
//set width to 500
const myObj = {
  border: "1px solid black",
  width: 500,
  color: "red",
  padding: "10px",
};
$("div:first").css(myObj); //set border to 1px solid black

///////////////// camelCase css properties
// key => camelCase or quoted
$("div:first").css({
  "border-radius": "10px",
  "background-color": "green",
});

$("div:first").css({
  border: "1px solid black",
  color: "red",
  padding: "10px",
  backgroundColor: "green",
}); //set border to 1px solid black
// VALUES MUST BE QUOTED ""
```

---

### break

```js
//// Array.prototype.filter() ////////////////
[
  { name: "ahmed", age: 20 },
  { name: "ali", age: 30 },
  { name: "mohamed", age: 40 },
].filter((e) => e.age > 20); //[{name:"ali",age:30},{name:"mohamed",age:40}]
//first parameter is element , second parameter is index

//get all divs with width > 120px
/////////////////filter()
//first parameter is index , second parameter is element
let targetDivs = $("div").filter(function (index, element) {
  console.log(index, element); //index of element in collection , element itself
  this; //element itself
  return $(this).width() > 120; //return true or false
  //if true element will be added to the collection
});

targetDivs.forEach((e) => console.log(e)); //Error
//forEach is not a function
//Collection => not an array
//we use each() instead

/////////////////each() ////////////////
//first parameter is index , second parameter is element
$(targetDivs).each(function (index, element) {
  console.log(index, element); //index of element in
});

//we can chain each() to filter()
$("div")
  .filter(function (index, element) {
    return $(this).width() > 120;
  })
  .each(function (index, element) {
    this; //element
    //we can use this or element
    $(element).css("border", "1px solid black");
  });
```

---

### classes

```js
/////////////////////////addClass() , removeClass() , toggleClass()
$(function () {
  /////////////////////////addClass()
  $("input:eq(0)").click(function () {
    $("p").addClass("style1");
  });

  /////////////////////////removeClass()
  $("input:eq(1)").click(function () {
    $("p").removeClass("style1");
  });
  /////////////////////////toggleClass()
  $("input:eq(2)").click(function () {
    $("p").toggleClass("style1");
  });
}); //end of load
```

---

#### Effects

> html effects.html

```js
$(function () {
  /////////slideUp()
  $("input:eq(0)").click(function () {
    // $("p").slideUp(2000);//2s transition hide
    //we want to make action when transition is done
    //we use callback function
    $("p").slideUp(2000, function () {
      console.log("transition is done");
      //if we click the button again the callback function will be called again
      //it must complete the first transition to be called again
    });
  });
  /////////slideDown()
  $("input:eq(1)").click(function () {
    $("p").slideDown(2000, function () {
      console.log("transition is done");
    });
  });
  /////////slideToggle()
  $("input:eq(2)").click(function () {
    $("p").slideToggle(2000, function () {
      console.log("transition is done");
    });
  });
  /////////fadeOut()
  $("input:eq(3)").click(function () {
    $("p").fadeOut(2000, function () {
      console.log("transition is done");
    });
    //hide() is the same as fadeOut()
    // but different in the way it hides the element
    $("p").hide(2000); //
  });
  /////////fadeIn()
  $("input:eq(4)").click(function () {
    $("p").fadeIn(2000, function () {
      console.log("transition is done");
    });
    //show() is the same as fadeIn()
    // but different in the way it shows the element
    $("p").show(2000);
  });
  /////////fadeToggle()
  $("input:eq(5)").click(function () {
    $("p").fadeToggle(2000, function () {
      console.log("transition is done");
    });
  });
}); //end of load
```

> [!danger] how to animate classes

---

### animation

```js
////////////////animate() //////////////
// change properties over time
$("div").animate(
  {
    width: "500px",
    height: "500px",
    "border-radius": "50%",
    "background-color": "red",
  },
  3000
);
//can run animation sequentially
$("div")
  .animate(
    {
      width: "500px",
      height: "500px",
    },
    3000
  )
  .animate(
    {
      "border-radius": "0%",
      "background-color": "green",
    },
    3000
  );
// on every click animate shape
$("button:first").click(function () {
  $("div").animate(
    {
      width: "500px",
      height: "500px",
    },
    3000
  );
}); //it will change only on first click
//we need to add value every click
$("button:first").click(function () {
  $("div").animate(
    {
      width: "+=50px",
      height: "+=50px",
    },
    3000
  );
});
//we can make condition

/////////////////
```

> [!danger] Search
>
> Animation Queue

---

### Exception handling

> C# try catch finally

```cs
static void Main(string[] args)
{
    Console.WriteLine("Enter two numbers");
    int firstNumber = int .Parse(Console.ReadLine());
    //if firstNumber is string => runtime error
    //System.FormatException: Input string was not in a correct format.
    int secondNumber = int .Parse(Console.ReadLine());
    //if secondNumber is 0 => runtime error
    //System.DivideByZeroException: Attempted to divide by zero.
    int result = fn/ sn;
    Console.WriteLine("Result = " + result);
    //Exception :runtime error
    //Imediate stop of program (abnormal termination)
    //we need to handle exception

    try
    {
        Console.WriteLine("Enter two numbers");
        int firstNumber = int .Parse(Console.ReadLine());
        int secondNumber = int .Parse(Console.ReadLine());
        int result = fn/ sn;
        //once exception is thrown => we skip the rest of the code go directly to catch
        Console.WriteLine("Result = " + result);

    }//can't write try alone without catch or finally
    //should be in order try catch finally


    catch (DivideByZeroException ex)//specific exception not suiable for all exceptions
    // if we have other error type => program will stop

    // catch (FormatException ex)
    //types of exceptions
    {
        Console.WriteLine(ex.Message);
        Console.WriteLine("Inside catch Divided by zero");
    }//catch => handle error so program continues
    // if  no exception => we skip catch

    catch (FormatException ex)
        //we can make multiple catch blocks
    {
        console.WriteLine(ex.Message);
        Console.WriteLine("Inside catch Format exception");
    }
    //but we need to make sure that the most specific exception is first
    //we need to have Exception catch block at the end
    //if exception passed all catch it will be handled by Exception catch block
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        Console.WriteLine("Inside catch Exception");
    }
    //    catch (DivideByZeroException ex){}
	 // Exception is the parent of all exceptions
     //catch(OveloadException ex){} this inherits from ArithmeticException which inherits SystemException which inherits Exception
    //so we need to make sure that the most specific exception is first
    //children first then parent

    finally
    {
        //finally block is optional
        //it will be executed in all cases
        //even if exception is thrown
        //even if no exception is thrown
        Console.WriteLine("Inside finally");

    }
    Console.WriteLine("End of program");
    //this run depends on exception handling
    //if exception is thrown :


}
```
