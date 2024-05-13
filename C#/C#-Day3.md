```cs
int32 x = 20; //this stores 4 bytes of memory
int16 y = 10; //  2 bytes of memory

//can store int16 in int32
// widening casting
x = y;//implicit
x = (int32)y;//explicit

//can store int32 in int16 but it will lose data
//narrowing casting
y = (int16)x;//explicit: we have to tell the compiler that we know what we are doing

x = 32768;
y = (int16)x;//-32768 as it  is out of range
```

> [!WARNING] complier allows implicit casting from smaller to larger data types but not the other way around
>
> > [!tip] we can use explicit casting to convert from larger to smaller data types but we have to be careful as we might lose data

> [!note] byte & sbyte
> byte (0-255) is an unsigned data type
> sbyte (-128 to 127) is a signed data type
> can't store sbyte in byte or vice versa
> as they don't have the same range
> same with uint16 and int16,short and ushort,

> [!tip] float and double
> float to double is implicit
> double to float is explicit
> as float is 4 bytes and double is 8 bytes
> using suffix f or (float) to convert to float
> `float x = 10.5f;` >`float y = (float)10.5;`
> same with decimal and double
> using suffix m or (decimal) to convert to decimal
> `decimal x = 10.5m;` > `decimal y = (decimal)10.5;`

---

> [!NOTE] Convert.ToInt32()
> Convert.ToDeciaml()
> used to convert from different data types to int32 and decimal respectively

> [!TIP] int.Parse()
> used to convert from string to int32

---

```cs
namespace CSharp_Day3
{
    class Program
    {
        static void Main(string[] args)
        {
          Int32 x = 20;
            Int16 y = 10;
            x = y;//works fine
            y = x;//error: cannot implicitly convert type 'int' to 'short'. An explicit conversion exists (are you missing a cast?)
            y = (Int16)x;//works fine
            x = 32767;//in range of int32
            x = 32768;//in range of int32 but not int16
            y = (Int16)x;//-32768 as it  is out of range
            //overflow happened
            //program will still run
            //to check if overflow happened (loss of data)
            //we can use checked keyword
            checked
            {
                y = (Int16)x;//error: Arithmetic operation resulted in an overflow.
            } //this will throw an exception if overflow happens
            //this will stop the program from running
            // only check for overflow

            //unnchecked keyword(default behaviour)
            // used inside checked block
            // to stop checking for overflow
            checked
            {
                unchecked
                {
                    y = (Int16)x;//no error
                }
            }

            long = 2;//8 bytes
            float = l; //4 bytes but its internal representation can store long

            float f = 10.5;//error: cannot implicitly convert type 'double' to 'float'. An explicit conversion exists (are you missing a cast?)
            float f = 10.5f;//works fine
            float f = 2;//works fine as int can be stored in float
            // decimal to double is explicit
            decimal d = (decimal)10.5;
            double dou2 = (double)d;//  has to be explicit


        Int32 x = 32768;
            Int16 y = 20;

            y = Convert.ToInt16(x);//error: Value was either too large or too small for an Int16.
            //this will throw an exception if overflow happens
            y = Convert.ToInt16("dfjdf");//return 16 bit => short

            //null
            string s = null;
            y = Convert.ToInt16(s);//error: Object reference not set to an instance of an object.

            string s = "10";
            y = Convert.ToInt16(s);// returns 10
            s = "10.5f";
            y = Convert.ToInt16(s);//error:fomat exception

            //int.Parse() vs Convert.ToInt32()
            //both have format exception & overflow exception
            // but int.parse() has null exception
            // Convert.ToInt32() doesn't have null exception returns 0 instead

    }
}
```

---

```cs
namespace CSharp_Day3
{
    class Program
    {
        static void Main(string[] args)
        {
            int age = null;//error doesn't acccept null
            Nullable<int>  age = null;//this makes it accept null
            //syntactic sugar
            int? age = null;//this makes it accept null
            short? s1 = null;
            short s2 = 10;
            s1 = s2;//implicit
            s2 = s1;//error: cannot implicitly convert type 'short?' to 'short'. An explicit conversion exists (are you missing a cast?)
            //as s1 is nullable we have to use explicit casting
            s2 = (short)s1;//Error: System.InvalidOperationException: Nullable object must have a value.
            s1 = 10;
            s2 = (short)s1;//works fine

            //to be safe from
            if (s1 != null)
            {
                s2 = (short)s1;//works fine
            }else
            {
                s2 = 0;
            }
            //syntactic sugar
            s2 = s1!=null ? (short)s1 : (short);

            //HasValue
            //only works with nullable data types
            if (s1.HasValue)
            {
                //s2 = (short)s1;
                s2 = s1.Value;

            }else
            {
                s2 = 0;
            }

            //if we use s1.Value when s1 is null
            //it will throw an exception
    //        so we use it in if statement
            //syntactic sugar
            s2 = s1.HasValue ? s1.Value : (short)0;

            //syntactic sugar
            s2 = s1 ?? 0;
            //coalescing operator ??
            //if s1 is null then s2 = 0 else s2 = s1
            s2 = s1 ?? default(short);
            //default(short) = 0

        }
    }
}
```

> [!DANGER] IN ANY PROGRAM WE HAVE TO PREVENT EXCEPTIONS

> [!bug] `s2 = s1.HasValue ? s1.Value : 0;` ERROR: cannot implicitly convert type 'int' to 'short'.
> we have to use explicit casting `s2 = s1.HasValue ? (short)s1.Value : (short)0;`
> all has to be of the same type
> it is like when add int to int it will return int

---

> [!NOTE] var : implicitly typed local variable
>
> ```cs
> var x = 10;//x is int
> var y = 10.5;//y is double
> var z = 10.3f;//z is float
> // var can't reassign to different data type
> x = 10.5;//error: Cannot implicitly convert type 'double' to 'int'. An explicit conversion exists (are you missing a cast?)
> ```
>
> > [!TIP] so we us object
> >
> > ```cs
> > int x = 10;
> > float y = 10.5f;
> > string s = "hello";
> > object o = x;//boxing
> > Console.WriteLine($"{o}:{o.GetType()}");//10:System.Int32
> > o = y;
> > Console.WriteLine($"{o}:{o.GetType()}");//10.5:System.Single
> > o = s;
> > Console.WriteLine($"{o}:{o.GetType()}");//hello:System.String
> > ```

> [!NOTE] Boxing
>
> - take value type from stack and convert it to object in heap and reference it
> - update reference in stack when changing
> - avoid boxing as it affects performance
> - used in function parameters to accept any data type

---

> [!danger] Unboxing
>
> - take object from heap and convert it to value type in stack
> - `int x = (int)o;` if o is not int it will throw an exception otherwise it will work fine
> - if o is float and we want to convert it to int
> - `int x = (int)((float)o);` this will work fine
> - avoid unboxing as it affects performance

---

# Break

---

> [!tip] enum
>
> ```cs
> string s = "saturday";//we need s to have only 7 values of the week
> //string  accepts any string
> //we can use enum
> ```
>
> ---
>
> ```cs
> enum DayOfWeek
> {
>    Saturday,
>    Sunday,
>    Monday,
>    Tuesday,
>    Wednesday,
>    Thursday,
>    Friday
> }
> ```
>
> ---
>
> ```cs
> DayOfWeek d = "saturday";//error: Cannot implicitly convert type 'string' to 'DayOfWeek'
> DayOfWeek d = DayOfWeek.Saturday;//works fine
> Console.WriteLine($"{d}");//Saturday
> DayOfWeek d = DayOfWeek.xyz;//error:has to be one of the 7 values
> ```
>
> ---
>
> > [!tip] take input from user
> > we have to parse it to enum
> >
> > ```cs
> > Console.WriteLine("Enter day of week");
> > DayOfWeek d = (DayOfWeek)Enum.Parse(typeof(DayOfWeek),Console.ReadLine());//returns object so we have to cast it to DayOfWeek
> > ```
> >
> > > [!danger] input case sensitive
> > > friday will throw an exception
> > > Friday will work fine

---

> [!example] Gender enum
>
> ```cs
> enum Gender{
> Male,// alias name for int value 0 => saves in memory as 0
> Female //1
> }
> ```
>
> ---
>
> > [!tip] Pares is used using Enum class `Enum.Parse()`
> >
> > ```cs
> > Gender g1 = Gender.Female;//4 bytes
> > g1  =(Gender) Enum.Parse(typeof(Gender),"Male")
> > ```

> [!done] how to know the Names of enum
> we can use `Enum.GetNames()`
> returns string array of names

```cs
Gender g1 = Gender.Female;
g1 = 0;//works fine
g1 = 1;//compilation error
g1 = (Gender) 1;//female
g1 = (Gender) 10;//10
var z = Enum.GetNames(typeof(Gender));
foreach (var item in z)
{
    Console.WriteLine(item);
}

```

> [!tip] make enum store 1 byte
>
> ```cs
> enum dayOfWeek : byte
> {
>    Saturday , sunday, Monday, Tuesday, Wednesday, Thursday, Friday
> }
> ```

> [!example] we can set different values for enum
>
> ```cs
> enum dayOfWeek : byte
> {
>    Saturday = 10 , sunday = 30, Monday = 40, Tuesday = 50, Wednesday = 60, Thursday, Friday =255//255 is max value for byte
> } //if we didn't assign value for sunday it will be 11 and so on...
> ```

> [!done] ITIBrances
>
> ```cs
> enum ITIBranches
> {
>    Mansoura = 100, Tanta = 200, Alexandria = 300, Cairo = 400, Aswan = 500
> }
> ```
>
> ---
>
> ```cs
> ITIBranches branch = ITIBranches.Mansoura;
> branch = 300;//error: Cannot implicitly convert type 'int' to 'ITIBranches'. An explicit conversion exists (are you missing a cast?)
> branch = (ITIBranches)300;//works fine
> Console.WriteLine($"{branch}");//Alexandria
> branch = (ITIBranches)Enum.Parse(typeof(ITIBranches), Console.ReadLine());//Aswan
> Console.WriteLine($"{branch}");//Aswan
> Console.WriteLine($"{(int)branch}");//500
> Console.ForegroundColor = ConsoleColor.Red;//ConsoleColor is enum
> //As you see if we have a code for each branch → it will be useful to refer to that branch by code or name.
> ```

---

> [!warning] enum file permission
>
> ```cs
> [Flags] //to allow multiple values
> enum FilePermission:byte
> {
>    Read = 1 , Write = 2 , Execute = 4 ,Modify = 8
> }
> ```
>
> ---
>
> > [!tip] we want to give multiple permissions
> > we can use array [Read,Write,Execute,Modify]
> > binary representation Read _ 2^0 + Write _ 2^1 + Execute _ 2^2 + Modify _ 2^3
> >
> > - p2 = [1,0,0,0] => Read,no Write,no Execute,no Modify => decimal value = 1
> > - p3 = [0,1,0,0] => no Read,Write,no Execute,no Modify => decimal value = 2
> > - p4 = [0,0,1,0] => no Read,no Write,Execute,no Modify => decimal value = 4
> > - p5 = [0,0,0,1] => no Read,no Write,no Execute,Modify => decimal value = 8
> >   if we have read and we want to give him write permission we XOR p2 with p3
> >
> > ```cs
> > FilePermissions fp = FilePermissions.Read;
> > fp = FilePermissions.Write;
> > Console.WriteLine($"{fp}");//Write
> > // we want to add read permission or remove it we use XOR operator (^)
> > fp^=FilePermissions.Read;
> > Console.WriteLine($"{fp}");//Write,Read using [Flags]
> > Console.WriteLine($"{(int)fp}");//3
> > fp^=FilePermissions.Write; //remove write permission
> > Console.WriteLine($"{fp}");//Read
> > //we can use OR operator (|) to add permission
> > fp |= FilePermissions.Write;//add write permission
> > ```

---

> [!tip] Reference
>
> - Reference: is a variable that holds the address of an object in memory
> - reference is in stack and object itself is in heap
> - object can have reference to other object

> [!example] Struct
> Complex number
>
> ```cs
> struct Complex
> {
>    //public int real;
>    //public int imaginary;
> private int real;
> private int imaginary;
> //setters and getters when making it private
>   public void setReal(int _real){
> //this.real = real;//this is not pointer it is reference it is the object itself
> real = _real;//this is the parameter
>   }
>   public void setImaginary(int _imaginary){
> //this.imaginary = imaginary;
> imaginary = _imaginary;
>   }
>   public int getReal(){
> return real;
>   }
>   public int getImaginary(){
> return imaginary;
>   }
>   public void print(){
> if(imaginary>0)
>   Console.WriteLine($"{real}+{imaginary}i");
>   else if(imaginary<0)
>   Console.WriteLine($"{real}{imaginary}i");
>   else
>   Console.WriteLine($"{real}");
>       }
>    }
> }
> ```
>
> ---
>
> ```cs
> Complex c1;//we don't have to use new
> c1.real = 10;
> c1.imaginary = 20;
> c1.print();//10+20i
> Complex c2 = new Complex();//we can use new to initialize members in struct
> c2.print();//0
> ```
>
> ---
>
> > [!bug] Struct is value type
> > always in stack not in heap even if we use new
>
> > [!Warning] Struct can't inherit or be inherited from
> >
> > > [!done] But `class` can

> [!NOTE] expression bodied method
> used when we have one line of code
>
> ```cs
> public int getReal() => real;
> public int getImaginary() => imaginary;
> public void setReal(int _real) => real = _real;
> public void setImaginary(int _imaginary) => imaginary = _imaginary;
> ```

> [!example] create constructor for struct
>
> > [!bug] unlike class if we create constructor for struct we still have the default constructor
>
> ```cs
> struct Complex
> {
>    public Complex(int _real, int _imaginary)
>    {
>        real = _real;
>        imaginary = _imaginary;
>    }
> }
> ```
>
> ---
>
> ```cs
> Complex c1 = new Complex(10,20);
> Complex c2 = new Complex();//works fine
> ```
>
> > [!warning] we can write no parameter constructor for struct
> >
> > ```cs
> > struct Complex
> > {
> >    public Complex()
> >    {
> >        real = 5;
> >   }
> > }
> > ```
> >
> > ---
> >
> > ```cs
> > Complex c1 = new Complex(10,20);
> > Complex c2 = new Complex();//works fine
> > Console.WriteLine($"{c2.real}");//5
> > ```

> [!example] we can create 1 parameter constructor for struct
>
> ```cs
> struct Complex
> {
>    public Complex(int _real)
>    {
>        real = _real;
>        imaginary = 0;
>    }
> }
> ```

> [!done] we can chain constructors
>
> ```cs
> struct Complex
> {
>    public Complex():this(0,0)
> //this calls the 2 parameter constructor first then the default constructor
>    {
>       // this(0,0);//error: cannot use this keyword in struct
> Console.WriteLine("default constructor");
>    }
>    public Complex(int _real):this(_real,0)
>    {
>        Console.WriteLine("1 parameter constructor");
>    }
>    public Complex(int _real, int _imaginary)
>    {
>        real = _real;
>        imaginary = _imaginary;
>     Console.WriteLine("2 parameter constructor");
>    }
> }
> ```

> [!danger] we write bigger constructor first as we can call it from smaller constructor

> [!tip] use property instead of setters and getters
> `public int Real {get;set;}`
> same name as member but with capital letter real => Real
> `public int Real {get;set;} `
>
> ```cs
> public int Real{
> get{
> return real;
> }
> set{
> real = value;//value is the parameter passed to the setter
> }
> }
> ```
>
> ---
>
> ```cs
> Complex c1 = new Complex(10,20);
> c1.Real = 30;
> Console.WriteLine($"{c1.Real}");//30
> ```

> [!example] we can do the same with imaginary
>
> ```cs
> public int Imaginary{
> get{
> return imaginary;
> //return Imaginary; //error: Stack overflow will call itself as we used the property not the field
> }
> set{
> imaginary = value;
> }
> //we can make it private
> //can't make both private as property public and has to have at least one accessor but the property itself can be private
> private set{
> imaginary = value;
> }
> }
> ```
>
> ---
>
> ```cs
> Complex c1 = new Complex(10,20);
> c1.Imaginary = 30;
> Console.WriteLine($"{c1.Imaginary}");//30
> ```

> [!danger] property access modifiers
>
> - `property` can `private` / `protected` / `public`
> - `setter` can `private` / `protected` / `public`
> - `getter` can `private` / `protected` / `public`
>   but at least one of them has to ==match== the ==property== ==access modifier==
>   Also, ==inner== access modifiers should be ==higher== security or ==equal== with ==outer==.
>
> ```cs
> public int Real {get=>real;private set=>real = value;}
> ```

> [!done] override ToString()
> we have to override ToString() in any created class or struct.
>
> ```cs
> public override string ToString()
> {
>    if (imaginary > 0)
>        return $"{real}+{imaginary}i";
>    else if (imaginary < 0)
>        return $"{real}{imaginary}i";
>    else
>        return $"{real}";
> }
> ```

---

### #Lab-3

- word document
