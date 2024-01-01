# Tree

> [!tip] Tree Data Structure
> non-linear data structure
> Every node has one parent except the root node which has no parent
> Every node can have multiple children

> [!example] Example of Tree
> File System in OS

> [!note] Types of Tree
> Binary Tree: Every node has at most 2 children
> Terinary Tree: Every node has at most 3 children
> N-ary Tree: Every node has at most N children

## Tree terminology

- Root: The top node in a tree, has no parent
- Edge: The link from parent to child
- Leaf: A node with no children
- Sibling: Nodes with the same parent
- Ancestor: If there is a path from root to a node, then root is the ancestor of that node
- Depth of a node: Length of the path from root to that node
- Height of a node: Length of the path from that node to the deepest leaf
- Height of a tree: Height of the root node
- Depth of a tree: Depth of the root node

### Binary Tree

> [!tip] Binary Tree
> Every node has at most 2 children

##### Binary Search Tree

> [!tip] Binary Search Tree
> Every node has at most 2 children
> Left child is smaller than the parent
> Right child is greater than the parent

### Binary Tree Array Representation

> [!tip] Binary Tree Array Representation
> If the index of the parent node is i, then
>
> - the index of the left child is ==2i+1==
> - the index of the right child is ==2i+2==

### Binary Tree Linked List Representation

> [!tip] Binary Tree Linked List Representation
> Every node has 3 fields
>
> - Data
> - Pointer to left child
> - Pointer to right child

##### BST

```cpp

struct Node{
    int id;
    Node* left;
    Node* right;
};
class BST{
    Node* root;
    void printInorder( Node* temp){
        if(temp == NULL){
            return;
        }
        printInorder(temp->left);
        cout<<temp->id<<" ";
        printInorder(temp->right);
    }
    void printPreorder( Node* temp){
        if(temp == NULL){
            return;
        }
        cout<<temp->id<<" ";
        printPreorder(temp->left);
        printPreorder(temp->right);
    }
    void printPostorder( Node* temp){
        if(temp == NULL){
            return;
        }
        printPostorder(temp->left);
        printPostorder(temp->right);
        cout<<temp->id<<" ";
    }
    Node* getParent(int id){
        Node* parent = root;
        while(parent != NULL){
            if(id <= parent->id && parent->left != NULL){
                return parent;
            }else if(id > parent->id && parent->right != NULL){
                return parent;
            }
            if(id <= parent->id){
                parent = parent->left;
            }else{
                parent = parent->right;
            }
    }
    return parent;
    }
    public:
    BST(){
        root = NULL;
    }
    void add(int id){
        Node* newNode = new Node;
        newNode->id = id;
        newNode->left = NULL;
        newNode->right = NULL;
        if(root == NULL){
            root = newNode;
        }else{
            Node* temp = getParent(id);
            if(id <= temp->id){
                temp->left = newNode;
            }else{
                temp->right = newNode;
            }
    }
    Node* search(int id){
        Node* temp = root;
        //searchRecursive(temp,id) //to search recursively
        while(temp != NULL){
            if(temp->id == id){
                return temp;
            }
            else if(id < temp->id){
                temp = temp->left;
            }
            else{
                temp = temp->right;
            }
        }
        return NULL;
    }
    Node* searchRecursive(Node* temp, int id){
        if(temp == NULL){
            return NULL;
        }
        if(temp->id == id){
            return temp;
        }
        else if(id < temp->id){
            return searchRecursive(temp->left, id);
        }
        else{
            return searchRecursive(temp->right, id);
        }
    }
    void printInorder(){
        printInorder(root);
    }
    void printPreorder(){
        printPreorder(root);
    }
    void printPostorder(){
        printPostorder(root);
    }
    //not in lecture
    /*
    void deleteNode(int id){
        Node* temp = search(id);
        if(temp == NULL){
            return;
        }
        Node* parent = getParent(id);
        if(temp->left == NULL && temp->right == NULL){
            if(parent->left == temp){
                parent->left = NULL;
            }else{
                parent->right = NULL;
            }
            delete temp;
        }else if(temp->left == NULL){
            if(parent->left == temp){
                parent->left = temp->right;
            }else{
                parent->right = temp->right;
            }
            delete temp;
        }else if(temp->right == NULL){
            if(parent->left == temp){
                parent->left = temp->left;
            }else{
                parent->right = temp->left;
            }
            delete temp;
        }else{
            Node* min = temp->right;
            while(min->left != NULL){
                min = min->left;
            }
            temp->id = min->id;
            deleteNode(min->id);
        }
    }*/
};
int main(){
    BST bst1;
    bst1.add(50);
    bst1.add(30);
    bst1.add(20);
    bst1.add(40);
    bst1.add(70);
    bst1.printInorder();
    bst1.printPreorder();
    bst1.printPostorder();
    bst1.add(60);

    return 0;
}
```

> [!tip] Inorder vs Preorder vs Postorder
> Inorder: Left, Root, Right
> Preorder: Root, Left, Right
> Postorder: Left, Right, Root

> [!warning] Deleting a node
>
> 1. If the node has no children, then just delete the node
> 1. If the node has one child, then replace the node with its child
> 1. If the node has two children:
>    - replace the node with the smallest node in the right subtree
>    - (or the largest node in the left subtree)

> [!faq] Need to know terminology of tree
> inorder, delete (sudocode), preorder, postorder, height, depth, parent, child, leaf, root, edge, ancestor, sibling, binary tree, binary search tree, ternary tree, n-ary tree, array representation, linked list representation

---

# presentation

###### Hashing

> [!tip] Hashing
> Hashing is a technique to store and retrieve data in a very efficient manner
> Hashing is a technique to convert a range of key values into a range of indexes of an array
> `i/p (key) -> hash function -> index`
> o(1) time complexity
>
> > [!bug] collision
> > two keys have the same hash value
> > <br>
