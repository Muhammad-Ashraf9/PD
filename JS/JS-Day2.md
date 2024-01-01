## Number Data Type

```js
var myNum = 5523;
//object
//toString(?radix);
myNum.toString(); //"5523"
```

> [!info] advantage of parseInt() vs Number()
> is that it takes ==radix== as a second parameter. The radix parameter is used to specify which numeral system to be used, for example, a radix of 16 (hexadecimal) indicates that the number in the string should be parsed from a hexadecimal number to a decimal number.

```js
parseInt("10A5B", 16); // 68315
parseInt("10A5Bg", 16); // 68315 g is ignored
parseInt("10A5Bg", 17); // 1099483 g not ignored in radix 17
parseInt("10A5Bgfx", 36); // 1099483 g and fx not ignored in radix 36
//range of radix is 2 to 36
parseInt("10A5Bg", 1); // NaN
parseInt("10A5Bg", 37); // NaN
```

> [!info] **radix**
> ==radix== is an optional parameter that specifies the base of the number in numObj. The radix can be an integer between 2 and 36.

> [!tip] `toString(radix)`

```js
(1024).toString(2); // "10000000000"
(1024).toString(8); // "2000"
(1024).toString(10); // "1024"
(1024).toString(16); // "400"
(1024).toString(32); // "100"
```

## String Data Type

```js
var myStr = "Hello World!"; //literal declaration in stack
var myStr = new String("Hello World!"); //object declaration in heap

//length
myStr.length; //12 (including space) called by String object
myStr[0]; //"H"
myStr[0] = "h"; //won't change => immutable
myStr[30]; //undefined out of range
myStr.length = 30; //won't change => read-only
```

> [!danger] **String is immutable**

#### String Methods

1. Manipulating
1. Formatting

```js
var myStr = "iti mansoura branch Mansoura";
//charAt(index)
myStr.charAt(0); //"H"
myStr.charCodeAt(27); //"a"
myStr.charCodeAt(28); //"" empty string length = 0
myStr.charCodeAt(3); //" " space length = 1
//indexOf(searchValue[, fromIndex])
//left to right
myStr.indexOf("Mansoura"); //20
myStr.indexOf("Mansoura", 21); //-1

//lastIndexOf(searchValue[, fromIndex])
//right to left
myStr.lastIndexOf("Mansoura"); //20
//validate
"abc.com".indexOf(".com"); //3
"abc.com".indexOf("@"); //-1
//then arrange

//replace(searchValue, replaceValue)
//return new string should be assigned to a variable
myStr.replace("Mansoura", "Cairo"); //"iti mansoura branch Cairo"
//case sensitive
myStr.replace("I", "i"); //"iti mansoura branch Mansoura"

"ITI" === "iti"; //false
//toLowerCase()
"ITI".toLowerCase() === "iti".toLowerCase(); //true
//toUpperCase()
"ITI".toUpperCase() === "iti".toUpperCase(); //true

mystr.replace("i", "I"); //"Iti mansoura branch Mansoura"

//we can use regular expression
myStr.replace(/i/, "I"); //"Iti mansoura branch Mansoura" => first i
//use flag g to replace all
myStr.replace(/i/g, "I"); //"ItI mansoura branch Mansoura" => all small i
//use flag i to ignore case
myStr.replace(/i/gi, "I"); //"ItI mansoura branch Mansoura" => all i

//split(separator[, limit])
//return array (always)
myStr.split(" "); //["iti", "mansoura", "branch", "Mansoura"]
myStr.split(" ").length; //4

// in case of empty string or more than one space
"    ds   ds    ".split(" "); //["", "", "", "", "ds", "", "", "", "ds", "", "", ""]
"    ds   ds    ".split(" ").length; //12

//if spliter not found
myStr.split("@"); //["iti mansoura branch Mansoura"]
//array of one element
myStr.split().length; //1

//convert case of every charachter
var result = "";
for (var i = 0; i < myStr.length; i++) {
  if (myStr[i] === myStr[i].toUpperCase()) {
    //myStr[i].toLowerCase();//immutable => won't change
    result += myStr[i].toLowerCase();
  } else {
    result += myStr[i].toUpperCase();
  }
}
//substring(start,[ end]) => end not included
//slice(start[, end]) => end not included
//substr(start[, length])

myStr.substring(0, 3); //"iti"
myStr.substring(3, 0); //"iti" => will be sorted
myStr.length; //27
myStr.substring(0, 30); //"iti mansoura branch Mansoura" out of range =>  get all
myStr.substring(-100, 100); // any negative value will be 0 => is the same as substring(0,27) all
myStr.substring(); //same as substring(0) => get all
myStr.substring(30, 50); //empty string =>

myStr.substr(0, 3); //"iti"
myStr.substr(4, 2); //"ma" => start from index 4 and get 2 chars
myStr.substr(2, 4); //"i m" => start from index 2 and get 4 chars
//get last mansoura in myStr
myStr.substr(myStr.lastIndexOf("Mansoura")); //"Mansoura"

//negative value
myStr.substr(-1); //"a" => last char
myStr.substr(-2); //"ra" => last 2 chars
myStr.substr(0); //"iti mansoura branch Mansoura" => get all
myStr.substr(100, 200); //empty string => out of range

//slice(start[, end]) => end not included
myStr.slice(0, 3); //"iti"
myStr.slice(-4); //"oura" => last 4 chars

//Formatting
document.write("<b><i>" + myStr + "</i></b>");
myStr.bold(); //"<b>iti mansoura branch Mansoura</b>"
myStr.italics(); //"<i>iti mansoura branch Mansoura</i>"
myStr.fontcolor("red"); //"<font color="red">iti mansoura branch Mansoura</font>"

//method chaining

myStr.bold().italics().fontcolor("red"); //"<font color="red"><i><b>iti mansoura branch Mansoura</b></i></font>"
//they are called in order from left to right output of bold() is calling italics() and output of italics() is calling fontcolor("red")

document.write(myStr.bold().italics().fontcolor("red"));
```

> [!danger] undefined+"" => "undefined"
> string + any => string

---

## Array Data Type

> [!tip] Array is a special type of object
> Array is a collection of elements of the same type or different types
>
> 1.  Dense Array
> 1.  Associative Array
>     both literal and constructor declaration saved in heap

```js
//literal declaration
var myArr = []; //length = 0
var myArr = [1, 2, 3, 4, 5]; //length = 5
//constructor declaration
var myArr = new Array(); //length = 0
var myArr = new Array(5); //length = 5
var myArr = new Array(1, 2, 3, 4, 5); //length = 5

var myArr = [
  1,
  "ITI",
  true,
  undefined,
  null,
  {
    id: 1,
    name: "ITI",
    print: function () {
      console.log("Hello ITI");
    },
  },
  [1, 2, 3, 4, 5],
];
myArr[5].name; //"ITI"
myArr[5].print(); //"Hello ITI"
//out of range
myArr[100]; //undefined

//length
myArr.length; //8
myArr.length = 20; //length = 20 add 12x undefined to the end

//not preferred to set with index
myArr[100] = 100; //length = 101 add 100x undefined to the end

//sum integers in array not homogeneous
var sum = 0;
//constructor.name
if (myArr.constructor.name === "Array") {
  for (var i = 0; i < myArr.length; i++) {
    if (myArr[i].constructor.name === "Number") {
      sum += myArr[i];
    }
  }
}
console.log(sum); //15

var arr = [10, 20, 30, 40, 50];
arr[arr.length] = 60; //arr[5] = 60
//but not preferred
//index => getter

//delete
delete arr[0];
true;
arr[0]; //undefined
arr.length; //6

//Manipulating methods

//push(element1, ..., elementN)
//return new length
arr.push(70); //arr[6] = 70
arr.push(80, 90, 100); //arr[7] = 80, arr[8] = 90, arr[9] = 100
//unshift(element1, ..., elementN)
//return new length
arr.unshift(0); //arr[0] = 0

//splice(start[, deleteCount[, item1[, item2[, ...]]]])
//return array of deleted elements
//if deleteCount is 0 => add items to array
//if deleteCount is greater than 0 => delete items from array
//if deleteCount is not specified => delete all elements from start to end

arr.splice(2, 3); //delete 3 elements from index 2
//return [30, 40, 50] deleted elements
arr.splice(1, 2, 20, 30); //delete 2 elements from index 1 and add 20, 30
//return [10, 20] deleted elements

//pop()
//return deleted element
arr.pop(); //100

//shift()
//return deleted element
arr.shift(); //0
```

---

# break

---

```js
//reverse()
//return new array
var arr = [10, 20, 30, 40, 50];
arr.reverse(); //[50, 40, 30, 20, 10]

//sort()
//mutate the original array
var arr = [-10, 100, -100, 20, 2000, 200, 150];

//sort() => ASCII => works with strings but not numbers
console.log("Before sort");
console.table(arr);
arr.sort(); //[-10, -100, 100, 150, 20, 200, 2000]
console.log("After sort");
console.table(arr);

function mySort(a, b) {
  if (a > b) {
    return 1;
  } else if (a < b) {
    return -1;
  } else {
    return 0;
  }
}
//mySort(10, 20) => -1
//mySort(20, 10) => 1
//mySort(10, 10) => 0

arr.sort(mySort); //[-100, -10, 20, 100, 150, 200, 2000]
//mySort is callback function => called by sort() method
// callback function is a function passed to another function as an argument

//mySort is specifically made for sort method so we can use anonymous function
arr.sort(function (a, b) {
  if (a > b) {
    return 1;
  } else if (a < b) {
    return -1;
  } else {
    return 0;
  }
});
//facotring
//ascending
arr.sort(function (a, b) {
  return a - b;
});
//descending
arr.sort(function (a, b) {
  return b - a;
});

//select odd numbers from array
var arr = [10, 20, 30, 40, 50];
var oddArr = [];
for (var i = 0; i < arr.length; i++) {
  //   if (arr[i] % 2 !== 0) {
  //     oddArr.push(arr[i]);
  //   }
  if (isOdd(arr[i])) {
    oddArr.push(arr[i]);
  }
}
function isOdd(num) {
  //predicate function : function that takes arguments and returns boolean
  return num % 2 !== 0;
}
//we can use filter

//filter(predicate(element[, index[, array]])[, thisArg])
//filter(callback(element[, index[, array]])[, thisArg])
//return filtered array
var oddArr = arr.filter(isOdd);
console.table(oddArr);
//anonymous function
var oddArr = arr.filter(function (num) {
  return num % 2 !== 0;
}); //[ 5, 15, 25, 35, 45 ]

// we can sort filtered array
var oddArr = arr
  .filter(isOdd)
  .sort(function (a, b) {
    return a - b;
  })
  .reverse();

//forEach(callback(element[, index[, array]])[, thisArg])
//return undefined
var arr = [10, 20, 30, 40, 50];
arr.forEach(function (num) {
  console.log(num);
}); //10 20 30 40 50
arr
  .filter(function (num) {
    return num >= 30;
  })
  .forEach(function (num) {
    console.log(num);
  }); //30 40 50

//reduce(callback(previousValue, currentValue[, index[, array]])[, initialValue])
//return previousValue
var arr = [10, 20, 30, 40, 50];
var sum = arr.reduce(function (prev, current) {
  return (prev += current);
}, 10); //10 is initialValue => 160
//if we don't specify initialValue => it will be set according to operation + => 0, * => 1, + => 0

var names = ["ahmed", "ali", "mohamed", "mahmoud", "mohamed", "ahmed"];

var result = names.reduce(function (prev, current) {
  return (prev += current);
});
console.log(result); //ahmedalimohamedmahmoudmohamedahmed
var result = names.reduceRight(function (prev, current) {
  return (prev += current);
}); //ahmedmohamedmahmoudmohamedaliali

//slice(start[, end]) => end not included
//return new array => doesn't mutate the original array
//shallow copy
var arr = [10, 20, 30, 40, 50];
var newArr = arr.slice(); //[10, 20, 30, 40, 50]
//XXXX var newArr = arr; //reference
newArr[0] = 100;
console.log(arr); //[10, 20, 30, 40, 50]
console.log(newArr); //[100, 20, 30, 40, 50]

arr.slice(1); //[20, 30, 40, 50] => start from index 1 to end
arr.slice(1, 3); //[20, 30] => start from index 1 to index 3 not included
arr.slice(-1); //[50] => last element
arr.slice(-2); //[40, 50] => last 2 elements

//toString()
//return string
var arr = [10, 20, 30, 40, 50];
arr.toString(); //"10,20,30,40,50"

//join(separator)
//return string
arr.join(); //"10,20,30,40,50"
arr.join(" "); //"10 20 30 40 50"
arr.join(" - "); //"10 - 20 - 30 - 40 - 50"

//eval()
//return result of expression

eval("10 + 20"); //30
eval(arr.join("+")); //150

//XXXXXXXXXXXXX DON'T USE eval() anymore XXXXXXXXXXXXXXX

var names = ["ahmed", "ali", "mohamed", "mahmoud", "mohamed", "hamed"]; //ahmed@gmail.com

//map(callback(element[, index[, array]])[, thisArg])
//return new array with the same length of original array but with different values
var result = names.map(function (name) {
  return name + "@gmail.com";
});

var salaries = [1000, 2000, 3000, 4000, 5000];
//net salary
var result = salaries.map(function (salary) {
  return 0.75 * salary;
});
```

---

### Math Object

```js
//Math.PI
Math.PI; //3.141592653589793
Number(Math.PI.toFixed(2)); //3.14

//round()
// if decimal part >= 0.5 => round up
Math.round(10.5); //11
Math.round(10.4); //10
Math.round(10.6); //11

//ceil()
// round up to the nearest integer

Math.ceil(10.5); //11
Math.ceil(10.4); //11
Math.ceil(10.6); //11

//floor()
// round down to the nearest integer
Math.floor(10.5); //10
Math.floor(10.4); //10
Math.floor(10.6); //10

//random()
// return random number between 0 and 1
Math.random(); //0.123456789
Math.floor(Math.random() * 10); //0 - 9
Math.floor(Math.random() * 11); //0 - 10

//getrandomintrange(min, max)

function getRandomIntRange(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
getRandomIntRange(10, 20); //10 - 20
var colors = ["red", "green", "blue", "yellow", "black", "white"];
colors[getRandomIntRange(0, colors.length - 1)]; //random color

document.body.style.backgroundColor =
  colors[getRandomIntRange(0, colors.length - 1)];
```

---

### Boolean Data Type

```js
truthy values
//literal declaration
var myBool = true;
//constructor declaration
var myBool = new Boolean();//default value is false
new Boolean()// accept any value and return true if value is not falsy value otherwise return false

//falsy values
new Boolean(false);//false
new Boolean(null);//false
new Boolean(undefined);//false
new Boolean(0);//false
new Boolean(NaN);//false
new Boolean("");//false
//truthy values => any value not falsy value
new Boolean("0");//true
if(5){//truthy value
    console.log("true");
}//true
if("aa"){//truthy value
    console.log("true");
}//true


// && => AND
// || => OR
// ! => NOT

//(condition1 && condition2 && condition3 && ...) => true if all conditions are true otherwise false
//if(condition1 ) true => return condition2
//else return condition1
//if(condition2 ) true => return condition3 and so on

"ahmed" && "moustafa" // "moustafa"
"" && "moustafa" // ""
null && undefined // null

//(condition1 || condition2 || condition3 || ...) => true if any condition1 is true return condition1 otherwise return condition2

var test = prompt("Enter your name") || "anonymous";
```

---

### Date Object

> get
> set
> to

```js
var today = new Date(); //current date and time
today.getYear(); //123 - 1900
today.getFullYear(); //2023
today.getMonth(); //0 - 11
today.getDay(); //0 - 6 => 0 is sunday
today.getDate(); //1 - 31

today.setFullYear(2024); //return 173568347834
new Date(173568347834); //Fri Jan 05 2024 11:34:07 GMT+0200 (Eastern European Standard Time)

var event = new Date(2023, 11, 31); //2023-12-31T22:00:00.000Z
//brfore 2000 can use two digits for year
var birthday = new Date(1990, 11, 31); //1990-12-31T22:00:00.000Z

//Locale from machine
//Egypt => ar-EG
//Saudi Arabia => ar-SA
today.toLocaleTimeString(); //"11:34:07 AM"
today.toLocaleDateString("ar-EG");
today.toLocaleDateString("ar-SA");
today.toLocaleDateString("en-US");

(123123123).toLocaleString(); //"123,123,123"
(123123123).toLocaleString("ar-EG"); //"١٢٣٬١٢٣٬١٢٣"
(123123123).toLocaleString("ar-SA"); //"١٢٣٬١٢٣٬١٢٣"
(123123123).toLocaleString("en-US"); //"123,123,123"
(123123123).toLocaleString("nl-BE"); //"123.123.123"
```

---

##### some & every

```js
var arr = [10, 20, 30, 40, 50];
//some(callback(element[, index[, array]])[, thisArg])
//return true if any element in array satisfy the condition otherwise false
arr.some(function (num) {
  return num > 30;
}); //true => 40, 50
arr.some(function (num) {
  return num > 100;
}); //false

//every(callback(element[, index[, array]])[, thisArg])
//return true if all elements in array satisfy the condition otherwise false
arr.every(function (num) {
  return num > 30;
}); //false
arr.every(function (num) {
  return num > 0;
}); //true
```

---

# #lab-js2

- what sorting algorithm is used in sort() method?
