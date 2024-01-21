### Interface

> [!tip] Interface is a data type that has abstract methods.
>
> - used instead of multiple inheritance
>   - can only inherit one class but can implement multiple interfaces
>
> > [!warning] struct does not inherit or get inherited but can implement interfaces.
> >
> > - provide implementation for all methods in the interface
>
> > [!done] can reference from interface to object of class that implements it
> > `InterfaceName obj = new ClassName();`
>
> > [!danger] Contract:
> >
> > - class that implements interface must provide implementation for all methods in the interface with the ==same signature==
> >
> > ```csharp
> > interface IShape
> > {
> >     void Draw();
> > }
> > class Circle : IShape
> > {
> >     public void Draw()
> >     {
> >         Console.WriteLine("Draw a circle");
> >     }
> > }
> > ```
>
> > [!example] open-closed principle
> >
> > - open for extension, closed for modification
> > - add new functionality without modifying existing code
> >
> > ```csharp
> >  class OddSeries{
> > 	 int n = 1;
> >      public void GetNextOddNumber()//return 1,3,5
> >      {
> >          //code
> >          int i = n;
> >          n += 2;
> >          return i;
> >      }
> >  }
> >  class EvenSeries{
> > 	 int n = 0;
> >      public void GetNextEvenNumber()//return 2,4,6
> >      {
> > 	     int i = n;
> > 	     n += 2;
> > 	     return i;
> >          //code
> >      }
> >  }
> > class Series{
> >      public void  printSeries(OddSeries o)//we can only pass OddSeries object we can't pass EvenSeries object
> >      {
> >          //code
> >      }
> >
> > }
> > ```
> >
> > - to be open for extension, we need to make interface and make OddSeries and EvenSeries implement it and pass ISeries object to printSeries method
>
> ---
>
> > [!done] Interface Inheritance
> >
> > ```csharp
> > interface ISeries
> > {
> >     int GetNextNumber();
> > }
> > class OddSeries : ISeries
> > {
> >     int GetNextNumber(){
> >         //code to return odd number
> >     }
> > }
> > class EvenSeries : ISeries
> > {
> >     int GetNextNumber(){
> >         //code to return even number
> >     }
> > }
> > class Series
> > {
> >     public void PrintSeries(ISeries obj)//obj can only see methods in ISeries interface with obj implementaion of OddSeries or EvenSeries
> >     {
> >         //code
> >     }
> > }
> > ```
>
> > [!tip] if we wanted to add new series we just need to implement ISeries interface and pass object of it to PrintSeries method
> >
> > - fibonacci series
> >
> > ```csharp
> > class FibonacciSeries : ISeries
> > {
> >     int GetNextNumber(){
> >         //code to return fibonacci number
> >     }
> > }
> > ```
>
> ---
>
> - Now we can say that our code is open for extension and closed for modification

---

```csharp
    namespace Demo1Interface
    {
        interface ISeries
        //Interface name start with capital I
        {
            int GetNextNumber();
        }

        class OddSeries : ISeries
        //we need to make OddSeries implement ISeries interface
        {
            int current =  1;
            public int GetNextNumber()
             //we have to provide implementation for all methods in the interface(otherwise we get error)
            {
                int i = current;
                current += 2;
                return i;
            }
        }
        class EvenSeries : ISeries
        //we need to make EvenSeries implement ISeries interface
        {
            int current =  0;
            public int GetNextNumber()
             //we have to provide implementation for all methods in the interface(otherwise we get error)
            {
                int i = current;
                current += 2;
                return i;

            }
        }
       class SeriesEngine
        {
            // public void PrintSeries(OddSeries obj)
            // public void PrintSeries(EvenSeries obj)
            public void PrintSeries(ISeries obj)
            //we can pass any object that implement ISeries interface
            //other wise we get Compile time error
            {
                Console.WriteLine(obj.GetNextNumber());
            }
        }

        // struct FibonacciSeries : ISeries
        //struct had problem => search (boxing and unboxing)
        //we need to make FibonacciSeries implement ISeries interface
        class FibonacciSeries : ISeries
        {
            int current;
            int prev;
            //Error: A 'struct' with field initializers
            // we create constructor to initialize fields
            public FibonacciSeries()
            {
                 int current = 1 ;
                int prev = 0;
            }
            public int GetNextNumber()
             //we have to provide implementation for all methods in the interface(otherwise we get error)
            {
                int i = current;//1 -> 1 -> 2 -> 3
                current += prev;//1 -> 2 -> 3 -> 5
                prev = i;//1 -> 1 -> 2 -> 3
                return i;
            }
        }

        class Program
        {
            static void Main(string[] args)
            {                OddSeries odd = new OddSeries();
                Console.WriteLine(odd.GetNextNumber());
                Console.WriteLine(odd.GetNextNumber());
                ///
                SeriesEngine engine = new SeriesEngine();
                engine.PrintSeries(odd);
                ///
                EvenSeries even = new EvenSeries();
                engine.PrintSeries(even);//error because PrintSeries method only accept OddSeries object
                //we need to change PrintSeries method to accept ISeries object(Interface for all series)
                ///
                ISeries obj = new OddSeries();
                engine.PrintSeries(obj);
                obj = new EvenSeries();
                engine.PrintSeries(obj);
                // new series (fib) struct but does implement ISeries interface
                FibonacciSeries fib = new FibonacciSeries();
                engine.PrintSeries(fib);//error because PrintSeries method only accept ISeries object
                // make FibonacciSeries implement ISeries interface
                fib = new FibonacciSeries();
                engine.PrintSeries(fib);

            }
        }
    }
```

> [!tip] Interface access modifiers
>
> - public or internal

```csharp
namespace Demo1Interface
{
   internal interface IMath
    {
        int Add(int x, int y);
        int Sub(int x, int y);

    }
    internal class Xyz : IMath
    {
        public int Add(int x, int y)
        //if we implemented only one method we get error
        {
            return x + y;
        }
        public int Sub(int x, int y)
        {
            return x - y;
        }
        public int myMethod()
        {
            return 0;
        }
    }
}
```

```csharp
//main
namespace Demo1Interface
{
    class Program
    {
        static void Main(string[] args)
        {
            Xyz obj = new Xyz();
            Console.WriteLine(obj.Add(10, 20));
            Console.WriteLine(obj.Sub(10, 20));
            Console.WriteLine(obj.myMethod());
            //if we create object of interface we can only access methods in the interface
            IMath obj2 = new Xyz();
            Console.WriteLine(obj2.Add(10, 20));
            Console.WriteLine(obj2.Sub(10, 20));
            //Console.WriteLine(obj2.myMethod());//error
            //late binding

        }
    }
}
```

> [!tip] we can add default implementation for methods in interface
>
> - so classes that implement this interface can use this implementation or override it

```csharp
namespace Demo1Interface
{
    interface IMath
    {
        int Add(int x, int y);
        int Sub(int x, int y);
        int Mul(int x, int y)
        {
            return x * y;
        }
    }
}
    class Xyz : IMath
    {
        public int Add(int x, int y)
        {
            return x + y;
        }
        public int Sub(int x, int y)
        {
            return x - y;
        }
        public int myMethod()
        {
            return 0;
        }
        //we can override Mul method if we want but we don't have to
    }

```

> [!tip] we can add properties to interface
>
> - classes that implement this interface must provide implementation for this properties
>
> ```csharp
>     interface ISeries
>     {
>         int Current { get; set; }
>         int GetNextNumber();
>     }
> ```
>
> > [!danger] we can't add fields to interface
> >
> > <br>
> >
> > ```csharp
> >     interface ISeries
> >     {
> >         int Current { get; set; }
> >         int GetNextNumber();
> >         int x;//error
> >     }
> > ```
> >
> > - because fields are not allowed in interface
>
> > [!done] we can add static fields to interface
> >
> > ```csharp
> >     interface ISeries
> >     {
> >         int Current { get; set; }
> >         int GetNextNumber();
> >         static int x;//ok
> >     }
> > ```

```csharp
//interface ISeries
namespace Demo1Interface
{
    interface ISeries
    {
       public int Current { get; set; } //property
        int GetNextNumber();
    }
    class OddSeries : ISeries
    {
        public int Current { get=>current}
        public int GetNextNumber()
        {
            int i = Current;
            Current += 2;
            return i;
        }
    }
}
```

> [!tip] interface can inherit from another interface
> <br>
>
> ```csharp
> interface IShape
> {
>    void Draw();
> }
> interface ICircle : IShape
> {
>    void DrawCircle();
> }
> class Circle : ICircle
> {
>    public void Draw()
>    {
>        Console.WriteLine("Draw a circle");
>    }
>    public void DrawCircle()
>    {
>        Console.WriteLine("Draw a circle");
>    }
> }
> ```

> [!tip] class can implement multiple interfaces
> <br>
>
> ```csharp
> interface IShape
> {
>    void Draw();
> }
> interface ICircle
> {
>    void DrawCircle();
> }
> class Circle : IShape,ICircle
> {
>    public void Draw()
>    {
>        Console.WriteLine("Draw a circle");
>    }
>    public void DrawCircle()
>    {
>        Console.WriteLine("Draw a circle");
>    }
> }
> ```

> [!tip] we can make explicit implementation for interface methods to avoid name collision
> <br>
>
> ```csharp
> interface IShape
> {
>    void Draw();
> }
> interface ICircle
> {
>    void Draw();
> }
> class Circle : IShape,ICircle
> {
>    void IShape.Draw()
>    {
>        Console.WriteLine("Draw a circle");
>    }
>    void ICircle.Draw()
>    {
>        Console.WriteLine("Draw a circle");
>    }
> }
> ```

> [!example] on implementing multiple interfaces with same method name
>
> - TRY making different objects of different interfaces and call the method

---

### Break

---

```csharp
//main
static void Main(string[] args)
{
    int[] arr = { 45, 12, 78, 34, 56, 90 };
    Array.Sort(arr);
    //sort array in ascending order in place
    foreach (int i in arr)
    {
        Console.WriteLine(i);//12 34 45 56 78 90
    }
    int[] names = { "abc", "xyz", "pqr" };
    Array.Sort(names);
    // same implementation for sorting array of strings
    //sort it in alphabetical order
    foreach (string i in names)
    {
        Console.WriteLine(i);//abc pqr xyz
    }
}

```

```csharp
//complex class
internal struct Complex
{
    public int Real { get; set; }
    public int Imaginary { get; set; }
}
```

```csharp
//main
static void Main(string[] args)
{
    Complex[] arr = { new Complex (){Real=10,Imaginary=52},
                      new Complex (){Real=34,Imaginary=64},
                      new Complex (){Real=5,Imaginary=6},
                      new Complex (){Real=3,Imaginary=81},
                      new Complex (){Real=9,Imaginary=10},
                      new Complex (){Real=11,Imaginary=12},
                    };
    Array.Sort(arr);
    //sort does not know how to compare Complex objects
    //Array.Sort method uses IComparable interface to compare objects
    //every object that we want to sort must implement IComparable interface
    //error because Complex class does not implement IComparable interface
    foreach (Complex i in arr)
    {
        Console.WriteLine(i.Real + " " + i.Imaginary);
    }
    IComparable obj = new Complex();//error because Complex class does not implement IComparable interface
    ///even with casting
    IComparable obj = (IComparable)new Complex();//Error

}
```

> [!tip] we can make Complex class implement IComparable interface
>
> - we need to provide implementation for CompareTo method
> - int , string...etc implement IComparable interface
>
> ```csharp
> internal struct Complex : IComparable
> {
>     public int Real { get; set; }
>     public int Imaginary { get; set; }
>     public int CompareTo(object obj)
>     {
> //if we return 0 or 1 => array will be the same so we need to compare based on Real if Real is the same we compare based on Imaginary
>        //Complex c = obj as Complex;//can't use (as)
>         Complex c = (Complex)obj;
>         if (this.Real > c.Real)
>             return 1;//this is greater than c => this comes after c
>         else if (this.Real < c.Real)
>             return -1; //this is less than c => this comes before c
>         else
>             return 0;//this is equal to c => this and c are the same
>   }//this sort based on Real
> }
> ```

> [!tip] we can use CompareTo of int to compare based on Real and Imaginary
> <br>
>
> ```csharp
> internal struct Complex : IComparable
> {
>    public int Real { get; set; }
>    public int Imaginary { get; set; }
>    public int CompareTo(object obj)
>    {
>     return  this.Real.CompareTo(c.Real);//compare based on Real
>   }
> //
>    public int CompareTo(object obj)
>    {
>     return  this.Real.CompareTo(c.Real);//compare based on Real
>   }
> }
> ```

```csharp
//main
static void Main(string[] args)
{
    Complex c1 = new Complex() { Real = 10, Imaginary = 52 };
    Complex c2 = new Complex() { Real = 34, Imaginary = 64 };
    c1 = c2;//reference equality
    // we need to create new object and with same state and assign it to c1
    // we use clone
```

> [!tip] Clone
>
> - we need to implement ICloneable interface
> - we need to provide implementation for Clone method
> - Clone method return object so we need to cast it to Complex
>
> ```csharp
> internal struct Complex : IComparable, ICloneable
> {
>    public int Real { get; set; }
>    public int Imaginary { get; set; }
>    public int CompareTo (object obj)
>    {
>        Complex c = (Complex)obj;
>        if (this.Real > c.Real)
>            return 1;
>        else if (this.Real < c.Real)
>            return -1;
>        else
>            return 0;
>    }
>    public object Clone()
>    {
>        Complex c = new Complex();
>        c.Real = this.Real;
>        c.Imaginary = this.Imaginary;
>        return c;
>    }
> }
> ```

```csharp
//main
static void Main(string[] args)
{
    Complex c1 = new Complex() { Real = 10, Imaginary = 52 };
    Complex c2 = new Complex() { Real = 34, Imaginary = 64 };
    c1 = c2;//reference equality
    // we need to create new object and with same state and assign it to c1
    // we use clone
    c1 = (Complex)c2.Clone();
    //now c1 and c2 are different objects with same state
    //we can use CompareTo method to compare c1 and c2

}
```

---

## Generics

> [!tip] Generics
>
> - like templates in c++

```csharp
//satck class
// we can only push int to stack
//we need to create stack class for every data type
namespace Demo1Generics
{
    class IntStack
    {
        int[] arr;
        int tos;
        public IntStack(int size)
        {
            arr = new int[size];
            tos = 0;
        }
        public void Push(int x)
        {
            arr[tos] = x;
            tos++;
        }
        public int Pop()
        {
            tos--;
            return arr[tos];
        }
    }
}
//float stack
namespace Demo1Generics
{
    class FloatStack
    {
        float[] arr;
        int tos;
        public FloatStack(int size)
        {
            arr = new float[size];
            tos = 0;
        }
           public bool IsFull()
        {
            return tos == size - 1;
        }
        public bool IsEmpty()
        {
            return tos == -1;
        }
        public void Push(float x)
        {
               {

                throw new StackFullException(size);
            }
            tos++;
            arr[tos] = x;
        }
        public float Pop()
        {
            if (IsEmpty())
            {

                throw new StackEmptyException();

            }
            int x = arr[tos];
            tos--;
            return x;
        }
    }
}
```

```csharp
//object stack
namespace Demo1Generics
{
    class ObjectStack
    {
        object[] arr;
        int tos;
        public ObjectStack(int size)
        {
            arr = new object[size];
            tos = 0;
        }
     public bool IsFull()
        {
            return tos == size - 1;
        }
        public bool IsEmpty()
        {
            return tos == -1;
        }
        public void Push(object x)
        {
            if (IsFull())
            {

                throw new StackFullException(size);
            }
            tos++;
            arr[tos] = x;
        }
        public object Pop()
        {
            if (IsEmpty())
            {

                throw new StackEmptyException();

            }
            object x = arr[tos];
            tos--;
            return x;
        }
    }
}
```

> [!bug] Object
>
> - we can push any type of object to stack
> - NO type safety

```csharp
//main
static void Main(string[] args)
{
    IntStack s1 = new IntStack(10);
    s1.Push(10);
    s1.Push(20);
    s1.Push(30);
    Console.WriteLine(s1.Pop());
    Console.WriteLine(s1.Pop());
    Console.WriteLine(s1.Pop());
    //s1.Push(10.5);//error
    //s1.Push("abc");//error
    //s1.Push(true);//error
    FloatStack s2 = new FloatStack(10);
    s2.Push(10.5f);
    s2.Push(20.5f);
    s2.Push(30.5f);
    Console.WriteLine(s2.Pop());
    Console.WriteLine(s2.Pop());
    Console.WriteLine(s2.Pop());
    //s2.Push(10);//error
    //s2.Push("abc");//error
    //s2.Push(true);//error
    ObjectStack s3 = new ObjectStack(10);
    s3.Push(10);
    s3.Push(10.5f);
    s3.Push("abc");
    s3.Push(true);
    Console.WriteLine(s3.Pop());
    Console.WriteLine(s3.Pop());
    Console.WriteLine(s3.Pop());
    Console.WriteLine(s3.Pop());
        s3.Push("abc");
    int c = (int)s3.Pop();//Error: InvalidCastException
    //no type safety
}
```

> [!tip] Generics

```csharp
//Generic stack class
namespace Demo1Generics
{
    class Stack<T>
    //T is a type parameter (can be any type)
    {
        T[] arr;//data type
        int tos;
        public Stack(int size)
        {
            arr = new T[size];//create array of type
            tos = 0;
        }
        public bool IsFull()
        {
            return tos == size - 1;
        }
        public bool IsEmpty()
        {
            return tos == -1;
        }
        public void Push(T x)
        {
            if (IsFull())
            {

                throw new StackFullException(size);
            }
            tos++;
            arr[tos] = x;
        }
        public T Pop()
        {
            if (IsEmpty())
            {

                throw new StackEmptyException();

            }
            T x = arr[tos];
            tos--;
            return x;
        }
    }
}
```

```csharp
//main
static void Main(string[] args)
{
    Stack<int> s1 = new Stack<int>(10);//T is int
    s1.Push(10);
    s1.Push(20);
    s1.Push(30);
    Console.WriteLine(s1.Pop());//30
    Console.WriteLine(s1.Pop());//20
    Console.WriteLine(s1.Pop());//10
    //s1.Push(10.5);//error
    //s1.Push("abc");//error
    //s1.Push(true);//error
    Stack<string> s2 = new Stack<string>(10);//T is string
    s2.Push("abc");
    s2.Push("xyz");
    s2.Push("pqr");
    Console.WriteLine(s2.Pop());//pqr
    Console.WriteLine(s2.Pop());//xyz
    Console.WriteLine(s2.Pop());//abc
    //s2.Push(10);//error
    //s2.Push(10.5);//error
    //s2.Push(true);//error


}
```

> [!done] JIT Compiler
> JIT compiler will create 2 versions of stacks` Stack<int> and Stack<string>`
>
> - just create needed versions
> - IL won't have this versions of Stack class
> - instead of creating many classes we create one generic class
> - JIT compiler will create these classes for us saves time and doesn't affect performance

> [!TIP] SAME with swap function
>
> - we can create swap function for any type (Generic)
> - let JIT compiler create swap function for each type we use

```csharp
 static void Swap<T>(ref T x, ref T y)
 {
    T temp = x;
    x = y;
   y = temp;
 }
```

```csharp
//main
static void Main(string[] args)
{
    int x = 10, y = 20;
    Swap<int>(ref x, ref y);
    Console.WriteLine(x + " " + y);//20 10
    string s1 = "abc", s2 = "xyz";
    Swap<string>(ref s1, ref s2);
    Console.WriteLine(s1 + " " + s2);//xyz abc
    float f1 = 10.5f, f2 = 20.5f;
    Swap<float>(ref f1, ref f2);
    Console.WriteLine(f1 + " " + f2);//20.5 10.5

    //we can use it without <int> <string> <float> because JIT compiler will know from arguments type
    //have to be same type
    Swap(ref x, ref y);
    Swap(ref s1, ref s2);
    Swap(ref f1, ref f2);


    //we can swap created Datatype
    //complex
    Complex c1 = new Complex() { Real = 10, Imaginary = 52 };
    Complex c2 = new Complex() { Real = 34, Imaginary = 64 };
    Swap<Complex>(ref c1, ref c2);
    Console.WriteLine(c1.Real + " " + c1.Imaginary);//34 64
    Console.WriteLine(c2.Real + " " + c2.Imaginary);//10 52

    Swap(ref c1, ref c2);
}
```

> [!danger] can't use stack without generic type
>
> ```csharp
> Stack s1 = new Stack(10);//error
>
> ```

> [!tip] create stack of Complex

```csharp
Stack<Complex> s1 = new Stack<Complex>(10);
Copmlex c1 = new Complex() { Real = 10, Imaginary = 52 };
Complex c2 = new Complex() { Real = 34, Imaginary = 64 };
s1.Push(c1);
s1.Push(c2);
Console.WriteLine(s1.Pop());//34 64
```

---

###### Generic Swap

```csharp
// generic swap function
public static void Swap<T>(ref T x, ref T y)
{
    //some data types don't have all operators
    //we can only use operators that are defined for all data types

    T n;     //1.we define of any data type
    n = x;   //2.equal operator

    n = x + y; //not all data types have + operator
    if(n == x) //struct don't have == operator
    n =null; //not all data types have null

    //3.GetHashCode() , 4.Equals() , 5.ToString() , 6.GetType() are defined for all data types
    //7.default()
    n = default(T);

    //default value for reference type is null
    // 8. we can reference any data type to object (boxing)
    object obj = x;

    // 9. we can reference object to any data type (unboxing)
    z = (T)obj;

    //////////////////////////////////////////////////
    T temp = x;
    x = y;
    y = temp;
}
```

---

> [!bug] we can add constraints to generic type
>
> - we can only use data types that implement IComparable interface -> secondary constraint(can be more than one condition)

```csharp
public static void Swap<T>(ref T x, ref T y) where T : IComparable
```

- ##### we can add multiple constraints(secondary constraint only)

```csharp
public static void Swap<T>(ref T x, ref T y) where T : IComparable, ICloneable
```

---

> [!warning] we can add class constraint -> primary type (Only one condition)
>
> - now we can assign null to x and y
> - we can use ==operator

```csharp
public static void Swap<T>(ref T x, ref T y) where T : class, IComparable, ICloneable
```

> [!bug] we can add struct constraint
>
> - now we can't assign null to x and y
> - we can't use ==operator

```csharp
public static void Swap<T>(ref T x, ref T y) where T : struct, IComparable, ICloneable
```

> [!tip] default
>
> - default is constraint by class, struct

````csharp
 public static void Swap<T>(ref T x, ref T y) where T : struct,class,IComparable, ICloneable{
 } ```


> [!done]  is the same as
```csharp
public static void Swap<T>(ref T x, ref T y) where T : IComparable, ICloneable
````

> [!warning] we can add class like Complex constraint
>
> - this will make it only for Complex class and its derived classes

```csharp
public static void Swap<T>(ref T x, ref T y) where T : Complex, IComparable, ICloneable
```

> [!danger] NOT the same as changing the ref of parameter to Complex
>
> - this will make it only for Complex class

```csharp
public static void Swap<Complex>(ref Complex x, ref Complex y) where Complex : IComparable, ICloneable
```

> [!tip] we can add new() constraint(third Constraint)
>
> - have to have parameterless constructor
> - we can create object of T using new operator `T obj = new T();`

```csharp
public static void Swap<T>(ref T x, ref T y) where T :class, new(), IComparable, ICloneable
```

---

### Break BS

---

###### Array List

```cs
//array list
//not fixed size like array
//we can add any number of elements
//we can add any type of elements (no type safety)

ArrayList arr = new ArrayList();
arr.Add(10);
arr.Add(100);
arr.Add(10);
arr.Add(1000);
arr.Add(10000);
foreach (int i in arr)
{
    Console.WriteLine(i);
}
arr.Remove(10); //remove first occurence of 10

arr.Add("abc");
arr.Add(true);
arr.Add(10.5f);
foreach (int i in arr)
//will throw exception because we can't cast string to int
{
    Console.WriteLine(i);
}
//not type safe
//reduced performance due to boxing and unboxing
//to get length
Console.WriteLine(arr.Count);
```

###### Generic List

```cs
//generic list
//we can add any number of elements

List<int> arr = new List<int>();
arr.Add(10);
arr.Add(20);
arr.Add(10);
// arr.Add("abc");//error: can't convert string to int
foreach (var i in arr)
{
    Console.WriteLine(i);
}

//to get length
Console.WriteLine(arr.Count);

//to remove element
arr.Remove(10);//remove first occurence of 10


List<string> names = new List<string>();
names.Add("abc");
names.Add("xyz");
names.Add("abc");
names.Add("pqr");
Console.WriteLine(names.Count);//3
foreach (string i in names)
{
    Console.WriteLine(i);//abc xyz abc pqr
}
names.Remove("abc");//remove first occurence of "abc"
foreach (string i in names)
{
    Console.WriteLine(i);//xyz abc pqr

}
//we can use index to access or update elements but we can't use index to add elements
names[0] = "mno";//ok => replace "xyz" with "mno"
Console.WriteLine(names[0]);//ok => mno
names[4] = "mno";//error => index out of range


//sort
names.Sort();//generic type has to implement IComparable interface
//string implements IComparable interface


```

> [!tip] what happen when creating and adding in List
>
> - `List<int> arr = new List<int>();` this create array of size 4 each can hold int
> - `arr.Add(10);` this add 10 to first element in array
> - `arr.Add(20);` this add 20 to second element in array
> - `arr.Add(10);` this add 10 to third element in array
> - `arr.Add(20);` this add 20 to fourth element in array
> - once we add fifth element it will create new array of size 8 and copy all elements from old array to new array and add fifth element to new array
> - this will happen every time we add element and array is ==full==

```cs
//capacity
//we can get capacity of list
//capacity is the size of array that list is using
//capacity is always >= count
//on first add it will create array of size 4

List<int> arr = new List<int>();
Console.WriteLine(arr.Capacity);//4
Console.WriteLine(arr.Count);//0
arr.Add(10);
Console.WriteLine(arr.Count);//1
Console.WriteLine(arr.Capacity);//4
arr.Add(20);
Console.WriteLine(arr.Count);//2
Console.WriteLine(arr.Capacity);//4
arr.Add(30);
Console.WriteLine(arr.Count);//3
Console.WriteLine(arr.Capacity);//4
arr.Add(40);
Console.WriteLine(arr.Count);//4
Console.WriteLine(arr.Capacity);//4
arr.Add(50);
Console.WriteLine(arr.Count);//5
Console.WriteLine(arr.Capacity);//8

//we can set initial capacity
List<int> arr = new List<int>(10);//initial capacity is 10
Console.WriteLine(arr.Capacity);//10
//when adding 11th element it will create new array of size double the old array size (20)
Console.WriteLine(arr.Count);//11
Console.WriteLine(arr.Capacity);//20

//TrimExcess
//we can use TrimExcess to remove extra space
names.TrimExcess();//remove extra space
Console.WriteLine(names.Count);//3
Console.WriteLine(names.Capacity);//3
```
---

###### Stack

```cs
//stack (not generic)
Stack s1 = new Stack();
s1.Push(10);
//not type safe
// we use the generic version

Stack<int> s1 = new Stack<int>();
s1.Push(10);
s1.Push(20);
s1.Push(30);
Console.WriteLine(s1.Pop());//30
Console.WriteLine(s1.Pop());//20
Console.WriteLine(s1.Pop());//10
Console.WriteLine(s1.Pop());//error:invaild operation exception

//to clear stack
s1.Clear();//make tos = -1 or 0 (depends on implementation)
```

>[!bug] LAB: build stack using list


###### Queue

```cs
//same as stack
```

>[!warning] SEARCH: HashSet vs SortedSet vs SortedList 


###### Dictionary

>[!tip] Dictionary
> 
> - key value pair
> - key has to be unique
> - key can be any type even Dictionary
```cs
//main
static void Main(string[] args)
{
    Dictionary<int, string> names = new Dictionary<int, string>();
    // key => int , value => string
    //all keys have to be unique
    
    names.Add(1, "abc");
    names.Add(2, "xyz");
    names.Add(3, "pqr");
    names.Add(4, "mno");

    //to print all data
    //Dictionary has indexer 
    //Dictionary[key] => value
    Console.WriteLine(names[1]);//abc

    // foreach (var i in names)
    //i not int or string it is KeyValuePair<int,string>
    foreach (KeyValuePair<int, string> i in names)
    {
        Console.WriteLine(i.Key + " " + i.Value);
        //1 abc 
        //2 xyz
        //3 pqr
        //4 mno
    }
    //Count
    Console.WriteLine(names.Count);//4

    names.Add(1, "abc");//error: key already exists System.ArgumentException: 'An item with the same key has already been added. Key: 1'

    //to check if key exists (complex data type) it uses GetHashCode() and Equals()
}
```