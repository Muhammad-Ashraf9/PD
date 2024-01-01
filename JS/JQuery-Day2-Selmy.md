****
## ajax({})
it is used to send requests to the server and get responses from it without reloading the page
syntax : ajax({url:"",method:"",dataType:"",success:function(){},error:function(){}})
Example:
```javascript
ajax({
    url: 'http://localhost:3000/api',
    method: 'get',
    dataType: 'json',
    success: function (data) {
        console.log(data); // data is an object or array of objects depending on the response from the server
    },
    error: function (err) {
        console.log(err); // err is an object (error message) containing the error status code and error message respectively
    }
});
```
## extend()
it is used to add new methods to jquery object 
syntax : $.extend({"method_name":function(){}}) // you can add as many methods as you want
example:
```javascript
$.fn.extend({
    "greenify": function () {
        $(this).css("color", "green")
        .css("border", "1px solid green");
    },
    "redify": function () {
        $(this).css("color", "red")
        .css("border", "1px solid red");
    }
});
$("li:first").greenify();
$("li:last").redify();
// you can't call others jquery methods like $("li:first").redify().css("color","green") because it is not a jquery object but a DOM object (ركز مين بينادي)

//if you want to call other jquery methods you have to return the jquery object

syntax : $.fn.extend({
    "greenify": function () {
        $(this).css("color", "green")
        .css("border", "1px solid green");
        return $(this); //<- this is the key to call other jquery methods
    },
    "redify": function () {
        $(this).css("color", "red")
        .css("border", "1px solid red");
        return $(this); //<- and this
    }
});

// now you can call other jquery methods like this
$("li:first").greenify().css("font-size","20px"); // it will work
```
## jquery UI
it is a library that contains a lot of plugins for jquery like drag and drop, autocomplete, datepicker, etc.
it's used to make the website more interactive and user friendly
to use it you have to download it from the website and include it in your html file
but you have to include jquery first
you can see examples and source code for jquery UI plugins in the website http://www.jqueryui.com
```html
<script src="jquery.js"></script> // you have to include jquery first
<script src="jquery-ui.js"></script> // then you can include jquery UI
```

 > [!tip] Play Time
 > open the website and play with different animations and effects 
 > when you want to copy the animation you see on the website just press on source bellow the animation