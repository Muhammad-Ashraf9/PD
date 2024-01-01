### Complex Class

```cpp
#include <iostream>

using namespace std;

class Complex
{
    double real;
    double img;
public:
    Complex()
    {
        img = 0;
        real =0;
        cout<<"no arg constructor"<<endl;
    }
    Complex(double _real)
    {
        img = 0;
        real = _real;
        cout<<"one arg constructor"<<endl;
    }
    Complex(double _real, double _img)
    {
        real = _real;
        img = _img;
        cout<<"two arg constructor"<<endl;
    }
    // Complex(Complex c)//error making copy constructor with object as parameter not reference it will call itself and so on infinite loop
    Complex(Complex const& c)// copy constructor
    {//this is a pointer to the object that called the function
    //c is a reference to the object passed to the function
        real = c.real;
        img = c.img;
        //c.real = 0;//change the value of the object passed to the function so we need to pass by const reference
        cout<<"copy constructor"<<endl;
    }
    ~Complex()//Destructor
    {
        print();//= this->print(); // not destructed yet print before destruction
        cout<<"Complex Destructor"<<endl;
    }
    double getReal()
    {
        return real;
    }
    double getImg()
    {
        return img;
    }
    void setReal(double _real)
    {
        real = _real;
    }
    void setImg(double _img)
    {
        img = _img;
    }
    void setComplex(double _real, double _img)
    {
        real = _real;
        img = _img;
    }
    void print()
    {
        cout<<endl;
        if(real != 0 )
            cout<<real;
        if (img < 0)
        {
            if(img != -1)
                cout<<img;
            else
                cout<<"-";
            cout<<"j";

        }
        if(img > 0)
        {
            if(real != 0)
                cout<<"+";

            if(img != 1)
                cout<<img;
            cout<<"j";
        }
        if(img == 0 && real == 0)
            cout<<0;

    }
    Complex add(Complex c)//copy constructor
    {
        Complex res;//no arg constructor
        //can access private members of other objects of the same class no need for getters and setters
        res.real = real + c.real;//this->real + c.real;
        res.img = img + c.img;
        return res;//default assignment operator is called here
    }//destructor called twice once for res and once for c
};
Complex add(Complex c1, Complex c2)
{
    Complex res;
    res.setReal(c1.getReal() + c2.getReal());//standalone function can't access private so we need getters and setters
    res.setImg(c1.getImg() + c2.getImg());
    return res;

}
void myfun(Complex c)
{
    c.print();
}
int main()
{
    Complex c1(1,2);
    Complex c2(3,4);
    Complex c3 = add(c1,c2);
    c3.print();
    c3 = c1.add(c2);
    c3.print();
    Complex* c4;
    c4 = new Complex(5,6);//create object in heap and assign its address to pointer c4
    c4->print();//access members of object in heap using pointer
    delete c4;//delete object in heap
    Complex c5;
    Complex& c6 = c5;//create reference to object c5 only alias to c5 no new object created
    c6.setComplex(7,8);
    c5.print();
    c6.print();

    //Destructor is called when object is destroyed
    //when object is created in stack destructor is called when it goes out of scope
    Complex c7;//parameterless constructor
    c7.setComplex(9,10);
    {
        Complex c8(1,2);//2 arg constructor
        c8.setComplex(11,12);
    }//c8 goes out of scope destructor is called
    myfun(c7);//


Complex* c9;
c9 = new Complex();//parameterless constructor
delete c9;//destructor

cout<<"end of main"<<endl;
    return 0;
}

```

> [!Note] object in stack is destroyed when it goes out of scope
> object in heap is destroyed when delete is called
> as when going out of scope the pointer is destroyed not the object in heap

> memory representation of object in stack

| myfun | c =>copied by copy constructor from c7 |
| ----- | -------------------------------------- |
| main  | c7                                     |

> [!info] objects passed by value call default copy constructor
> default copy constructor copies the values of the members of the object to the new object
> calls destructor when object goes out of scope

> [!tldr] objects passed by reference and address don't call default copy constructor

```cpp
Complex c1(1,2), c2(3,4);

```

> [!tldr] Cases of calling copy constructor
>
> 1. passing object by value
> 2. returning object by value
> 3. creating object with another object`Complex c2(c1);`
> 4. creating object with another object`Complex c2 = c1;`
>    > [!fail] `Complex c2; c2 = c1;` this is not calling the copy constructor
>    > this is calling the default constructor then the assignment operator

> [!Bug] returning object by address
> undetermined behavior as it is saved in stack
> object is destroyed when function returns

> [!success] returning object by dynamic memory allocation
> object is saved in heap and is not destroyed when function returns
> we need to delete it manually

> [!Bug] when we write copy constructor manually
> the default constructor is removed

> [!tip] Destructor is called when object is destroyed
>
> > [!info] can't have parameters can't be overloaded(We can only have one destructor per class)

#### when we need to write copy constructor

> [!tip] when the object has a pointer

---

### this needs to be revised

> [!hint] when we create an object in a function and return it by value it doesn't call the copy constructor
> but if we returned the parameter by value it calls the copy constructor

> [!tldr] Examples of calling copy constructor
> `Complex myfun(Complex c){return c;}` this calls the copy constructor
> `Complex myfun(){Complex c; return c;}` this doesn't call the copy constructor

---

# Stack

> [!Note] data structure that stores data in a last in first out manner(LIFO)
> Can't access elements like array
> Can only access the top element
> Can only add and remove elements from the top

![](Pasted%20image%2020231121144405.png)

> [!tldr] Examples of stack
>
> 1. function calls
> 2. undo/redo
> 3. back button in browser

## Implementation of stack using array

size =5

|         |     |
| ------- | --- |
| tos = 0 |     |

`push(30)`

| tos = 1 |     |
| ------- | --- |
|         | 30  |

`push(40)`

| tos = 2 |     |
| ------- | --- |
|         | 40  |
|         | 30  |

`push(50)`

| tos = 3 |     |
| ------- | --- |
|         | 50  |
|         | 40  |
|         | 30  |

`push(60)`

| tos = 4 |     |
| ------- | --- |
|         | 60  |
|         | 50  |
|         | 40  |
|         | 30  |

`push(70)`

| tos = 5 |     |
| ------- | --- |
|         | 70  |
|         | 60  |
|         | 50  |
|         | 40  |
|         | 30  |

> [!tldr] stack overflow

> `pop()`=> returns 70

| tos = 4 |     |
| ------- | --- |
|         | 60  |
|         | 50  |
|         | 40  |
|         | 30  |

> `pop()`=> returns 60

| tos = 3 |     |
| ------- | --- |
|         | 50  |
|         | 40  |
|         | 30  |

> `pop()`=> returns 50

| tos = 2 |     |
| ------- | --- |
|         | 40  |
|         | 30  |

> `pop()`=> returns 40

| tos = 1 |     |
| ------- | --- |
|         | 30  |

> `pop()`=> returns 30

| tos = 0 |     |
| ------- | --- |

```cpp
class Stack
{
    int arr[5];//static
    int tos;//top of stack need in push and pop
    int size;//we need it for dynamic stack
public:
    Stack()
    {
        tos = 0;
        size = 5;
    }

    int push(int x)
    {
        if(tos == size)
        {
            cout<<"Stack overflow"<<endl;
            return 0;
        }
        arr[tos] = x;
        tos++;
        return 1;
    }
    int pop(int& popped)
    {
        if(tos == 0)
        {
            cout<<"Stack is empty"<<endl;
            return 0;
        }
        tos--;
        popped = arr[tos];

        return 1;
    }

    void print()
    {
	    int i;
        cout<<endl;
        for(int i = tos-1; i >= 0; i--)
            cout<<arr[i]<<" ";
        cout<<endl;
    }
};

int main(){
    Stack s1;
    s1.push(20);
    s1.push(30);
    s1.push(40);
    s1.push(50);
    s1.push(60);
    s1.print();
    int popped;



    if(s1.pop(popped) == 1)
        cout<<popped<<endl;//60

   if(s1.pop(popped) == 1)
        cout<<popped<<endl;//50
    if(s1.pop(popped) == 1)
        cout<<popped<<endl;//40
    if(s1.pop(popped) == 1)
        cout<<popped<<endl;//30
    if(s1.pop(popped) == 1)
        cout<<popped<<endl;//20
    if(s1.pop(popped) == 1)
        cout<<popped<<endl;//stack is empty

    return 0;
}
```

```cpp
int main(){

        {
        Stack s2(s1);
        if(s2.pop(popped) == 1)
            cout<<popped<<endl;//60
        if(s1.pop(popped) == 1)
            cout<<popped<<endl;//60
    }
    if(s1.pop(popped) == 1)
        cout<<popped<<endl;//50
}
```

# #lab3

1. complex again
2. Stack
