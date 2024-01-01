## String:

> [!important]+ null character => `\0`

```C
#define size 4
char name [size] = {"a","l","y","\0"}; // size = 3 +\0 =4
printf("%s",name);  //
scanf("%s",name); // no & string      --
char string [10]  = "aly";
int i=0;
while(string[i] != '\0')
	printf("%c",arr[i++]);

// string  = "kdfjs"; //Comp Error only in intialization

char string [10]  = "alyds";
scanf("%s",name); //  scanf neglect what comes after space so we use gets(name)
```

### `gets(string);`

> [!important]+ unlike `scanf` it reads space.
> so we input something like this `"hello world"`

### `sizeof(string )`

```C
printf("%d", sizeof(string)); //  original size of array not actual characters so we use  while or strlen(arr)
```

## `strlen(arr)`

> [!tip]+ implemented by while

```C

#include <string.h> //add to use string functions

printf("%d", strlen(string));

char name[10];
```

## `strcpy(destination, source)`

```C

strcpy(name,arr);
//name = "mohamed" //comp Error
strcpy(name,"mohamed"); //size should be less by 1 for \0 null
```

## `strcat( string1 ,string2)

> [!info]+ concatenates two strings and returns a string

> [!faq]+ Why we have to intialize or use strcpy() first?
> `strcat` will look for the null-terminator, interpret that as the end of the string, and append the new text there, overwriting the null-terminator in the process, and writing a new null-terminator at the end of the concatenation.

```C

//fullname
char fname[10];
char lname[10];
char fullname[20];
scanf("%s",fname);
scanf("%s",lname);
//strcat(fullname,fname); //only if we intialize fullname[10]="" so we use strcpy()

strcpy(fullname,fname);
strcat(fullname,lname);
```

## `strcmp( Firststring,Secondstring )`

> [!info]+ case sensitive
> 0 => equal
> +ve => First before second in ASCII  
> -ve =>

## `strcmpi( string,string ) `

> [!info]+ same as `strcmp` but case insensitive

## string arrays

```C
char names  [ 3 ][10 ] = {"ahmed","aly","mohamed"}; //noOfNames = 3 -- maximumLength+1 =10
int i;
for(i = 0; i<3; i++)
{
	scanf("%s",names[i]);
}
for(i = 0; i<3; i++)
{
	printf("%s",names[i]);
}
char target[10] = "hfhfhfh";
for(i = 0; i<3; i++)
{
	if(strcmp(names[i],target) == 0)
	{
		printf("\n found at index %d", i);
		break;
	}
}
if (i == 3)
{
	printf(" Not found ");
}

```

## normal vs extended KEYS

| `:`   | normal        | Extended       |
| ----- | ------------- | -------------- |
| bytes | 1             | 2              |
| ASCII | standard code | -32 + standard |

> [!search]+ how `getch( )` gets extended keys like arrow keys & home & end
>
> memory
> buffer z [Data buffer - Wikipedia](https://en.wikipedia.org/wiki/Data_buffer)

```C

char ch; //1 byte
ch = getch(); //up 72 71 79 down 80
if (ch == -32)
{
	ch = getch(); //no need to input  takes the second byte after -32
	printf(" extended key %d", ch);
}else
{
		printf(" normal key %d", ch);
}
```

---

## ` textattr(int i)`

> [!info]+ change color

```C

textattr(0x05); // 0=bg  5 = fg

```
---
## Menu

```C

char ch;
char menu[3][10] = {"new","display","exit"};
flag =1;
int cursor =0;
do
{
	system("cls");
	//goto
	for( i = 0; i<3; i++)
	{
		gotoxy(0,i*5);
		if (i == cursor)
		{
			textattr(0x16)
		}

		printf("%s",menu[i]);
					textattr(0x07)//default

	}
	ch =getch();
	switch(ch)
	{
		case -32: //extended key
			ch = getch();
			switch (ch)
			{
				case 80://down
				cursor++;
				if(cursor > 2)
				{
					cursor = 0;
				}
					case 71: //home
				cursor = 0;
				beak;
					case 79: //end
				cursor = 2;
				beak;
					case 72: //up
				cursor --;
				if(cursor < 0){
				cursor = 2;
				}
				break;
		

			break;
			}
			case 13: //enter
				system("cls");
				printf("\n %s",menu[cursor]);
				getch();
				if (cursor ==2)
					flag =0;
			   break;
			case 27: //esc
				flag = 0;
				break;


	}
}while(flag);
```

### #lab4

1. fname lname => fullname

2. ch => normal || extended

3. menu

4. sub menu (bonus)
