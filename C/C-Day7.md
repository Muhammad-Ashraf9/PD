##### prime number

```C
int n,i;
printf("Enter a number: ");
scanf("%d",&n);
for(i=2;i<n;i++)
{
    if(n%i==0)
    {
        printf("Not prime");
        break;
    }
}
if(i==n)
{
    printf("Prime");
}



```

### Data Types

```C
int i = 5; // 4 bytes
float f = 5.5; // 8 bytes
f = i; // implicit casting
i = (int) f; // explicit casting
```

\>>>> implicit casting

char - short - int - long - float - double

<<<< explicit casting

[Type Casting in C: Type Conversion, Implicit, Explicit with Example (guru99.com)](https://www.guru99.com/c-type-casting.html)

---

```C
int x = 10993;
scanf("%d",x);// could work or not depending if this value is in the memory or not

```

### void\*

```C
void* ptr; // pointer to any data type
int i = 5;
double d = 5.5;
ptr = &i;
printf("%d",*ptr); // comp error
printf("%d",*(int*)ptr); // 5 we need to cast it to int* first to dereference it
```

---

```C
int x = 5;
int y = 10;
int* ptrx = &x;//variable pointer
int* const ptry = &y;// const pointer

ptrx = &y; // ok
ptry = &x; // comp error
```

---

---

memory representation of arr
| arr |  
| ---- |
| adress 0xff8400 |
| int |
| size |

memory
| adress | value |
| --------------- | --------------- |
| 0xff8400 | 1 |
| 0xff8404 | 2 |
| 0xff8408 | 3 |
| 0xff840c | 4 |
| 0xff8410 | 5 |

> [!important]+ stack size less than heap size
> this is why we use should know the size of the array in advance
> so it decides if it will be in the stack or heap

---

```C
int arr[5] = {1,2,3,4,5}; //static array  allocation in stack
printf("%d",arr[0]); // 1
int* ptr = arr[0]; // pointer to the first element of the array (base address) reads 4`bytes` from the first address (int)
printf("%d",*ptr); // 1
ptr++; // points to the second element +4 bytes as int
ptr+2; // points to the third element +8 bytes as int

*(ptr + 2 ); // 3
///////////
int* ptr = arr;
printf("%d",*ptr); // 1
printf("%d",*(ptr + 1)); // 2
int i;
for(i=0;i<5;i++)
{
    printf("%d,%d,%d,%d",*(ptr+i),ptr[i],*(arr+i),arr[i]);// 1,1,1,1 2,2,2,2 3,3,3,3 4,4,4,4 5,5,5,5
    printf("%d",*ptr++); //
    printf("%d",*++ptr); //
    printf("%d",++*ptr); //
    printf("%d",++(*ptr)); //
    printf("%d",++*(ptr)); //
    printf("%d",*arr++); // arr constant so comp error
    printf("%d",*++arr); // arr constant so comp error
    printf("%p",&arr[i]); // 0xff8400 0xff8404 0xff8408 0xff840c 0xff8410
    printf("%p",arr+ i); // 0xff8400 0xff8404 0xff8408 0xff840c 0xff8410
}
//scanf
for(i=0;i<5;i++)
{
    scanf("%d",arr+i); // &arr[i]
    scanf("%d",ptr+i); // &ptr[i]
}
```

> [!danger]+ EXAM Search all compinations of `*(ptr++),ptr[i],*(++arr),*(arr)` and understand them

> [!important]+ arr is constant pointer to the first element of the array
> that's why we can't do this `arr1 = arr2;`

> [!important]+ compiler turns `arr[i]` to `*(arr+i)`

---

### Dynamic allocation

##### malloc( size )

> size -> bytes

> [!important]+ returns void\* pointer to the first address of the allocated memory or NULL if it fails
> it allocates in the heap
> it doesn't initialize the allocated memory

```C
int n;
scanf("%d",&n);
 int arr[5] = {1,2,3,4,5}; //static array  allocation in stack
int* ptr = (int*) malloc(n*sizeof(int)); //n = 5 => 20 bytes dynamic allocation in heap cast it to int* to dereference it explicitly
if(ptr == NULL)
{
    printf("Error");
    exit(1);// exit the program
}

scanf("%d",ptr); // 1
scanf("%d",ptr+1); // 2
scanf("%d",ptr+2); // 3
double* ptr2 = (double*) malloc(n*sizeof(double)); // 40 bytes dynamic allocation in heap cast it to double* to dereference it explicitly

free(ptr); // free the allocated memory
```

> [!important]+ memory leak if we don't free the allocated memory

> [!important]+ fragmentation if we allocate and free memory many times

---

### dynamic struct array


>[!important] `(*ptr).name` is the same as `ptr->name`
```

```C
struct student
{
    char name[20];
    int age;
};
int n,i;
scanf("%d",&n);
struct student* ptr = (struct student*) malloc(n*sizeof(struct student)); //  dynamic allocation in heap cast it to struct student*
for(i=0;i<n;i++)
{
    scanf("%s",ptr[i].name); // or scanf("%s",(ptr+i)->name);
    scanf("%d",&ptr[i].age); // or scanf("%d",&(ptr+i)->age);
}
for(i=0;i<n;i++)
{
    printf("%s",(ptr+i)->name); // or printf("%s",ptr[i].name); or printf("%s",(*(ptr+i)).name);
    printf("%d",(ptr+i)->age);// or printf("%d",ptr[i].age); or printf("%d",(*(ptr+i)).age);
}
free(ptr); // free the allocated memory
//struct pointer
struct student e1 = {"aly",20};
struct student* ptr = &e1;
printf("%s",ptr->name); // aly ptr->name == (*ptr).name
printf("%d",ptr->age); // 20 ptr->age == (*ptr).age
scanf("%s",ptr->name); // or scanf("%s",&(ptr+i)->name);
scanf("%d",&ptr->age); // or scanf("%d",&(ptr+i)->age);
// ptr.name = "aly"; // comp error
//*ptr.name = "aly"; // comp error priority of . is higher than *
(*ptr).name = "aly"; // ok


struct student* ptr2 = (struct student*) malloc(1*sizeof(struct student)); //

```

---

#### pointer to pointer

```C
int x = 5;
int* ptr = &x; // pointer to int
int** ptr2 = &ptr; // pointer to pointer  to int
//scan
scanf("%d",&x ); // 5
scanf("%d",ptr ); // 5
scanf("%d",*ptr2 ); // 5
//print
printf("%d",x); //  5
printf("%d",*ptr); // 5
printf("%d",**ptr2); // 5

printf("%d",*ptr2); // 0xff8400

// *x // comp error it gets the value in memory of address x which is 5

```

representing of pointer to pointer in memory


| adress | value | variable |
| --------------- | --------------- | --------------- |
| 0xff8400 | 0xff8404 | ptr |
| 0xff8404 | 5 | x |
| 0xff8408 | 0xff8400 | ptr2 |


----
[c - Why use double indirection? or Why use pointers to pointers? - Stack Overflow](https://stackoverflow.com/questions/5580761/why-use-double-indirection-or-why-use-pointers-to-pointers)


[Querying the Document | HackerRank](https://www.hackerrank.com/challenges/querying-the-document/problem?isFullScreen=true)

---

![](Pasted%20image%2020231103205956.png)
---
## #lab7

1. static allocated allocted int array pointer
   - print with different ways arr[i],_(arr+i),ptr[i],_(ptr+i)
   -
2. dynamic allocated int array
   - print
   - scan
   - free

3.dynamic array of struct

4. pointer to struct
   - print
   - scan
