# #lab7

- fraction
- queue
- string
- duration(h,m,s)

```cpp
class Circle{
    Point* center;
    int radius;
    public:
    Circle(Point* _center){
        center = _center;
        radius = 1;
    }
    Circle(Point* _center, int _radius){
        center = _center;
        radius = _radius;
    }
    ~Circle(){
        cout << "Circle Destructor" << endl;
    }
    void setCenter(Point* _center){
        center = _center;
    }
    void setCenter(int x, int y){
        center->setPoint(x,y);
    }

    void setRadius(int _radius){
        radius = _radius;
    }

    void print(){
        cout << "Center: ";
        center->print();
        cout << "Radius: " << radius ;
    }


};
```

```cpp
class Pic{
    Circle* c;
    Rect* r;
    int numCircles;
    int numRects;
    public:
    Pic(){
        numCircles = 0;
        numRects = 0;
        r = NULL;
        c = NULL;
    }
    Pic(Rect* _r, int _numRects, Circle* _c, int _numCircles){
        numCircles = _numCircles;
        numRects = _numRects;
        r = _r;
        c = _c;
        }
        void paint(){
            for(int i = 0; i < numRects; i++){
                r[i].draw();
            }
            for(int i = 0; i < numCircles; i++){
                c[i].draw();
            }
        }
};
int main(){
    int gdriver = DETECT, gmode, errorcode;
    initgraph(&gdriver, &gmode, "");
    Rect r1[3] = {Rect(10,20,30,40),Rect(100,100,200,200),Rect(300,300,400,400)};
    Circle c1[2] = {Circle(100,100,50),Circle(200,200,50)};
    Pic p(r1,3,c1,2);
    p.paint();
    getch();
    closegraph();
    return 0;
}

```

## Inheritance

```cpp
class Person{
    protected:
    char[10] name;
    int age;
    int id;
    public:
    Person(){
        id = -1;
        name = "No Name";
        age = 18;
        cout << "Person Parameterless Constructor" << endl;
    }
    Person(int _id, char* _name){
        id = _id;
        strcpy(name,_name);
        age = 18;
    }
    Person(int _id, char* _name, int _age){
        id = _id;
        strcpy(name,_name);
        age = _age;
    }

    // Setter for id
    void setId(int _id) {
        id = _id;
    }

    // Getter for id
    int getId() {
        return id;
    }

    // Setter for name
    void setName(char* _name) {
        strcpy(name, _name);
    }

    // Getter for name
    char* getName() {
        return name;
    }

    // Setter for age
    void setAge(int _age) {
        age = _age;
    }

    // Getter for age
    int getAge() {
        return age;
    }
    void print(){
        cout << "ID: " << id << endl;
        cout << "Name: " << name << endl;
        cout << "Age: " << age << endl;
    }
};
class Student : public Person{
    char grade;
    public:
    Student(){
        id = 3;//can't Parent's private members so we use protected
        cout << "Student Parameterless Constructor" << endl;
        }
        Student(int _id, char* _name):Person(_id,_name){
          grade = 'A';
        }
        Student(int _id, char* _name, int _age, char _grade):Person(_id,_name,_age){
            //no need to set id, name, age because we called the parent's constructor
            grade = _grade;
        }
        setGrade(char _grade){
            grade = _grade;
        }
        char getGrade(){
            return grade;
        }
        void print(){//overriding
            // cout << "ID: " << id << endl;
            // cout << "Name: " << name << endl;
            // cout << "Age: " << age << endl;
            Person::print();//calling parent's print
            cout << "Grade: " << grade << endl;
        }
};
class Employee:Pulic Person{
    float salary;
    public:
    Employee(){
        salary = 3000;
    }
    Employee(int _id, char* _name):Person(_id,_name){
        salary = 3000;
    }
    Employee(int _id, char* _name, int _age):Person(_id,_name,_age){
        salary = 3000;
    }
    Employee(int _id, char* _name, int _age, float _salary):Person(_id,_name,_age){
        salary = _salary;
    }
    void setSalary(float _salary){
        salary = _salary;
    }
    float getSalary(){
        return salary;
    }
    void print(){//overriding
        Person::print();//calling parent's print
        cout << "Salary: " << salary << endl;
    }
};
void myFunc(Person s){
    s.print();
}
int main(){
Student s1;//calls Person's parameterless constructor
s1.setId(10);
s1.setName("Ahmed");
s1.setAge(20);
s1.print();
s1.Person::print();//calling parent's print
Employee e1(20,"Ali",30,5000);
myFunc(s1);
myFunc(e1);
Person arr[3] = {Person(1,"Ahmed",20),Student(2,"Ali",30,'A'),Employee(3,"Omar",40,5000)};


return 0;
}

```

### private vs protected

> [!info] private members are inherited but can't be accessed directly from the child class

> [!info] protected members are inherited and can be accessed directly from the child class

- Trace copy constructors

---

#### multilevel inheritance vs multiple inheritance

> [!tldr] multilevel inheritance is when a class inherits from another class that inherits from another class

> [!tldr] multiple inheritance is when a class inherits from more than one class

> [!Note] `virtual` is used to solve the diamond problem
> happens when a class inherits from two classes that inherit from the same class


# #lab7 

- classes (Emp Student Person)