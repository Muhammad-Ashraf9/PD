# structure

## struct

```C
struct Employee{
	int id; //4byte
	char name[10]; //10
	int salary;//4
};// 18 byte total

int main()
{
	//intialization
	//1. with declaration like array
	struct Employee e1 ={10,"ahmed",2000};//with arrangement id first


	struct Employee e1; //rubbish data

	//2.

	e1.id = 10;
	// e1.name = "ahmed"; Error => isntead use strcpy()
	strcpy(e1.name,"ahmed");
	//(.) access operator
	e1.salary =2000;
	printf("%d",e1.d);
	printf("%s",e1.name);
	//scan
	scanf("%d",&e2.id); //& need address =>int
	scanf("%s",e2.name); // no need for &
	scanf("%d",&e2.salary);


	struct Employee arr[1000]; // array for employees
	struct Employee e2;
	e2 = e1; //can assign values from e1 in e2 like strcpy();(same type)
	// and not (refernce) only (value)
// or this way
	e2.id = e1.id;
	e2.salary = e1.salary;
	//e2.name = e1.name; Error
	strcpy(e2.name,e1.name);

	//Array of structs
	struct Employee employees[5]; //we can intialize
	// struct Employee employees[5]={{1,"ali",34343},{},{}...size}
	int i;
	// #define size 5
	for(i = 0; i<5; i++)
	{
		printf("%d %s %d",employees[i].id,employees[i].name,employees[i].salary);
	}
	//scan
	for(i = 0; i<5; i++)
	{
		printf("Enter (%d) emp id ",i+1);
		scanf("%d",&employees[i].id);//& int

		printf("Enter e (%d)mp name ",i+1);
		scanf("%s",employees[i].name);//no & string

		printf("Enter emp (%d) salary ",i+1);
		scanf("%d",&employees[i].salary);
		,employees[i].name,employees[i].salary
	}


}

```

> [!danger]- can't assign 2 struct of different types even if their attributes (members) are the same.
> `struct  Emp e1; struct Man e2; e1= e2`

> [!info]+ can define struct inside other struct.

> [!hint]+ can't initialize inside struct. (other programming languages delegate this to constructor)

### how access work

| name   | size | offset |
| ------ | ---- | ------ |
| id     | 4    | 0      |
| name   | 10   | 4      |
| salary | 4    | 14     |

e1 => address in memory =1000

| id     | 1000 |
| ------ | ---- |
| name   | 1004 |
| salary | 1014 |

> [!not]+ address + offset
> >[!example]- e1.salary
> 1000 + 14 = 1014



----


```C
struct Emp employess[3];
```
`employess` in memory

 | employess[0].id     | 1000 |
 | ------------------- | ---- |
 | employess[0].name   | 1004 |
 | employess[0].salary | 1014 |
 |                     |      |

 | employess[1].id   | 1018 |
 | ----------------- | ---- |
 | employess[1].name | 1022 |
 | employess[1].salary|1032|

---




```C
#define size 5
struct Address{
char city[20];
int streetNo ;
}; //should be defined before emp
struct emp{ // add overtime deduction and calc total salary
	int id; //4byte
	char name[10]; //10
	int salary;//4
	int overtime;
	int deduction;
	struct Address empAddress; //struct inside struct

};
```

> [!important]+ can assign struct to other struct of the same type even if they include arrays
>
> > [!caution]+ (can't assign arrays to each other)

## #lab5

1. employee(id ,name , age , salary, deduction, overtime).
   - initialize
   - scan
   - print
2. array of emp
   - scan
   - print

---

## structure Programming

### functions

>[!tip] can be written before or after main ( )

>[!info]  **standard**
> write function after main and put header in preprocessing


>[!important]  no overloading in c
>overloading : functions with same name but different parameters

```C

#include <stdio.h>
#include <stdlib.h>
// prototype - header - signature - declaration
void printFnV1(); //should be in preprocessing if funtion after main
void printFnV2();
void printFnV3(char ch);
void printFnV4(char ch, int n);
int add (int num1,int num2);
int factorial(int num);
int getMax(int x,int y);

int main()
{
	int x = 3,y =5;
	int z;
	char
	z = add(x,y); //arguments
	printf("%d",add(c,y)*2);
	printFnV4(ch, x);
	z = add(y++,y++); // passing arguments from right to left y passed as original then incremented then passed second argument from right then incremented again

	printf("\n hello world");
	printFnV1();
	printf("\n hello world");
	//printFnV1("as"); compilation Error no parametares in declaration
	printf("\n hello world");
	printFnV1();
	return 0; // 0 => success 
	//1 => failure (optional)
	// 2 =>memory error (optional)
	// 3 => i/o file error (optional)
}
void printFnV1()//header   void => no return
{ //body
//repeating code
	printf("\n ****");
	return;// can return with void to stop excuting
	printf("skfaskdf"); //unreachable code
	exit(0);// stop excuting
}
void printFnV2()
{
	char ch;
	int i;
	ch = getch();
	printf("\n");
	for (i = 0; i<5;i++)
	{
		printf("%c",ch);
	}
}
void printFnV3(char ch)
{
	int i;
		printf("\n");
	for (i = 0; i<5;i++)
	{
		printf("%c",ch);
	}
}
void printFnV4(char ch, int n)
{
	int i;
	//	printf("\n"); optional for dev do only one thing
	for (i = 0; i < n;i++)
	{
		printf("%c",ch);
	//	printf("\n");
	}
}
int add (int num1,int num2) //parameters
{
	return num1+num2;
}
int factorial(int num)
{
	int i,result = 1;
	for(i = num;i > 1;i--)
	{
		result*=i;
	}
	return result;// can return only 1 value
	return i;// unreachable code
}
int getMax(int x,int y)
{
	if (x>y)
		return x;
	else
		return y;

}
```

> [!note]- arguments vs parameters



## #lab5

1. fact(int )
2. power(int i , int k)
3. void magicbox(int)
