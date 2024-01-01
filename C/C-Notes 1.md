

#### passing arguments from right to left
####  increment 
```C
#include <stdio.h>

#include <stdlib.h>

int a;

int add(int a, int b);

int main()

{

    printf("Hello World!\n");

    // printf("%d\n", a);

    int x = 3, y, *z, g;

    z = &x;

    y = ++*z;

    g = *z++;

    printf("%d\n", y);//4

    printf("%d\n", x);//4

    printf("%d\n", *z);//4

    printf("%d\n", g);//4

    int v = add(x++, x++);// b =4 , a = 5 

    printf("%d\n", v);//=> v = 9

    printf("%d\n", x);//6 as it incremented after its passed 
       int n = add(++x, ++x);// b =7 , a = 8 increment first then pass argument 

    printf("%d\n", n);//15

    return 0;

}

int add(int a, int b)

{

    return a + b;

}
```