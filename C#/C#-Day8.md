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
            myDelegate.Invoke();//print2 print
            //call will be in order of adding functions to delegate
            //invokation list
        }
    }
}
```
>[!tip] multicast delegate
> - Trace
> - myDelegate => Non-Public members => _invocationList,_methodPtr:has address of last function added