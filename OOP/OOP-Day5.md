> to add int to complex, we need
> to add a constructor that takes int or operator+(int, Complex)
> casting from int

```cpp
#include <iostream>
using namespace std;
class{

complex(int real)
{
    this->real = real;
    this->img = 0;
}

explicit operator int()//explicit used to avoid implicit casting and force explicit casting

{//casting operator
    return real;
}

}

int main()
{
    Complex c1(1, 2);
    Complex c4 = 3 + c1;//this works cuz of implicit casting from int to complex even if we didn't overload the + operator
    Complex c2 = 3;//implicit casting from int to complex
    //calling the constructor Complex(int real)
    int c3 = c1;//implicit casting from complex to int
    int c3 = (int)c1;//explicit casting so we need to overload the casting operator

    return 0;
}
```

> [!tip] `c3 = 3+c1;`
> if we dont overload the ` operator+(int c1, Complex c2)`, this will call the constructor `Complex(int real)`to cast 3 => Complex and then call `operator+(Complex c1, Complex c2)`
>
> > [!bug] if we don't have the constructor `Complex(int real)` this will not work

> [!summary] we have to options to add int to complex
>
> 1. overload the `operator+(int c1, Complex c2)`
> 2. overload constructor `Complex(int real)`
>    > [!success] if we have both, the compiler will call the `+ operator(int c1, Complex c2)`

> [!tip] we use explicit to avoid implicit casting

> [!info] casting operator : unary operator
> that takes no parameters and returns a value
> is a member function that has no return type and takes no parameters
>
> > [!tip] `operator int()` this can be used to cast from complex to int implicitly or explicitly
>
> <br>
>
> > [!warning] `explicit operator int()` to only allow explicit casting as part of the data will be lost

<article align="center">sadfsdf</article>
<div align="right">sadfsdf</div>
<aside align="left">sadfsdf</aside>
<hr>

# pre vs post increment

```cpp
#include <iostream>
using namespace std;
class Complex
{

    Complex operator++()//prefix ++c;
    {
        real++;
        return *this;
    }
    Complex operator++(int)//postfix c++;
    {
        Complex res = *this;
        real++;
        return res;
    }

};
int main()
{
    Complex c1(1, 2);
    Complex c3 = c1++;// precedence of ++ is higher than = so c1++ is called first then the result is assigned to c3
    c3.print();//1
    c1.print();//2
    Complex c2 = ++c1;//precedence of ++ is higher than = so ++c1 is called first then the result is assigned to c2
    c1.print();//3
    c2.print();//3
    return 0;
}
```

> [!warning] Assignment (=), subscript ([]), and function call (“()”) operators ==must== be defined as ==member functions==
>
> > [!success] all other operators can be either member functions or a non member (stand alone) functions.

---

### `operator[]`

```cpp
class Complex
{
    int operator[](int index)//read only
    {
        if(index == 0)
            return real;
        else if(index == 1)
            return img;

    }
   /* void operator[](int index, int value)
    {
        if(index == 0)
            real = value;
        else if(index == 1)
            img = value;

    }*/ //Error next lecture
}
int main()
{
    cout << c1[0];//c1.operator[] error
    //has to be a member function
    //c1[0] = 5;//error operator[]  read only
    return 0;
}
```

---

### assignment operator

```CPP
   class Complex
   {
       Complex& operator=(Complex const& c)
       {
           real = c.real;
           img = c.img;
           return *this;
       }
   }
   int main()
   {
       Complex c1(1, 2);
       Complex c2(3, 4);
       Complex c3;
     c3 = c1 = c2;
       return 0;
   }
```

>

> [!tip] pass by value =>calls copy constructor
> pass by reference => DON'T => better performance

> [!fail] we can't return local variables by reference as they will be destroyed after the function ends
> but we can return `this` as it is a pointer to the object itself and it will not be destroyed

---

> [!tip] if we don't overload the assignment operator, the compiler will generate a default one
>
> the default assignment operator does a ==shallow copy==
>
> if we have a ==pointer== in the class, we need to overload the assignment operator

---

### dynamic stack assignment operator

```cpp

class Stack
{

    Stack& operator=(Stack const& s)
    {
       // this = &s;//error read only
       // this->size = s.size;//can change the data but not the this itself
        if(this != &s)
        {
            delete [] arr;
            arr = new int[s.size];
            for(int i = 0; i < s.tos; i++)
                arr[i] = s.arr[i];
            tos = s.tos;
            size = s.size;
        }
        return *this;
    }
    int main()
    {
        Stack s1(5);
        Stack s2(10);
        s1 = s2;//assignment operator
        Stack s3 = s2;//copy constructor
        return 0;
    }
}
```

# #lab5

1. Complex
   - ++ pre and post
   - [ ]
   - explicit casting
   - = operator
1. Stack
   - = assignment operator
   - s1 + s2 (size = s1.size + s2.size, tos = s1.tos + s2.tos, arr = new int[size], copy s1.arr first and s2.arr to arr)

---

```cpp
class Stack
{
     Stack operator+(Stack const& s)
     {
            Stack res(size + s.size);
            for(int i = 0; i < tos; i++)
                res.arr[i] = arr[i];
            for(int i = 0; i < s.tos; i++)
                res.arr[i + tos] = s.arr[i];
            res.tos = tos + s.tos;
            return res;
     }
}



```

---

```cpp
#include <iostream>
using namespace std;
/*
int add(int a, int b)
{
    return a + b;
}
int add(int a, int b, int c)
{
    return a + b + c;
}
*/
//Default arguments

int add(int a, int b = 0, int c = 0, int d = 0)
{
    return a + b + c + d;
}
/*
int add(int a = 0, int b , int c = 0, int d = 0){//error
//default arguments must be at the end
    return a + b + c + d;

}

int add(int a)//error ambiguous
{
    return a;
}
*/
int main()
{
    cout << add(1, 2) << endl;
    cout << add(1, 2, 3) << endl;
    cout << add(1, 2, 3, 4) << endl;
    return 0;
}
```

---

## default arguments in Stack

```cpp
class Stack{
    Stack()
    {
        size = 10;
        arr = new int[size];
        tos = 0;
    }
    Stack(int size = 10)
    {
        this->size = size;
        arr = new int[size];
        tos = 0;
    }
}
int main()
{
    Stack s1;//error ambiguous (Stack() or Stack(int size = 10)
    Stack s2(20);
    return 0;
}
```

> [!info] can replace constructors with default arguments

```cpp
class Complex
{
    Complex(int _real = 0, int _img = 0)
    {
        real = _real;
        img = _img;
    }
}
```

---

### complex array

```cpp
int main(){

Complex arr[5];//parameterless constructor called 5 times
//Complex arr[5] = {Complex(10,20) };// parameterless constructor called 4 times and 2 parameter constructor called once
//Complex arr[5] = {Complex(1, 2), Complex(3), Complex(5, 6), Complex(7, 8), Complex(9, 10)};
for(int i = 0; i < 5; i++)
    //arr[i].print();
    arr[i].setComplex(0,0);
//pointers
for(int i = 0; i < 5; i++)
    (arr+i)->print();//arr[i].print();
}
```

#### dynamic array of complex

```cpp
int main()
{
    Complex* arr = new Complex[5];//parameterless constructor called 5 times

    for(int i = 0; i < 5; i++)
        arr[i].print();
    //pointers
    for(int i = 0; i < 5; i++)
        (arr+i)->print();//arr[i].print();
    delete [] arr;
}
```

### array of Dynamic Stack

```cpp
int main()
{
    Stack* arr = new Stack[5];//parameterless constructor called 5 times

    for(int i = 0; i < 5; i++)
        arr[i].print();
    //pointers
    for(int i = 0; i < 5; i++)
        (arr+i)->print();//arr[i].print();
    delete [] arr;//calls the destructor 5 times and then delete the array
    //delete arr;//delete only the first element
}
```

> [!tldr] In C++ we can create objects in the stack or in the heap
> in the stack we use the constructor and the destructor is called automatically
> `Complex c1(1, 2);`
> in the heap we use the new operator and the destructor is called manually
> `Complex* c1 = new Complex(1, 2);`

> [!example] In other programming languages like Java or C#, all objects are created in the heap and the garbage collector is responsible for calling the destructor

---

## member function defined outside the class

```cpp
class Complex
{
    void print();
    void setComplex(int real, int img);
};

void Complex::print()
{
    cout << real << " + " << img << "i" << endl;
}

int main()
{
    Complex c1(1, 2);
    c1.print();
    return 0;
}
```

> [!info] we can define member functions outside the class
>
> > [!info] :: scope resolution operator

---
