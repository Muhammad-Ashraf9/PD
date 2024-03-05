  

> [!warning] EntityLists

  

```csharp

//DepartmentList.cs in EntityLists folder

namespace DataBusinessLayer

{

    public class DepartmentList : List<Department>

    {

        //overriding Add (optional)

        public void Add(Department department)

        {

            base.Add(department);

        }

  

    }

}

```

  

```csharp

//StudentList.cs in EntityLists folder

namespace DataBusinessLayer

{

    public class StudentList : List<Student>

    {

    }

}

```