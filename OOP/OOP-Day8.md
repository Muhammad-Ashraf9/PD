```cpp
class parent{
    int x;
    int y;
    public:
    parent(int _x=0,int _y=0){
        x=_x;
        y=_y;
    }
    int getX(){
        return x;
    }
    int getY(){
        return y;
    }
    void setX(int _x){
        x=_x;
    }
    void setY(int _y){
        y=_y;
    }
    int add(){
        return x+y;
    }
    int mul(){
        return x*y;
    }
    void print(){
        cout<<x<<" : "<<y;
    }
}
class child:public parent{
    int z;
    public:
    child(int _x=0,int _y=0,int _z=0):parent(_x,_y){
        z=_z;
    }
    int getZ(){
        return z;
    }
    void setZ(int _z){
        z=_z;
    }
    int add(){
        return Parent::add()+z;
        //return getX()+getY()+z;
    }


    void print(){
        Parent::print();
        cout<<" : "<<z;

    }

}
int main(){
    parent p1(1,2);
    cout << p1.add() << endl;
    child c1(1,2,3);
    cout << c1.add() << endl;//search for the function in the child class if not found search in the parent class
    cout << c1.mul() << endl;

    p1 = c1;//

    //c1 = p1;//error

    //pointer of type parent
    parent *p2 = &p1;
    p2->print();
    //pointer of type child
    child *c2 = &c1;
    c2->print();


    return 0;
}
```

> [!error] `childObject = parentObject` is not allowed
> `c1 = p1;` compiler error

> [!success] `parentObject = childObject` is allowed
> `p1 = c1;` it copies the parent part of the child object

> [!error] `childPointer = &parentObject` is not allowed
> `cptr = &p1;` compiler error

> [!success] `parentPointer = &childObject` is allowed
> `pptr = &c1;` can access the parent part of the child object
> `pptr->print();` it will print the parent part of the child object with child data
> `pptr->add();` it will call the parent add function
>
> > [!error] `pptr->setZ(5);` it will not work because the parent pointer can't access the child part of the child object

> [!important] `pptr->add()` will call the add depending on the pointer type not the object type

> [!info] early or static binding: the binding of the function call to the function definition happens at compile time

> [!info] late or dynamic binding: the binding of the function call to the function definition happens at run time
> by using `virtual` keyword in the parent class function

> [!important] `virtual int add(){return x+y;}` in the parent class and `int add(){return x+y+z;}` in the child class will call the child add function

> [!important] `virtual void print(){cout<<x<<" : "<<y;}` in the parent class and `void print(){cout<<x<<" : "<<y<<" : "<<z;}` in the child class will call the child print function

```cpp
class parent{
    public:
    virtual void print(){
        cout<<"parent";
    }
}
class child:public parent{
    public:
    void print(){
        cout<<"child";
    }
}
void myFunction(parent *p){
    p->print();
}
void myFunction(parent p){
    p.print();
}

int main(){
    parent* pptr;
    parent p1;
    child c1;
    pptr = &c1;
    pptr->print();//child
    myFunction(&p1);//parent
    myFunction(&c1);//child (virtual + override)
    // else => parent
    return 0;
}
```

> [!error] if parent class doesn't have print function
> `pptr->print();` error even if the child class has print function

> [!info] in programming languages like Java methods are virtual by default

```cpp

    Parent* pptr =  new child(4,5,6);
    pptr->print();//child
    pptr = new otherChild(4,5,6);
    pptr->print();//otherChild
    pptr = new child(9,8,7);
    pptr->print();//child
    delete pptr;


```

```cpp
Parent arr[3]={child(4,5,6),otherChild(4,5,6),Parent(9,8,7)};
for(int i=0;i<3;i++){
    arr[i].print();//parent parent parent
}

// so we need to use pointers and (virtual)
child c1(4,5,6);
Parent* arr[3]={&c1,new otherChild(4,5,6),new Parent(9,8,7)};
for(int i=0;i<3;i++){
    arr[i]->print();//child otherChild parent
}
```

> [!important] Reference is the same as pointer

```cpp
Parent& p1 = c1;
p1.print();//child
```

---

```cpp
class shape{
    int dim1;
    int dim2;
    public:
    shape(int _dim1=1,int _dim2=1){
        dim1=_dim1;
        dim2=_dim2;
    }
    int getDim1(){
        return dim1;
    }
    int getDim2(){
        return dim2;
    }
    void setDim1(int _dim1){
        dim1=_dim1;
    }
    void setDim2(int _dim2){
        dim2=_dim2;
    }
    virtual void calcArea(){
        cout<<"Area = "<<dim1*dim2<<endl;
    }


};
class Rect:public shape{
    public:
    Rect(int _dim1=1,int _dim2=1):shape(_dim1,_dim2){}
    int calcArea(){
        return getDim1()*getDim2();
    }
};
class Triangle:public shape{
    public:
    Triangle(int _dim1=1,int _dim2=1):shape(_dim1,_dim2){}
    float calcArea(){
        return getDim1()*getDim2()/2.0;
    }


};
void printArea(shape* s){
    cout<<s->calcArea();
}

int main(){
    shape s1(4,5);
    Rect r1(4,5);
    Triangle t1(4,5);
    printArea(&s1);//20
    printArea(&r1);//20
    printArea(&t1);//10
    return 0;
}
```

```cpp
class shape{
    public:
    virtual void calcArea()=0;//pure virtual function
}

int main(){
    shape s1;//error
    return 0;
}
```

> [!error] `shape s1;` error because shape is an abstract class
> abstract class can't be instantiated
>
> > [!success] `shape* s1;` is allowed because it's a pointer
> > `shape& s1;` is allowed because it's a reference

> [!error] `shape* s1 = new shape();` error because shape is an abstract class

> [!tldr] classes that inherit from abstract classes must implement all the pure virtual functions
>
> > [!error] `class Rect:public shape{};` error because it doesn't implement the pure virtual function

> [!tldr] ==Concrete== class is a class that doesn't have any pure virtual functions

```cpp
class Circle:public shape{
    public:
    Circle(){}
    Circle(int _dim1=1):shape(_dim1,_dim1){}
    void setDim1(int _dim1){ //we  have to add virtual to the parent function
     dim1=_dim1;
        dim2=_dim1;
    }
    void setDim2(int _dim2){
        dim1=_dim2;
        dim2=_dim2;
    }
    float calcArea(){
        return getDim1()*getDim2()*3.14;
    }

}
//SOLID
//open for extension closed for modification
void printArea(shape* s){
    cout<<s->calcArea();
}
int main(){
    shape* s1 = new Circle(4);
    printArea(&s1);//50.24
    s1->setDim1(5);

    return 0;
}
```

---

> [!info] virtual is inherited so if a function is virtual in the parent class it's virtual in the child class

> [!tip] if we have 3 classes A,B,C and B inherits from A and C inherits from B
> if we have a virtual function in A and we override it in C then `A* ptr = new C();` and `ptr->func();` will call the function in C

>[!warning]  but if the fuction virtual in B not A `A* ptr = new C();` and `ptr->func();` will call the function in A

---

```cpp
float calcTotalArea(shape* arr[],int size){//shape** arr or shape* arr[] => array of pointers to shape
//shape* arr* => pointer to array of pointers to shape
    float totalArea = 0;
    for(int i=0;i<size;i++){
        totalArea += arr[i]->calcArea();
    }
    return totalArea;
}
int main(){
    Rect r1(4,5);
Shape arr[3];//error
//Shape* arr[3];//allowed
shape** arr = new shape*[3];//array of pointers to shape objects (shape*)
arr[0] = &r1;
arr[1] = new Triangle(4,5);
arr[2] = new Circle(4);

delete arr[1];//delete the objects
delete arr[2];
delete[] arr;//delete the array
    return 0;
}
```

##### #lab8 simplify Pic class

> [!tldr] ==Read== Private vs Public vs Protected inheritance
> public inheritance: allows virtual functions to be inherited and late binding
> private inheritance: all functions are inherited as private
> protected inheritance: all functions are inherited as protected but private

# #lab8

- Copy & paste lecture
