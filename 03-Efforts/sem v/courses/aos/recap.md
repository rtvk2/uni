template:

```
#include <iostream>
using namespace std;

// making a basic node class for a linked list
class Node {
public: // means main() can actually see and touch these variables
    int data;
    Node* next; // pointer to the next node

    // constructor - this just runs automatically when u make a new node
    Node(int val) {
        data = val;
        next = NULL; 
    }
};

int main() {
    // making nodes dynamically on the heap using 'new'
    Node* head = new Node(10);
    Node* second = new Node(20);

    // linking them up
    head->next = second;

    // printing it out to check
    cout << "first node holds: " << head->data << endl;
    
    // notice we use -> to look inside a pointer
    cout << "second node holds: " << head->next->data << endl;

    return 0;
}
```
