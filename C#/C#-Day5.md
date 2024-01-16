### Exception Handling

```cs
namespace Demo1
{
   internal class Program
    {
        static void Fun2()
        {
            int x;
            int y;
            int z = 1;
            x = int.Parse(Console.ReadLine());//if we input a string, it will throw an exception and the program will stop
            // (System.FormatException: Input string was not in a correct format.)
            y = int.Parse(Console.ReadLine());//if we input 0, it will throw an exception and the program will stop
            // (System.DivideByZeroException: Attempted to divide by zero.)
            z = x / y;
        }
        static void Fun1()
        {
            Fun2();
        }
        static void Main(string[] args)
        {

        }
    }
}
```

> [!error] System.FormatException: Input string was not in a correct format.
> at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
> at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
>
> > [!example] Stack Trace
> > at System.Convert.ToInt32(String value)
> > at Demo1.Program.Fun2() in
> > at Demo1.Program.Fun1() in
>
> > [!warning] we need to hide the exception from the user as it is security issue

> [!done] `Object Browser` in VS we can search for any class in the .NET Framework class library
> use it to see the exception and the hierarchy of the exception
>
> > [!warning] System.Exception inherits from System.Object
> > properties of the exception

> [!example] Any exception that is thrown is an object of type System.Exception
>
> ---
>
> > [!done] System.Exception is the base class for all exceptions in the .NET Framework class library
>
> ---
>
> > [!tip] to create a custom exception, we need to inherit from System.Exception

> [!warning] int.Parse() has 3 types of exceptions
>
> - System.FormatException
> - System.ArgumentNullException
> - System.OverflowException

> [!done] we need to use try-catch block to handle exceptions

```cs
//fun2
int x;
int y;
int z = 1;
try
{
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    z = x / y;
Console.WriteLine(z);
}
catch{
    Console.WriteLine("Error");
}
Console.WriteLine("End of program");


```

> [!done] in case of no exception, the code will run normally
>
> - output:
> - 2
> - 2
> - 1
> - End of program

> [!error] in case of exception the catch block will run
> and the program will continue
>
> - output:
> - 2
> - 0
> - Error
> - End of program

> [!tip] we need to provide the user with a message that is more specific to the exception
> we can use the exception type to specify the message

```cs
//fun2
int x;
int y;
int z = 1;
try
{
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    z = x / y;
Console.WriteLine(z);
}
catch(FomatException e){
    Console.WriteLine("Error: " + e.Message);
}
catch(DivideByZeroException e){
    Console.WriteLine("Error: " + e.Message);
}
Console.WriteLine("End of program");
```

```cs
//fun2
//if we have array and we want to access an element that is out of range
int x;
int y;
int z = 1;
int[] arr = new int[5];
// int t = arr[5];//this will throw an exception (System.IndexOutOfRangeException: Index was outside the bounds of the array.)
//we don't have this type of exception in the try-catch block
//we need to use the base class System.Exception
//same if we have a null reference
arr = null;
// int t = arr[5];//this will throw an exception (System.NullReferenceException: Object reference not set to an instance of an object.)
//this will go to Exception type in the catch block which is the base class of all exceptions
// we can't put the Exception type first in the catch block as it will catch all exceptions
// we need to put it last
// and the order of the catch block is important
// we need to put the most specific exception first
try
{
    arr[5] = 10;
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    z = x / y;
Console.WriteLine(z);

}catch(FomatException e){
    Console.WriteLine("Enter a number");
}catch(DivideByZeroException e){
    Console.WriteLine("Enter a number other than 0");
}catch(Exception e){
    Console.WriteLine("Error occured");
}

```

> [!done] finally block
>
> - it will run whether there is an exception or not
> - used to close files, database connections, etc.

```cs
//fun2
int x;
int y;
int z = 1;
int[] arr = new int[5];
try
{
    arr[5] = 10;
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    z = x / y;
Console.WriteLine(z);
}
catch(FomatException e){
    Console.WriteLine("Enter a number");
}
catch(DivideByZeroException e){
    Console.WriteLine("Enter a number other than 0");
}
catch(Exception e){//catches all exceptions that are not handled
    Console.WriteLine("Error occured");
}
finally{
    Console.WriteLine("Finally block");
}
Console.WriteLine("End of program");
```

> [!example] input: string
>
> - output:
> - Enter a number
> - Finally block
> - End of program

> [!example] input: 0
>
> - output:
> - Enter a number other than 0
> - Finally block
> - End of program

> [!tip] there is difference between the code in finally block and the code after the try-catch block => Code After try-catch => will run only when no Error occurs.
>
> - if we had a statement that needs to be executed if we have an exception or not, we need to put it in the finally block ()

> [!example] we can use try-finally without catch
>
> > [!error] if no we have try we have to have catch or finally

> [!tip] we can use Exception properties:
>
> - Message
> - StackTrace
>   to log the exception in a file or database to help us debug the program

---

> [!done] inner try-catch block
>
> - we can have a try-catch block inside another try-catch block

> [!tip] Exception propagation:
>
> - if a method throws an exception, we can handle it in the method itself or in the calling method
> - if we don't handle the exception in fun2, it will be propagated to fun1
> - if we don't handle the exception in fun1, it will be propagated to Main

````cs
//fun2
//no exception handling
int x;
int y;
int z = 1;
try
{
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    z = x / y;
Console.WriteLine(z);
}
finally{
    Console.WriteLine("Finally block");//will be executed
}
Console.WriteLine("End of fun2");//won't be executed


```cs
//fun1
//format exception handling
static void Fun1()
{

    try
    {
        Fun2();
    }
    catch(FomatException e){
        Console.WriteLine("Enter a number Fun1");
    }
    Console.WriteLine("End of fun1");
}
````

```cs
//main
//divide by zero exception handling
static void Main(string[] args)
{
    try
    {
        Fun1();
    }
    catch(DivideByZeroException e){
        Console.WriteLine("Enter a number other than 0 MAIN");
    }
    Console.WriteLine("End of program MAIN");
}
```

> [!done] no exception
>
> - output:
> - 2
> - 2
> - 1
> - Finally block
> - End of fun2
> - End of fun1
> - End of program MAIN

> [!bug] input: string => FormatException
>
> - output:
> - Enter a number Fun1
> - End of fun1
> - End of program MAIN

> [!bug] input: 0 => DivideByZeroException
>
> - output:
> - Enter a number other than 0 MAIN
> - End of program MAIN

> [!error] in case of inhandled exception like null reference exception :
>
> - output:
> - Finally block
> - Unhandled exception. System.NullReferenceException: Object reference not set to an instance of an object.

> [!example] we can rethrow the exception to the calling method

```cs
//fun2
// catch(FomatException e)

int x;
int y;
int z = 1;
try
{
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    z = x / y;
Console.WriteLine(z);
}
catch(FomatException e){
    Console.WriteLine("Enter a number");
    throw;//rethrow the exception
    //to Fun1
    // this appears in the stack trace at the original line of the exception
    throw e;//rethrow the exception this appears in the stack trace at this line not at the line of the original exception so it is not recommended use throw;
    throw new Exception();//that throws a new exception of type Exception to Fun1
    // this hides the original exception
}
finally{
    Console.WriteLine("Finally block");
}
Console.WriteLine("End of program");
```

> [!tip] throw; vs throw e; vs throw new Exception();

> [!warning] if we have a method that takes a string to parse it to int
>
> - handle the exception in the calling method not in the method itself as it is its responsibility to enter right format

---

# Break

---

## int.TryParse()

> [!tip] we can use int.TryParse() to return a boolean value if the parsing is successful or not
> `int.TryParse(string, out int);`
> Protects against exceptions

```cs
//fun2
//protective programming
int x;
int y;
int z = 1;
int[] arr = new int[5];
int number;
var isParsed = int.TryParse(Console.ReadLine(), out int number);//if the parsing is successful, it will return true and the number will be assigned to the variable number

do{
    Console.WriteLine("Enter a number");
    b = int.TryParse(Console.ReadLine(), out int number);
}while(isParsed == false);

//we can check before dividing if the number is 0 or not
// and we can check if the array is null or not

if(number != 0){
    z = x / y;
    Console.WriteLine(z);
}

```

---

### Complex Class

> [!tip] operator overloading
>
> - indexers `c[0]`=>return the real part , `c[1]`=>return the imaginary part

> [!example] Indexer
>
> - we can use indexers to access the elements of a class like arrays
> - we can use indexers to send more than one value(paramter) to a property of class. We can't do that with properties

```cs
class Complex
{
    //previous Complex class code
    public int this[int index]{
        get{//it has to return a value in all cases or throw an exception
            if(index == 0){
                return real;
            }else if(index == 1){
                return imaginary;
            }
                throw new IndexOutOfRangeException("Complex only has 2 parts");

        }

    //if we want a send more than one value we use indexers
        set{
            if(index == 0){
                real = value;
            }else if(index == 1){
                imaginary = value;
            }else{
                throw new IndexOutOfRangeException("Complex only has 2 parts");
            }
        }
    }

    public int this[string part]{
        get{
            if(part == "real"){
                return real;
            }else if(part == "imaginary"){
                return imaginary;
            }
                throw new IndexOutOfRangeException("Complex only has 2 parts");
        }
        set{
            if(part == "real"){
                real = value;
            }else if(part == "imaginary"){
                imaginary = value;
            }else{
                throw new IndexOutOfRangeException("Complex only has 2 parts");
            }
        }
    }
}
```

```cs
//main
Complex c = new Complex(1,2);
Console.WriteLine(c[0]);//1
Console.WriteLine(c[1]);//2
c[0] = 10;
c[1] = 20;
Console.WriteLine(c[0]);//10
Console.WriteLine(c[1]);//20
Console.WriteLine(c[2]);//IndexOutOfRangeException: Complex only has 2 parts
//c["real"], c["imaginary"]

Console.WriteLine(c["real"]);//10
Console.WriteLine(c["imaginary"]);//20
```

---

###### NotImplementedException

> [!tip] we can use NotImplementedException to throw an exception if we have a method that is not implemented yet

```cs
static int myMethod(){
    throw new NotImplementedException();
    //or
    throw new NullReferenceException();
}
```

> [!exapmle] Indexers Real World Example
>
> - PhoneBook class => 2 arrays of strings (names, phone numbers)
>
> ```cs
> PhoneBook p = new PhoneBook();
> p["Ahmed"] = "01012345678";//this will get the index of Ahmed and assign the phone number to it in the phone numbers array
> p["Ahmed"];//this will get the index of Ahmed and return the phone number from the phone numbers array
> p["Ali","01012345678"] = 3;//this will add the name and phone number to the arrays in the index 3
> ```
>
> > [!done] this aleady exists in some classes like String
> >
> > ```cs
> > string s = "Hello";
> > s[0];//H
> > s[1];//e
> > ```
>
> > [!danger] Task : phone book

---

###### static constructor

> [!tip] static constructor
>
> - used to initialize static members
> - it is called only once when the class is used for the first time
>
>   > [!done] can have no parameters constructor and no parameters static constructor at the same time
>
>   > [!error] only one static constructor is allowed

> [!bug] can't access static members from object
>
> ```cs
> Complex c = new Complex(1,2);
> c.counter;//error
> ```
>
> > [!done] this was allowed in C++ but not in C#

```cs
class Complex{

    //previous Complex class code
    //  static int counter;
    public static int counter;//can be accessed anywhere in the program
    //we can make static getter
    public static int getCounter(){
        return counter;
    }
    //so we make it private and we can access it through a property
    public static int Counter{
        //now we can only access it through the property (read only)
        get{
            return counter;
        }

    }

    //can't have access modifiers
//   XXXXXXXX public static Complex()XXXXXX
    static Complex()
    {//can't access non static members
        counter = 0;
        Console.WriteLine("Static constructor");
    }
}
```

```cs
//main
Complex c = new Complex(1,2);
Complex c2 = new Complex(1,2);
Complex c3 = new Complex(1,2);
c.counter;//error: can't access static members from object
Console.WriteLine(Complex.getCounter());//0
Console.WriteLine(Complex.Counter);//0
```

---

#### Static Class

> [!tip] static class
>
> - contains constants fields and static methods
>   > [!bug] once class is declared static:
>   >
>   > - all fields must be constant
>   > - all methods must be static

```cs
//Math class
class Math{
    //   double PI = 3.14;// Error: must be constant

     public const double PI = 3.14;
    public static double Add(double x, double y){
        return x + y;
    }
    public static double Multiply(double x, double y){
        return x * y;
    }

}
```

###### using static class

> [!tip] can't create an object of a static class

```cs
//main
static void Main(string[] args)
{
    // Math m = new Math();//error: can't create an object of a static class
    Console.WriteLine(Math.PI);//3.14
    Console.WriteLine(Math.Add(1,2));//3
    Console.WriteLine(Math.Multiply(1,2));//2
}
```

> [!example] we already have Math static class `MathF` in System namespace
>
> ```cs
> Console.WriteLine(MathF.PI);//3.14
> ```

> [!example] we have static constructor in static class
>
> ```cs
> static class Math{
> public const double PI = 3.14;//we can't reassign it as it is constant even in the static constructor
> public double PI2;
>    static Math(){
>        PI = 3.14;//not constant so we can reassign it
>        Console.WriteLine("Static constructor");
>     }
> }
> ```

---

## Design Patterns

> [!faq] what is design pattern?
>
> - it is a solution to a common problem

> [!bug] Problem: we need to create only one object of a class
>
> > [!done] Solution: Singleton Design Pattern

> [!example]
> NIC class : Network Interface Card

```cs
class NIC{
    string ip;

//   public NIC(string ip){
//         this.ip = ip;
//     }

// 1. make the constructor private
    private NIC(string ip){
        this.ip = ip;
    }
    // 2. create a static method that creates the object
    // public NIC CreateNICObject(string ip){
    //    NIC nic = new NIC(ip);
    //           return nic;
    // }

    // 3. make sure that we can't create more than one object
    static NIC nic = null;
    public static NIC CreateNICObject(string ip){
        if(nic == null){
            nic = new NIC(ip);
        }
        return nic;
    }
    public  override  string ToString(){
        return $"IP: {ip}";
    }
}


```

```cs
//main
NIC nic = new NIC("192.168.1.1");
Console.WriteLine(nic.ToString());//IP:
NIC nic2 = new NIC("192.168.1.1");
Console.WriteLine(nic2.ToString());//IP:
//we need to create only one object of NIC class
// we make the constructor private
NIC nic3 = NIC.CreateNICObject("192.186.1.1");
NIC nic4 = NIC.CreateNICObject("192.186.1.2");
//we just moved the creation of the object.
//we can still create more than one object

//we need to make sure that we can't create more than one object

```

---

#### class library

> [!tip] we can create a class library to use it in other projects
>
> 1. create a new project
> 2. select class library

> [!bug] depnedency we need in every project is Microsoft.NETCore.App

> [!warning] doesn't have a main method
> unlike all other kinds of projects(Console, Windows Forms, WPF, ASP.NET Core, etc.)

```cs
//class library
namespace utility
{
    //class point
    // internal class Point
    //class has to be public to be used in other projects
    public class Point
    {
        public int x;
        public int y;
        public Point(int x, int y)
        {
            this.x = x;
            this.y = y;
        }
        public override string ToString()
        {
            return $"({x},{y})";
        }
    }
}
```

> [!example] internal vs public
> internal: can be used only in the same project
> public: can be used in other projects(need to add reference)

> [!done] we can use it in other projects
>
> 1. add reference (project reference) - check the box on utility project
> 2. add `using utility`; in the other project
>    - or we can use it without adding `using utility;` by using the full name of the class `utility.Point p = new utility.Point(1,2);`

```cs
//main
using utility;
namespace Demo1
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Point p = new Point(1,2);
            Console.WriteLine(p.ToString());//(1,2)
        }
    }
}
```

> [!example] protected access modifier for properties
> protected: can be accessed in the same class and the derived class
>
> > [!bug] can't be accessed in the other project or other classes that are not derived from the class

> [!example] internal
> internal: can be accessed in the same project (assembly)
>
> > [!bug] can't be accessed in the other project even if it is derived from the class

> [!example] internal protected
>-  public inside the same project
> -  to be accessed in other projects, it has to be derived from the class

> [!example] private protected
>-  can be accesed in same project if it is derived from the class
> - can't be accessed in other projects even if it is derived from the class

###### Available Access Modifiers for members

> [!tip] public vs internal vs protected vs private vs private protected vs internal protected
>
> - public: can be accessed anywhere.
> - internal: can be accessed in the same project (assembly).
> - Protected: can be accessed in class or its children only.
> - private: can be accessed inside class only.
> - internal Protected: Allows access to the declared member within the same assembly and by derived classes in any assembly.
> - private Protected: It allows access to the declared member within the same assembly and only by derived classes.

###### Available Access Modifiers for classes

> [!tip] public vs internal

> [!warning] struct in namespace can have only public, internal or private access modifier
>
> > [!bug] inside a class, struct can have any access modifier as it is a member of the class()

---

> [!done] object browser in VS
> or https://source.dot.net/
> to see the class hierarchy in the .NET Framework class library

---

## #lab-5

- 12/13/14/15 in lab-3 document
- 7/8/9 in lab-4 document (tryParse)


