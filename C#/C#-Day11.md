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
>   > -  when new object is created and no space is available in the heap
>   > -  GC will run and check application root to see if there is any unreferenced object
>   > -  add any refernced objects to ==Graph==
> - any object that is referenced by the application root will not be collected by the GC

>[!tip] Compaction
> - when the GC frees the memory of the unreferenced objects
> - GC will move the objects to the beginning of the heap to make the memory contiguous and update the next object pointer
> - GC will stop the application from running while it is moving the objects

>[!tip] in c++ we use free() to free the memory of the object
> - if we forget to free the memory of the objects we will have space allocated in memory that is not used
> - in c# GC is responsible for freeing the memory of  unreferenced objects

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
>[!tip] Performance Profiler
> - Check .NET Counters =>  start
> - `GC Heap Size` => the size of the heap
> - graph is going up and down as the GC is freeing the memory of the unreferenced objects

>[!tip] add list to have reference to the objects
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

>[!danger] `GC.Collect()` to force the GC to run
> - Not recommended
> - recommended to let CLR decide when to run the GC


>[!tip] Revise from powerpoint

---
