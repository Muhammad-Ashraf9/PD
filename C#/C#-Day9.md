#### Revision on Delegates

```cs
namespace Day9{
// 1.Delegate declaration
 delegate int Delegate1(int a, int b);
 //2.instance member
   public int Add(int a, int b)
    {
        Console.WriteLine("Instance Add");
        return a + b;
    }
    static int Add(int a, int b)
    {
        Console.WriteLine("Static Add");
        return a + b;
    }
    static int Sub(int a, int b)
    {
        Console.WriteLine("Static Sub");
        return a - b;
    }
    static int Mul(int a, int b)
    {
        Console.WriteLine("Static Mul");
        return a * b;
    }
 internal class Program
 {
    static void Main(string[] args)
    {
        Program p = new Program();
        // 3.Delegate instantiation
        Delegate1 del1 = new Delegate1(Add);//if the method is static
        //or
        Delegate1 del1 = new Delegate1(p.Add);//if the method is instance
        // 4.Delegate invocation
        int result = del1(10, 20);
        //or
        result = del1.Invoke(10, 20);

        // method & target
        // method: the method that is invoked when the delegate is invoked
        // target: the object on which the method is invoked
        Console.WriteLine(result);

        //we can add multiple methods to a delegate(+=) add to the invokation list
        // Invokation list: the list of methods that are invoked when the delegate is invoked
        del1 += new Delegate1(Sub);

        //we can remove a method from the delegate(-=) remove from the invokation list
        del1 -= new Delegate1(Sub);

        // if Mul was in the same class static
        del1 += new Delegate1(Mul);
        //if Mul was in another class static
        del1 += new Delegate1(AnotherClass.Mul);
    }
 }

}
```

> [!tip] Generic Delegates
>
> - we use generic delegates instead of declaring a delegate for each method signature (data types)

```cs
// 1.Delegate declaration
//int
delegate int Delegate1(int a, int b);
//double
delegate double Delegate2(double a, double b);

//it is better to use generic delegates instead of declaring a delegate for each method signature (data types)
delegate T3 Delegate3<T1, T2, T3>(T1 a, T2 b);
```

```cs
//main
internal class Program
{
    static int Mul(int a, int b)
    {
        Console.WriteLine("Static Mul");
        return a * b;
    }

    static void Main(string[] args)
    {
        Delegate3<int, int, int> del1 = Mul;

        //problem:  different method signatures
        //we have to declare a delegate for each method signature

        //using Func instead

        // Using Func (when function returns a value)
        Func<int, int, int> del2 = Mul;

        var result = Subtract;
        //let the compiler infer the type =>Func<int, int, int>

        //once variable is declared its type cannot be changed
        // we can't assign other methods to result with different signature

        //Anonymous function
        Func<int, int, int> del3 = delegate (int a, int b)
        {
            Console.WriteLine("Anonymous Sub");
            return a - b;
        };

        //lambda expression
        Func<int, int, int> del4 = (a, b) =>
        {
            Console.WriteLine("Lambda Sub");
            return a - b;
        };
    }
}
```

> [!warning] `Func` vs `Action` vs `Predicate`
>
> - `Func` is used when the method returns a value
> - `Action` is used when the method does not return a value
> - `Predicate` is used when the method returns a boolean value

```cs
//main
static void Main(string[] args)
{
    //create a list of integers
    List<int> list = new List<int> ();
    list.Add(1);
    //create a list and initialize it
    List<int> list = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
    // or
    List<int> list = [1, 2, 3, 4, 5, 6, 7, 8, 9];

    // list.FindAll(predicate) pridicate(int) => bool
    //int as list is a list of integers
    // we create a predicate that takes an int and returns a bool (Condition1)

    Predicate<int> predicate = Condition1;
    list.FindAll(predicate);

    //instead of creating a predicate we can use function name
    list.FindAll(Condition1);
    //or use anonymous function / lambda expression (on using only once)
    list.FindAll(delegate (int i)
    {
        return i >= 5 && i <= 60;
    });
    //or
    list.FindAll((i) =>
    {
        return i >= 5 && i <= 60;
    });
    //we can remove the curly braces if the body of the function is one line
    list.FindAll((i) => i >= 5 && i <= 60);


    //we can create method that returns a list MyFindAll

}
```

```cs
//Conditions
//Condition1
public static bool Condition1(int i)
{
    return i>=5 && i<=60;
}
```

```cs
//MyFindAll
public static List<int> MyFindAll(List<int> list)
{
    List<int> result = new List<int>();
    foreach (var item in list)
    {
        if (item >= 5 && item <= 60)
        {
            result.Add(item);
        }
    }
    return result;
}
```

```cs
//main
static void Main(string[] args)
{
    //create a list of integers
    List<int> list = [1, 2, 3, 4, 5, 6, 7, 8, 9];
    //call MyFindAll
    var result = MyFindAll(list);

    //if we wan to use other conditions we have to create other methods (MyFindAll2, MyFindAll3, ...)
}
```

```cs
//MyFindAll2
public static List<int> MyFindAll2(List<int> list)
{
    List<int> result = new List<int>();
    foreach (var item in list)
    {
        if (item >= 5 && item <= 60)//the only difference between MyFindAll and MyFindAll2
        //we can pass the condition as a parameter in MyFindAll
        {
            result.Add(item);
        }
    }
    return result;
}
```

```cs
//MyFindAll: take a condition as a parameter
public static List<int> MyFindAll(List<int> list, Func<int, bool> condition)
//we can use Predicate<int> instead of Func<int, bool> as Predicate<int> is specialized version of Func<int, bool>
public static List<int> MyFindAll(List<int> list, Predicate<int> condition)
{
    List<int> result = new List<int>();
    foreach (var item in list)
    {
        if (condition.Invoke(item))//in main we can pass the condition as a parameter
        {
            result.Add(item);
        }
    }
    return result;
}
```

```cs
//main
static void Main(string[] args)
{
    //create a list of integers
    List<int> list = [1, 2, 3, 4, 5, 6, 7, 8, 9];
    //call MyFindAll
    var result = MyFindAll(list, (i) => i >= 5 && i <= 60);
    //other condition same signature
    var result = MyFindAll(list, Condition1);
    //or
    var result = MyFindAll(list, (i) => i >= 5 && i <= 60 && i % 2 == 0);



    //synatic sugar for array
    int[] arr = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
    //or instead
    int[] arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
}
```

> [!tip] LinQ
>
> - Language Integrated Query
> - used to query data from different data sources
> - same syntax for different data sources

---

```cs
//swap (generic)
public void Swap<T>(ref T a, ref T b)
{
    T temp = a;
    a = b;
    b = temp;
}
```

```cs
//sortAscending
public void SortAscending(int[] arr)
{
    for (int i = 0; i < arr.Length; i++)
    {
        for (int j = i + 1; j < arr.Length; j++)
        {
            if (arr[i] > arr[j])
            {
                Swap<int>(ref arr[i], ref arr[j]);//we can remove <int> as the compiler can infer the type
                Swap(ref arr[i], ref arr[j]);
            }
        }
    }
}
```

```cs
//main
static void Main(string[] args)
{

    int[] arr = [14, 32, 35, 14, 15, 3, 7, 8, 9];
    SortAscending(arr);
    //if SortAscending was instance method in class Sorting
    Sorting s = new Sorting();
    s.SortAscending(arr);
    foreach (var item in arr)
    {
        Console.WriteLine(item);
    }
}
```

```cs
//SortDescending
public void SortDescending(int[] arr)
{
    for (int i = 0; i < arr.Length; i++)
    {
        for (int j = i + 1; j < arr.Length; j++)
        {
            if (arr[i] < arr[j])//same as SortAscending but with < instead of >
            //we can create a method SortAscendingOrDescending that takes 2 parameters and return a bool
            {
                Swap<int>(ref arr[i], ref arr[j]);//we can remove <int> as the compiler can infer the type
                Swap(ref arr[i], ref arr[j]);
            }
        }
    }
}
```

```cs
SortAscendingOrDescending(int arr[], Func<int, int, bool> condition)
//we have to use Func<int, int, bool> instead of Predicate<int> as Predicate<int> takes only one parameter
{
    for (int i = 0; i < arr.Length; i++)
    {
        for (int j = i + 1; j < arr.Length; j++)
        {
            if (condition.Invoke(arr[i], arr[j]))
            {
                Swap(ref arr[i], ref arr[j]);
            }
        }
    }
}
```

````cs
//main
static void Main(string[] args)
{
    int[] arr = [14, 32, 35, 14, 15, 3, 7, 8, 9];
    SortAscendingOrDescending(arr, (a, b) => a > b);
    //or
    SortAscendingOrDescending(arr, (a, b) => a < b);

}



```cs
//we can declare Func<int, int, bool> condition as a field in the class
//and use it in SortAscendingOrDescending

class Sorting
{
    Func<int, int, bool> condition;
    public void SortAscendingOrDescending(int[] arr)
    {
        for (int i = 0; i < arr.Length; i++)
        {
            for (int j = i + 1; j < arr.Length; j++)
            {
                if (condition.Invoke(arr[i], arr[j]))//fire the event
                // then the event handler will be invoked

                //Exception: Object reference not set to an instance of an object. (condition is null)
                //default value of Reference type is null
                //in main we have to initialize condition
                {
                    Swap(ref arr[i], ref arr[j]);
                }
            }
        }
    }
}
````

```cs
internal class Program
{

public void conditionDesc(int a, int b)
{
    return a < b;
}
//main
    static void Main(string[] args)
    {
        int[] arr = [14, 32, 35, 14, 15, 3, 7, 8, 9];
        Sorting s = new Sorting();
        s.condition = (a, b) => a > b;//initialize condition
        //so we can use this condition in multiple methods in Sorting class
        s.SortAscendingOrDescending(arr);//sort ascending

        //change condition
        s.condition = conditionDesc;//registeration: register conditionDesc to the event handler in invokation list of condition

        s.SortAscendingOrDescending(arr);//sort descending
        // to add another condition we change the condition in
        //Sorting class to be (event )

        // we can use =
        s.condition = (a, b) => a > b && a % 2 == 0;//Error
        //we have to use +=
        s.condition += (a, b) => a > b && a % 2 == 0; //to add event handler to the invokation list
    }
}
```

> [!danger] Event handling model
>
> - now we can say that 2 classes are linked together
> - Sorting class and Program class
> - Event handling model
> - publish-subscribe model => publisher(Sorting) and subscriber(Program)
> - any change in the publisher will be reflected in the subscriber

> [!example] Form & Button Example (Event handling model)

---

> [!example] Student in ITI Example (Event handling model)
>
> - we want to kick out students who have have absences more than 3 days
> - we create an event on the student class(absenceMoreThan3Days)
>
>   > [!bug] Student class
>   >
>   > - `public event Func<Student,void> absenceMoreThan3Days;` (event)
>
> - (that will be fired when the student has more than 3 absences)
> - we can add event handlers to the event in the main(all the fuctions that we want to happen when the event is fired=>kick out the student)
> - the event (student has more than 3 absences)
> - the event handler (kick out the student)maybe.. (send an email to the parents)
>   > [!bug] Departement class
>   >
>   > - we have students list in the departement class
>   > - we have kickStudent(Student s) method in the departement class
>   > - we have SendEmailToHisFather(Student s) method in the departement class
>
> > [!warning]Main
> >
> > - we have to register the event handler (kickStudent) from the departement class to the event ()

---

> [!tip] ITIDataTypes Class Library

```cs
namespace ITIDataTypes
{
    //event publisher
    public class Student
    //we have to make it public to use it in other projects (internal by default)
    {
        //event

        //Step1: declare the event
        public Action<Student> StudentAbsent;
        //we add event to the name of the event so we can have multiple subscribers (department and club)
        public event Action<Student> StudentAbsent;
        int absentDays;//
        public string Id { get; set; }
        public string Name { get; set; }
        public int Age { get; set; }
        public int AbsentDays
        {
            get => absentDays;
            set
            {
                absentDays += value;
                if (absentDays > 5)
                {
                    // fire the
                    StudentAbsent.Invoke(this);
                    //if we call it before the event is registered with (event handlers => functions)we will get NullReferenceException

                    //adding event handlers to the event after adding the student to the departement will solve the problem  (in the departement class)
                        StudentAbsent?.Invoke(this);//to check if the event is null or not
                }
            }
        }
        public Student()
        {
            Id = "0000";
            Name = "No Name";
            Age = 0;
            AbsentDays = 0;
        }
    //overriding ToString
        public override string ToString()
        {
            return $"Id: {Id}, Name: {Name}, Age: {Age}, AbsentDays: {AbsentDays}";
        }
    }
}
```

```cs
namespace ITIDataTypes
{
    public class Departement
    //we have to make it public to use it in other projects (internal by default)
    {
        List<Student> students;
        public string DepartementName { get; set; }
        public Departement()
        {
            DepartementName = "No Name";
            students = new List<Student>();
        }
        public void AddStudent(Student s)
        {
             //we don't care about the absent days before the student is added to the departement
            //so we add event handlers after adding the student to the departement
            students.Add(s);
            //Step2: register the event handler
            s.StudentAbsent += RemoveStudent;
        }
        //4. event handler
        public void RemoveStudent(Student s)
        {
            students.Remove(s);
        }
        public void print()
        {
            Console.WriteLine($"Departement Name: {DepartementName}, Students: ");
            foreach (var item in students)
            {
                Console.WriteLine(item);
            }
    }
    //overriding ToString
        public override string ToString()
        {
            return $"Departement Name: {DepartementName},contains {students.Count} students";
        }
    }
}
```

```cs
//class Club
    namespace ITIDataTypes
    {
        public class Club
        {
            List<Student> students;
            public string ClubName { get; set; }
            public Club()
            {
                ClubName = "No Name";
                students = new List<Student>();
            }
            public void AddStudent(Student s)
            {
                students.Add(s);
                //Step2: register the event handler
                s.StudentAbsent += RemoveStudent;
            }
            //4. event handler
            public void RemoveStudent(Student s)
            {
                students.Remove(s);
            }
            public void print()
            {
                Console.WriteLine($"Club Name: {ClubName}, Students: ");
                foreach (var item in students)
                {
                    Console.WriteLine(item);
                }
            }
            //overriding ToString
            public override string ToString()
            {
                return $"Club Name: {ClubName},contains {students.Count} students";
            }
        }
    }
```

```cs
//main
using ITIDataTypes;
namespace Day9
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Student s1 = new Student(){Id = "1", Name = "Ahmed", Age = 20};
            Student s2 = new Student(){Id = "2", Name = "Ali", Age = 21};
            Student s3 = new Student(){Id = "3", Name = "Mohamed", Age = 22};
            Student s4 = new Student(){Id = "4", Name = "Mahmoud", Age = 23};

            Console.WriteLine(s1);
            Departement pd = new Departement(){DepartementName = "PD"};
            Departement os = new Departement(){DepartementName = "OS"};
            pd.AddStudent(s1);
            pd.AddStudent(s2);
            pd.AddStudent(s3);
            pd.AddStudent(s4);
            //student has to be only once in the departement
            //we can use HashSet instead of List

            Console.WriteLine(pd);//Departement Name: PD,contains 4 students

            pd.print();//Departement Name: PD, Students:
            //Id: 1, Name: Ahmed, Age: 20, AbsentDays: 0
            //Id: 2, Name: Ali, Age: 21, AbsentDays: 0
            //Id: 3, Name: Mohamed, Age: 22, AbsentDays: 0
            //Id: 4, Name: Mahmoud, Age: 23, AbsentDays: 0

            //we want to check for students who have more than 5 absences
            //remove them from the departement

            //want to excute fuction RemoveStudent(Student s) in the departement class when the event(student has more than 3 absence) is fired

            s1.AbsentDays = 6;//fire the event

            Club c1 = new Club(){ClubName = "Mansoura"};
            c1.AddStudent(s1);
            c1.AddStudent(s2);
            c1.AddStudent(s3);
            c1.AddStudent(s4);
            //when student has more than 5 absences remove him from the club

            pd.print();//Departement Name: PD, Students:
            //Id: 2, Name: Ali, Age: 21, AbsentDays: 0
            //now s1 is removed from the departement

            c1.print();//Club Name: Mansoura, Students:
            //Id: 1, Name: Ahmed, Age: 20, AbsentDays: 0
            //s1 still in the club we have to add event handler to event

            //we can remove event handler from the event
            s1.StudentAbsent -= pd.RemoveStudent;

        }
    }
}
```

> [!danger] we add `event` to force the user to use `+=` and `-=` to add and remove event handlers
>
> > [!example] without `event` keyword
> >
> > - we can use `=` instead of `+=` and `-=` to add and remove event handlers

> [!warning] to send message on removing student from the departement
>
> - we can send additional data to the event handler so we use `EventHandler` instead of `Action`
>
> > [!done] EventHndler
> >
> > - `public event EventHandler studentAbsent;`
> > - `void EventHandler(object sender, EventArgs e);`
> > - object sender: the object that fired the event (student)(this)
> > - EventArgs e: the data that we want to send to the event handler (message)
> > - change all RemoveStudent(Student s) to RemoveStudent(object sender, EventArgs e)
> >
> > ```cs
> > public void RemoveStudent(object sender, EventArgs e)
> > {
> >     //we have to cast sender to Student
> >     Student s = sender as Student;//as: if sender is Student cast it to Student else return null
> > Students.Remove(s);
> > }
> > ```


