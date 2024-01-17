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
> - if it was no paramterless constructor in base class it will throw an error:

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
            //if we have the ovveride
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
        //we need to add abstract to Rectangle class

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

>[!warning] sealed class: can not be inherited from
>like String class: `public sealed class String`

```cs
//sealed class
class Abc: String
//Error: 'String': cannot derive from sealed type 'String'
    {

    }
```

>[!tip] sealed method: can not be overriden
> has to be with override keyword 
>like ToString() method in String class: `public sealed override string ToString()`

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
>[!tip] sealed vs new vs new virtual  vs override

```cs