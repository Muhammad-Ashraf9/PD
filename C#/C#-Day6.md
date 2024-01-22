### Inheritance

> [!tip] Inheritance importance:
>
> - Code reusability
> - Method overriding
> - Genralization

> [!tip] Shape class:

```cs
namespace Demo1
{
    public class Shape
    {
         int dim1;
         int dim2;
         public Dim1 {get=>dim1; set=>dim1=value;}
         public Dim2 {get=>dim2; set=>dim2=value;}

        public Shape(int _dim1, int _dim2)
        {
            dim1 = _dim1;
            dim2 = _dim2;
        }
        public Shape(int _dim1)
        {
            dim1 = _dim1;
            dim2 = _dim1;
        }
        public Shape()
        {
            dim1 = 1;
            dim2 = 1;
        }
        public void  print()
        {
            Console.WriteLine($"Dim1: {dim1} Dim2: {dim2}");
        }
        public override string ToString()
        {
            return $"Dim1: {dim1} Dim2: {dim2}";
        }
        public float CalculateArea()
        {
            return 0;
        }

    }
}

```

```cs
namespace Demo1
{
  internal  class Program
    {
        static void Main(string[] args)
        {

            Shape s1 = new Shape(10, 20);
            s1.print();//Dim1: 10 Dim2: 20
            Shape s2 = new Shape(10);
            s2.print();//Dim1: 10 Dim2: 10
            Shape s3 = new Shape();
            s3.print();//Dim1: 1 Dim2: 1

            Shape s4 = new Shape(10, 20){Dim1=100, Dim2=200};//after intialization it will set the values for dim1 and dim2 to 100 and 200
            s4.print();//Dim1: 100 Dim2: 200

            Console.WriteLine(s4.CalculateArea());//0
            //we don't know the area of the shape
        }
    }


}
```

###### class Rectangle

> [!bug] Can only inherit from one class
> `class Rectangle : Shape, Shape2`
>
> > [!danger] Error: `error CS1721: Class 'Rectangle' cannot have multiple base classes: 'Shape' and 'Shape2'`

> [!info] Shape inherits from object class Implicitly
> if we don't specify the base class it will inherit from object class
> Rectangle inherits from Shape that inherits from object class

> [!done] Rectangle class: has all the members of Shape class and object class like ToString() and Equals() and GetHashCode(),CalculateArea() and print() and Dim1 and Dim2

```cs
namespace Demo1
{
    public class Rectangle : Shape //inheritance
    //Rectangle inherits from Shape
    // if it was in other namespace we need to add using namespaceName; or namespaceName.className
    {
       //constructor doesn't get inherited
       //so we need to create a constructor for Rectangle class

       public Rectangle(int _dim1, int _dim2){//this calls the constructor of Shape class before executing the constructor of Rectangle class

              dim1 = _dim1;//Error: 'Shape.dim1' is inaccessible due to its protection level
              //we use properties to access or give them other access modifiers(protected)
                Dim1 = _dim1;
                Dim2 = _dim2;

       }
       //performance issue: it sets the values twice
         //we can use the constructor of Shape class
         // in c++ we used to call base class by name as it can inherit from multiple classes
            //in c# we use :base() to call the constructor of base class
            //base() calls the default constructor of base class
            public Rectangle(int _dim1, int _dim2):base(_dim1, _dim2)
            {
                //this calls the constructor of Shape class before executing the constructor of Rectangle class and sets the values of dim1 and dim2 ONCE
            }
            public void print()
            {
                Console.Write("Rectangle");
                base.print();//Dim1: 1 Dim2: 1
            }
    }
}
```

> [!danger] Struct can only inherit from interface
>
> - `struct Rectangle : Shape
> - `Error: `error CS0527: Type 'Rectangle' in interface list is not an interface`
> - struct can not inherit from other struct or class

> [!danger] if we didn't create a constructor for Rectangle class it will call the ==default constructor== of Shape class by default
>
> - if it was no parameterless constructor in base class it will throw an error.

> [!example] in case of 2 parameters constructor in Rectangle class
>
> - before calling the constructor of Rectangle class it will call the default constructor of Shape class

> [!warning] derived class calls the paramterless constructor of base class by default
>
> - we have to specify the constructor of base class to call it -`Rectangle(int _dim1, int _dim2):base(_dim1, _dim2)`
> - `Rectangle(int _dim1, int _dim2):base(_dim1)`
> - Default :`Rectangle(int _dim1, int _dim2):base()`

```cs
//main
public static void Main(string[] args)
        {
            Rectangle r1 = new Rectangle(10, 20);//Error we don't have a constructor that takes 2 arguments
            Rectangle r2 = new Rectangle(10, 20){Dim1=100, Dim2=200};
            r2.print();//Dim1: 100 Dim2: 200
            //this calls the constructor of Shape class then Rectangle class then sets the values of dim1 and dim2 to 100 and 200
        }
```

###### Triangle class

```cs
class Triangle : Shape
    {
        public Triangle(int _dim1 = 1, int _dim2 = 1):base(_dim1, _dim2)
        // base class has to have a constructor that takes 2 arguments
        {

        }
    //   public void print()

        {
            Console.Write("Triangle");
            base.print();//Dim1: 1 Dim2: 1
        }

    }
```

```cs
//main
public static void Main(string[] args)
        {
            Rectangle r1 = new Rectangle(10, 20);
            Triangle t1 = new Triangle();
            t1.print();//Dim1: 1 Dim2: 1
            Triangle t2 = new Triangle(10);
            t2.print();//Dim1: 10 Dim2: 10
            Triangle t2 = new Triangle(10, 20);
            t2.print();//Dim1: 10 Dim2: 20
            //to make print method print Tringle before the values we need to override it

            Shape s1 = t1;//generalization
            s1.print();//Dim1: 1 Dim2: 1
            s1 = r1;
            s1.print();//Dim1: 10 Dim2: 20
            //this calls the Shape class print method
            //we need it to call children class print method
            // use Virtual and override keywords
        }
```

> [!warning] we have to use `virtual` and `override` keywords to call the children class print method
>
> - `virtual` : to allow the children class to override the method
> - `override` : to override the method in children class

```cs
//Shape class
//virtual - override - abstract
public virtual void print()
        {
            Console.WriteLine($"Dim1: {dim1} Dim2: {dim2}");
        }
```

```cs
//Triangle class
public override void print()
        {
            Console.Write("Triangle");
            base.print();//Dim1: 1 Dim2: 1
        }
```

```cs
//Rectangle class
public override void print()
        {
            Console.Write("Rectangle");
            base.print();//Dim1: 1 Dim2: 1
        }
```

> [!tip] the compiler gives us a warning if we don't use `override` keyword for virtual method in base class
>
> - if we want to create `new` method in children class we can use `new` keyword
>   > [!danger] `new` calls the base class method if we have a reference of base class and object of children class
>   > `Shape s1 = t1; s1.print();` calls the print method of Shape class

```cs
//Triangle class
public new void print()
        {
            Console.Write("Triangle");
        }
```

> [!done] late binding: we can call the method of children class if we have a reference of base class and object of children class
> `Shape s1 = t1; s1.print();` calls the print method of Triangle class

> [!info] early binding: we can call the method of base class if we have a reference of base class and object of base class
> `Shape s1 = new Shape(); s1.print();` calls the print method of Shape class

```cs
//main
//now we can call the print method of children class
public static void Main(string[] args)
        {
            Rectangle r1 = new Rectangle(10, 20);
            Triangle t1 = new Triangle();
            Shape s1 = t1;//generalization
            s1.print();//Triangle Dim1: 1 Dim2: 1
            s1 = r1;
            s1.print();//Rectangle: Dim1: 10 Dim2: 20


            object o1 = t1;
            //if we have the override
            o1.ToString();//Triangle Dim1: 1 Dim2: 1
            //no override
            //calls object class ToString() method
            o1.ToString();// Demo1.Triangle

        //1. check if Object class ToString() is virtual and overriden in Shape class
        //2. if it was => check if Shape class ToString() is virtual and overriden in Triangle class

        // no override in Triangle class
        // virtuality stops at Shape class
        // calls Shape class ToString() method
        // prints Dim1: 1 Dim2: 1 NOT Triangle Dim1: 1 Dim2: 1
        }
```

> [!tip] we have class ABC inherits from class Triangle
> we can stop the virtuality at Triangle class by using `new` keyword for print method in ABC class
>
> > [!danger] `new` is the opposite of `virtual` and `override`
> > we can not override a method that is not virtual => `new`
>
> > [!done] we can use `new virtual` to stop the virtuality at Triangle class and allow children classes of ABC class to override the print method

```cs
//class ABC
class ABC : Triangle
    {

        public new virtual void print()
        //this stops the virtuality at Triangle class
        // allows children classes of ABC class to override the print method
        {
            Console.Write("ABC");
        }
    }
```

```cs
//class XYZ inherits from ABC
class XYZ : ABC
    {
        public override void print()
        {
            Console.Write("XYZ");
        }
    }
```

```cs
//main
public static void Main(string[] args)
        {
            Rectangle r1 = new Rectangle(10, 20);
            Triangle t1 = new Triangle();
            Shape s1 = t1;//generalization
            s1.print();//Triangle Dim1: 1 Dim2: 1
            s1 = r1;
            s1.print();//Rectangle: Dim1: 10 Dim2: 20
        }

```

> [!warning] in C++ we couldn't stop the virtuality once we used `virtual` keyword it will be virtual in all children classes
>
> > [!done] in C# we can stop the virtuality by using `new` keyword
> > and allow children classes to override the method by using `new virtual` keywords

###### Abstract class

> [!tip] Abstract class can not be instantiated
>
> - can not create an object of abstract class
>   - `Shape s1 = new Shape();`
>   - Error: `error CS0144: Cannot create an instance of the abstract class or interface 'Shape'`
> - can declare a reference of abstract class - `Shape s1;` - `Shape s1 = new Rectangle(10, 20);`
>   > [!danger] Concrete class: can be instantiated
>   >
>   > - can NOT have abstract methods

```cs
//Shape class
abstract class Shape
    {
        int dim1;
        int dim2;
        public int Dim1 { get => dim1; set => dim1 = value; }
        public int Dim2 { get => dim2; set => dim2 = value; }

        public Shape(int _dim1, int _dim2)
        {
            dim1 = _dim1;
            dim2 = _dim2;
        }
        public Shape(int _dim1)
        {
            dim1 = _dim1;
            dim2 = _dim1;
        }
        public Shape()
        {
            dim1 = 1;
            dim2 = 1;
        }
        public void print()
        {
            Console.WriteLine($"Dim1: {dim1} Dim2: {dim2}");
        }
        public override string ToString()
        {
            return $"Dim1: {dim1} Dim2: {dim2}";
        }
        public abstract float CalculateArea();
        //we don't know the area of the shape
        //we need to override it in children classes
    }
```

```cs
//Recaangle class
class Rectangle : Shape
    {
        public Rectangle(int _dim1, int _dim2) : base(_dim1, _dim2)
        {

        }
        public override float CalculateArea()
        //we have to override the abstract method from base class
        //otherwise Rectangle class will be abstract
        //we will need to add abstract to Rectangle class

        {
            return Dim1 * Dim2;
        }
    }
```

```cs
//Triangle class
class Triangle : Shape
    {
        public Triangle(int _dim1 = 1, int _dim2 = 1) : base(_dim1, _dim2)
        {

        }
        public override float CalculateArea()
        {
            return .5f * Dim1 * Dim2;
        }
    }
```

```cs
//main
Shape s1 = new Rectangle(10, 20);
Console.WriteLine(s1.CalculateArea());//200
//use CalculateArea() method of Rectangle class
s1 = new Triangle(10, 20);
Console.WriteLine(s1.CalculateArea());//100
//use CalculateArea() method of Triangle class
```

###### sealed class

> [!warning] sealed class: can not be inherited from
> like String class: `public sealed class String`

```cs
//sealed class
class Abc: String
//Error: 'String': cannot derive from sealed type 'String'
    {

    }
```

> [!tip] sealed method: can not be overriden
> has to be with override keyword
> like ToString() method in String class: `public sealed override string ToString()`

```cs
//sealed method
//Triangle class
public sealed override void print()
//this stops the virtuality at Triangle class
// doesn't allow children classes to override the print method
        {
            Console.Write("Triangle");
        }
```

> [!tip] sealed vs new vs new virtual vs override

---

# Break

---

```cs
public static void Main(string[] args)
        {
            // Reference of base class can refer to object of derived class
            // but the opposite is not true
            Rectangle r1 = new Rectangle(10, 20);
            Triangle t1 = new Triangle();
            Shape s1 = t1;//works fine
            s1.print();
            s1 = r1;
            s1.print();//works fine
            s1 = t1;

            r1 = (Rectangle)s1;//Error: InvalidCastException: Unable to cast object of type 'Demo1.Triangle' to type 'Demo1.Rectangle'.
// triangle and rectangle has the same base class but we can not cast triangle to rectangle or vice versa
            s1 = r1;
            t1 = (Triangle)s1;//Error:

        //we can cast the reference of base class to object of derived class
        ABC abc = new ABC();
        t1 = (Triangle)abc;
        t1 =
        XYZ xyz = (XYZ)t1;
        }

        //is operator
        //checks if the object is of specific type
        //returns true or false
        if(s1 is Triangle)
        // if s1 is Triangle or derived from Triangle(Abc, XYZ)

        {
            Triangle t2 = (Triangle)s1;
        }
        else
        {
            Console.WriteLine("s1 is not Triangle");
        }
        //as operator
        //if the object is of specific type it will cast it to that type and return it
        //if not it will return null
        s1 = new XYZ();
        Triangle t3 = s1 as Triangle;
        if(t3 == null)
        {
            Console.WriteLine("s1 is not Triangle");
        }
        else
        {
            Console.WriteLine("s1 is Triangle");
        }

        //to cast there has to be a relation between the types
        //we can not cast Triangle to string
        Triangle t4 = new Triangle();
        string s2 = t4 as string;//Error: Cannot convert type 'Demo1.Triangle' to 'string'

        int a = 10;
        int b = 20;
        float c = 30;
        a = z as int;//Error:

        //but if they were nullable types we can cast them
        int? x = 10;
        int? y = 20;
        float? z = 30;
        a = z as int?;//works fine
```

###### Equals() method

> [!tip] Equals() method

```cs
//main
public static void Main(string[] args)
        {
            Rectangle r1 = new Rectangle(10, 20);
            Rectangle r1 = new Rectangle(10, 20);

            Console.WriteLine(r1.Equals(r2));//False
            //object class Equals() method compares the references
            //we need to override it in Rectangle class to compare the values


        }
```

> [!tip] override Equals() method

```cs
//Rectangle class
public override bool Equals(object? obj)
        {
            Rectangle r = obj as Rectangle;
            if (r == null)
            {
                return false;
            }
            return Dim1 == r.Dim1 && Dim2 == r.Dim2;
        }
```

> [!info] try it in main

```cs
//main
public static void Main(string[] args)
        {
            Rectangle r1 = new Rectangle(10, 20);
            Rectangle r1 = new Rectangle(10, 20);

            Console.WriteLine(r1.Equals(r2));//True
            if(r1.Equals(r2))
            {
                Console.WriteLine("r1 and r2 are equal");
            }
            else
            {
                Console.WriteLine("r1 and r2 are not equal");
            }
          if (r1.Equals(null))
            {
                Console.WriteLine("r1 and r2 are equal");
            }
            else
            {
                Console.WriteLine("r1 and r2 are not equal");
            }

            if (r1.Equals("abc"))
            {
                Console.WriteLine("r1 and r2 are equal");
            }
            else
            {
                Console.WriteLine("r1 and r2 are not equal");
            }

          Triangle t1 = new Triangle();
          Triangle t2 = new Triangle();
          Console.WriteLine(t1.Equals(t2));//false
          //we need to override Equals() method in Triangle class
          if(t1.Equals(t2))
          {
              Console.WriteLine("t1 and t2 are equal");
          }
          else
          {
              Console.WriteLine("t1 and t2 are not equal");
          }
        }
```

> [!tip] override Equals() method in Triangle class

```cs
//Triangle class
public override bool Equals(object? obj)
       {
           Triangle t = obj as Triangle;
           if (t == null)
           {
               return false;
           }
           return Dim1 == t.Dim1 && Dim2 == t.Dim2;
       }
```

```cs
//main
public static void Main(string[] args)
        {
          Triangle t1 = new Triangle();
          Triangle t2 = new Triangle();
          Console.WriteLine(t1.Equals(t2));//true

          if(t1.Equals(t2))
          {
              Console.WriteLine("t1 and t2 are equal");
          }
          else
          {
              Console.WriteLine("t1 and t2 are not equal");
          }
          XYZ xyz = new XYZ();
            Console.WriteLine(t1.Equals(xyz));//true
            //but this is wrong as XYZ is not a Triangle
            // we need to check if the object is of type Triangle

        }
```

> [!tip] add extra check in Equals() method in Triangle class
>
> - check if the object is null

```cs
//Triangle class
public override bool Equals(object? obj)
        {
            //we want to check if the object is null
            // as it throws an exception in (obj.GetType() )
            if(obj == null)
            {
                return false;
            }
            if(this.GetType() != obj.GetType())
            {
                return false;
            }
            //can't use is operator as it checks if the object is of specific type or (derived) from it  so it will return true for XYZ

                Triangle t = obj as Triangle;
                if (t == null)
                {
                    return false;
                }
                return Dim1 == t.Dim1 && Dim2 == t.Dim2;

            return false;
        }
```

```cs
//main
public static void Main(string[] args)
        {
          Triangle t1 = new Triangle();
            Triangle t2 = new Triangle();
            Console.WriteLine(object.ReferenceEquals(t1, t2));//false
            Console.WriteLine(t1.GetHashCode());//-1521134295
            Console.WriteLine(t2.GetHashCode());//-1521134888
            //not the same hash code
            t2 = t1;//reference equality
            Console.WriteLine(object.ReferenceEquals(t1, t2));//true
            Console.WriteLine(t1.GetHashCode());//1521134295
            Console.WriteLine(t2.GetHashCode());//1521134295
            //the same hash code
            //object.Equals()
            Triangle t3 = new Triangle();
            Triangle t4 = new Triangle();
            Console.WriteLine(object.Equals(t3, t4));// true


```

> [!done] object.Equals()
>
> - this calls the Equals() method of the instance => the equals we override (state equality)
> - if it was not overriden it will call the Equals() method of object class (reference equality)

---

#### Stack class

```cs
    class Stack
    {
        int[] arr;
        int size;
        int tos;
        public Stack(int _size = 5)
        {
            size = _size;
            arr = new int[size];
            tos = -1;
        }
        public int Size { get=>size; }
        public bool IsFull()
        {
            return tos == size - 1;
        }
        public bool IsEmpty()
        {
            return tos == -1;
        }
        public void Push(int x)
        {
            if (IsFull())
            {
                // Console.WriteLine("Stack is full");
                // return;

                //create exception for stack overflow
                //StackFullException
                //has to inherit from Exception class
                throw new StackFullException(size);
                //developer can handle the exception try catch
                //or check if the stack is full before pushing
            }
            tos++;
            arr[tos] = x;
        }
        public int Pop()
        {
            if (IsEmpty())
            {
                // Console.WriteLine("Stack is empty");
                // return -1;//but it can be a valid value in the stack

                //create exception for stack underflow
                //StackEmptyException
                //has to inherit from Exception class
                throw new StackEmptyException();
                //developer can handle the exception try catch
                //or check if the stack is empty before popping
            }
            int x = arr[tos];
            tos--;
            return x;
        }
    }
```

> [!tip] StackFullException class

```cs
class StackFullException : Exception
    {
        public StackFullException(int size) : base($"Stack is full, size: {size}")
        {

        }

    }
```

> [!tip] StackEmptyException class

```cs
class StackEmptyException : Exception
    {
        public StackEmptyException() : base("Stack is empty")
        {

        }

    }
```

```cs
//main
public static void Main(string[] args)
        {
            Stack s1 = new Stack();
            //we use protective programming to avoid throwing exceptions
            if(!s1.IsEmpty())
            {
                Console.WriteLine(s1.Pop());
            }

            try{
                s1.Pop();
            }catch(StackFullException e)
            {
                Console.WriteLine(e.Message);
            }
            s1.Push(10);
            s1.Push(20);
            s1.Push(30);
            s1.Push(40);
            s1.Push(50);
            s1.Push(60);//StackFullException: Stack is full, size: 5
            Console.WriteLine(s1.Pop());//50
            Console.WriteLine(s1.Pop());//40
            Console.WriteLine(s1.Pop());//30
            Console.WriteLine(s1.Pop());//20
            Console.WriteLine(s1.Pop());//10
            Console.WriteLine(s1.Pop());//StackEmptyException: Stack is empty
        }
```

---

#### #lab-6

- stack
- queue
- shape
