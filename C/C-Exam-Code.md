![](Screenshot%202023-11-22%20120548.png)
```C
char revert( char c){
char res;


if (c >= 'a' && c <= 'z'){
    res = c-('a' - 'A');
   
}else if(c >= 'A' && c <= 'Z'){
    res = c +('a' - 'A');
}else {
  res = -1;
}


return res;

}
```
![](Screenshot%202023-11-22%20120532.png)
```C
#include <stdio.h>
#include <stdlib.h>
struct person{
int id;
char *name;
int age;
};

int main(){
int n,i;
printf("please enter number of poeple\n");
scanf("%d",&n);
struct person *p = (struct person *) malloc(n* sizeof(struct person));
for (i = 0; i < n; i++){
   (p+i)->id = -1;

}
return 0;
}
```
![](Pasted%20image%2020231122120801.png)

```C
void concat( char *s1, char *s2){
int i = 0,j = 0;

while ( *(s1+i) != '\0'){
    i++;
   }
while ( *(s2+j) != '\0'){
  *(s1+i) = *(s2+j);
    i++;
   j++;
   }
*(s1+i) = '\0';

}
```

![](Screenshot%202023-11-22%20120437.png)
```C
void  min_max(int arr[],int size, int *min,int* max){
int i,j;
*min = size;
*max = 0;
for (i = 0; i < size; i++){
   for ( j = i+1; j< size; j++){
    if ( arr[i] == arr[j]){

       if ( j-i > *max )
          *max = j-i;
       
      if ( j-i < *min)
          *min= j-i;

       }

    }

  }

}
```