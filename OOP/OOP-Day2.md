1. create classes
   - menu
   - magic box

---

#### overloading

> [!tip] function overloading
> same name but different parameters (number or type)
> function signature = function name + parameters
>
> > [!bug] return type is not part of the signature

```cpp
int add(int a, int b){
    return a+b;
}
int add(int a, int b ){ // error => same signature
    return a+b;
}
float add(int a, int b){ // error  => return type is not part of the signature
    return a+b;
}
int add(int a, int b, int c){ // ok => different number of parameters
    return a+b+c;
}
int add(double a, double b){ // ok => different type of parameters
    return a+b;
}
int add(int a, double b){ // ok => different type of parameters
    return a+b;
}
int add(double a, int b){ // ok => different arrangement of parameters
    return a+b;
}


int main(){

    cout << add(1,2) << endl; // add(int, int)
    cout << add(1,2,3) << endl;// add(int, int, int)
    cout << add(1.5,2.5) << endl;// add(double, double)
    cout << add(1,2.5) << endl;// add(int, double)
    cout << add(1.5,2) << endl;// add(double, int)
    float x = 2.2;
    cout << add(x,2) << endl;// add(double, int)
    double y = 2.2;

    cout << add(x,y) << endl;// add(double, double)
    return 0;
}
```

---

###### Employee class

> [[OOP-Day1](OOP-Day1.md)]

> [!info] functions that are part of the class are saved only once in the memory and shared between all objects of the class

> [!info] attributes are saved in the memory for each object

## this

```cpp
class Employee{
    char[20] name;
    int id;
    float salary;
public:
    void setId(int _id){
        this->id = _id; // ok this is a pointer to the object that called the function (passed as a hidden parameter)(passed by reference so we use -> to access members and change the value)
    }
    int getId(){
        return this->id;
    }
    void setName(string _name){
        strcpy(this->name, _name);
    }

    void print(){//this hidden parameter
        cout << this->name << endl;
        cout << this->id << endl;
        cout << this->salary << endl;
        //we can use without this
        cout << name << endl;
        cout << id << endl;
        cout << salary << endl;
    }
};
void print(Employee e){
    this->name = e.name;// error no this in non member function
    cout << e.name << endl;
    cout << e.id << endl;
    cout << e.salary << endl;
}
int main(){
    Employee e1, e2;
    e1.setId(1);
    e1.setName("ahmed");
    e1.print();//this is e1 passed as a hidden parameter => address of object e1
    print(e1);
    e2.setId(2);
    e2.setName("ali");
    e2.print();
    print(e2);
    return 0;
}
```

---

### Reference

```cpp
int main(){
   int x = 5;
   int y = 10;
   int& xRef = x;// same adress with 2 names x and xRef (must ref to in intialization)
   cout << x << endl;// 5
    cout << xRef << endl;// 5
    xRef = 10;
    cout << x << endl;// 10
    cout << xRef << endl;// 10
    //address
    cout << &x << endl;// 0x61ff0c
    cout << &xRef << endl;// 0x61ff0c
    xRef = y;// like x = y
    cout << x << endl;// 10
   float z = 15;

}
```

### compiler table

| name              | size | address            |
| ----------------- | ---- | ------------------ |
| x                 | 4    | 0x61ff0c           |
| y                 | 4    | 0x5a654c           |
| z                 | 8    | 0xfa654c           |
| xRef(alias for x) | 4    | 0x61ff0c same as x |

#### address vs reference

```cpp
swap(int* x, int* y){// by address
//x,y copies of the passed variables addresses
    int* temp = x;
    x = y;
    y = temp;
}
swap(int& x, int& y){// by reference
// x,y just aliases for the passed variables from main
    int temp = x;
    x = y;
    y = temp;
}
int main(){
    int x = 5;
    int y = 10;
    swap(&x, &y);// by address
    cout << x << endl;// 10
    cout << y << endl;// 5
    swap(x, y);// by reference
    cout << x << endl;// 5
    cout << y << endl;// 10
    return 0;
}
```

## stack representation

#### address

|      |                                 |
| ---- | ------------------------------- |
| swap | x =0xffac21 , y = 0xcc4526,temp |
| main | x = 5,y = 10                    |

vs

#### refrence

|      |              |
| ---- | ------------ |
| swap | only temp    |
| main | x = 5,y = 10 |

> [!tip] reference is better memory wise and performance wise

> [!error] cannot (overload) have both reference and address functions

> [!tip] to make reference const (read only) use const before the type

```cpp
int add (const int& x, const int& y){
    //no place in memory for x and y just aliases for the passed variables
    //better performance
    x = 10;// error
    int& z = x;//try
    z=3;//try
    return x+y;
}
int main(){
int y = 10;
int z = 20;
cout << add(y,z) << endl;
float x = 5;
 float const& xRef = x;
    xRef = 10;// error
    cout << xRef << endl;// 5
}
```

---

# Constructors

- same name as the class
- no return type
- called ONCE automatically when an object is created

## Bank Account Class

```cpp
class BA{
    int id;
    char name[20];
    float balance;
    //float balance = 0;//gets trnsferred to the constructor
    public:
    BA(){
        id = 0;
        strcpy(name, "");
        balance = 0;
    }
    BA(int balance){
        id = 0;
        strcpy(name, "");
        this->balance = balance;
    }
    BA(int _id, char* _name){
        id = _id;
        strcpy(name, _name);
        balance = 0;
    }
    BA(int _id, char* _name, float _balance){
        setId(_id);//to validate the id
        strcpy(name, _name);
        balance = _balance;
    }
    void setId(int _id){
        id = _id;
    }
    void setName(char* _name){
        strcpy(name, _name);
    }
    void setBalance(float _balance){
        balance = _balance;
    }
    void print(){
        cout << id << endl;
        cout << name << endl;
        cout << balance << endl;
    }
    void deposit(int amount){
        balance += amount;
    }
    void withdraw(int amount){
        if(amount <= balance){
            balance -= amount;
        }else{
            cout << "insufficient balance" << endl;
        }
};
    int main(){
        BA b1;
        BA b2(1000);
        BA b3(1, "ahmed");
        BA b4(1, "ahmed", 5000);
        b4.deposit(1000);
        b4.withdraw(2000);
        b1.print();
        b1.setId(1);
        b1.setName("ahmed");
        b1.setBalance(1000);
        b1.print();

        return 0;
    }
}
```

> [!tldr] Constructor is a special function with same name of the class that is called once automatically when an object is created

> [!info] constructor can be overloaded

> [!tip] if we don't write a constructor the compiler will create a default constructor with no parameters and no body

> [!warning] if we write a constructor the compiler will not create a default constructor

> [!info] struct can have a constructor

---

#### complex number class

```cpp
class Complex{
    int real;
    int img;
public:
    Complex(){
        real = 0;
        img = 0;
    }
    Complex(int _real){
        real = _real;
        img = 0;
    }
    Complex(int _real, int _img){
        real = _real;
        img = _img;
    }
    getReal(){
        return real;
    }
    getImg(){
        return img;
    }
    setReal(int _real){
        real = _real;
    }
    setImg(int _img){
        img = _img;
    }
    void print(){
        if(img < 0)
            cout << real << " - " << -img << "i" << endl;
        else
            cout << real << " + " << img << "i" << endl;
    }

};
int main(){
    Complex c1;
    cout << c1.getReal() << endl;
    cout << c1.getImg() << endl;
    Complex c2(1,2);


    return 0;
}
```

# #lab2

1. complex (All)
2. BankAccount(2-param&3-param)
3. TRACING
4. search STACK

---

1.

```cpp
#include <iostream>

using namespace std;

class Complex
{
    int real;
    int img;
public:
    Complex()
    {
        img = 0;
        real =0;
    }
    Complex(int _real)
    {
        img = 0;
        real = _real;
    }
    Complex(int _real, int _img)
    {
        real = _real;
        img = _img;
    }
    int getReal()
    {
        return real;
    }
    int getImg()
    {
        return img;
    }
    void setReal(int _real)
    {
        real = _real;
    }
    void setImg(int _img)
    {
        img = _img;
    }
    void print()
    {
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
};

int main()
{
	Complex c1;
    Complex c2(1);
    Complex c3(-1);
    Complex c4(2);
    Complex c5(-2);
    Complex c6(0,1);
    Complex c7(0,-1);
    Complex c8(0,2);
    Complex c9(0,-2);
    Complex c10(1,1);
    Complex c11(1,-1);
    Complex c12(1,2);
    Complex c13(1,-2);
    Complex c14(2,1);
    Complex c15(2,-1);
    Complex c16(2,2);
    Complex c17(2,-2);
    Complex c18(-1,1);
    Complex c19(-1,-1);
    Complex c21(-1,2);
    Complex c22(-1,-2);
    Complex c23(-2,1);
    Complex c25(-2,-1);
    Complex c26(-2,2);
    Complex c27(-2,-2);
    c1.print();
    c2.print();
    c3.print();
    c4.print();
    c5.print();
    c6.print();
    c7.print();
    c8.print();
    c9.print();
    c10.print();
    c11.print();
    c12.print();
    c13.print();
    c14.print();
    c15.print();
    c16.print();
    c17.print();
    c18.print();
    c19.print();
    c21.print();
    c22.print();
    c23.print();
    c25.print();
    c26.print();
    c27.print();
    return 0;
}

```

2.

```cpp
#include <iostream>
#include <string.h>
using namespace std;

class BankAccount{
    int id;
    char name[20];
    float balance;

public:
    BankAccount(int _id, char* _name){
        setId(_id);
        setName(_name);
        balance = 0;
    }
    BankAccount(int _id, char* _name , int _balance){
        setId(_id);
        setName(_name);
        balance = _balance;
    }
    void setId(int _id){
        if(_id>0)
            id = _id;
        else
            cout<<"id should be more than 0";
    }
    void setName(char* _name){
        if(strlen(_name)>2)
            strcpy(name,_name);
        else
            cout<<"name should be more than 2 characters";

    }
    void withdraw(int amount){
        if(balance - amount > 0)
            balance -= amount;
        else
            "Not enough credit";
    }
    void deposit(int amount){
        balance += amount;
    }
    void print(){
        cout << "Id: "<<id << endl;
        cout << "Name: "<< name << endl;
        cout << "Balance: "<< balance << endl;
    }

};

int main()
{
    BankAccount b1(123,"mohamed");
    b1.print();
    BankAccount b2(0,"ali",2323);
    b2.print();

    return 0;
}

```
