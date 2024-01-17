```cs
namespace Demo2
{
   struct Complex
    {
        private int real;
        private int imaginary;

        public int Real
        {
            get { return real; }
            set { real = value; }
        }

        public int Imaginary
        {
            get { return imaginary; }
            set { imaginary = value; }
        }

        public Complex(int real = 0, int imaginary = 0)
        {
            //this is hidden parameter which is reference to current object
            this.real = real;
            this.imaginary = imaginary;
        }

        public override string ToString()
        {
            return $"{real} + {imaginary}i";
        }
    }
}
```

> [!tip] f10 to step over(execute without going into method) and f11 to step into(execute and go into method)

---

```cs
using Demo2;
namespace Demo1
{
  class Program
  {
    static void Main(string[] args)
    {
        Complex c1 = new Complex(1, 2);
        Console.WriteLine(c1.ToString());
        Console.WriteLine(c1);//this will call ToString() method
        //object intializer with setting public properties
        Complex c2 = new Complex (2, 3){Real = 4, Imaginary = 5};
        Console.WriteLine(c2);// 4 + 5i
        Complex c3 = new Complex();
        Console.WriteLine(c3);// 0 + 0i
        //even though we have not defined any constructor with no parameters
        //members are initialized to default values

        Complex c4 = new Complex(){Real = 4, Imaginary = 5};
        Console.WriteLine(c4);// 4 + 5i
        //object initializer

    }
  }
}
```

---

> [!warning] Automatic Properties -> we can use properties directly without fields
> and fields will be created automatically by compiler `k__BackingField`
> and setters and getters will be created automatically by compiler
>
> > [!danger] NOW we can make manage getter and setter
> > and we can make them private, protected
> > `public int Real { get; private set; }`
>
> > [!warning] if we want to write code in getter and setter we have to use fields not automatic properties
> > like validation in setter

> [!success] it is better to use fields in most cases

```cs
namespace Demo2
{
   struct Complex
    {
        // int real; //we can add fields after properties and users of the struct will not be affected
        // we have to change the properties to use fields and remove intialization
        public int Real { get; set; } = 0;//can be initialized
        public int Imaginary { get; set; } = 20;

        public Complex(int real = 0, int imaginary = 0)
        {
            //this is hidden parameter which is reference to current object
            Real = real;
            Imaginary = imaginary;
        }

        public override string ToString()
        {
            return $"{Real} + {Imaginary}i";
        }
    }
}
```

---

> [!success] value equality

```cs
namespace Demo1
{
  class Program
  {
    static void Main(string[] args)
    {
        Complex c1 = new Complex(1, 2);
        Complex c2 = new Complex(3, 4);
        c2 = c1;//value equality as it is struct
        Console.WriteLine(c1);// 1 + 2i
        Console.WriteLine(c2);// 1 + 2i
        if (c1.Equals(c2))//to compare values of two objects (state)
        {
            Console.WriteLine("c1 and c2 are equal");
        }
        else
        {
            Console.WriteLine("c1 and c2 are not equal");
        }
        c1.Real = 10;
        Console.WriteLine(c1);// 10 + 2i
        Console.WriteLine(c2);// 1 + 2i
    }
  }
}
```

> [!tip] Equals() used to compare values of two objects (state)
> has performance issue

---

### functions in C#

```cs
namespace Demo1
{
 internal class Program
  {
    public void fun1()
    {
        Console.WriteLine("fun1");
    }

    public static void fun2()
    {
        int t = 10;
        Console.WriteLine("fun2");

    }
       public static void fun1(int a)
    {
        Console.WriteLine("fun1");
        int z = 10;//stack trace(main stack frame , fun1 stack frame)
        fun2();//stack trace(main stack frame , fun1 stack frame , fun2 stack frame)
    }
   static void Main(string[] args)
    {
        //can't call instance methods without object
        //fun1();//error
        //so we use static methods
        fun2();//fun2
        int x = 10;
        int y = 20;
    }
  }
}
```

> [!tip] on calling functions it creates stack frame and it is removed after function call
> first stack frame is main stack frame
>
> > [!example] stack trace : is the list of stack frames
> > we can see it in visual studio in debug mode(TRACE)
> > call stack window

---

> [!tip] we can use StackTrace class to get stack trace
> example in fun2()

```cs
public static void fun2()
{
    int t = 10;
    Console.WriteLine("fun2");
    StackTrace st = new StackTrace();
    foreach (var item in st.GetFrames())
    {
        Console.WriteLine(item.GetMethod().Name);
        //main
        //fun2
        //fun1
    }
}
public static void fun1(int a)
{
    Console.WriteLine("fun1");
    int z = 10;//stack trace(main stack frame , fun1 stack frame)
    fun2();//stack trace(main stack frame , fun1 stack frame , fun2 stack frame)
    StackTrace st = new StackTrace();
    foreach (var item in st.GetFrames())
    {
        Console.WriteLine(item.GetMethod().Name);
        //main
        //fun1
    }
}
public static void Main(string[] args)
{
    //can't instace methods without object
    //fun1();//error
    //so we use static methods
    fun2();//fun2
    int x = 10;
    int y = 20;
    fun1(10);
    StackTrace st = new StackTrace();
    foreach (var item in st.GetFrames())
    {
        Console.WriteLine(item.GetMethod().Name);
        //main
    }
}
```

> [!tip] on GetMethod() we can get method info like name , return type , parameters , attributes , ...etc

---

##### swap fucntion

```cs
//swap by value
public static void swap(int x, int y)
{
    int temp = x;
    x = y;
    y = temp;
}
//swap by reference
public static void swap(ref int x, ref int y)
{
    int temp = x;
    x = y;
    y = temp;
}

public static void Main(string[] args)
{
    int x = 10;
    int y = 20;
    swap(x, y);//call by value
    Console.WriteLine($"x = {x} , y = {y}");//x = 10 , y = 20

    swap(ref x, ref y);//call by reference
    //we have to use ref keyword in both function call and function definition

    Console.WriteLine($"x = {x} , y = {y}");//x = 20 , y = 10
}
```

> [!done] we can use reference for input and output
>
> ````cs
> public static void swap(ref int x, ref int y)
> {
>    int temp = x;
>    x = y;
>    y = temp;
> a = 10;//this will change the value of x in main
> //but x has to be initialized before using it in main
> }```
> ````

> [!error] Can't use variable as reference before initializing it

---

#### calc fucntion

> [!bug] by value
> sum , sub , mul , div will not be changed
>
> ```cs
> public static void calc(int x, int y, int sum, int sub, int mul, int div)
> {
>    sum = x + y;
>    sub = x - y;
>    mul = x * y;
>    div = x / y;
> }
> ```

> [!example] by reference
>
> - we have to initialize the variables before using them
> - input and output
>
> ```cs
> public static void calc( int x,  int y, ref int sum, ref int sub, ref int mul, ref int div)
> {
>    sum = x + y;
>    sub = x - y;
>    mul = x * y;
>    div = x / y;
> }
> ```

> [!done] 
>
> - no need to initialize the variable
> - output only
> - can't read the value of the variable before initializing it
> - we can declare the variable in the function call instead of declaring it before
> - in main `calc(x, y, out int sum, out int sub, out int mul, out int div);`
> - we have to use all out parameters or it throws error
>
> ```cs
> public static void calc( int x,  int y, out int sum, out int sub, out int mul, out int div)
> {
>    sum = x + y;
>    sub = x - y;
>    mul = x * y;
>    div = x / y;
> }
> public static void Main(string[] args)
> {
>    int x = 10;
>    int y = 20;
>    int sum, sub, mul, div;
>    calc(x, y, out sum, out sub, out mul, out div);
>    Console.WriteLine($"sum = {sum} , sub = {sub} , mul = {mul} , div = {div}");
> }
> ```

---

# Break

---

### send reference data

```cs
class Program
{
    public static void fun1(int[] arr)
    {
        //arr is reference to the array in main (copy of the address)
        arr[0] = 100;//this will change the value of arr[0] in main
        arr = NULL;//this will not change the value of arr in main this set arr in fun1 to NULL

        arr = new int[] { 1, 2, 3, 4, 5 };//this will create new array and change the value of arr in fun1 to point to it
        //but it will not change the value of arr in main
        foreach (var item in arr)
        {
            Console.WriteLine(item);
        }

    }
    public static void Main(string[] args)
    {
        int[] arr = { 1, 2, 3, 4, 5 };
        fun1(arr);
        Console.WriteLine(arr[0]);//100
    }
}
```

> [!done] sending reference data by value is the same as sending value data by reference
>
> - reference data by value : copy of the address in variable in function stack frame

---

### reference data by reference

```cs
class program
{
    public static void fun1(ref int[] arr2)
    {
        //arr2 is alias name to the array in main no variable in function stack frame
        arr2[0] = 100;//this will change the value of arr[0] in main
        arr2 = new int[] { 1, 2, 3, 4, 5 };//this will create new array and change the value of arr in MAIN to point to it
        arr2 = null;//this will change the value of arr in MAIN to NULL
        foreach (var item in arr2)
        {
            Console.WriteLine(item);
        }


    }
    public static void Main(string[] args)
    {
        int[] arr1 = { 1, 2, 3, 4, 5 };
        fun1(ref arr1);
        foreach (var item in arr1)
        //Error : as arr1 is NULL now
        {
            Console.WriteLine(item);
        }
        Console.WriteLine(arr1[0]);
    }
}
```

---

### Named Arguments

> [!faq] how to set which parameter to get which argument
>
> - by position

```cs
internal class Program
{
    public static void add(int x, int y,int z)

    {
         return x + y+z;
    }

    public static void Main(string[] args)
    {
        int a = 10;
        int b = 20;
        int c = add(y, x);//
        Console.WriteLine(z);
        add(x:a, y:b);

        add(x:a, b);//if order of parameters is the same we can pass b without name

        add(y:b, x:a, z:c);//we can change the order of parameters
        // and if changed we have to use names for all parameters
        }
}
```

---

#### Params

> [!bug] if we have alot of parameters and we dont want to use overload for each case
>
> > [!done] use params
> >
> > - we can pass nothing
> > - must be the last parameter (if we have more than one paramater)
> > - if we want to have at least 2 parameters
> > - `public static void add(int x,int y,params int[] arr)`
> > - we can pass any number of parameters
> >
> > ```cs
> > public static void add(params int[] arr)
> > {
> >    int sum = 0;
> >    foreach (var item in arr)
> >    {
> >        sum += item;
> >    }
> >    return sum;
> > }
> > ```
> >
> > ```cs
> > public static void Main(string[] args)
> > {
> >    int a = 10;
> >    int b = 20;
> >    int c = add(a, b);//30
> >    int d = add(a, b, c);//60
> >    int e = add();//0
> >    int f = add(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);//55
> > }
> > ```

---

```cs
namespace Demo1
{
    class Employee{
        int id;
        string name;
        float salary;
        public int Id { get; set; }
        public string Name { get =>name; set=> name = value; }//property => Capitalize first letter
        //field => small letter
        public int Salary { get;
        set
        {
            if (value > 3000)
            {
                salary = value;
            }
            else
            {
                salary = 3000;
            }
        };
        }
        //need to override ToString() method for every class (convention)
        public override string ToString()
        {
            return $"Id = {Id} , Name = {Name} , Salary = {Salary}";
        }
    }

}
```

```cs
//main
Employee e1;
e1.Salary = 1000;//Error object not created in heap yet
//e1 is just a reference no refering to any thing
Employee e1 = new Employee();
//1. create ref e1 in stack
//2. allocate memory in heap enough to store all fields of Employee
//3. call constructor of Employee
//4. update e1 to refer to the allocated memory in heap

e1.Salary = 1000;


Console.WriteLine(e1.ToString());//Id = 0 , Name = , Salary = 1000
Employee e2 = new Employee(1, "ahmed", 2000);
Console.WriteLine(e2.ToString());//Id = 1 , Name = ahmed , Salary = 2000

Employee e3 = e2;//reference equality
//e3 and e2 are refering to the same object in heap
e3.Salary = 3000;
Console.WriteLine(e2.ToString());//Id = 1 , Name = ahmed , Salary = 3000
Console.WriteLine(e3.ToString());//Id = 1 , Name = ahmed , Salary = 3000

//in case of struct
//value equality
// change in e3 will not affect e2




```

> [!warning] but if Employee is struct
>
> ```cs
> Employee e1;
> e1.Salary = 1000;//no error it will work just fine
> ```

> [!tip] `Employee e1 = new Employee();`
>
> 1.  create ref e1 in stack
> 2.  allocate memory in heap enough to store all fields of Employee
> 3.  call constructor of Employee (intialize according to parameters default values)
>
>     - any object created in heap will be initialized to default values
>
> 4.  update e1 to refer to the allocated memory in heap

> [!example] Before executing any line in main
>
> create ref e1 , e2 , e3 in stack
> then execute the lines in main one by one
> and update the references e1 , e2 , e3 to refer to the allocated memory in heap

#### == operator

> [!done] == operator exists by default in every class
> it works on the identity of the object not the state
> identity : the address of the object in heap
> state : the values of the fields of the object

> [!example] `Equals()` in case of class
>
> - Class : it works on the identity of the object not the state
> - Struct : it works on the state of the object not the identity
>
> > [!danger] struct inherits from ValueType which inherits from Object
>
> > [!done] class inherits from Object directly

```cs
//main
Employee e1 = new Employee(){Id = 1, Name = "ahmed", Salary = 2000};
Employee e2 = new Employee(){Id = 1, Name = "ahmed", Salary = 2000};
Console.WriteLine(e1 == e2);//false
//e1 and e2 are refering to different objects in heap
Console.WriteLine(e1.GetHashCode());//46546544
Console.WriteLine(e2.GetHashCode());//56465465

e2 = e1;
Console.WriteLine(e1 == e2);//true
//e1 and e2 are refering to the same object in heap
Console.WriteLine(e1.GetHashCode());//46546544
Console.WriteLine(e2.GetHashCode());//46546544

```

> [!danger] if we created constructor with parameters we have to create default constructor unlike struct
>
> - struct : if we created constructor with parameters we don't have to create default constructor(it exists by default)
>
> ```cs
> public Employee()
> {
> }
> public Employee(int id, string name, float salary)
> {
>    Id = id;//we use properties not fields to use validation we wrote in setter
>    Name = name;
>    Salary = salary;
> }
> ```

---

### Constructor Chaining

```cs
    class Employee
    {

        public Employee():this(0,"",0)

        {

        }
        public Employee(int id, string name, float salary)
        {
            Id = id;//we use properties not fields to use validation we wrote in setter
            Name = name;
            Salary = salary;
        }
        public Employee(int id, string name) : this(id, name, 3000)
        {
        }

    }
```

---

```cs
namespace Demo3
{
    class Complex
    {
        public int Real { get; set; }
        public int Img { get; set; }
        public Complex(int _real = 0, int _img = 0)
        {
            Real = _real;
            Img = _img;
        }
        public override string ToString()
        {
            return $"{Real} + {Img}j";
        }
        //overloading + operator to add two complex numbers
        public static Complex operator +(Complex c1, Complex c2)
        {
            return new Complex(c1.Real + c2.Real, c1.Img + c2.Img);
        }
        //overloading + operator to add complex number and int
        public static Complex operator +(Complex c1, int x)
        {
            return new Complex(c1.Real + x, c1.Img);
        }
        //overloading + operator to add int and complex number
        public static Complex operator +(int x, Complex c1)
        {
            //use the other operator
            return c1 + x;
        }
        //overloading == operator to compare two complex numbers
        //we have to overload != operator too for this to work
        public static bool operator ==(Complex c1, Complex c2)
        {
            return c1.Real == c2.Real && c1.Img == c2.Img;
        }
        //overloading != operator to compare two complex numbers
        public static bool operator !=(Complex c1, Complex c2)
        {
            return !(c1 == c2);
        }
    }
}
```

```cs
namespace Demo3
{
    internal class Program
    {
        static void Main(string[] args)
        {
	        Complex c1 = new Complex(1, 2);
           Complex c2 = new Complex(){Real = 30,Img =   20};
           Complex c3 = new Complex();
            c3 = c1 + c2;
            Console.WriteLine(c3);//31 + 22j

            Complex c4 = new Complex(){Real = 30,Img = 20};

        }
    }
}
```

> [!danger] == operator in struct DOES NOT exist by default unlike ==class==
> we have to overload it to use it
>
> > [!done] in class:
> >
> > - it works on the identity of the object not the state by default
> > - so we have to overload it to work on the state

> [!danger] can't overload = operator EVER

> [!example] we can check if two references are refering to the same object in heap using ReferenceEquals() method
><br>
> ```cs
> Complex c1 = new Complex(1, 2);
> Complex c2 = new Complex(){Real = 30,Img =   20};
> Complex c3 = new Complex();
> Console.WriteLine(ReferenceEquals(c1,c2));//false
> Console.WriteLine(ReferenceEquals(c1,c3));//false
> c3 = c1;
> Console.WriteLine(ReferenceEquals(c1,c3));//true
> ```

> [!bug] these operators can't be overloaded alone
>
> - == has to be overloaded with !=
> - has to be overloaded with <
> - = has to be overloaded with <=

> [!done] in C# we can don't need to overload prefix and postfix we can overload only one of them and the other will work
><br>
> ```cs
> public static Complex operator ++(Complex c1)
> {
>    return new Complex(c1.Real + 1, c1.Img + 1);
> }
> public static Complex operator --(Complex c1)
> {
>    return new Complex(c1.Real - 1, c1.Img - 1);
> }
> ```

> [!warning] can't overload += operator but it will work after overloading + operator

> [!error] can't overload dot (.) operator

> [!bug] can't access properties of null
> <br>
>
> ```cs
> Complex c1 = null;
> Console.WriteLine(c1.Real);//error: Object reference not set to an instance of an object.
> ```
>
> <br>
>
> > [!done] we can use null conditional operator
> > can't be overloaded
> > `??` null coalescing operator can't be overloaded as well
> >
> > ```cs
> > Complex c1 = null;
> > Console.WriteLine(c1?.Real);//0
> > ```

---
## #lab-4
- cast operator?? search
```cs
public static explicit operator int(Complex c1)
{
    return c1.Real;
}
```
```cs
//main
Complex c1 = new Complex(1, 2);
int x = (int)c1;
Console.WriteLine(x);//1
```
- implicit operator
```cs
public static implicit operator Complex(int x)
{
    return new Complex(x, 0);
}
```
```cs
//main
Complex c1 = new Complex(1, 2);
int x = (int)c1;
Console.WriteLine(x);//1
```

>[!example] to get date and time
>```cs
>DateTime dt = DateTime.Now;
>Console.WriteLine(dt);//9/29/2021 10:30:00 AM
>```
>
> - to get date only
> ```cs
> DateOnly d = DateOnly.FromDateTime(dt);
> Console.WriteLine(d);//9/29/2021
> ```
> - to get time only
> ```cs
> TimeOnly t = TimeOnly.FromDateTime(dt);
> Console.WriteLine(t);//10:30:00 AM
> ```

