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
> ```csharp
> Action<int> a = (x) => Console.WriteLine(x);
> a.Invoke(10);//10
> ```
>
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
>
> ```csharp
> Predicate<int> p = (x) => x>10;
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

foreach (var item in res)
{
    Console.WriteLine(item);//6,7,8,9,10
}
```
