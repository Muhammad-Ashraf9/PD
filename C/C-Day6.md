### word-letter count

```C
char ch;
int wordCount = 0,letterCount = 0;
do
{
    ch = getch();
    if (ch == ' ')
    {
        wordCount++;
    }
    else
    {
        letterCount++;
    }
    printf("%c",ch);
}while(ch != '\r'); // '\r' is the enter key
// do while =>  `gets()`
printf("\nWord Count = %d\nLetter Count = %d",wordCount,letterCount);
```

> [!info]+ we can use fuction for this

---
### local variables 

```C
#include <stdio.h>
#include <stdlib.h>
int add(int num1,int num2);

int main()
{
    int x = 3,y =5;//scope inside main
    int z;
    z = add(x,y);
    printf("%d",add(c,y)*2);
    return 0;
}
int add (int num1,int num2)//scope inside add
{
    int sum; //lifetime call=>end of excution
    sum = num1 + num2;
    return sum;
}
```

---

RAM:
code section
heap

---

>[!info]+ ==stack== is less in size and  faster than heap

>on calling main

|                         |
| ----------------------- |
| variables in main scope |
| x = 10 ,y = 20,z        |

call by value `add(a,b)`

---

#### global variables

outside any function
life time start of program until the end
in global section

```C
int c =30; //global
int main()
{
printf("%d",c);//30
changeGlobal();
printf("%d",c);//50 if no local variable with the same name
return 0;
}
void changeGlobal()
{
   // int c; // valid as function search for local variables first then global
    //in this case c is local and the change only in local
    c = 50;
}
```

> [!danger]+ global variables are not recommended
> as any fuction can change it
> and it is hard to debug

> [!important]+ search statement to deal with global variables when having local variables with the same name

[How to Access Global Variable if there is a Local Variable with Same Name in C/ C++? - GeeksforGeeks](https://www.geeksforgeeks.org/how-to-access-global-variable-if-there-is-a-local-variable-with-same-name-in-c-cpp/)

[extern Keyword in C - GeeksforGeeks](https://www.geeksforgeeks.org/understanding-extern-keyword-in-c/)

---

### swap 2 numbers

```C
#include <stdio.h>
#include <stdlib.h>
void swap(int x,int y);
int main()
{
    int x = 3,y =5;
    swap(x,y); //call by value no cahnge in x and y
    printf("x = %d\ny = %d",x,y);
    return 0;
}
void swap(int x,int y)
{
    int temp;
    temp = x;
    x = y;
    y = temp;
    // can't return 2 values
}
```

---

```C
#include <stdio.h>
#include <stdlib.h>
struct Emp{
    int id;
    char name[20];
    int salary;
}; //struct first
struct Emp globalVariableEmp;//global variable after struct as we can define
void printEmp(struct Emp e);// after struct
struct Emp returnStruct(struct Emp e);
void printArr(int arr[],int size);
int main()
{
    struct Emp e1 = {1,"ahmed",5000};
    struct Emp e2 = {2,"ali",6000};
    printEmp(e1);
    printEmp(e2);
   struct Emp e3 = returnStruct(e1);
    printEmp(e3);
    int x[5] = {1,2,3,4,5};
    printArr(x,5);
    printf("%d",x[0]);//10 as it is changed in printArr function as it is passed by (reference)

    return 0;

}
void printEmp(struct Emp e)
{
    printf("%d %s %d",e.id,e.name,e.salary);
    e.id = 10;//change in local variable only not in e1 or e2
}
struct Emp returnStruct(struct Emp e)//we can return multiple values using structs
{
    printf("%d %s %d",e.id,e.name,e.salary);
    e.id = 10;
    return e; //now it is changed in e1 or e2
}
void printArr(int arr[],int size)//as we dont know the size
//workaround we can use sizeof and divide by datatybe size to get the size
//
{
    int i;
    for(i = 0; i<size; i++)
    {
        printf("%d",arr[i]);
    }
    x[0] = 10;//array changes in main as it is passed by (reference)
   // return arr[]; //cant return (local) array in c as it return address and after function end it is destroyed

}
```

# #pointers

```C
char ch ='a';//00001111
double d = 3.5;//
int x = 5;//
printf("%d",ch);//97
printf("%d",&ch);//& address of ch
printf("%x",&ch);//& hex address of ch
printf("%p",&ch);//&  %p suitable for address
printf("%p",&d);// &d address of d after ch in memory
printf("%c",ch);//a
printf("%o",ch);//141 octal
printf("%x",ch);//61 hex
const int x = 5;//constant variable cant be changed after intialization and must be intialized when declared

x = 3;//error as it is constant


int* ptrx = &x;//pointer to int address of x in memory
printf("%p",ptrx);//address of x
printf("%d",*ptrx);// value of x => 5//derefernce (indirect) operator * => 5
// ptrx has first byte address of x and reads 4 bytes according to data type of ptrx (int) and print it

double* ptrd = &d;//pointer to double address of d in memory
printf("%p",ptrd);//address of d  in memory 
printf("%p",*ptrd);
//it reads 8 bytes according to data type of ptrd (double) and print it

 printf("%d",sizeof(ptrx));//4 bytes
 printf("%d",sizeof(ptrd));//4 bytes
 //size of pointers are 4 bytes in 32 bit and 8 bytes in 64 bit as it is address of any data type

```

>[!important] Pointers size
>In C size of a pointer is not fixed as it depends on Word size of the processor. In general **a 32-bit computer machine then size of a pointer would be 4 bytes** while for a 64-bit computer machine, it would be 8 bytes.



```C
int x = 5;
int* ptrx = &x;
printf("%d",x);//5
printf("%d",*ptrx);//5
printf("%p",&x);//address of x
printf("%p",ptrx);//address of x
scanf("%d",&x);//5
scanf("%d",ptrx);//5
```

> [!info]+ ptrx is pointer to x
> *ptrx is value of x `*ptrx = x`
> ptrx is address of x ptrx = &x

```C
int x = 5;
int* ptrx = &x;
int y = 10;
int* ptry = &y;

*ptrx = 10;//change value of x
printf("%d",x);//10
*ptrx = *ptry; //change value of x to y
printf("%d",x);//10
ptrx = ptry;//change address of x to y
// ptrx = &y; 2pointers to same address
//we change the value of y through ptrx or ptry or y itself
```

> [!info]+ `*` is derefernce operator

---

> [!hint]+ we can use pointers to implement swap function

```C

#include <stdio.h>
#include <stdlib.h>
void swap(int* x,int* y);
int main()
{
    int x = 3,y =5;
    swap(&x,&y); //call by reference change in x and y
    printf("x = %d\ny = %d",x,y);
    return 0;
}
void swap(int* x,int* y)
{
    int temp;
    temp = *x;
    *x = *y;
    *y = temp;
}
```


## #lab6

1. swap  ==value==  ==address==
2. pass arr , struct to fucn
3. Menu arr of struct emp[5]
	- new => index => id, name, salary (ask for modification) check on id 
	- display show only entered data
	- search by id
	- search by name
	- delete by id 
	- delete by name
	- exit