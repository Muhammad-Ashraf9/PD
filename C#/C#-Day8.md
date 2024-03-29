### Delegate

> [!tip] Delegate is a pointer to a function.
>
> - variable has address of a function
>   > [!done] callbak function
>   >
>   > - pass function as parameter
>
> > [!example] Event
> >
> > - to do something when something happens
>
> > [!warning] thread
> >
> > - when creating multiple threads and we want them to excute functions in parallel
> > - we pass a function to the thread (delegate)

> [!info] memory:
>
> - stack:
> - heap:
> - code section:
>   `statement 1 = 0x0001` > `statement 2 = 0x0002`

```csharp
namespace Demo1
    {
        //first step
        public delegate int MyDelegate(int x, int y);
        //int as Add function return int
        //int x, int y as Add function parameters

        //second step
        //define function with same signature as delegate
        static int Add(int x, int y)
        {
            return x + y;
        }
        static void fun()
        {
            Console.WriteLine("fun");
        }
        static int Mul(int x, int y)
        {
            return x * y;
        }
        static void Main(string[] args)
        {
            int x = 10;
            int y = 20;
            int z = Add(x, y);
            //to save function in a variable
            //we need to create new data type delegate

            //third step
            //define new Reference of type MyDelegate  point to Add function
            //pass function name to delegate
            MyDelegate myDelegate = new MyDelegate(Add);
            //can store only fuction with same signature as delegate
            //myDelegate = new MyDelegate(fun);//error: fun not same signature

            //this is the same as above
            MyDelegate myDelegate2 = Add;

            //fourth step
            //invoke delegate
            //call function using delegate(through reference/address)
            z = myDelegate.Invoke(x, y);
            //or
            z = myDelegate(x, y);

            //we can change the address of delegate to point to another function with same signature
            myDelegate = Mul;

            //if we call null delegate
            //myDelegate.Invoke(x, y);//error: Object reference not set to an instance of an object.

            //myDelegate(x, y);//error: Object reference not set to an instance of an object.

            //we can check if delegate is null or not before calling it
            myDelegate?.Invoke(x, y);//if delegate is null, do nothing
        }
    }
```

---

```csharp
namespace Demo2
{
    internal class Program
    {
        public delegate void MyDelegate();
        //so we can save any function with no return value and no parameters
        //it is like making no data type
        // now we can create Reference of type MyDelegate
        public static void print()
        {
            Console.WriteLine("print");
        }
        public static void print2()
        {
            Console.WriteLine("print2");
        }
        static void Main(string[] args)
        {
            MyDelegate myDelegate = print;
            myDelegate.Invoke();//print

        //we use same delegate to save different functions
            myDelegate = print2;
            myDelegate.Invoke();//print2
        //multicast delegate
        //add function to delegate so we can call both functions

            myDelegate += print;
            myDelegate.Invoke();//print2 print then print 
            //call will be in order of adding functions to delegate

            //invokation list
            myDelegate.GetInvocationList();//return array of functions
            Console.WriteLine(myDelegate.GetInvocationList().Length);//2: 2 functions in delegate
        }
    }
}
```

> [!tip] multicast delegate
>
> - Trace:
> - myDelegate => Non-Public members => \_invocationList,\_methodPtr:has address of last function added
> - in dll: myDelegate extends MulticastDelegate

> [!tip] pass delegate as parameter

```csharp
public static void myFun(MyDelegate myDelegate)
//pass delegate as parameter
{

    myDelegate.Invoke();//call functions in delegate
}
```

```csharp
//main
myFun(myDelegate);

```

```csharp
//main
List<int> list = [10,5,32,6,8,9,7,4,3,2,1];//C# 12
//list.FindAll(delegate(int x){return bool;});
//accept delegate as parameter : its return type is bool and it has one parameter(Predicate)
//to get numbers > 30 ...etc we need to pass function to delegate

var res = list.FindAll(isGreaterThan30);//
foreach (var item in res)
{
    Console.WriteLine(item);//32
}
//we can pass any function with same signature as delegate

res = list.FindAll(isLessThan20);
foreach (var item in res)
{
    Console.WriteLine(item);//10 5 6 8 9 7 4 3 2 1
}

```

```csharp
//condition function
public static bool isGreaterThan30(int x)
{
    return x > 30;
}

 //less than 20
    public static bool isLessThan20(int x)
    {
        return x < 20;
    }

```

---

```csharp
//condition function
//length of string > 3
public static bool isGreaterThan3(string x)
{
    return x.Length > 3;
}
public static bool startWithA(string x)
{
    return x.StartsWith("a");
}
```

```csharp
//main
List<string> names = ["ahmed","ali","mohamed","mahmoud","mohamed","ahmed","ali","mohamed","mahmoud","mohamed"];

var res = names.FindAll(isGreaterThan3);
foreach (var item in res)
{
    Console.WriteLine(item);//ahmed mohamed mahmoud mohamed ahmed mohamed mahmoud mohamed
}

res = names.FindAll(startWithA);//we pass function name to FindAll which accept delegate as parameter
foreach (var item in res)
{
    Console.WriteLine(item);//ahmed ali ahmed ali
}
```

> [!tip] sortAscending function

```csharp
public static void sortAscending(int x, int y)
{
    for (int i = 0; i < x.Length; i++)
    {
        for (int j = 0; j < y.Length; j++)
        {
            if (x[j] > y[j + 1])
            {
                swap(x[j], y[j + 1]);
            }
        }
    }
}
public static void sortDescending(int x, int y)
{
    for (int i = 0; i < x.Length; i++)
    {
        for (int j = 0; j < y.Length; j++)
        {
            if (x[j] < y[j + 1])
            {
                swap(x[j], y[j + 1]);
            }
        }
    }
}
```

```csharp
//descending is the same but the only change is if (x[j] < y[j + 1])
//we can pass this function to Sort function ascending or descending

//condition1: x[j] > y[j + 1]
//condition2: x[j] < y[j + 1]
public static bool condition1Ascending(int x, int y)
{
    return x > y;
}
public static bool condition2Ascending(int x, int y)
{
    return x < y;
}
```

```csharp
public delegate bool MyDelegate(int x, int y);

//sort function only one function for ascending and descending
public static void sort(int[] arr, MyDelegate myDelegate)
{
    for (int i = 0; i < arr.Length; i++)
    {
        for (int j = 0; j < arr.Length; j++)
        {
            if (myDelegate(arr[j], arr[j + 1]))
            {
                swap(arr[j], arr[j + 1]);
            }
        }
    }
}

//main
int[] arr = [10,5,32,6,8,9,7,4,3,2,1];
MyDelegate myDelegate = condition1Ascending;
sort(arr, myDelegate);
foreach (var item in arr)
{
    Console.WriteLine(item);//1 2 3 4 5 6 7 8 9 10 32
}
myDelegate = condition2Descending;
sort(arr, myDelegate);
foreach (var item in arr)
{
    Console.WriteLine(item);//32 10 9 8 7 6 5 4 3 2 1
}

//we can add multiple functions to delegate
myDelegate += condition1Ascending;
//the last function added to delegate will be evaluated last in sort function
sort(arr, myDelegate);


//in case of instace method we pass obj.functionName
//unlike static method we pass only functionName
```

> [!tip] in delegate we don't care about function name unlike interface and CompareTo method

---

# Break

---

> [!example] if the function is used one place or one time
>
> - we can use anonymous function
> - we can use lambda expression

```csharp
//main

//anonymous function
MyDelegate myDelegate1  = delegate(int x, int y)
{
    return x > y;
};
// this will create function in memory and save its address in myDelegate
//we only need to cast it to delegate type(no name or return type needed)


//lambda expression
MyDelegate myDelegate2 = (int x, int y) =>
{
    return x > y;
};
//we can omit curly braces and return keyword
MyDelegate myDelegate2 = (int x, int y) => x > y;
//we can omit data type
MyDelegate myDelegate3 = (x, y) => x > y;//x and y are int from delegate type

//we pass lambda expression to sort function
sort(arr, (x, y) => x > y);//if it was more than one line we need to use curly braces

names.FindAll((x) => x.StartsWith("a"));
//we can omit round brackets if we have only one parameter
names.FindAll(x => x.StartsWith("a"));

//no parameters: we use empty round brackets
MyDelegate myDelegate4 = () => Console.WriteLine("hello");
```

---

```csharp
namespace Demo5
{
    delegate void MyDelegate(int x);
    internal class Program
    {
        public static void print1(int x)
        {
            Console.WriteLine($"print1 {x}");
        }
        public static void print2(int x)
        {
            Console.WriteLine($"print2 {x}");
        }
        static void Main(string[] args)
        {
            MyDelegate myDelegate = print1;
            myDelegate += print2;
            myDelegate.Invoke(10);//print1 10 print2 10
            //we can add lambda expression to delegate
            myDelegate += (x) => Console.WriteLine($"lambda {x}");
            myDelegate.Invoke(10);//print1 10 print2 10 lambda 10
            //we can add anonymous function to delegate
            myDelegate += delegate(int x)
            {
                Console.WriteLine($"anonymous {x}");
            };
            myDelegate.Invoke(10);//print1 10 print2 10 lambda 10 anonymous 10
        }
    }

}
```

> [!tip] add & subtract delegate

```csharp
delegate void MyDelegate2(int x, int y);

//delegate return type is void and it has 0 parameters
delegate void MyDelegate3();
// delegate return type is double and it has 2 parameters (double x, double y)
delegate double MyDelegate4(double x, double y);



public static void Add(int x, int y)
{
    Console.WriteLine($"Add {x + y}");
    return x + y;
}
public static void Sub(int x, int y)
{
    Console.WriteLine($"Sub {x - y}");
    return x - y;
}
public static void print1()
{
    Console.WriteLine($"print1 ");
}
public static double Add(double x, double y)
{
    Console.WriteLine($"Add {x + y}");
    return x + y;
}
static void Main(string[] args)
{
    MyDelegate2 myDelegate2 = new MyDelegate2(Add);
    //or
    myDelegate2 = Add;//more common
    myDelegate2 += Sub;
    int z = myDelegate2.Invoke(10, 20);//store last function return value
    //Add 30    Sub -10
    Console.WriteLine(z);//-10

    myDelegate2 += delegate(int x, int y)
    {
        Console.WriteLine($"anonymous Mul{x * y}");
        return x * y;
    };

    z = myDelegate2.Invoke(10, 20);
    //Add 30    Sub -10    anonymous Mul200
    Console.WriteLine(z);//200
    myDelegate2 += (x, y) =>
    {
        Console.WriteLine($"lambda Div{x / y}");
        return x / y;
    };
    z = myDelegate2.Invoke(20, 10);
    //Add 30    Sub 10    anonymous Mul200    lambda Div2

    Console.WriteLine(z);//2

    //we can create new delegate void return type and no parameters
    //as function has to same signature as delegate

    MyDelegate3 myDelegate3 = print1;
    myDelegate3.Invoke();//print1
    myDelegate3 += () => Console.WriteLine("lambda print2");
    myDelegate3.Invoke();//print1 lambda print2

    //we can remove function from delegate
    myDelegate3 -= print1;
    myDelegate3.Invoke();//lambda print2

//double delegate

}
```

> [!danger] instead of having similar delegates with different data types
>
> > [!done] we can use generic delegate

```csharp
delegate T MyDelegate<T>(T x, T y);
// repacling  int , double functions with one delegate


delegate T3 MyDelegate2<T1, T2, T3>(T1 x, T2 y);
//this replace all delegates with 2 parameters it takes 3 data types (can be same or different)
//but we has to state the 3 data types when creating delegate




public static void Add(int x, int y)
{
    Console.WriteLine($"Add {x + y}");
    return x + y;
}
public static void Sub(int x, int y)
{
    Console.WriteLine($"Sub {x - y}");
    return x - y;
}

public static void Mul(int x, int y)
{
    Console.WriteLine($"Mul {x * y}");
    return x * y;
}
public static void Div(int x, int y)
{
    Console.WriteLine($"Div {x / y}");
    return x / y;
}

public static double AddDouble(double x, double y)
{
    Console.WriteLine($"Add {x + y}");
    return x + y;
}
public static int MyFun(int x, float y)
{
    Console.WriteLine($"MyFun {x + y}");
    return x + y;
}
static void Main(string[] args)
{
    MyDelegate<int> myDelegate = Add;
    myDelegate += Sub;
    myDelegate += Mul;
    myDelegate += Div;
    myDelegate.Invoke(10, 20);
    //Add 30    Sub -10    Mul 200    Div 0.5
    MyDelegate<double> myDelegate2 = AddDouble;

    //we has to state the 3 data types when creating delegate
    MyDelegate2<int, int, int> myDelegate3 = Add;

    MyDelegate2<int, int, double> myDelegate4 = Add;

    MyDelegate2<int, float, int> myDelegate5 = MyFun;
}
```

> [!danger] we can constrain on generic delegate (in, out)

```csharp
delegate T3 MyDelegate2<in T1,in T2,out T3>(T1 x, T2 y);
// delegate T1 MyDelegate2<in T1,in T2,out T3>(T1 x, T2 y);//error: T1 is input
// delegate T3 MyDelegate2<in T1,in T2,out T3>(T1 x, T3 y);//error: T3 is output

//if we have 3 parameters function we have to create delegate with 3 parameters
delegate T4 MyDelegate2<in T1,in T2,in T3,out T4>(T1 x, T2 y, T3 z);
//..and so on with 4,5,6,7,8,9,10 parameters
```

> [!warning] Func
>
> - `System.Func<T1, T2, T3, T4, T5, T6, T7, T8, T9,...16,out TResult>`
> - takes any number of parameters of any data type
> - only when we have function with more than 16 parameters we use delegate
>   -used when we have return type => TResult

> [!tip] Action
>
> - `System.Action<T1, T2, T3, T4, T5, T6, T7, T8, T9,...16>`
> - takes any number of parameters of any data type
> - return type is ==void==

> [!example] Predicate
>
> - `System.Predicate<T>`
> - return type is ==bool==

> [!warning] EventHandler
>
> - `System.EventHandler<TEventArgs>`
> - `System.EventHandler
> - takes 2 parameters (object , TEventArgs )


>[!done] we can use (Func, Action, Predicate, EventHandler) instead of creating delegate
```csharp
//main

//return type is not void
Func<int, int,int> myDelegate = Add;
Func<int, float,double> myDelegate2 = MyFun;


//return type is void
Action<int> myDelegate3 = Print1;

```

---
## #lab-8
