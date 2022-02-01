# CLinkedList-
C++ Doubly linked lists for MS Visual Studio

**VERY IMPORTANT ADDITIONAL INFORMATION**:

In the HTML help documentation, I state that the use of mandatory functions are required on List construction.
This is true if you wish to use the sorting facilities or any other function requiring a Comparison function but
you can also invoke without the comparison function or FreeData function.  So, this means that you can create a
list with a simple invocation of:

// 1. Bare bones list

CLinkedList myList( sizeof( Structure ) );

IF YOU USE THE ABOVE INVOCATION, YOU MUST VISIT EACH NODE IN THE LIST SEQUENTIALLY AND TEST ACCORDINGLY,
YOU WON'T BE ABLE TO SORT THE LIST OR USE MUCH OF THE LIST FUNCTIONALITY.

OR:

// 2. One with a comparison function

CLinkedList myList( sizeof( Structure ), Compare );

// 3. One with comparison and data freeing functions

CLinkedList myList( sizeof( Structure ), Compare, FreeData ); // Think of FreeData as a destructor.
 
**YOU CAN ALSO CREATE A LIST OF CLASSES (as opposed to Structures only).**

Example:

// Simple class declaration

class X
{

public:

        X() { cout << "X Constructor" << endl; }
        
        virtual ~X() { cout << "X Destructor" << endl; }
        
        void set(int value) { i = value; }
        
        int  get(void) { return i; }
private:
        int i;
        
};

int TestListWithClasses( void )

{

        CLinkedList myList(sizeof X);
        
        X x;
        
        X *px = (X*)0;
        
        for (auto i = 0; i < 10; i++) {
                x.set(i + 1 );
                myList.dladd(&x);  // ADD CLASS TO LIST
        }
      
        // Ok, theoretically, we have 10 classes in the list.
        // We don't have a comparison function but we should be able to traverse the list
        // It should be noted that the user should overide the < operator so the list may be always sorted on insert.
        cout << "There are " << myList.dlcount() << " Classes in the list" << endl;
        
        // Print the class values...
        px = (X*)myList.dltofirst();
        while (px) {
            cout << "X value = (" << px->get() << ")" << endl;
            px = (X *)myList.dlgofwd();
        }
        X *px = (X *)mylist.dltofirst();
        cout << "list contents ..." << endl;
        while (px) {
                cout << "X = (" << px->get() << ")" << endl;
                px = (X *)mylist.dlgofwd();
        }
        return myList.GetStatus();
}

These lists are in fact the precursor to the STL.  They have been around for many years and have been thouroughly tested.
Each node in the list consists of three pointers, 2 "Node" pointers and a void pointer.
Here is figure 1 "ClinkedList synopsis":
![CLinkedList_Figure_01](https://user-images.githubusercontent.com/67572802/151897842-3522a1a4-08eb-4eee-a549-d0df3a7871b7.JPG)



