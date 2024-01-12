> [!tip] value types vs reference types
>
> - value types: in stack (int, float, double, char, bool, struct)
> - reference types: in heap and reference to it in stack

> [!tip] `int[] arr` => arr reference in stack
>
> > [!tip] `arr = new int[5]` => new int[5] in heap

> [!tip] `string s = "hello"` => s reference in stack
> varaible doesn't contain the data,but reference to the data

> [!tip] `int[] arr1 = new int[5]{10,20,30,40,50}` => arr1 reference in stack and data in heap (1,2,3,4,5)
> `int[] arr2 = new int[3]{1,2,3}` => arr2 reference in stack and data in heap (1,2,3)
>
> > [!bug] `arr2 = arr1` => reference equality
> > `arr1[0] = 100` => arr1 (100,20,30,40,50)
> > `arr2[0] => 100` => arr2 (100,20,30,40,50)

> [!example] Garbage Collector
>
> - when the object is no longer referenced, it will be collected by the garbage collector

> [!warning] `object x = x;`
>
> - overhead data in heap
> - depend on machine architecture (32 bit or 64 bit)
> - 32 bit: 4 bytes * 2 vars (2 variables each one 4 bytes)
> - 64 bit: 8 bytes * 2 vars (2 vars (2 variables each one 8 bytes)

> [!warning] to create value type we use:
>
> - struct
> - enum

> [!bug] to create reference type we use:
>
> - class
> - interface
> - delegate

---

```cs
namespace Demo1{
    class Program{
        static void Main(string[] args){
            #region valuetype
            //used for grouping related code
            int x = 10;
            int y = 20;
            x = y; //value equality
            Console.WriteLine(y); // 20
            Console.WriteLine(x); // 20
            #endregion
            #region referencetype
            // int[] arr1; //reference in stack
            // no object created yet ( use new keyword to create object)
            int[] arr1 = new int[5]; //new int[5] in heap intialize with default value of int (0)
            Console.WriteLine(default(int)); // 0

            int[] arr2 = new int[5]{1,2,3}; // compilation error as we have to initialize all elements
            int[] arr3 = new int[5]{1,2,3,4,5}; // ok

            for(int i = 0; i < 5; i++){
                Console.WriteLine(arr1); // System.Int32[]
                Console.WriteLine(arr1[i]); // 0 0 0 0 0
            }

            int[] arr4 = new int[2]{1,2};
            for(int i = 0; i < 2; i++){
                Console.WriteLine(arr4[i]); // 1 2
            }

            arr3 = arr4; // reference equality
            // we can use = operator with different array sizes

            arr3[0] = 555555;
            Console.WriteLine(arr3[0]); // 555555
            Console.WriteLine(arr4[0]); // 555555

            #endregion
        }
    }
}
```

> [!danger] access out of range index in array
>
> - `int[] arr = new int[5]{1,2,3,4,5};`
> - `Console.WriteLine(arr[5]);` Error -
> - System.IndexOutOfRangeException

---

## String

> [!tip] string is reference type but act as value type

```cs
namespace Demo1{

    class Program{
        static void Main(string[] args){
            // string is reference type but act as value type
            string s1 = "aly";
            string s2 = "mohamed";
            s1 = s2; // act as value equality
            Console.WriteLine(s1); // mohamed
            Console.WriteLine(s2); // mohamed
            s2 = "sara";
            Console.WriteLine(s1); // mohamed
            Console.WriteLine(s2); //  sara
        }
    }
}
```

> [!example] string is immutable object
> to change the value of string we have to create new string object and assign it to the variable

> [!info] `s1 = s2` => s1 reference to mohamed in heap as it already exist
> `s2 = "sara"` => s2 create new object in heap and reference to it

> [!warning] `s1 +="khaled"` => create new object in heap and reference to it
>
> > [!tip] wait for garbage collector to delete the unreferenced object
> > it works when it detects the remaining memory asigned is about to run out, it works in other thread but stop the program so we can use StringBuilder

```cs
namespace Demo1{
    class Program{
        static void Main(string[] args){
           String s1 = "aly";
           for(int i = 0; i < 1000000; i++){
               s1 += i.ToString();
           }
        }
    }
}
```

> [!info] StringBuilder
>
> - mutable object
> - used to create string with large number of concatenation
> - it is faster than string

> [!Danger] Task
>
> - count number of 1 from 0 to 1000000

```cs
namespace Demo1{
    class Program{
        static void Main(string[] args){
            String s1 = "aly";
            s1[0] = 'm'; // compilation error as string is immutable

            int x = 10,y = 20,z = 0;
            Console.WriteLine(x); // 10
            Console.WriteLine(y); // 20
            Console.WriteLine("x = {0} y = {1}",x,y); // x = 10 y = 20
            //formatted string
            Console.WriteLine("x = {0} y = {1}",y,x,z);
            //runs normally but z is not printed
            Console.WriteLine("x = {0} y = {4}",x,y); // error: System.FormatException: Index (zero based) must be greater than or equal to zero and less than the size of the argument list.

            //string interpolation
            Console.WriteLine($"x = {x} y = {y}"); // x = 10 y = 20
            //more readable and less error prone


            float salary = 1000.5452f;
            Console.WriteLine(salary); // 1000.5452
            Console.WriteLine($"salary = {salary:c}"); // salary = $1,000.55
            //c: currency
            Console.WriteLine($"salary = {salary:c2}"); // salary = $1,000.55
            //c2: currency with 2 decimal places

            Console.WriteLine($"y = {y:d5}"); // y = 00020
            //d5: decimal with 5 digits

            Console.WriteLine($"salary = {salary:f3}"); // salary = 1000.545
            //f3: float with 3 decimal places

            //we can use escape characters \n \t \a: alert sound
            Console.WriteLine("hello\nworld"); // hello
                                                // world
            Console.WriteLine("hello\tworld"); // hello   world
            Console.WriteLine("hello\a"); // alert sound

            //verbatum string
           // @: ignore escape characters
            string path = @"c:\users\aly\desktop\c#";
            Console.WriteLine(path); // c:\users\aly\desktop\c#



        }
    }
}
```

---

### ReadLine

> [!tip] ReadLine
>
> - read string from user
> - return string
> - read until user press enter
> - `Console.ReadLine()`
> - to convert to int we use `int.Parse(Console.ReadLine())`
> - same with float, double
>   > [!danger] `int.Parse("10.5")` => error: System.FormatException: Input string was not in a correct format.

> [!tip] `Console.Read()`
>
> - read one character from user
> - return int

```cs
namespace Demo1{
    class Program{
        static void Main(string[] args){
            char ch = 'a';
            ch = char.Parse(Console.ReadLine());//Error: when user enter more than one character
            //print ascii code of ch
            int y = ch;
            Console.WriteLine(y); // 97


        }
    }
}
```

> [!example] `Console.ReadKey()`
>
> - return ConsoleKeyInfo
> - this used to read when user press control key or shift key or alt key with another key

```cs
namespace Demo1{
    class Program{
        static void Main(string[] args){
            ConsoleKeyInfo key = Console.ReadKey();
            Console.WriteLine(key.Key); // A
            Console.WriteLine(key.KeyChar); // A
            Console.WriteLine(key.Modifiers); // 0
            Console.WriteLine(key.KeyChar); // A

        }
    }
}
```

---

### Break

---

# foreach

```cs
namespace Demo1{
    class Program{
        static void Main(string[] args){

        int[] arr = new int[5]{1,2,3,4,5};
        int[] arr = new int[]{1,2,3,4,5};//we can skip writing the size in initialization
                // we can get the size of array using arr.Length
        foreach(int x in arr){
                    //can't change the value of x
                        Console.WriteLine(x); // 1 2 3 4 5
                }
                // to change the values in the array we have to use for loop
                for(int i = 0; i < arr.Length; i++){
                        arr[i] = 0;
                }
                //we can declare array with initialization
                int[] arr2 = {1,2,3,4,5};
                // can't reinitialize array
                // arr2 = {1,2,3,4,5}; // error
                //but we can create new array and assign it to arr2
                arr2 = new int[]{1,2,3,4,5};
                // in C# 12
                int[] arr3 = [1,2,3,4,5];


                // reference equality
                int[] arr4 = arr2;//both reference to the same object
                Console.WriteLine(arr4.GetHashCode()); // 46104728
                Console.WriteLine(arr2.GetHashCode()); // 46104728
                // GetHashCode: return the hash code of the object

                //to copy the values of arr2 to arr5
                //clone: create new object with the same values
                int[] arr5 = (int[])arr2.Clone();
            }
        }
}
```

> [!example] State vs Identity
>
> ```cs
> int[] arr1 = new int[5]{1,2,3,4,5};
> int[] arr2 = new int[5]{1,2,3,4,5};
> int[] arr3 = (int[])arr2.Clone();
> Console.WriteLine(arr1.GetHashCode()); // 46104728
> Console.WriteLine(arr2.GetHashCode()); // 46104567
> Console.WriteLine(arr3.GetHashCode()); // 46104577
> //different hash code as they are different objects
> //same state but different identity
> ```

---

#### Multidimensional Array

```cs
int [,,]//3D array
int [,,,]//4D array
int [,,,,]//5D array

    //2D array
    int[,] arr = new int[2,3]{{1,2,3},{4,5,6}};//2 rows 3 columns
    //or
    int[,] arr = {{1,2,3},{4,5,6}};
    Console.WriteLine(arr.length); // 6 (2*3)
    // number of elements in the array
    Console.WriteLine(arr.GetLength(0)); //(1 dimension) 2 rows
    Console.WriteLine(arr.GetLength(1)); //(2 dimension) 3 columns
    Console.WriteLine(arr.Rank); // 2 (number of dimensions)

    int [] arr = {1,2,3,4,5,6,7,8,9,10,11,12};
    Console.WriteLine(arr.Rank); // 1

    int [,] arr = new int[2,3]{{1,2,3},{4,5,6}};
    Console.WriteLine(arr[0,0]); // 1
    Console.WriteLine(arr[0,1]); // 2

    for(int i = 0; i < arr.GetLength(0); i++){
        for(int j = 0; j < arr.GetLength(1); j++){
            Console.WriteLine($"{arr[i,j]}\t");
        }
        Console.WriteLine();
    }

    //jagged array
    // number of columns is not fixed
    int [][] arr = new int[3][];

    arr[0] = new int[2]{1,2};
    arr[1] = new int[3]{1,2,3};
    arr[2] = new int[4]{1,2,3,4};
    for(int i = 0; i < arr.GetLength(0); i++){
        for(int j = 0; j < arr[i].Length; j++){
            Console.WriteLine($"{arr[i][j]}\t");
        }
        Console.WriteLine();
    }


```

> [!warning] jagged array vs Rectangular Array
>
> - jagged array: number of columns is not fixed
> - Rectangular multidimensional array: number of columns has to be fixed
> - jagged array is not contigous in memory as it creates new object for each row
> - Rectangular Array is contigous in memory

---

### Array Sort

```cs
int[] arr = [5,77,2,12,7,4]
Array.Sort(arr);// what algorithm is used?  -> introspective sort(quicksort, heapsort, and insertion sort)
for(int i = 0; i < arr.Length; i++){
    Console.WriteLine(arr[i]);
}

```

---

#### var

```cs
// C# is type safe language, we have to specify the type of the variable before compilation
int x = 10;
x = "hello"; // error
// but we can use var keyword
//let the compiler infer the type of the variable from the initial value
//MUST initialize
var y = 10;
Console.WriteLine(y.GetType()); // System.Int32
var z = 5.5;
Console.WriteLine(z.GetType()); // System.Double
var z = 53.5f;
Console.WriteLine(z.GetType()); // System.Single

var z = "hello";
Console.WriteLine(z.GetType()); // System.String

var arr = new int[5]{1,2,3,4,5};
Console.WriteLine(arr.GetType()); // System.Int32[]

var y = 10;
y = 2.3; // error

var r = Console.ReadLine();
Console.WriteLine(r.GetType()); // System.String
var r = int.Parse(Console.ReadLine());
Console.WriteLine(r.GetType()); // System.Int32
var r = char.Parse(Console.ReadLine());
Console.WriteLine(r.GetType()); // System.Char


int [] arr1 = new int[5]{1,2,3,4,5};
 double [] arr2 = new double[5]{1,2,3,4,5};
 foreach(var x in arr1){
     Console.WriteLine(x.GetType()); // System.Int32
 }
 foreach(var x in arr2){
     Console.WriteLine(x.GetType()); // System.Double
 }

```

> [!tip] var
> var implicit Typed local variable
> used only for local variables in functions
> have to be initialized
> can't be used as return type of function

---

> [!done] Linq: Language Integrated Query
>
> - used to query data from different data sources
> - we can query data from array, list, database, xml, json, csv
> - we can query data from different data sources using the same syntax

---

```cs
    int x = 1;
    // if(x = 1){//error as x is not boolean
    if(x == 1){
        Console.WriteLine("hello");
    }


```

---

### app

l1:

```cs
    int x, y;
    char op;
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    op = char.Parse(Console.ReadLine());
    switch(op){
        //in can switch on int, char, string
        case '+':
            Console.WriteLine($"{x} + {y} = {x+y}");
            break;
        // case '*':
        // Console.WriteLine($"{x} * {y} = {x*y}");
        // case '-':
        // Error: Control cannot fall through from one case label ('case "*"') to another
        case '-':
        //no code

        case '*':
            Console.WriteLine("subtraction or multiplication");
            //we can use goto to jump to another case
            goto case '+';
            break;
        case '*':
            Console.WriteLine($"{x} * {y} = {x*y}");
            break;
        case '/':
            Console.WriteLine($"{x} / {y} = {x/y}");
            break;
        default:
            Console.WriteLine("invalid operator");
            break;//last case has to end with break

    }
    goto l1; ///////// NEVER USE GOTO
```

```cs
    int x, y;
    char op;
    x = int.Parse(Console.ReadLine());
    y = int.Parse(Console.ReadLine());
    op = char.Parse(Console.ReadLine());
    switch(op){
        //case grouping
        case 1:
        case 2:
        case 3:
            Console.WriteLine("1,2,3");
            break;
        case 4:
        case 5:
        case 6:
            Console.WriteLine("4,5,6");
            break;
        default:
            Console.WriteLine("invalid operator");
            break;//last case has to end with break
    }


```

---

```cs
    object x = 10;
    Console.WriteLine(x.GetType()); // System.Int32
    x = "hello";
    Console.WriteLine(x.GetType()); // System.String

```

---

```cs
    int x = 10, y = 20;
    int z = x / y;//int/int => int
    float z = x / y;//int/int => int
    float z = x*1.0/y;//int*float/int => double Error
    float z = x*1.0f/y;//int*float/int => float

    byte b1 = 2, b2 = 3;
    b1 + b2;//byte + byte => int
    byte b3 = b1 + b2;//error : cannot implicitly convert type 'int' to 'byte'. An explicit conversion exists (are you missing a cast?)

    int b3 = b1 + b2;//ok
```

---

# #lab2

- C# D02
- `Stopwatch sw = new Stopwatch();`
- `sw.Start();`
- `sw.Stop();`
