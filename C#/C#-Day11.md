### `RichTextBox`

> [!tip] rich text box is a control that displays formatted text
>
> - can be used to create a text editor
> - has the following features:
> - save and open files
> - change the font of the text
> - change the color of the text

> [!example] to save to a file
>
> - `saveFileDialog` provides the following properties to configure the file save dialog:
> - `saveFileDialog.Filter`: to filter the file type (`rtf files|*.rtf|text files|*.txt|all files|*.*`)
> - `saveFileDialog.FileName`: to set the file name

```csharp
//add button to save the file
private void btnSave_Click(object sender, EventArgs e)
{

    if (saveFileDialog.ShowDialog() == DialogResult.OK)
    //show the dialog to save the file with specified filters
    {
        //richTextBox is the name of the control(can be changed)
        richTextBox.SaveFiled(saveFileDialog.FileName);

    }
}
```

> [!tip] to open the file
>
> - `openFileDialog`: to open the file
> - `openFileDialog` provides the following properties to configure the file open dialog:
> - `openFileDialog.Filter`: to filter the file type (`rtf files|*.rtf|text files|*.txt|all files|*.*`)
> - `openFileDialog.FileName`: to set the file name

```csharp
//add button to open the file
private void btnOpen_Click(object sender, EventArgs e)
{

    if (openFileDialog.ShowDialog() == DialogResult.OK)
    //show the dialog to open the file with specified filters
    {
        //richTextBox is the name of the control(can be changed)
        richTextBox.LoadFile(openFileDialog.FileName);

    }
}
```

> [!tip] to change the font of the text
>
> - `FontDialog`: to change the font of the text

```csharp
//add button to change the font of the text
private void btnFont_Click(object sender, EventArgs e)
{
    //to open the font dialog with the selected font
    fontDialog.Font = richTextBox.SelectionFont;

    if (fontDialog.ShowDialog() == DialogResult.OK)
    //show the dialog to change the font of the text
    {
        richTextBox.SelectionFont = fontDialog.Font;

    }
}
```

> [!tip] to change the color of the text
>
> - `ColorDialog`: to change the color of the text

```csharp
//add button to change the color of the text
private void btnColor_Click(object sender, EventArgs e)
{
    //to open the color dialog with the selected color
    colorDialog.Color = richTextBox.SelectionColor;

    if (colorDialog.ShowDialog() == DialogResult.OK)
    //show the dialog to change the color of the text
    {
        richTextBox.SelectionColor = colorDialog.Color;

    }
}
```

---

### object

> [!tip] Compilation vs Runtime

```csharp
namespace Demo1Dynamic
{
    class Program
    {
        static void Main(string[] args)
        {
            //compilation time => int
            int i = 10;

            //compilation => object
            object o = 50;//runtime => int

            Console.WriteLine(o.GetType());//System.Int32
            o = "hello";//runtime => string
            Console.WriteLine(o.GetType());//System.String


            Console.WriteLine(o.Length);//error: object does not contain a definition for 'Length'
            //delay check to runtime if the object has the property or not
            //to solve this problem we use dynamic
            dynamic d = "hello";
            Console.WriteLine(d.Length);//5
            Console.WriteLine(d.GetType());//System.String

            Complex c1 = new Complex();//compilation => Complex

            dynamic c2 = new Complex();//runtime => Complex
            Console.WriteLine(c2.Real);//0

            dynamic c3 = new Complex(){Real = 10};
            Console.WriteLine(c3.Real);//10
            Console.WriteLine(c3.GetType());//Demo1Dynamic.Complex

            c3 = 30;//change the type of the object to int

            Console.WriteLine(c3.GetType());//System.Int32
            Console.WriteLine(c3.Real);//error: 'int' does not contain a definition for 'Real'

            var c4 = new Complex();//compilation => Complex

            c4 = 30;//can not change the type of the object to int
            //error: Cannot implicitly convert type 'int' to 'Demo1Dynamic.Complex'

        }
    }
}
```

```csharp
class Complex
{
    public int Real { get; set; }
    public int Imaginary { get; set; }
}
```

> [!danger] dynamic vs object vs var
>
> - both are used to store any type of data
> - (can store different types of data at runtime) unlike `var` > `var`: used to store only one type of data once it is assigned
> - `object` has only the properties of the `object` class (`GetType()`, `ToString()`, `Equals()`, `GetHashCode()`)
> - the ==compiler== checks the object at compile time(throws exception if the property does not exist)
> - `dynamic` delay check to ==runtime== if the object has the property or not(throws exception if the property does not exist)

---

### Generic Interface

> [!tip] Generic Interface

```csharp
interface ICalc
{
    int Add(int x, int y);
}
interface ICalc2
{
    int Add(float x, float y);
}
// both are the same except the type of the parameters
// to solve this problem we use generic interface
```

```csharp
interface ICalc<T>
{
    T Add(T x, T y);
}
```

```csharp
//class that implements the generic interface
class Calc : ICalc<int>
{
    public int Add(int x, int y)
    {
        return x + y;
    }

}
```

```csharp
//class that implements the generic interface float
class Calc2 : ICalc<float>
{
    public float Add(float x, float y)
    {
        return x + y;
    }

}
```

```csharp
//class that implements the generic interface (int, float)
class Calc3 : ICalc<int>, ICalc<float>
{
    public int Add(int x, int y)
    {
        return x + y;
    }

    public float Add(float x, float y)
    {
        return x + y;
    }
}
//the JIT compiler will create 2 interfaces
```

```csharp
//class that implements the generic interface (int, float,IComparable)
class Calc4 : ICalc<int>, ICalc<float>, IComparable<calc4>//ToCompare parameter(calc4)
//use the generic IComparable interface to compare the
{
    public int Add(int x, int y)
    {
        return x + y;
    }

    public float Add(float x, float y)
    {
        return x + y;
    }

    public int CompareTo(calc4 other)
    {
        //no need to cast as we are using the generic interface parameter of type calc4
        throw new NotImplementedException();
    }
}

```

> [!tip] class library output => dll
>
> - console application output => exe
> - windows form application output => exe

> [!example] project settings

```xml
<NulLable>enable</NulLable>
<!-- to give warning instead of error -->
```

```xml
<NulLable>disable</NulLable>
<!-- to give error instead of warning -->
```

> [!faq] why `NulLable`?
>
> - as reference type can be null

```csharp
    //main
    static void Main(string[] args)
    {
        Complex c1 = new Complex();
        Console.WriteLine(c1.Real);//0
        c1 = null;//Error: NullReferenceException: Object reference not set to an instance of an object.
        Console.WriteLine(c1.Real);//error: Object reference not set to an instance of an object.

        //we can use ? to check if the object is null or not
        Console.WriteLine(c1?.Real);//doesn't return
        //won't throw an exception if the object is null
    }
```

---

#### Garbage Collector

> [!tip] stack vs heap C++
>
> - stack: is faster than heap as it doesn't to check if there is enough space or not to allocate the memory
> - memory in c++ fragmental (it has to be contiguous to allocate the memory otherwise it will return null)

> [!tip] stack vs heap C#
>
> - stack: value types (int, float, double, bool, char, struct, enum)
> - heap: reference types (class, interface, delegate, string, array)
> - CLR (Common Language Runtime) is responsible for allocating the memory in the heap

> [!info] Garbage Collector
>
> - managed object: object that is allocated in the heap by the CLR
> - unmanaged object: object that is allocated by the developer (File,db connection, ...)
> - Garbage Collector: is responsible for freeing the memory of the managed objects (CLR)
> - next object pointer: pointer that points to the next object in the heap in managed heap
>
> > [!danger] if no space is available in the heap for the new object
>
> > [!done] the garbage collector(GC) will free the memory of the unused objects
>
> > [!warning] GC runs in the background (it is not guaranteed to run at a specific time)
> >
> > - we can force the GC to run by calling `GC.Collect()` but it is not recommended
> > - it clears the memory of the ==unreferenced== objects

> [!example] Application Root
>
> - application root: has a reference to all the objects that are used in the application
> - `obj1=null;` => obj1 is not referenced by the application root anymore
>   > [!danger] Mark and Compact
>   >
>   > - when new object is created and no space is available in the heap
>   > - GC will run and check application root to see if there is any unreferenced object
>   > - add any refernced objects to ==Graph==
> - any object that is referenced by the application root will not be collected by the GC

> [!tip] Compaction
>
> - when the GC frees the memory of the unreferenced objects
> - GC will move the objects to the beginning of the heap to make the memory contiguous and update the next object pointer
> - GC will stop the application from running while it is moving the objects

> [!tip] in c++ we use free() to free the memory of the object
>
> - if we forget to free the memory of the objects we will have space allocated in memory that is not used
> - in c# GC is responsible for freeing the memory of unreferenced objects

---

```csharp
//someclass.cs
class SomeClass
{
    DateTime N{get;set;}
}
internal class Program
{

    static void Main(string[] args)
    {

        for (int i = 0; i < 10000000000; i++)
        {
            Console.ReadLine();
            SomeClass s1 = new SomeClass();
            s1.N = DateTime.Now;
        }//every loop will create a new object and reference it to s1
        //the GC won't run every time there is unreferenced object
        //it will run when there is no space available in the heap
    }
}
```

> [!tip] Performance Profiler
>
> - Check .NET Counters => start
> - `GC Heap Size` => the size of the heap
> - graph is going up and down as the GC is freeing the memory of the unreferenced objects

> [!tip] add list to have reference to the objects
>
> - the GC won't free the memory of the objects as they are referenced by the list
> - the graph will go up as the GC won't free the memory of the objects

```csharp
internal class Program
{
    List<SomeClass> list = new List<SomeClass>();
    static void Main(string[] args)
    {

        for (int i = 0; i < 10000000000; i++)
        {
            Console.ReadLine();
            SomeClass s1 = new SomeClass();
            s1.N = DateTime.Now;
            list.Add(s1);
        }
        //list will have reference to the objects
        //the GC won't free the memory of the objects as they are referenced by the list
    }
}
```

> [!danger] `GC.Collect()` to force the GC to run
>
> - Not recommended
> - recommended to let CLR decide when to run the GC

> [!tip] Revise from powerpoint

---

### Destructor (Finalizer)

> [!tip] Destructor(Finalizer)
>
> - is called when the object is destroyed

> [!warning] Finalizer Queue
>
> - is a queue that contains the objects that have a destructor

> [!example] Application Root
>
> - application root: has a reference to all the objects that are used in the application

> [!danger] before the GC frees the memory of the object it will check if the object has a destructor or not (if it is in the finalizer queue or not)
>
> - if the object has a destructor it will add its reference to the Freachable queue and won't free the memory of the object

> [!tip] Freachable queue
>
> - rooted queue: object referenced in this queue will not be freed by the GC
> - is a queue that contains the reference to object that have a destructor
> - after GC finishes another thread (GC) will check the Freachable queue and call the destructor of the objects in the queue
> - the destructor will remove the reference of the object from the Freachable queue
> - in the next GC round the object will be freed as it is not in the Freachable queue or Finalizer Q anymore
>   > [!bug] thats how destructor affects the performance of the application
>   >
>   > - in allocation it will add the object to the finalizer queue
>   > - in deallocation it will add the object to the Freachable queue
>   > - call the destructor
>   > - in the next GC round it will free the memory of the object

> [!example] Examples: Look up the code in the powerpoint

```csharp
    class SomeClass
    {
        public int X { get; set; }
        ~SomeClass()
        {
            Console.WriteLine("Destructor");
        }

    }
    internal class Program
    {
        static void Main(string[] args)
        {
            SomeClass s1 = new SomeClass();
            s1.X = 10;
            Console.WriteLine(s1.X);
            s1 = null;
            GC.Collect();
            Console.WriteLine("End of Main");
            //10
            //End of Main
            //destructor is not called

        }

    }


```

```cs
//string list
//add in memory and file
class StringList:list<string>
{
    StreamWriter sw;//unmanaged object
    public StringList()
    {
        sw = new StreamWriter(@"d:\temp\gc.txt",true);
    }
    public void Add(string s)
    {
        Console.WriteLine(s);
        base.Add(s);

    }
    public void AddRange(List<string> list)
    {
        foreach (string item in list)
        {
            Console.WriteLine(item);

        }
        base.AddRange(list);
    }

}
```

```cs
    static void MyFun(){
        StringList list = new StringList();
        list.Add("hello");
        list.Add("world");
        List<string> list2 = new List<string>(){ "hello", "world" };
        list.AddRange(list2);
        foreach (string item in list)
        {
            Console.WriteLine(item);
        }
    }
    //main
    static void Main(string[] args)
    {
        StringList list = new StringList();
        list.Add("hello");
        list.Add("world");
        list.Remove("hello");
        list = null;
        GC.Collect();
        Console.WriteLine("End of Main");
        //End of Main
        //destructor is not called


///////////////////////////////////////
        myFun();
        Console.WriteLine("after myFun");
        //can't access the file as it is still used by application  ConsoleApplication1
        //need to close the application to access the file
        //or use destructor to close the file

        //better
        //pattern IDisposable

    }
```

> [!tip] IDisposable
>
> - Dispose(): is called explicitly by the developer unlike destructor
> - we need to have destructor and IDisposable(if we forget to call Dispose() the destructor will be called)
> - GC.SuppressFinalize(this): to remove the object from the finalizer queue(won't call the destructor - as we are calling Dispose() explicitly)

```cs
    class StringList:list<string>,IDisposable
    {
        StreamWriter sw;//unmanaged object
        public StringList()
        {
            sw = new StreamWriter(@"d:\temp\gc.txt",true);
            //@: to ignore the escape sequence
            //same as d:\\temp\\gc.txt
        }
        public void Add(string s)
        {
            Console.WriteLine(s);
            base.Add(s);

        }
        public void AddRange(List<string> list)
        {
            foreach (string item in list)
            {
                Console.WriteLine(item);
            }
            base.AddRange(list);
        }
        bool disposed = false;
        public void Dispose()
        {
            sw.Dispose();
            if (disposed ==false)
            {
            GC.SuppressFinalize(this);//to remove the object from the finalizer queue(won't call the destructor - as we are calling Dispose() explicitly)
            }
        }
        ~StringList()
        {
            Console.WriteLine("Destructor");
            disposed = true;
            Dispose();//no need to call GC.SuppressFinalize(this) as we are calling the destructor so we create a flag to avoid calling it
        }

    }
    static void MyFun(){
        StringList list = new StringList();
        list.Add("hello");
        list.Add("world");
        List<string> list2 = new List<string>(){ "hello", "world" };
        list.AddRange(list2);
        foreach (string item in list)
        {
            Console.WriteLine(item);
        }
        list.Dispose();
    }
```

> [!tip] using
>
> - using: is used to call Dispose() automatically
> - object has to implement IDisposable
> - try finally => in dll

```cs
    static void MyFun(){
        using(StringList list = new StringList())
        {
            list.Add("hello");
            list.Add("world");
            List<string> list2 = new List<string>(){ "hello", "world" };
            list.AddRange(list2);
            foreach (string item in list)
            {
                Console.WriteLine(item);
            }
        }
    }

```

> [!bug] In .NET we don't guarantee that the destructor will be called

> [!example] REVIVE OBJECT
>
> - add reference to the object in the destructor

> [!tip] GC works faster in smaller memory size
>
> - gen0(256kb), gen1(2mb), gen2(10mb: rest), LOH (large object heap )
> - LOH: is used to store large objects (more than 85kb) no compaction as it affects the performance
> - object created together are deleted together
> - first time created object is stored in gen0
> - when the GC runs it will check gen0 if there is no space available it will move the object that still used to gen1
> - object that survived the GC round are moved to gen1
> - it will do the same until gen1 is full aswell then it will move the object to gen2
> - gen2: is last generation
>   > [!done] to get generation of the object
>   >
>   > - `GC.GetGeneration(obj)`
>   >
>   > ```cs
>   >   static void Main(string[] args)
>   > {
>   >     SomeClass s1 = new SomeClass();
>   >     Console.WriteLine(GC.GetGeneration(s1));//0
>   >     GC.Collect();
>   >     Console.WriteLine(GC.GetGeneration(s1));//0
>   >     GC.Collect();
>   >     Console.WriteLine(GC.GetGeneration(s1));//1
>   >     GC.Collect();
>   >     Console.WriteLine(GC.GetGeneration(s1));//2
>   >     GC.Collect();
>   >     Console.WriteLine(GC.GetGeneration(s1));//2
>   > }
>   > ```


>[!tip] performance profiler
> - check heap size , gen0, gen1, gen2
---

 >[!danger] Look up ==selmy== File
#### #Lab-csharp-11

- newList  string implement Dispose