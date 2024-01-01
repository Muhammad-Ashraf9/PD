# liner programming vs structure programming

---

## liner programming problem

---

##### member function vs stand alone function

```cpp
#include <iostream>
using namespace std;
int main()
{
    int x = 10;
    int y = 20;
    cout << "Hello World" << endl;
    cout << "x = " << x << endl;
    cout << "y = " << y << endl;
    cin >> x >> y;
    cout << "x = " << x << " y = " << y << endl;
    int* ptr = new int[5];// 20 bytes 5*4
    int i = 0;
    for (i = 0; i < 5; i++)
    {
        ptr[i] = i;
    }
    for (i = 0; i < 5; i++)
    {
        cout << ptr[i] << endl;
    }
    delete[] ptr;//free memory in heap

    int * ptr2 = new int(10);// 4 bytes this syntax for one element and initialize it with 10

    cout << *ptr2 << endl;
    cout << ptr2[0] << endl;

    delete ptr2;//free memory

    return 0;
}

```

> [!info] delete ptr2 vs delete[] ptr
> delete ptr2 => free memory in heap for one element
> delete[] ptr => free memory in heap for array

```cpp

#include <iostream>

#include <string.h>

using namespace std;

struct Employee
{
    int id;
    char name[20];
    float salary;
    void print(){ //member function
        cout << id << endl;
        cout << name << endl;
        cout << salary << endl;
    }
};
void print(Employee e){ //stand alone function
    cout << e.id << endl;
    cout << e.name << endl;
    cout << e.salary << endl;
}
int main()
{
    Employee e1; // no struct keyword in c++ but in c we need it

    e1.id = 1;
    strcpy(e1.name, "ahmed");
    e1.salary = 1000;

    cout << e1.id << endl;
    cout << e1.name << endl;
    cout << e1.salary << endl;

    print(e1);
    e1.print();

    Employee e2 = { 2, "mohamed", 2000 };//direct initialization  =>  address in stack and object in stack limited size (stack overflow)

    cout << e2.id <<  e2.name <<  e2.salary << endl;
    cin >> e2.id >> e2.name >> e2.salary;
    cout << e2.id <<  e2.name <<  e2.salary << endl;
    cout << e2.id << " " << e2.name << " " << e2.salary << endl;

    e2.print();


    Employee* ptr = new Employee;//indirect  =>  address in stack and object in heap
    ptr->id = 2;
    strcpy(ptr->name, "mohamed");
    ptr->salary = 2000;
    cout << ptr->id << endl;
    cout << ptr->name << endl;
    cout << ptr->salary << endl;

    delete ptr;//we need to free memory after using it

	return 0;
}
```

### Access Modifiers

##### public vs private

```cpp
struct Employee
{
    private://in struct members is public by default so we need to make it private
    int id;
    char name[20];
    float salary;
    public:// we can make it private and use setters and getters to access it
    void print(){ //member function
        cout << id << endl;
        cout << name << endl;
        cout << salary << endl;
    }
    void setId(int _id){
        if(_id > 100000)
       id = _id;
    else
        cout << "invalid id" << endl;
    }
    int getId(){
        return id;
    }
    void setName(char* _name){
        if(strlen(_name) < 2)
        strcpy(name, _name);
        else
        cout << "invalid name" << endl;
    }
    char* getName(){
        return name;
    }
    void setSalary(float _salary){
        if(_salary > 1000)
        salary = _salary;
        else
        cout << "invalid salary" << endl;
    }
    float getSalary(){
        return salary;
    }

};

 int main() {
    Employee e1;
    e1.id = 1;//error
    strcpy(e1.name, "ahmed");//error
    e1.salary = 1000;//error
    e1.print();//error

    e1.setId(10050000);
    cout << e1.getId() << endl;
    e1.setName("ahmed");
    cout << e1.getName() << endl;
    e1.setSalary(1000);
    cout << e1.getSalary() << endl;
    return 0;
}
```

## class vs struct

> [!info] members is ==private== by default in ==class==

> [!info] members is ==public== by default in ==struct==

> struct have only encapsulation and data hiding from OOP

```cpp
class Employee
{
    private: // for readability and to make it clear that this is private
    int id;
    char name[20];
    float salary;
    public:
    void print(){ //member function
        cout << id << endl;
        cout << name << endl;
        cout << salary << endl;
    }
    void setId(int _id){
        if(_id > 100000)
       id = _id;
    else
        cout << "invalid id" << endl;
    }
    int getId(){
        return id;
    }
    void setName(char* _name){
        if(strlen(_name) < 2)
        strcpy(name, _name);
        else
        cout << "invalid name" << endl;
    }
    char* getName(){
        return name;
    }
    void setSalary(float _salary){
        if(_salary > 1000)
        salary = _salary;
        else
        cout << "invalid salary" << endl;
    }
    float getSalary(){
        return salary;
    }
    int main() {
        Employee* ptr = new Employee();//indirect  =>  address in stack and object in heap
        ptr->id = 1;//error
        ptr->setId(10050000);
        ptr->print();
        Employee e1;//direct address in stack and object in stack limited size (stack overflow)
        delete ptr;

    return 0;
    }


};
```

# #lab1

1.  struct emp
    - attr {id,name , age , salary }
    - behaviour : setters , getters , print
    - standalone function print (emp e)
1.  struct => class
1.  bankaccount class
