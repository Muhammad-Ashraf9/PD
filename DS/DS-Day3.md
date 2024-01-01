## Reverse Array

```cpp
void reverseArray(int* arr, int size){
    int* start = arr;
    int* end = arr + size - 1;
    while(start < end){
        int temp = *start;
        *start = *end;
        *end = temp;
        start++;
        end--;
    }
}
```

## Fibonacci

```cpp
//Recursive
int fib(int n){
    if(n == 0 || n == 1){
        return n;
    }
    return fib(n-1) + fib(n-2);
}
```

# #lab3

- fibonacci iterative

---

# Stack and Queue

```cpp
//stack
class Node{
    int id;
    Node* next;
    Node* prev;
};
class Stack{
    Node* head;
    Node* tail;
    public:
    Stack(){
        head = tail = NULL;
    }
    void push(int id){//same as append
        Node* newNode = new Node;
        newNode->id = id;
        newNode->next= newNode->prev = NULL;
        if(head == NULL){
            head = tail = newNode;
        }else{
            tail->next = newNode;
            newNode->prev = tail;
            tail = newNode;
        }
    }
    int pop(int &id){
        if(head == NULL){
            cout<<"Stack is empty"<<endl;
            return 0;
        }
        id = tail->id;
        Node* temp = tail;
        if(head == tail){//only one node in stack
            head = tail = NULL;
        }else{//more than one node in stack
            tail = tail->prev;
            tail->next = NULL;
        }
        delete temp;
        return 1;
    }
};
```

> [!tip] other approach with (tail) only

```cpp
class Node{
    int id;
    Node* next;
    Node* prev;
};
class Stack{
    Node* tail;
    public:
    Stack(){
        tail = NULL;
    }
    void push(int id){
        Node* newNode = new Node;
        newNode->id = id;
        newNode->next= newNode->prev = NULL;
        if(tail == NULL){
            tail = newNode;
        }else{
            tail->next = newNode;
            newNode->prev = tail;
            tail = newNode;
        }
    }
    int pop(int &id){
        if(tail == NULL){
            cout<<"Stack is empty"<<endl;
            return 0;
        }
        id = tail->id;
        Node* temp = tail;
        if(tail->prev == NULL){//only one node in stack
            tail = NULL;
        }else{//more than one node in stack
            tail = tail->prev;
            tail->next = NULL;
        }
        delete temp;
        return 1;
    }
};
```

> [!tip] other approach with (prev) +(tail) only

```cpp
class Node{
    int id;
    Node* prev;
};
class Stack{
    Node* tail;
    public:
    Stack(){
        tail = NULL;
    }
    void push(int id){
        Node* newNode = new Node;
        newNode->id = id;
        newNode->prev = NULL;
        if(tail == NULL){
            tail = newNode;
        }else{
            newNode->prev = tail;
            tail = newNode;

        }

    }
    int pop(int &id){
        if(tail == NULL){
            cout<<"Stack is empty"<<endl;
            return 0;
        }
        id = tail->id;
        Node* temp = tail;
        if(tail->prev == NULL){//only one node in stack
            tail = NULL;
        }else{//more than one node in stack
            tail = tail->prev;
            tail->next = NULL;
        }
        delete temp;
        return 1;
    }
    int isEmpty(){
        return tail == NULL;
    }
    int isFull(){//checks if there is enough memory to allocate a new node in the heap
        Node* newNode = new Node;//returns NULL if there is no enough memory
        if(newNode == NULL){
            return 1;
        }
        delete newNode;//free the memory to avoid memory leak
        return 0;
    }

};
```

> [!warning] peek() is used to return the top element without removing it from the stack

```cpp
int peek(){
    if(tail == NULL){
        cout<<"Stack is empty"<<endl;
        return 0;
    }
    return tail->id;
}
```

---

### Queue with linked list

```cpp
class Node{
    int id;
    Node* next;
};
class Queue{
    Node* head;
    Node* tail;
    public:
    Queue(){
        head = tail = NULL;
    }
    void enqueue(int id){//same as append
        Node* newNode = new Node;
        newNode->id = id;
        newNode->next= NULL;
        if(head == NULL){
            head = tail = newNode;
        }else{
            tail->next = newNode;
            tail = newNode;
        }
    }
    int dequeue(int &id){
        if(head == NULL){
            cout<<"Queue is empty"<<endl;
            return 0;
        }
        id = head->id;
        Node* temp = head;
        if(head == tail){//only one node in queue
            head = tail = NULL;
        }else{//more than one node in queue
            head = head->next;
        }
        delete temp;
        return 1;
    }
};
```

---

### Shift Queue with array

```cpp
class Queue{
    int* arr;
    int size;
    int rear;
    public:
    Queue(int size){
        this->size = size;
        arr = new int[size];
        rear = -1;
    }
    int enqueue(int id){
        if(isFull()){
            cout<<"Queue is full"<<endl;
            return 0;
        }
        rear = (rear + 1) % size;// as u know this condition will make the rear go back to the start of the array if it reaches the end
        arr[rear] = id;
        return 1;
    }

    int dequeue(int &id){
        if(isEmpty()){
            cout<<"Queue is empty"<<endl;
            return 0;
        }
        id = arr[0];
        for(int i = 0; i < rear; i++){
            arr[i] = arr[i+1];
        }
        rear--;
        return 1;
    }

    int isEmpty(){
        return rear == -1;
    }

    int isFull(){
        return rear == size - 1;
    }
};

```

### Circular Queue with array

```cpp
class Queue{
    int* arr;
    int size;
    int rear;
    int front;
    public:
    Queue(int size){
        this->size = size;
        arr = new int[size];
        rear = front = -1;
    }
    int enqueue(int id){
        if(isFull()){
            cout<<"Queue is full"<<endl;
            return 0;
        }
        if(front == -1){
            front = 0;
        }
        rear = (rear + 1) % size;//this increment rear and if it reaches the end of the array it will go back to the start
        arr[rear] = id;
        return 1;
    }

    int dequeue(int &id){
        if(isEmpty()){
            cout<<"Queue is empty"<<endl;
            return 0;
        }
        id = arr[front];
        if(front == rear){//only one element in queue
            front = rear = -1;
        }else{
            front = (front + 1) % size;//same as rear in enqueue
        }
        return 1;
    }

    int isEmpty(){
        return front == -1;
    }

    int isFull(){
        return (rear + 1) % size == front;//if rear is one step behind front then the queue is full
};

```
