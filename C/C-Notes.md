

```C
int getPrimeNumberCountBetweentwoNumbers(int x,int y)
{
    int i,j,count = 0;

    for ( i = x; i <= y; i++)
    {
	    if (i <= 1)  
		    continue; 

        for ( j = i - 1; j > 1; j--)
        {

            printf("j = %d",j);

            if (i % j == 0)
                break;
        }

        if (j == 1)
            count++;
    }

    return count;
}

```



```C
 struct person p1 = {3,"aly",30};
    printf("%d",p1);//3
    printf("%d",p1+1);//error: invalid operands to binary + (have 'struct person' and 'int')

    struct person *ptr =(struct person*) &p1;
    scanf("%d %s %d",&ptr->id,ptr->name,&ptr->age);//& 
    printf("%d, %s, %d",ptr->id,ptr->name,ptr->age);
    printf("%d,%s,%d",(*ptr).id,(*ptr).name,(*ptr).age);

```


```C
int getStringLength1(char string[]){
int count = 0,i = 0;
while(string[i] != '\0'){
    count++;
    i++;
}
return count;

}
int getStringLength2(char *ptrs){
int count = 0,i = 0;
while(*(ptrs+i) != '\0'){
    count++;
    i++;
}
return count;

}
```

---
![](Pasted%20image%2020231104154644.png)
### x = 15
> As no break between cases 1 and 2 
> x = 10 +2 = 12;
> x = 12 + 3 = 15

---

```C
void printAtoZ()
{
    int i;
    for (i = 'a'; i <= 'z'; i++)
    {
        printf("%c\n",i);
    }
}
```

can we return structure declared iside functions?
