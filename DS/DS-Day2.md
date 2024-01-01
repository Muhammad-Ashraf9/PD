
# Array

- homogeneous
- fixed size
- consecutive memory allocation
- index based starting from 0
- direct access to any element

> [!tip] Array insertion and deletion big O notation
> O(n) linear time complexity: need to shift all the elements

> [!tldr] Array access big O notation
> O(1) constant time complexity: direct access to any element

---

> [!info] fixed size array problem when adding new element
> need to create a new array with new size and copy all the elements from old array to new array

# Linked List

> [!tip] Linked List insertion and deletion big O
> O(1) constant time complexity: only need to change the pointer

> [!tldr] Linked List access big O
> O(n) linear time complexity: need to traverse the list to find the element

> [!info] when to use linked list vs array
>
> - when you need to insert or delete element frequently
> - when you don't know the size of the list

> [!info] when to use array vs linked list
>
> - when you need to access element frequently
> - when you know the size of the list
> - when you need to search element frequently

## Node

> [!tip] Linked List is a sequence of nodes that are connected/linked to each other
>
> - each node contains data and pointer to the next node
> - no need to allocate consecutive memory
> - head pointer points to the first node
> - tail pointer points to the last node

> [!info] To add a new node to the linked list
>
> - create a new node
> - set the data
> - set the pointer to the next node
> - set the tail pointer to the new node

## Singly Linked List

> [!tip] Singly Linked List
>
> - each node contains data and pointer to the next node
> - head pointer points to the first node
> - tail pointer points to the last node

> [!bug] limitation of singly linked list
>
> - can only traverse in one direction

## Doubly Linked List

> [!tip] Doubly Linked List
>
> - each node contains data and pointer to the next node and previous node
> - head pointer points to the first node
> - tail pointer points to the last node

> [!success] both singly linked list and doubly linked list can be circular
>
> - tail pointer points to the head node in circular singly linked list
> - tail pointer points to the head node and head pointer points to the tail node in circular doubly linked list

> [!example] 1 element circular singly linked list
>
> - head pointer points to the only node
> - node next pointer points to itself

```cpp
struct Node {
    int id;
    char name[20];
    int age;
    Node* next;
    Node* prev;
};
class LinkedList {
    Node* head;
    Node* tail;
public:
    LinkedList(){
        head = tail = NULL;
    }
    void append(int id, char* name, int age){
        Node* node = new Node;//heap
        node->id = id;
        strcpy(node->name, name);
        node->age = age;
        node->next = NULL;
        node->prev = NULL;
        if(head == NULL){
            head = node;
            tail = node;
        }else{
            tail->next = node;
            node->prev = tail;
            tail = node;
        }
    }

    void display(){
        Node* node = head;
        while(node != NULL){
            cout << node->id << " " << node->name << " " << node->age << endl;
            node = node->next;
        }
    }

    void count(){
        int count = 0;
        Node* node = head;
        while(node != NULL){
            count++;
            node = node->next;
        }
        cout << "count: " << count << endl;
    }

};
int main(){
    LinkedList list1;//stack
    list1.append(1, "John", 20);
    list1.append(2, "Mary", 21);
    list1.append(3, "Peter", 22);
    return 0;
}
```

> [!danger] we have maximum number of nodes in the linked list depending on the size of the memory
>
> - if we run out of memory, we can't add new node to the linked list
>   `Node* node = new Node;// return NULL if we run out of memory`

---

```cpp
struct Employee {
    int id;
    char name[20];
    int age;
};
struct Node {
    Employee data;
    Node* next;
    Node* prev;
};
class LinkedList {
    Node* head;
    Node* tail;
Node* searchByIdT(int id){
    Node* node = head;
    while(node != NULL){
        if(node->id == id){
            return node;
        }
        node = node->next;
    }
    return NULL;
}
public:
    Employee searchById(int id){
        Node* node = searchByIdT(id);
        if(node != NULL){
            return node->data;
        }
        Employee e;
        e.id = -1;
        return e;
    }
    void deleteById(int id){
        Node* node = searchByIdT(id);
        if(node == NULL){
            return;
        }

            if(node == head){
                head = head->next;
                head->prev = NULL;
            }else if(node == tail){
                tail = tail->prev;
                tail->next = NULL;
            }else{
                node->prev->next = node->next;
                node->next->prev = node->prev;
            }
            delete node;
        }
    void insert(int eId, int id , char* name, int age){
        if(head == NULL){
            append(id, name, age);
            return;
            }
        Node* node = searchByIdT(eId);
        if(node == NULL){
            return;
        }
        if(node == tail){
            append(id, name, age);
        }else{
            Node* newNode = new Node;
            newNode->data.id = id;
            strcpy(newNode->data.name, name);
            newNode->data.age = age;
            newNode->next = node->next;
            newNode->prev = node;
            node->next->prev = newNode;
            node->next = newNode;
        }
    }


};

int main(){
    LinkedList list1;//stack
    list1.append(1, "John", 20);
    list1.append(2, "Mary", 21);
    list1.append(3, "Peter", 22);
    list1.deleteById(2);

    return 0;
}
```

###### Destructor

```cpp
~LinkedList(){
    Node* node = head;
    while(node != NULL){
        Node* temp = node;
        node = node->next;
        delete temp;
    }
}
```

# #lab2

- create a linked list with all functionality above
