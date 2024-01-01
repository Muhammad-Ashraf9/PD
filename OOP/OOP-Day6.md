## relations between objects

- Composition
- Aggregation
- Association
- Inheritance

### Composition

> [!Note] Composition is a "part of or consists of" relationship
> uni directional relationship
> room has a wall not the other way around

> [!Example] Room and Wall
> if the room is destroyed the walls are destroyed too

> [!Example] Human and Heart

### Aggregation

> [!Note] Aggregation is a "has a" relationship
> uni directional relationship

> [!Example] Match (teams,location,referee,time)
> the match depends on the teams but if the match is destroyed the teams are not destroyed

> [!Example]  Room and projectors

### Association

> [!Note] Association is a "deal with" relationship
> bi directional relationship

> [!Example] Student and Instructor
> the student deals with the instructor but if the student is destroyed the instructor is not destroyed

> [!Example] Car and Driver

### Inheritance

> [!Note] Inheritance is an "is a" relationship

## Rectangle and Point Example

> [!Note] we can draw a rectangle using 2 points
> upper left and lower right

> [!Important] Rectangle and Point realtionship is composition
> in case Rectangle has object of type Point

> [!error] if Rectangle has a pointer to Point
> the relationship is aggregation

> [!Note] Association vs Aggregation
> Association is bi directional
> Aggregation is uni directional

---

```cpp
#include <iostream>
using namespace std;
class Point
{
    int x;
    int y;
    public:
    Point(){
        x = y = 0;
    }
    Point(int _x){
        x = y = _x;
    }
    Point(int _x, int _y){
        x = _x;
        y = _y;
    }
    ~Point(){
        cout << "Point Destructor" << endl;
    }
    void setX(int _x){
        x = _x;
    }
    void setY(int _y){
        y = _y;
    }
    int getX(){
        return x;
    }
    int getY(){
        return y;
    }
    setPoint(int _x, int _y){
        x = _x;
        y = _y;
    }
    void print(){
        cout << "(" << x << "," << y << ")";
    }
};
class Rectangle
{
    Point upperLeft;//Composition //Point* upperLeft //Aggregation
    Point lowerRight;
    public:
   // Rectangle():upperLeft(),lowerRight()//calls the parameterless constructor of Point
  //  Rectangle():upperLeft(0),lowerRight(0)//calls the constructor of Point with 1 parameter
      Rectangle()
    {//error here if we have one Point constructor with parameters can be avoided by :upperLeft(0,0),lowerRight(0,0)
        upperLeft.setPoint(0,0);
        lowerRight.setPoint(0,0);
    }
    Rectangle(int x1, int y1, int x2, int y2):upperLeft(x1,y1),lowerRight(x2,y2)
    //before the body of the constructor is executed
    //upperLeft and lowerRight Point constructors with  2 parameters are called instead of the parameterless constructor(default)
    //then the body of the constructor is executed

    {
       // upperLeft.setPoint(x1,y1); //not needed
        //lowerRight.setPoint(x2,y2);
    }
    Rectangle(Point p1, Point p2){
        upperLeft = p1;
        lowerRight = p2;
    }
    ~Rectangle(){
        cout << "Rectangle Destructor" << endl;
    }
    void setUpperLeft(Point p){
        upperLeft = p;
    }
    void setUpperLeft(int x, int y){
        upperLeft.setPoint(x,y);
    }
    void setLowerRight(Point p){
        lowerRight = p;
    }
    void setLowerRight(int x, int y){
        lowerRight.setPoint(x,y);
    }
    Point getUpperLeft(){
        return upperLeft;
    }
    Point getLowerRight(){
        return lowerRight;
    }
    void print(){
        cout << "Upper Left: ";
        upperLeft.print();
        cout << endl;
        cout << "Lower Right: ";
        lowerRight.print();
        cout << endl;
    }
};
int main()
{
    Point p1;
    Point p2(p1);//default copy constructor
    p1.print();
    p2.print();
    Rectangle r1;//Points Constructor is called twice before Rectangle Constructor
    r1.setUpperLeft(10,20);

    Rectangle r2(10,20,30,40);

    return 0;
}//Rectangle Destructor is called before Point Destructor called twice

```

> [!important] Point Constructor is called twice before Rectangle Constructor
> by default calls the parameterless constructor

> [!Error] ==Exam== if we have one Point constructor with parameters
> we get error creating Rectangle object at the Rectangle constructor because it calls the parameterless constructor of Point class

> [!Note] Rectangle destructor is called before Point destructor

> [!Note] Constructor is called Once for each object

> [!tldr] order of calling constructors and destructors are:
>
> 1. Point Constructor
> 2. Point Constructor
> 3. Rectangle Constructor
>    > > > > > > >
> 4. Rectangle Destructor
> 5. Point Destructor
> 6. Point Destructor

> [!bug] default parameters has to be constant values not variables
> can't do this `Rectangle(int x1, int y1, int x2, int y2 = x1 + y1)`

---

```cpp
class Circle{
    Point center;
    int radius;
    public:
    Circle(){
        radius = 1;
    }
    Circle(int _radius){
        radius = _radius;
    }
    Circle(int x, int y, int _radius):center(x,y){
        radius = _radius;
    }
    Circle(int _radius, Point _center){
        radius = _radius;
        center = _center;
    }
    Circle(Point _center, int _radius){
        radius = _radius;
        center = _center;
    }
    void setCenter(Point _center){
        center = _center;
    }
    void setCenter(int x, int y){
        center.setPoint(x,y);
    }
    void setRadius(int _radius){
        radius = _radius;
    }
    Point getCenter(){
        return center;
    }
    int getRadius(){
        return radius;
    }
    void print(){
        cout << "Center: ";
        center.print();
        cout << "Radius: " << radius ;
    }
};
int main()
{
    Circle c1;
    Circle c2(10);
    Circle c3(10,20,30);
    Circle c4(10,Point(20,30));
    Circle c5(Point(20,30),10);
    return 0;
}
```

---

# Installing Graphics

```cpp
#include <graphics.h>
#include <iostream>
using namespace std;
class Rectangle{

void draw(){
    rectangle(upperLeft.getX(),upperLeft.getY(),lowerRight.getX(),lowerRight.getY());
}
};
class Circle{

void draw(){
    circle(center.getX(),center.getY(),radius);
}
};
int main()
{
    int gdriver = DETECT, gmode, errorcode;
    initgraph(&gdriver, &gmode, "");
    Rectangle r1(100, 100, 400, 400);
    r1.draw();

    Circle c1(100, 100, 50);
    Circle c2(400, 400, 50);
    Circle c3(400,100,50);
    Circle c4(100,400,50);
    c1.draw();
    c2.draw();
    c3.draw();
    c4.draw();

    for(int i = 0; i < 100; i++){
       // Circle c(100 , 100,30 + i);
       c1.setRadius(30 + i);
        c1.draw();
    }
    /*
    rectangle(100, 100, 400, 400);
    circle(100, 100, 50);
    circle(400, 400, 50);
    circle(400,100,50);
    circle(100,400,50);
    line(100, 100, 400, 400);
    */
    getch();
    closegraph();
    return 0;
}
```

# #lab6

- Classes(Point, Rectangle, Circle, Line, Triangle(3 points or 3 lines))
- draw all

---

## Static members

```cpp
class Complex{
int real;
int img;
static int counter;//shared between all objects of the class
public:
Complex(int _real = 0, int _img = 0){
    real = _real;
    img = _img;
    counter++;
};
Complex(Complex const& c){
    real = c.real;
    img = c.img;
    counter++;
};
~Complex(){
    counter--;
};
static int getCounter(){
    return counter;
}
};

int Complex::counter = 0;//initialization of static member => outside the class

int main(){
    Complex c1, c2(10,20), c3(3,4);
    cout << Complex::getCounter() << endl;//3
    Complex c4(5,6);
    cout << Complex::getCounter() << endl;//4
    {
        Complex c5(7,8);
        cout << Complex::getCounter() << endl;//5
    }
    cout << Complex::getCounter() << endl;//4

    return 0;

}
```

> [!bug] can't access non static members from static functions as static functions are not related to any object of the class.
> `static int getReal(){return real;}` error here

> [!success] Only in C++ we can call static functions from objects of the class
> `cout << c1.getCounter() << endl;` works fine

---
## template

```cpp
#include <iostream>
using namespace std;
/*
void swap(int& a, int& b){
    int temp = a;
    a = b;
    b = temp;
}
void swap(double& a, double& b){
    double temp = a;
    a = b;
    b = temp;
}
*/
template<typename T> //typename or class
void swap(T& a, T& b){
    T temp = a;
    a = b;
    b = temp;
}
void
int main()
{
    int x = 10, y = 20;
    swap(x,y);
    cout << x << " " << y << endl;
    double d1 = 10.5, d2 = 20.5;
    swap(d1,d2);//error here => overload swap function
    cout << d1 << " " << d2 << endl;
    swap<int>(x,y);//explicitly call swap function with type int
    swap<double>(d1,d2);//explicitly call swap function with type double

	Complex c1(1,2), c2(3,4);
    swap<Complex>(c1,c2);//explicitly call swap function with type Complex
    //we can remove <Complex> as the compiler can deduce the type from the parameters
    swap(c1,c2);
    //swap(c1,d1);//error here
    return 0;
}
```

> [!Note] template is a generic function or class
> the compiler generates a function for each type used

> we can use it to implement stack with any type

```cpp
template<typename T>
class Stack{
    T* arr;
    int top;
    int size;
    public:
    Stack(int _size = 10){
        size = _size;
        arr = new T[size];
        top = -1;
    }

};
int main()
{
    Stack<int> s1;
    s1.push(10);
    Stack<double> s2;
    s2.push(10.5);
    Stack<Complex> s3;
    s3.push(Complex(1,2));
    return 0;
}
```


---

- counter + Template
