# Data structure

> [!info] Data structure is a way to organize data in order to use it efficiently.

> [!example] Examples: Array, Stack, Queue.
> queue: FIFO
> stack: LIFO

## Array vs Sorted Array

> [!info] Adding: Array is faster than Sorted Array.

> [!info] Searching: Sorted Array is faster than Array.
> Sorted Array ==Binary Search==
> Array uses ==Linear Search==

# Algorithms

> [!info] Algorithm is a step-by-step procedure to solve a problem.

> [!example] using which data structure depends on the business requirement.

> [!tldr] Omega vs Big O vs Theta
> Omega: best case
> ==Big O==: worst case
> Theta: average case

## Space Complexity

> [!info] Space Complexity is the amount of memory space required by the algorithm.

## Time Complexity

> [!info] Time Complexity is the amount of time required by the algorithm to run.

### Big O Notation

> [!info] Big O Notation is a way to measure the performance of an algorithm.

> [!tldr] Big O Time Complexity from fastest to slowest
>
> 1. O(1) : Constant : Best case
> 2. O(log n) : Logarithmic : binary search
> 3. O(n) : Linear : Linear search
> 4. O(n log n) : Merge sort
> 5. O(n^2) : Quadratic : Bubble sort
> 6. O(n^3) : Cubic

> [!tip] Merge sort vs Bubble sort
> Merge sort is faster than Bubble sort.
> Merge sort is O(n log n) while Bubble sort is O(n^2).
> but Merge sort uses more space than Bubble sort.
> Merge sort uses O(n) : creating a new array.
> Bubble sort uses O(1) : swapping elements in place.

#### Recursion

> [!info] Recursion is a function that calls itself.

> [!tldr] Recursion vs Iteration
> Recursion uses more space than Iteration because it creates a new stack frame.
> Recursion is slower than Iteration.
> But Recursion is easier to read and write than Iteration in some cases.

```cpp
// Recursion
int factorial(int n) {
    if (n == 0) {
        return 1;
    }
    return n * factorial(n - 1);
}
```

> [!Error] Stack overflow
> to avoid stack overflow: function calling itself nonstop.
> use ==Base Case== to stop the recursion.

> [!important] How recursive functions work
> Invoke the same function with a different input until you reach your ==BASE CASE==.

> [!important] Base Case
> The condition when the recursion ends.

> [!warning] Two essential parts of a recursive function
>
> 1. Base Case
> 2. Different Input

###### Factorial

```cpp
// Recursion
int factorial(int n) {
    if (n == 0) {// Base Case
        return 1;
    }
    return n * factorial(n - 1);//change input
}
int main() {
    cout << factorial(5) << endl;
    return 0;
}
```

##### Power

```cpp
// Recursion
int power(int base, int exponent) {
    if (exponent == 0) {// Base Case
        return 1;
    }
    return base * power(base, exponent - 1);//change input
}
```

> [!try] Try to pass base by reference.
> it won't copy the value of base to new stack frames.
> improve space complexity.

##### Recursion Linear Search

```cpp
int linearSearch(int arr[], int n, int key) {
    if (n == -1) {// Base Case
        return -1;
    }
    if (arr[n] == key) {
        return n;
    }
    return linearSearch(arr, n - 1, key);//change input
}
```

---

> [!important] Primitive vs Non-primitive Data Structures
> Primitive Data Structures
>
> 1. Integer
> 2. Float
>    ...
>    Non-primitive Data Structures
> 3. Array
> 4. Linked List

> [!important] Linear vs Non-linear Data Structures
> Linear Data Structures
>
> 1. Array
> 2. Linked List
> 3. Stack
> 4. Queue
> 5. Hash Table
>    ...
>    non-linear data structures
> 6. Tree
> 7. Graph

> [!important] Non-primitive can be physical or logical.
> Physical: Array, linked list
> Logical: Stack, Queue, Hash Table, Tree, Graph
> logical data structures are implemented using physical data structures.

---

###### Binary Search

```cpp
int binarySearch(int arr[], int size, int key) {
    int start = 0;
    int end = size - 1;
    int mid;
    while (start <= end) {
         mid = (start + end) / 2;
        if (arr[mid] == key) {
            return mid;
        }
        else if (arr[mid] > key) {
            end = mid - 1;
        }
        else {
            start = mid + 1;
        }
    }
    return -1;
}
```

###### Recursion Binary Search

```cpp

int binarySearch(int arr[], int start, int end, int key) {
    if (start > end) {
        return -1;
    }
    int mid = (start + end) / 2;
    if (arr[mid] == key) {
        return mid;
    }
    else if (arr[mid] > key) {
        return binarySearch(arr, start, mid - 1, key);
    }
    else {
        return binarySearch(arr, mid + 1, end, key);
    }
}
```


# #lab1 

- Binary search
