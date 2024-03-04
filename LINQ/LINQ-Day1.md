# LINQ

> [!note] LINQ
>
> - Language Integrated Query
> - Functions extension methods, operators, and query expressions,sequence (contains elements) of objects
> - Querying data from different data sources like arrays, lists, XML, SQL, Dataset, etc.
> - array,list => linq to object
> - xml => linq to xml
> - ado (datatable,dataset) => linq to ado
>
>   > [!done] remotly
>   >
>   > - linq to entity
>   > - linq to nosql
>
> - same query syntax for all data sources

> [!tip] sequence
>
> - any collection of objects that implements `IEnumerable<T> `interface

> [!important] LINQ methods
>
> - used for projection, filtering, grouping, joining, ordering, aggregating, etc.

![](linq%20methods.jpg)

---

### Implicit Typed variables

> [!tip] `var` keyword
>
> - used to declare a variable of any type
> - type is determined at compile time based on the value assigned to it
>
> ```csharp
> var i = 10; // int
> Console.WriteLine(i.GetType()); // System.Int32
> var j = 10.5; // double
> Console.WriteLine(j.GetType()); // System.Double
> var k = 10.5f; // float
> Console.WriteLine(k.GetType()); // System.Single
> k = 2.5; // error no implicit conversion from double to float
> var s = "hello"; // string
> i = "hello"; // error
> var t ; // error no value=> has to be assigned
> ```
>
> - used with anonymous types and LINQ queries

> [!warning] `object` keyword
>
> - used to declare a variable of any type
> - type is determined at runtime based on the value assigned to it (initiale value doesn't matter)
>
> ```csharp
> object i = 10; // int
> Console.WriteLine(i.GetType()); // System.Int32
> i = 10.5; // double
> Console.WriteLine(i.GetType()); // System.Double
> i = 10.5f; // float
> Console.WriteLine(i.GetType()); // System.Single
> i = "hello"; // string
> ```

---

> [!warning] `c#` is a statically typed language
>
> - any variable has to be declared with a type unlike `javascript` which is dynamically typed language

### Anonymous Types

```csharp
namespace Demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            Employee e1 = new Employee { ID = 101, Name = "Mark", Salary = 5000, Experience = 5 };
            //anonymous type
            var e2 = new { ID = 102, Name = "John", Salary = 6000, Experience = 6 };
            //this will create a class with these properties  and will instantiate the object of that class with these values

            Console.WriteLine(e2.GetType()); //Demo1.Program+<>f__AnonymousType0`4[System.Int32,System.String,System.Int32,System.Int32]

            Console.WriteLine(e2.GetType().Name); //<>f__AnonymousType0`4

            Console.WriteLine(e2.ID + " " + e2.Name + " " + e2.Salary + " " + e2.Experience);
            //102 John 6000 6

            Console.WriteLine(e2.ToString()); //{ ID = 102, Name = John, Salary = 6000, Experience = 6 }
            //overriding the toString method by default to display the properties of the object

            //new object with same state
            var e3 = new { ID = 102, Name = "John", Salary = 6000, Experience = 6 };
            Console.WriteLine(e3.GetType()); // won't create a new class, will use the same class as e2

            // but if the properties are different then it will create a new class
            var e4 = new { ID = 101, Name = "Mark", Salary = 5000, Experience = 5, Address = "123 street" };
            Console.WriteLine(e4.GetType()); //Demo1.Program+<>f__AnonymousType1`5[System.Int32,System.String,System.Int32,System.Int32,System.String]
            // these are 2 different objects in different memory locations
            Console.WriteLine(e3 == e1); //true
            //overriding the equals method by default

            e2.ID = 103; //error, read only property: can't change the value of anonymous type properties
        }
    }
}
```

> [!tip] anonymous type
>
> - c# compiler generates datatype for anonymous type at compile time
> - a class with read-only properties (immutable) can't change the value of properties after instantiation
> - overrides the `ToString` and `Equals` and `GetHashCode` methods by default

---

```csharp
//class MyMath

namespace Demo1
{
    class MyMath
    {
        public static int Add(int a, int b)
        {
            return a + b;
        }
    }
}

//main

namespace Demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            int x = 10;
            int y = 20;
            var result = MyMath.Add(10, 20);
            Console.WriteLine(result); //30

            //extension method
            x.Add(20); //30
            //how to add a method to a class without modifying the class

            Employee e1 = new Employee();
            e1.ToString(); //hidden parameter this reference to the object

            //we can mimic the same behavior by creating a static method in a static class
        }
    }
}

```

> [!tip] extension method

```csharp
namespace Demo1
{
    public static class MyMath
    {
        public static int Add(this int a, int b)
        {
            return a + b;
        }
    }
}

//any variable of type int can use this method

//main

namespace Demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            int x = 10;
            int y = 20;
            //extension method
           Console.WriteLine(x.Add(20)); //30

           //mirror method
            Console.WriteLine(MyMath.Mirror(x));//instead of we can use  x.Mirror()
             Console.WriteLine(x.Mirror());
            // we can achieve the same behavior by creating a static method in a static class
        }
    }
}


```

---

```csharp
//MyMath

namespace Demo1
{
    static class MyMath
    {
        public static int Add(this int a, int b)
        {
            return a + b;
        }

        public static int Mirror(this int x )
        {
            int r = x;
            int y = 0;
            while (x > 0)
            {
                y = (y * 10) + (r % 10);
                r = r / 10;
            }
            return y;
            //or Tostring and reverse
            // return int.Parse(x.ToString().Reverse().ToString());
        }
    }
}

//main

namespace Demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            int x = 10;
            int y = 20;

           //mirror method
            Console.WriteLine(MyMath.Mirror(x));//instead of we can use  x.Mirror()

             Console.WriteLine(x.Mirror());
            // we can achieve the same behavior by creating a static method in a static class
        }
    }
}

```

---

### Delegates

> [!example] Action
>
> - reference to a method that takes no parameters and no return value
>
> ```csharp
> Action a = () => Console.WriteLine("Hello");
> a.Invoke();//Hello
> ```
>
> - to take parameters use `Action<T>` where `T` is the type of the parameter
> - this doesn't return anything only used to print
>
>
```csharp
 Action<int> a = (x) => Console.WriteLine(x);> a.Invoke(10);//10
 ```

>[!example]
> - to take multiple parameters use `Action<T1,T2,...,Tn>` where `T1,T2,...,Tn` are the types of the parameters up to 16 parameters(more than 16 declare a delegate)
>
> ```csharp
> Action<int,int,float> a = (x,y,z) => Console.WriteLine(x+y+z);
> a.Invoke(10,20,30.5f);//60.5 => we have to add f to the float value
> ```

> [!warning] Func
>
> - reference to a method that takes parameters and returns a value
> - up to 16 parameters and the last parameter is the return type (has to return a value)
>
> ```csharp
> Func<int,int, float> f = (x,y) => x+y;//has to return a value
> Func<int,int, float> f = (x,y) => 2.5;//we can return double which is implicitly converted to float
> Func<int,int, float> f = (x,y) => "hello";//error no implicit conversion from string to float
> ```

> [!tip] predicate
>
> - reference to a method that takes a parameter and returns a boolean value

 ```csharp
 Predicate<int> p = (x) => x>10;
 

> Console.WriteLine(p.Invoke(20));//true
> Console.WriteLine(p.Invoke(5));//false
> ```

---

### LINQ Methods

> [!example] linq
>
> - linq is about 40 methods that are extension methods to the `IEnumerable<T>` interface
> - `GetEnumerator` method is used to iterate through the collection

> [!tip] `Enumerable` class
>
> - `system.linq` namespace
> - contains all the linq methods
> - like: `Where`, `Select`, `OrderBy`, `GroupBy`, `Join`, `Take`, `Skip`, `First`, `Last`, `Single`, `Count`, `Sum`, `Average`, `Max`, `Min`, `Aggregate`, `Contains`, `Any`, `All`, `Distinct`, `Concat`, `Union`, `Intersect`, `Except`, `Reverse`, `ElementAt`, `ElementAtOrDefault`, `DefaultIfEmpty`, `OfType`, `Cast
>   link:https://source.dot.net/#q=enumera

> [!example] `Where` method

```csharp
//list
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var res = Enumerable.Where(numbers, x => x > 5);//6,7,8,9,10
//this doesn't change the original list

foreach (var item in res)
{
    Console.WriteLine(item);//6,7,8,9,10
}

//or
var res = numbers.Where(x => x > 5);//6,7,8,9,10
//same performance and easier to write this will be converted to the previous one by the compiler

```

---

> [!bug] deferred execution vs immediate execution
>
> - linq methods are executed in 2 ways
> - deferred execution: the query is not executed until the result is iterated
> - immediate execution: the query is executed immediately
> - some methods are deferred and some are immediate
> - `Where` method is deferred

```csharp
//where method
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var res = numbers.Where(x => x > 5);//deferred execution: the query is not executed until the result is iterated not in this line
Console.WriteLine(res.GetType());//System.Linq.Enumerable+WhereListIterator`1[System.Int32]

numbers.Add(100);//the result will contain 100

foreach (var item in res)//it will be executed here
{
    Console.WriteLine(item);//6,7,8,9,10,100
}

//immediate execution
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var res = numbers.Where(x => x > 5).ToList();//immediate execution: the query is executed immediately in this line
Console.WriteLine(res.GetType());//System.Collections.Generic.List`1[System.Int32]
numbers.Add(100);//the result will not contain 100
//fluent syntax

foreach (var item in res)
{
    Console.WriteLine(item);//6,7,8,9,10
}
```

---

# Break

---

#### query expression

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var res = from n in numbers
          where n > 5
          select n;//any query has to end with select.
var res = from n in numbers
          where n > 5; //error
          //any query has to end with select or group by

foreach (var item in res)
{
    Console.WriteLine(item);//6,7,8,9,10
}

//all of this will be converted to the previous Enumerable.Method
```

---

> [!important]
>
> 1. class Customer, Order, Product implement IComparable interface (given in lab)

```csharp
using static Demo1.ListGenerators;//to use static methods in the ListGenerators class directly without the class name
//same with linq
using System.Linq;//implicit using => not necessary to use it
```

```csharp
// get products that are in stock
var res = ProductList.Where(p => p.UnitsInStock > 0);
foreach (var item in res)
{
    Console.WriteLine(item.ProductName);// all products that are in stock
}

// get products that are out of stock
var res = ProductList.Where(p => p.UnitsInStock == 0);
foreach (var item in res)
{
    Console.WriteLine(item.ProductName);// all products that are out of stock
}
// display only product name
var res = ProductList.Where(p => p.UnitsInStock > 0).Select(p =>  p.ProductName);
foreach (var item in res)
{
    Console.WriteLine(item);//  all products that are in stock with only the product name
}

// display only product name and ID
var res = ProductList.Where(p => p.UnitsInStock > 0).Select(p => new { p.ProductName, p.ProductID });

// display only product name and ID with alias
var res = ProductList.Where(p => p.UnitsInStock > 0).Select(p => new { Name = p.ProductName, ID = p.ProductID });

// display id + name concatenated
var res = ProductList.Where(p => p.UnitsInStock > 0).Select(p => new { data = p.ProductID + " " + p.ProductName });

// select all product
var res = ProductList.Where( p => p.UnitsInStock > 0).Select(p => p);

//Query Expression
//display all products that are in stock
var res = from p in ProductList
          where p.UnitsInStock > 0
        //   select p;//all products details
        //   select p.ProductName;//all products names
        //   select new { p.ProductName, p.ProductID };//all products names and ids
        //   select new { Name = p.ProductName, ID = p.ProductID };//all products names and ids with alias

/////////////// order by (ascending)
var res = ProductList.Where(p => p.UnitsInStock > 0)
.OrderBy(p => p.UnitPrice)
.Select(p => new { p.ProductName, p.UnitPrice });

/////////////// then by
var res = ProductList.Where(p => p.UnitsInStock > 0)
.OrderBy(p => p.UnitPrice)//if the unit price is the same then order by product id
.ThenBy(p => p.ProductID)
.Select(p => new { p.ProductName, p.UnitPrice, p.ProductID });

//we can add properties to the anonymous type
var res = ProductList.Where(p => p.UnitsInStock > 0)
.OrderBy(p => p.UnitPrice)
.ThenBy(p => p.ProductID)
.Select(p => new { p.ProductName, p.UnitPrice, p.ProductID, l = p.ProductName.Length });

///////////////// ThenByDescending
var res = ProductList.Where(p => p.UnitsInStock > 0)
.OrderBy(p => p.UnitPrice)
.ThenByDescending(p => p.ProductID)
.Select(p => new { p.ProductName, p.UnitPrice, p.ProductID });
// we can use multiple ThenBy

var res = from p in ProductList
          where p.UnitsInStock > 0
          orderby p.UnitPrice, p.ProductID //will order by unit price and if the unit price is the same then order by product id
          orderby p.UnitPrice, p.ProductID descending//will order by unit price and if the unit price is the same then order by product id descending
          orderby p.UnitPrice descending, p.ProductID descending//will order by unit price descending and if the unit price is the same then order by product id descending
          select new { p.ProductName, p.UnitPrice, p.ProductID };//has to end with select
```

---

###### indexed query

```csharp
//indexed query
var res = ProductList.Where((p, index) => p.UnitsInStock > 0 && index > 10);
//will return the products that are in stock and the index is greater than 10
var res = ProductList.Where((p, index) => p.UnitsInStock > 0 && index >= 10 && index <= 15);
//will return the products that are in stock and the index is between 10 and 15
//can be used with fluent syntax not with query expression

var res = ProductList.Where((p, index) => p.UnitsInStock > 0 && index > 10).Select((p, index) => new { p.ProductName, p.UnitPrice, index });
```

> [!example] Neutral order operator
>
> - `skip` and `take` methods
> - `SkipWhile` and `TakeWhile` methods

```csharp
//skip and take
//take 5 products that are in stock
var res = ProductList.Where(p => p.UnitsInStock > 0).Take(5);
//skip 5 products that are in stock and display the rest
var res = ProductList.Where(p => p.UnitsInStock > 0).Skip(5);
//skip 5 products that are in stock and take 5 products
var res = ProductList.Where(p => p.UnitsInStock > 0).OrderBy(p => p.UnitPrice).Skip(5).Take(5);

int i = 5;
var res = ProductList.Where(p => p.UnitsInStock > 0).OrderBy(p => p.UnitPrice).Skip(i*5).Take(5);

//Count property of list vs Count extension method from Enumerable
int size = ProductList.Count/5;//get the number of 5s in the list
for (int i = 0; i < size + 1; i++)
{
    var res = ProductList.Where(p => p.UnitsInStock > 0).OrderBy(p => p.UnitPrice).Skip(i*5).Take(5);
    foreach (var item in res)
    {
        Console.WriteLine(item.ProductName);
    }
    Console.ReadLine();
    Console.Clear();
}
//only 75 products will be displayed have to add 1 to the size
```
---
```csharp
List<int> arr = [1,2,3,4,5,6,7,8,9,10];
arr.SkipWhile(x => x < 5);//6,7,8,9,10
//skip the elements until the condition is false

arr.TakeWhile(x => x < 5);//1,2,3,4
//take the elements until the condition is false
```

---

>[!important] `First` and `Last` methods
> - operators that return only one element from the sequence
> - `First` returns the first element from the sequence
> - `Last` returns the last element from the sequence

```csharp
//first and last
List<int> arr = [1,2,3,4,5,6,7,8,9,10];
var res = arr.First();//1
var res = arr.Last();//10

var res = arr.First(x => x > 5);//get the first element that is greater than 5
var res = arr.Last(x => x > 5);//get the last element that is greater than 5

var res = arr.First(x => x > 10);//error no element satisfies the condition
//System.InvalidOperationException: 'Sequence contains no matching element'

//FirstOrDefault and LastOrDefault methods
// instead of throwing an exception if no element satisfies the condition it will return the default value of the type
var res = arr.FirstOrDefault(x => x > 10);//0
var res = arr.LastOrDefault(x => x > 10);//0

//Single and SingleOrDefault methods
// has to be only one element that satisfies the condition otherwise it will throw an exception
var res = arr.Single(x => x == 5);//5
var res = arr.Single(x => x > 5);//error :InvalidOperationException: Sequence contains more than one matching element

//SingleOrDefault
//Returns the only element of a sequence, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.
var res = arr.SingleOrDefault(x => x > 5);//error :InvalidOperationException: Sequence contains more than one matching element

var res = arr.SingleOrDefault(x => x > 10);//0

var res = arr.SingleOrDefault(x => x == 5);//5
```

---
```csharp
//ElementAt 
//returns the element at a specified index in a sequence
var res = arr.ElementAt(5);//6
var res = arr.ElementAt(99);//error: ArgumentOutOfRangeException: Index was out of range. Must be non-negative and less than the size of the collection.

```
>[!important] Aggregation methods
> - `Count` returns the number of elements in the sequence
> - `Sum` returns the sum of the elements in the sequence
> - `Average` returns the average of the elements in the sequence

```csharp
//aggregation methods
List<int> arr = [1,2,3,4,5,6,7,8,9,10];
var res = arr.Count();//10
var res = arr.Count(x => x > 5);//5

//Min and Max methods 
// has to be a sequence of comparable elements (implement IComparable interface)

var res = arr.Min();//1
var res = arr.Max();//10

Var res = ProductList.Min(p => p.UnitPrice);//get the minimum unit price

//Query Expression
var res = (from p in ProductList
          where p.UnitsInStock > 0
          select p.UnitPrice).Min();//get the minimum unit price

//sum and average
var res = ProductList.Sum(p => p.UnitPrice);//get the sum of the unit prices
var res = ProductList.Average(p => p.UnitPrice);//get the average of the unit prices
```

>[!important] Generation methods
> - has to be called by the `Enumerable` class
> - `Range` returns a sequence of numbers within a specified range
> - `Repeat` returns a sequence that contains one repeated value
> - `Empty` returns an empty sequence
```csharp
//generation methods
var res = Enumerable.Range(1, 10);//1,2,3,4,5,6,7,8,9,10

var res = Enumerable.Repeat("hello", 5);//hello,hello,hello,hello,hello
var res = Enumerable.Repeat(ProductList.First(), 5);//repeat the first product 5 times

var res = Enumerable.Empty<int>();//empty sequence
```

>[!warning] SelectMany method
> - the only method that returns a sequence of sequences


```csharp
    List <String> names = new List<String> { "Ali h", "Ahmed j", "Omar u" };
    var res = names.SelectMany(n => n);
    var res = names.SelectMany(n => n.ToCharArray());//
    foreach (var item in res)
    {
        Console.WriteLine(item);// A, l, i, h, A, h, m, e, d, j, O, m, a, r, u
    }
    var res = names.SelectMany(n => n.Split(" "));//split the names by space
```

>[!example] Intersect vs Union vs Concat vs Except
> - `Intersect` returns the common elements between 2 sequences
> - `Union` returns the unique elements between 2 sequences (no duplicates)
> - `Concat` returns the elements of 2 sequences
> - `Except` returns the elements of the first sequence that are not in the second sequence
```csharp
var r1 = Enumerable.Range(1, 10);
var r2 = Enumerable.Range(5, 10);
var res = r1.Intersect(r2);//5,6,7,8,9,10
foreach (var item in res)
{
    Console.WriteLine(item);//5,6,7,8,9,10
}

var res = r1.Union(r2);//1,2,3,4,5,6,7,8,9,10

var res = r1.Concat(r2);//1,2,3,4,5,6,7,8,9,10,5,6,7,8,9,10

var res = r1.Except(r2);//1,2,3,4
//won't throw an exception if all the elements of the first sequence are in the second sequence
```
>[!tip] Quantifiers
> - `Any` returns true if `any` element satisfies the condition
> - `All` returns true if `all` elements satisfy the condition


>[!example] zip operator
> - `Zip` returns a sequence of tuples
> - the first element of the first sequence is paired with the first element of the second sequence and so on
```csharp
var r1 = Enumerable.Range(1, 10);
List<String> names = new List<String> { "Ali h", "Ahmed j", "Omar u" };
var res = r1.Zip(names, (x, y) => new {id = x, name = y});

```
>[!important] Grouping
> - `GroupBy` returns a sequence of groups
> - each group has a key and a sequence of elements
```csharp
//Query Expression
 var res  = from p in ProductList
           group p by p.Category;
foreach (var item in res)
{
    Console.WriteLine($"Category Name: {item.Key}");
    foreach (var p in item)
    {
        Console.WriteLine($"Product Name: {p.ProductName}");
    }
}

//Fluent Syntax
var res = ProductList.GroupBy(p => p.Category);
           
```

>[!warning] casting operators
> - `ToArray` returns an array
> - `ToList` returns a list

---
>[!done] #Linq-Lab1
> - 8 pages 

> [!danger] to get All notes (`.md` files)
>
> - link: [https://github.com/Muhammad-Ashraf9/PD](https://github.com/Muhammad-Ashraf9/PD)