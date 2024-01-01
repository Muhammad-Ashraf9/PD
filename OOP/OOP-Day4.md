```cpp
 myfun(Stack s)
{//s=>  copy constructor

}

Stack myfun2(){
    Stack s;//default constructor
    return s;// copy constructor
}
int main(){
    Stack s1;
    //4 ways copy constructor is called
    Stack s2(s1);//1.
    Stack s3 = s1;//2.
    Stack s4 = myfun(s1);//3.
    Stack s5 = myfun2();//4.
}
```

## Dynamic Stack:

```cpp
class Stack{
    int* arr;
    int tos;
    int size;
public:
Stack(){
    tos = 0;
    size = 5;
    arr = new int[size];//dynamic array in the heap not in the stack
}
    Stack(int _size )
    {
        size = _size;
        arr = new int[size];// dynamic array in the heap not in the stack
        tos = 0;
    }
    Stack(const Stack& s)// copy constructor
    {
        size = s.size;
        tos = s.tos;
        arr = new int[size];//
        for(int i = 0; i < tos; i++)
            arr[i] = s.arr[i];
    }
    ~Stack()
    {
        delete []arr;//delete the array from the heap
        cout<<"Destructor"<<endl;
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
    friend void viewContent(Stack s);//friend function
};

void viewContent(Stack s)
{
    int x;
    while(s.pop(x))
        cout<<x<<" ";
}
//if we use call by reference, we will not call the destructor or copy constructor
//because we are not creating a new object
//we are just giving a new name to the object
void viewContent(Stack& s)

{
    int x;
    while(s.pop(x))
        cout<<x<<" ";
}

int main(){
    Stack s1;
    Stack s2(10);
    s1.push(20);
    s1.push(30);
    s1.push(40);
    s1.push(50);
    s1.push(60);//stack is full
    s1.print();
    {
        Stack s3(s1);//copy constructor
        s3.print();
    }//destructor will be called here for s3 object and the array will be deleted from the heap
    int x;
    viewContent(s1);//60 50 40 30 20//Deep copy
    while(s1.pop(x))
        cout<<x<<" ";//60 50 40 30 20//that is why we need to write our own copy constructor
    if(s1.pop(x) == 1)
        cout<<x<<endl;//60
    int popped;


    return 0;//destructor
    //we need to delete the array from the heap
    //we need to write a destructor
}
```

> [!Note] Array of the dynamic stack is allocated in the heap. So, we need to delete the array from the heap. We need to write a destructor.

> [!Note] The default copy constructor will not work with dynamic memory allocation. It will only copy the pointer to the array, not the array itself. So, we need to write our own copy constructor.

> [!Note] Shallow copy or Bitwise copy: Copying the pointer to the array, not the array itself.

> [!Note] Deep copy: Copying the array itself.

> [!Note] We write copy constructor to make new array in the heap and copy the elements of the old array to the new array.

# #lab4

1. Dynamic stack
2. static stack arrays different addresses

---

> [!tldr] When you return an object from a function and assign the returned value to another object, it can cause unexpected behavior.
>
> > [!Note] This is because the destructor of the returned object will delete the array from the heap.
> > As a result, the other object will be pointing to an array that has been deleted from the heap, leading to unpredictable consequences

```cpp


#include <iostream>

class Stack {
private:
int* arr;
int size;
int tos;
public:
Stack(int s) {
size = s;
arr = new int[size];
}
~Stack() {
	delete[] arr;
}

};

Stack createStack() {
Stack s(5);
s.push(10);
s.push(20);
s.push(30);
return s;//this line will generate a temporary object that is created using the copy constructor
}

int main() {
Stack s1;
s1 = createStack(); //this line could cause unexpected behavior
s1.print(); // Output: 30 20 10

    return 0;

}
```

> [!Note] The soultion for this problem is to overload the assignment operator.
> (not in this lecture)

---

### Friend function

> [!Note] Friend function violates OOP the encapsulation. It can access the private members of the class.

```cpp
friend void main();
void viewContent(Stack s)
{
  cout<<s.arr<< endl;//0x7ffeedc04c40 address of the array
}

int main()
{
    Stack s1;

}


```

> the main function can be a friend function to any class
> If you send an object parameter to a function by value

---

```cpp

int* ptr = new int(5);//ptr is a pointer to an integer in the heap
int*& x = ptr;//x is a reference to a pointer to an integer in the heap
cout<<*x<<endl;//5
cout<<*ptr<<endl;//5
```

---

### Complex class

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
    Complex(Complex const& c)
        real = c.real;
        img = c.img;
        cout<<"copy constructor"<<endl;
    }
    ~Complex()
    {
        print();
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
    Complex add(Complex const& c)
    {
        Complex res;
        res.real = real + c.real;
        res.img = img + c.img;
        return res;
    }
        Complex operator+(Complex const& c)
    {
        Complex res;
        res.real = real + c.real;
        res.img = img + c.img;
        return res;
    }
    Complex operator+(double x)
    {
        //better implementation
        Complex res(real + x, img);
        /*
        Complex res;
        res.real = real + x;
        res.img = img;*/
        return res;
    }
    int Complex operator==(Complex const& c)
    {
        return real == c.real && img == c.img;//is the same as the following
        if(real == c.real && img == c.img)
            return 1;
        return 0;
    }
    int Complex operator!=(Complex const& c)
    {
        return !(*this == c);//is the same as the following
        if(real != c.real || img != c.img)
            return 1;
        return 0;
    }
    Complex operator+=(Complex const& c)
    {
        real += c.real;
        img += c.img;
        return *this;
    }
    friend Complex operator+(double x, Complex c);
};
Complex add(Complex c1, Complex c2)
{
    Complex res;
    res.setReal(c1.getReal() + c2.getReal());
    res.setImg(c1.getImg() + c2.getImg());
    return res;

}
Complex operator+(double x, Complex c)
{
    //Complex res(c.getReal() + x, c.getImg());//or we can add it as a friend function
   // Complex res(c.real + x, c.img);
   // more better we can use the other fuction
   // return res;
   return  c + x;//
}



int main()
{
    Complex c1(1,2),c2(3),c3,c4(5,6),c5;
    c3 = ((c1.add(c2)).add(c4)).add(c5);//not efficient
    c3 = c1+c2+c4+c5;//more efficient so we need to overload the + operator
    c3 = c1.operator+(c2);//c1 is the object that calls the function (this)
    c3 = c1 + c2;// c1.operator+(c2)
    c3 = c1 + 5;//c1.operator+(5)
    c3 = 5 + c1;//error so we need to overload the + operator as a standalone function

    c3 = operator+(5,c1);

    if(c1 == c2)//error so we need to overload the == operator
        cout<<"Equal"<<endl;
    else
        cout<<"Not Equal"<<endl;
    c1 = c2;//not error because the compiler generates a default assignment operator
    c1+=c2;//error so we need to overload the compound += operator
    return 0;
}

```

> [!important] operator+ can be standalone function or member function

> [!Error] Some operators can't be standalone function like = assignment operator

> [!Bug] Can't change precedence or associativity of operators
> `x = y = z;` > `x = y+z+w+u;`

> [!Note] Operator that can be overloaded:
>
> 1. . (dot) member access operator
> 2. :: scope resolution operator
> 3. ?: ternary operator
> 4. - . -> . [] dereference operator
> 5. sizeof() sizeof operator

> [!Note] Rules for overloading operators:

> Lecture slides

> [!Note] If there is both standalone and member function for the same operator, the member function will be called first.

> [!Note] operator== can be standalone function or member function

# #lab4

1. Fraction class - + operator
   frac+int
   int+frac
   frac+frac
2. Complex class - + operator
   Comp+int
   int+Comp
   Comp+Comp
   ==
   !=
   +=
   ++
   int x = c1;//casting operator
   c1[0]//return real part
   c1[0] = 5;//set real part
