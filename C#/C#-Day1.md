

> [!tip] tools
> c# 12.0
> .net 8.0
> visual studio 2020

> [!info] course outline
>
> 1. c# fundamentals
> 2. oop
> 3. advanced oop
> 4. generics
> 5. delegates
> 6. events
> 7. threading
> 8. garbage collection

---

> [!warning] source code => machine code
> compiler
> source code => platform dependent
> machine code => needs to be compiled for each OS
>
> > [!success] need a language that is platform independent
> > .net framework 2002
> > .net core 2016

> [!example] source code in c# => `hello.cs`
> needs to be compiled
> `csc hello.cs` c sharp compiler => IL code: intermediate language, MSIL: microsoft intermediate language , CIL: common intermediate language
> first phase of compilation c sharp compiler compiles all files to one file program.exe / program.dll => assembly file(that what gets delivered to the user)
> another phase of compilation => CLR: common language runtime => runs the IL code => JIT compiler: just in time compiler => machine code depending on user's OS => runs on the machine(instruction set of the machine)
>
> > [!done] so we only write the source code once and it runs on any machine (cross platform)
>
> .net core is extension of .net framework that has different versions for different OSs

> [!tip] JITing
> JITing is the process of turning IL code into machine code at runtime. This is done by the JIT compiler, which is part of the .NET runtime (CLR). The JIT compiler is a part of the runtime, so it is not a separate component. The JIT compiler is responsible for compiling IL code to machine code 
>
> 1. per function, per function call: JIT only compiles the functions that are called
> 2. Caching: JIT caches the compiled code so that it doesn't have to compile the same code again
>    > [!bug] Problem: Il file can be viewed using ILDASM so we need to protect the code
>    
>    > [!done] Solution: obfuscation: obfuscate the code so that it is not readable

---

### CLR Components

> [!example] class loader: loads the assembly file

> [!tip] Exception handler: gives the option to the user to handle the exception during runtime

> [!info] Garbage collector: frees the memory that is not used anymore

> [!warning] Thread manager: manages the threads

> [!success] Security manager: manages the security of the application

---

> [!tip] all languages( .net ) that get compiled to IL code can use other languages classes or methods (libraries)
>
> - FCL: framework class library => contains all the classes that we can use in our code in dll files (IL code)
> - .net core: cross language

---

> [!info] .net core Versions
>
> - .net core 1.0
> - ....
> - .net V 8.0 (what we are going to use) / c# 12.0
>   > [!done] .net core features
>   >
>   > - cross platform
>   > - cross language
>   > - open source
>   > - component based: we can install only the components that we need (nuget)
>   > - cloud ready: we can deploy directly to the cloud (no configuration needed)
>   > - high performance
>   > - CLI: commands can be run from the terminal
>   > - framework dependent : we need to install the .net core runtime on the machine that we want to run the application on or we can self contain the application (we can include the runtime with the application)

---

### Namspace

> [!info] namespace
>
> - group of classes that are related to each other
>   > [!done] prevent collision of class names
>   > if we have two classes with the same name in different namespaces we can use the namespace to differentiate between them
>   > namespace can have sub namespaces
>   > `system.forms.button` button is a sub namespace of forms namespace which is a sub namespace of system namespace
>   > `system.forms.button b1;` b1 is an object of button class in forms namespace in system namespace

> [!tip] Solution: group of projects
> project: group of files that are related to each other
> proj1: desktop app, proj2: web app, proj3: mobile app all of them are in the same solution as they are related to each other
> compiles every project to a separate assembly file( manifest file: contains the information about the assembly file, datatype, resources)

---

### create project on VS 2022

> [!info] c# is fully object oriented language
> every function is a method of a class
>
> > [!done] do not use top level statements

> [!tip] solution Day1 , project: demo1

```cs
//program.cs
namespace demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}

//dependencies
// Microsoft.NETCore.App
```

> [!info] developer command prompt for VS 2022
> `ildasm` opens the IL disassembler
> open file => program.exe
> we can see the manifest file and the IL code

```cs
//main
//.entrypoint
//.maxstack 8
//IL_0000: ldstr "Hello World!"
//IL_0005: call void [System.Console]System.Console::WriteLine(string)
//IL_000a: ret

```

> [!tip] we can run app by clicking on Demo1.exe but it closes immediately so we need to add a line to keep the console open

> [!info] open in ILSpy
> we can see the source code of the assembly file
> IL => C# code
> c# code => IL code

> [!info] sharplab.io
> link: https://sharplab.io/
> : we can see the IL , JIT code of the c# code

> [!info] source.dot.net
> link: https://source.dot.net/
> we can see the source code of the .net framework

---

# Break

---

> [!danger] SEARCH in Lab
>
> search for a tool for obfuscation

---

> [!info] writing top level statements

```cs
//on top level
//program.cs
    Console.WriteLine("Hello World!");
    //compiler will add the namespace and the class and main method
    // ILSpy
    // we can see the namespace and the class and the main method

```

> [!error] only one compilation unit can have top level statements

```cs
//f1.cs
//top level statement
 Console.WriteLine("Hello World!");
 // Error: only one compilation unit can have top level statements
```

---

> [!info] we can only declare these 5 in namespace
> class
> struct
> enum
> interface
> delegate

```cs
namespace demo1 //can be different name
{
    //class
    //struct
    //enum
    //interface
    //delegate
    class Program
    {
        //field
        //property (replacement for setter and getter)
        //methods
        public static void Main(string[] args)
        // it has to be static
        //
        {
            Console.WriteLine("Hello World!");
        }
    }
}
//top level statements have higher priority than the class
// so f1.cs will be called first

```

> to make it call the class main function we need to add the namespace and the class name

```cs
//f1.cs
Console.WriteLine("Hello World!");
demo1.Program.Main();
//Hello World!
//Hello World!

```

<!-- //11:45 -->

> [!info] 2 main in same project :
> we can have 2 main functions in the same project but we need to specify which one to call

> [!info] 1 main , top level statement:
> top level statement will be called first

> [!bug] can't have 2 top level statements files in the same project

> [!tip] Main method
> has to be static
> can return int or void
> has to be Capital M
>
> > [!error] Program does not contain a static 'Main' method suitable for an entry point

```cs
//pascale case: first letter of each word is capital
//camel case: first letter of each word is capital except the first word
//c# uses pascale case except for the variables(camel case)
using System;
using static System.Console;
namespace demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            //searches for Console in the current namespace
            ////to make it search in the system namespace we need to add the namespace
            System.Console.WriteLine("Hello World!");
            //everytime we need to use console we need to add the namespace system
            //to avoid that we can add the namespace at the top
            // using system;
            //now it will search for console in the current namespace then in the system namespace
            Console.WriteLine("Hello World!");
            //////////// using static
            WriteLine("Hello World!");//Error: The name 'WriteLine' does not exist in the current context
            // using static System.Console;
            // we can use the static methods of the class without adding the class name

            //


        }
    }
}
```

> [!tip] implicit using:
> we can add the using statement to the project file so that we don't need to add it to every file
> we add this to the project file

```xml
    <implicitUsings>
        <implicitUsing>System</implicitUsing>
```

```cs
namespace demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");

        }
    }
}
```

---

> [!tip] `Console.ForegroundColor = "red";`
>
> > [!Error] : Cannot implicitly convert type 'string' to 'System.ConsoleColor'
> > better to use the enum (ConsoleColor): restricts the values that can be assigned to the variable
>
> > [!done] `Console.ForegroundColor = ConsoleColor.Red;`
> > same for background color `Console.BackgroundColor = ConsoleColor.Red;`

```cs
namespace demo1
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            Console.ForegroundColor = ConsoleColor.Red;
            Console.BackgroundColor = ConsoleColor.Blue;
            Console.WriteLine("Hello World!");
            Console.WriteLine("Hello World!");
        }
    }
}
```

---

> [!tip] store data in variables

```cs
    //12 =>integer: whole number no decimal point
    //2.3 =>float
    // 'a' =>char
    // "hello"=> string
    //true =>bool

    short s;
    // reserve 2 bytes in stack
    // 1 bit for sign + 15 bits for the number
    // -32768 to 32767
    // we need bigger range
    // int
    int i; // 4 bytes
    // -2,147,483,648 to 2,147,483,647
    uint ui; // 4 bytes 0 to 4,294,967,295 (unsigned)
    byte b; // 1 byte 0 to 255 (unsigned)
    sbyte sb; // 1 byte -128 to 127 (signed)
    short b; // 2 bytes -32768 to 32767 (signed)
    ushort us; // 2 bytes 0 to 65535 (unsigned)
    long l; // 8 bytes -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 (signed)
    ulong ul; // 8 bytes 0 to 18,446,744,073,709,551,615 (unsigned)

```

> [!warning] all of those are integer values
> [!info] IDE features: warning before compiling
>
> > [!bug] use of uninitialized local variable 'i'
> > we need to initialize the variable before using it

```cs
    int x;//warning: declared but never used
    int j;
    int i = 10;
    // we can use the variable
    Console.WriteLine(i);
    Console.WriteLine(j);//Error: use of unassigned local variable 'j'
    byte b = 256;//Error: Constant value '256' cannot be converted to a 'byte'
    // we need to use the range of the datatype
    sbyte sb = 128;//Error: Constant value '128' cannot be converted to a 'sbyte'

```

> [!tip] every datatype is struct has const values: maxValue,minValue
> `Console.WriteLine(int.MaxValue);` => 2147483647

```cs
    int i = 2.3;//Error: Cannot implicitly convert type 'double' to 'int'

    double d1 = 2.3;//8 bytes in memory
    double d1 = 2;//8 bytes in memory

    float f1 = 2.3;//4 bytes in memory
    //Error: Cannot implicitly convert type 'double' to 'float'
    //we need to add f to the end of the number
    //like casting from double to float
    float f1 = 2.3f;//4 bytes in memory
    // 7-8 digits after the decimal point
    // 9 will round up

    // double  14-15 digits after the decimal point
    //more accurate

    ///////////////decimal
    decimal d1 = 2.3;//16 bytes in memory
    //Error: Cannot implicitly convert type 'double' to 'decimal'
    //we need to add m to the end of the number (m for money)
    //28-29 digits after the decimal point
    //differnt in memory representation than double
    // so we need to cast from double to decimal
    decimal d1 = 2.3f;//Error: Cannot implicitly convert type 'float' to 'decimal'
    //different representation in memory

    ///////////////char
    char c1 = 'a';//2 bytes in memory
    // c1; search for the methods with char object
    // c1.isLetter, c1.isDigit,.....
    //try in IDE

//////////////////////////bool
bool b1 = true;//1 byte in memory
bool b2 = false;
bool b3 = 5<3;//false

int b4 = 5 < 3;//Error: Cannot implicitly convert type 'bool' to 'int'

bool b5 = 1;//Error: Cannot implicitly convert type 'int' to 'bool'

//////////////////////////string
string s1 = "hello";
Console.WriteLine(s1);//hello


///////////////////////object

int s = "ali";//Error: Cannot implicitly convert type 'string' to 'int'
int s = true;//Error: Cannot implicitly convert type 'bool' to 'int'
int s = 2.3;//Error: Cannot implicitly convert type 'double' to 'int'

//cts: common type system
//any datatype is a child of object

object s = true;//no error
```

> [!tip] CTS: common type system
> every datatype is a child of object
>
> > [!tip] .NET
> > in .NET anydatatype should inherit from object

```cs
    object s = true;
    object s = 2.3;
    object s = "hello";
    object s = 'a';
    object s = 2;
    // object methods
    // s.GetType(), s.ToString(),....
    // try in IDE
```

> [!tip] differnet languages(.NET) => IL code
> int => int32
> .....

> [!tip] Double vs double
> same

---

> [!warning] CTS: common type system divide data types into 3 categories
>
> 1. value types
> 2. reference types
> 3. pointer types XXXXX

> [!tip] all of the above types are value types except object, string

> [!info] value types
> variable stores the value itself (stack)

> [!done] reference types
> variable stores the address of the value (value in heap)

> [!info] struct, enum are value types

> [!info] class, delegate and interface are reference types

---
# #lab1
- download ASP.NET and web development
- download .NET desktop development
- download .NET Multi-platform App UI(optional)