#### Hover & Events

> html hoverplusevent.html

```js
//events
//mouseover & mouseout

$(function () {
  $("li").mouseover(function () {
    $(this).css("color", "yellow");
  });
  $("li").mouseout(function () {
    $(this).css("color", "black");
  });
});

//chaining
//same as above
$(function () {
  $("li")
    .mouseover(function () {
      $(this).css("color", "yellow");
    })
    .mouseout(function () {
      $(this).css("color", "black");
    });
});
///////hover(function mousein, function mouseout)
$(function () {
  $("li").hover(
    function () {
      $(this).css("color", "yellow");
    },
    function () {
      $(this).css("color", "black");
    }
  );
});

///we can add class
$(function () {
  $("li").hover(
    function () {
      $(this).addClass("hover");
    },
    function () {
      $(this).removeClass("hover");
    }
  );
});

//toggle
//////same effect as above
$(function () {
  $("li").hover(function () {
    $(this).toggleClass("hover");
  });
});
```

---

### attr vs prop

> html attrvsprop.html

```js
//attr vs prop
//attr: get the value of the attribute
//prop: get the value of the property
//check box
$(function () {
  $(":checkbox").attr("checked"); //return value of the attribute in tag in html
  //instead of true or false
  //return undefined if it is not in the tag
  //so we use prop
  $(":checkbox").prop("checked"); //return true or false
  //track the change of the checkbox
  $("img:first").attr("alt"); //return undefined because it is not in the tag
  $("img:first").prop("alt"); //return ""
  //width
  $("img:first").attr("width"); //return "100" string
  $("img:first").prop("width"); //return 100 number
  //////////////use prop/////
});
```

---

### Traversing

> html traversing.html

```js
//traversing
//get siblings - parent...

$("li:eq(2)").text(); //"About"

//get the parent
$("li:eq(2)").parent(); //<ul id="menu">

//check if it is ul or ol
$("li:eq(2)").parent().is("ul"); //true
$("li:eq(2)").parent().is("ol"); //false

//get the children (first level only)
$("ul:first").children(); //return all the li

//parent vs parents
//parent: return the first level only
//parents: return all the parents (all the way up)
$("ol:first").parent(); //return the div
$("ol:first").parents(); //return the div and the body and the html and the document
//get parents div only
$("ol:first").parents("div"); //return the div only
//get the children
$("ol:first").children(); //return all the li
//get the children li only
$("ol:first").children("li"); //return all the li
//constructor.name
$("ol:first").children("li").constructor.name; // "jQuery"
//jquery object

///////////get()
//return Array
$("#wrapper").get(); //return the div with id wrapper in an array
$("#wrapper").get("p"); //return the p tag in an array

$("ol:first li").get(); //return all the li in an array
//get the first li
$("ol:first li").get(0); //return the first li
//htmlLiElement
$("ol:first li").get(0).constructor.name; //"HTMLLIElement"

/////////////// find()
$("#mainWrapper").children(); //return all the children
//we can filter
$("#mainWrapper").children("div"); //return all the divs
//only first level

//find
$("#mainWrapper").find("div"); //return all the divs
//all the way down
//to convert to array we use get()
$("#mainWrapper").find("div").get(); //return all the divs in an array as array of html elements

$("#mainWrapper").children("p");
$("#mainWrapper").children("p[id]"); //return all the p with id
$("#mainWrapper").children("p").not("[id]"); //return all the p without id
$("#mainWrapper").children("p").not("[id]").get(); //return all the p without id in an array
$("#mainWrapper").children("p").not("[id]").get(0).innerHTML; // we  can't write .text() because it is not jquery object

$("ol:first li:eq(2)").next(); //return the next one only
//next all
$("ol:first li:eq(2)").nextAll(); //return all the next ones

//prev
$("ol:first li:eq(2)").prev(); //return the previous one only

//prev all
$("ol:first li:eq(2)").prevAll(); //return all the previous ones

//siblings
$("ol:first li:eq(2)").siblings(); //return all the siblings

$("ol:first li:eq(2)").prevAll().css("color", "red"); //change the color of all the previous ones

////////map()
//DOES NOT RETURN Array

$("ol:first li").map(function (index, element) {
  return $(element).text(); //
}); //jQuery.fn.init {0: "Home", 1: "About", 2: "Contact", 3: "Help", length: 4, prevObject: jQuery.fn.init(1)}
//collection as jquery object

//////////slice()
//same idea as array
//BUT return jquery object
$("ol:first li").slice(1, 3); //return About and Contact jQuery.fn.init {0: li, 1: li, length: 2, prevObject: jQuery.fn.init(4)}
```

---

#### Manipulation

> html manipulation.html

```js
//add inside (prepend - append  - prependTo - appendTo)
//append
$("li").append("Text."); //added to the end of the li
//add tag like bold
$("li").append("<b>Text.</b>"); //added to the end of the li
//p
$("li").append("<p>Text.</p>"); //added to the end of the li in a new line p=>block
//appendTo
//the same as above but the order is different
$("<p>Text.</p>").appendTo("li");

//prepend
$("li").prepend("Text."); //added to the beginning of the li

//wrap
$("li").wrap("<div></div>"); //wrap each li with div
//ul > div > li > text

//wrapInner
$("li").wrapInner("<div></div>"); //wrap the text of each li with div
//ul > li > div > text

//wrapAll
$("li").wrapAll("<div></div>"); //wrap all the li with one div
//ul > div(> li)

//removing
//   - empty
$("button:eq(0)").click(function () {
  $("ul").empty(); //will remove all the li inside the ul but the ul will stay
});
//   - remove
let targetUl;
$("button:eq(1)").click(function () {
  targetUl = $("ul").remove(); //will remove all the li inside the ul and the ul will be removed too
});
//return the removed element
//we can use it to recover

//  - detach
$("button:eq(2)").click(function () {
  targetUl = $("ul").detach(); //will remove all the li inside the ul and the ul will be removed too
  //but we can recover it with
});
//recover
$("button:eq(3)").click(function () {
  $("body").append(targetUl); //will add the ul to the body
  //but we lost the event in remove
  //so we use detach
  //detach will keep the event
});
//hearts
// &hearts;
$("span:first").click(function () {
  $(this).fadeOut(1000).fadeIn(1000); //regitered event
});
$("li").click(function () {
  $(this).append("span"); //it will move the span to the li
  //not copy it
  // still has the event
  //but we need to keep the original
  //so we use clone
  $(this).append($("span").clone()); //clone the span without the event
  //so we use clone(true)
  $(this).append($("span").clone(true)); //clone the span with the event
});

//cloning
//   - clone: shallow(no clone events)
//   - clone(true) : deep(clone events)

/// .one()
//register event only once
$("li").one("click", function () {
  $(this).append($("span").clone(true));
}); //it will add the span only once
//li > 1 > span
//li > 2 > span
//li > 4 > span

$("div:first").click(function () {
  $(this).append("<p>  text</p>");
}); //it will add the p every time we click
```

---

#### Events

> html events.html

```js
//events
// event registration
// 1. direct
//register 4 events
$("li").click(function () {
  $(this).css("color", "red");
});
$("li").dblclick(function () {
  $(this).css("color", "green");
});
$("li").mouseover(function () {
  $(this).css("border", "1px solid red");
});
$("li").mouseout(function () {
  $(this).css("border", "none");
});

$("ul").append("<li>Item 5</li>"); //it will not have the events
///////chaining

$("li")
  .click(function () {
    $(this).css("color", "red");
  })
  .dblclick(function () {
    $(this).css("color", "green");
  })
  .mouseover(function () {
    $(this).css("border", "1px solid red");
  })
  .mouseout(function () {
    $(this).css("border", "none");
  });
///////////////////// on()
$("li").on("click", function () {
  $(this).css("color", "red");
});
//multiple events
$("li").on("click dblclick", function () {
  $(this).css("color", "red");
}); //multiple events with one handler
//but still the appended li will not have the events
//////////////// off()
//remove the event
// off  vs removeEventListner
function fun1() {
  alert("Hello");
}
$("li").on("click", fun1);
$("li").on("click", fun2);
// X(
$("li").off("click", "fun2");
$("li").off("click", fun2);
$("li:last").off("click", "fun2");
//is second parameter is string or function
//we have to take care of version of jquery
//off function can't be anonymous we have to give it a name
$("li").off("click"); //off all the handlers of click

$("li").on({
  click: function () {
    $(this).css("color", "red");
  },
  dblclick: function () {
    $(this).css("color", "green");
  },
  mouseover: function () {
    $(this).css("border", "1px solid red");
  },
  mouseout: function () {
    $(this).css("border", "none");
  },
}); //event should be string
//function name can be anonymous or variable name
//still appended li will not have the events

// 2. delegation
$("ul").on(
  {
    click: function () {
      $(this).css("color", "red");
    },
    dblclick: function () {
      $(this).css("color", "green");
    },
    mouseover: function () {
      $(this).css("border", "1px solid red");
    },
    mouseout: function () {
      $(this).css("border", "none");
    },
  },
  "li"
);
// appended li will have the events finally
```

---

#### AJAX

> Ajax.html

```js
//AJAX
$(function () {
  //$.ajax({url:"", method:"", dataType:"", success:function, error:function})
  //caller is jquery object $.ajax
  //xmlhttprequest behind the scene
  $.ajax({
    url: "https://jsonplaceholder.typicode.com/posts", //required
    method: "GET", // default is GET
    dataType: "json", //optional
    success: function (data) {
      //required
      console.log(data); //object not string
      //we can use it directly as it is parsed
      $(data).each(function (index, element) {
        console.log(element.title);
        document.write(`<h1>${element.title}</h1>`);
      });
    },
    error: function (error) {
      console.log(error);
    },
  });
}); //end of load
```

## 2:55 ⌚

### 🔌⛔

---

### Plugins

> plugins.html

> [!success] Selmy
>
> brought to you by Selmy:

![Selmy](JQuery-Day2-Selmy.md)
