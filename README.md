# Introduction

Before you begin:

The enclosed test code is not C11 compatible.  I have used strcpy, sprintf
and used a lot of "C"isms that C++ purists do not like.  Also, because the CLinkedList
uses the qsort library function, I have to return 3 values (-1, 0, +1) on comparison
which is why the Comparison functions may look a little ugly.  We cannot use std::sort() 
because that particular function requires iterators and a simpler comparison function.
I have also omitted many static_cast<>(x) calls.

This class solves the problem of having multiple lists for multiple data types. It's also a 
solution for legacy code systems that may not provide for an STL solution or may simply be 
part of a Company's coding standards; ie: no class shall be passed by reference to a function.
(I know of one Company that has this coding requirement). 
This article shows how to construct a CLinkedList object and will also show you how to call 
many of the functions. Other linked lists (excluding the Standard Template Library) have Nodes 
with next and prev pointers and also include the data-type or Structure in the same node. 
The CLinkedList class does not. All memory for data-types is allocated on the heap and each Node 
has a pointer to the user's data. However, you don't really need to know how the class works, 
just call the relevant functions and test the return values.

## Examples
### Using the code
User defined structures

```
//
// User defined example structure
//
typedef struct {
        char     data[ 80 ];
        char     *myChar;
}
Structure;
```

## Comparison function examples
Pass the name of your comparison function to the CLinkedList constructor:

Example 1 - Comparing two string fields within a structure

 
    /******************************************************************
    * Compares one structure against another.
    * Mandatory if you want to use search and sort methods.
    * You can have as many comparison functions as you like
    ********************************************************************/
    int Compare( const void *d1, const void *d2 )
    {
        // Done properly, casting away the const here
        Structure *s1 = (static_cast<Structure*>(const_cast<void*>(d1)));
        Structure *s2 = (static_cast<Structure*>(const_cast<void*>(d2)));
	
        return strcmp((char *)s1->Data, (char *)s2->Data);
    }
    
Example 2 - Comparing two string fields in reverse

    int ReverseStringCompare(const void *d1, const void *d2)
    {
	    Structure	*s1 = (Structure *)d2;	// Opposite of Compare()
	    Structure	*s2 = (Structure *)d1;	// ditto

	    return strcmp((char *)s1->Data, (char *)s2->Data);
    }
    
Example 3 - Comparing two simple integers

    int CompareIntegers(const void *d1, const void *d2)
    {
	    int int_1 = *(int *)d1;
	    int int_2 = *(int *)d2;
	    int retCode = 0;

	    if (int_1 == int_2)
		    retCode = 0;
	    else if (int_1 < int_2)
		    retCode = -1;
	    else if (int_1 > int_2)
		    retCode = 1;

	    return (retCode);
}

Example 4 - Comparing two floating point numbers

    int CompareDoubles(const void* d1, const void *d2)
    {
	    double dbl1 = *(double *)d1;
	    double dbl2 = *(double *)d2;
	    int retCode = 0;

	    if (dbl1 == dbl2)
		    retCode = 0;
	    else if (dbl1 < dbl2)
		    retCode = -1;
	    else if (dbl1 > dbl2)
		    retCode = 1;

	    return (retCode);
}

## Data Freeing function (pseudo destructor)

The data freeing function is invoked when the CLinkedList destructor is invoked, or when a node is deleted in the list.

    A User supplied data freeing function example
    /*****************************************************
    * Frees any allocated memory in a structure.
    * similar to a class destructor.
    *****************************************************/
    void FreeData( void *d1 )
    {
        Structure *s = ( Structure *)d1;

        if( s->myChar )
            delete[ ] s->myChar;
    }
    
# Constructor - List declaration

Method 1 - bare bones list

	CLinkedList myList( sizeof( Structure ));
        
Method 2 - With a comparison function (required for list finding and sorting functions)

    CLinkedList myList( sizeof( Structure ), Compare );
    
Method 3 - With comparison and data freeing functions

    CLinkedList myList( sizeof( Structure ), Compare, FreeData );
    
# Example List creation code:
Create a list large enough to hold our Structure using a comparison function and do stuff with it.
````
 int AddSomeStructuresToAList( void )
{
        int         retCode = 0;
        Structure   myStructure;
        Structure   *pStructure = NULL;
	CLinkedList myList( sizeof( Structure ), Compare );  // In this case, we want to find and sort strings.

        // Add 100 Structures
	for ( int i = 0; i < 100; i++ ) 
        {
                sprintf( myStructure.data,"Data %d", i );
		// Add each element to beginning of list
		retCode = myList.dladdHead( &myStructure );  // Effectively adds in reverse order.
		if ( retCode != 0 ) 
                {
                        cout << "Error " << retCode << " Adding to List" << endl;
                        break;
                }
	}
        // Now many nodes in the list?
        cout << "There are: " << myList.dlcount() << " Nodes in the list." << endl;
        retCode = myList.dlqsort(); // quicksort the list.
        if (retCode != 0 ) 
        {
                cout << "Error " << retCode << " Adding to List" << endl;
                retCode = myList.GetStatus();  // Get the reason for the failure if there was one.
                return( retCode );
        }

        // Print contents of list
        pStructure = (Structure *)myList.dltofirst();
	while (pStructure) {
	        cout << ">" << pStructure->data	<< "< Position=(" << myList.dlposn() << ")" << endl;
		pStructure = (Structure *)myList.dlgofwd();
	}

    // After the list falls out of scope, it is deleted
    return( retCode );
}
````
## Basic principles
The user must be familiar with C/C++ pointers and type-casting. Always test the return value from a list function.

## CLinkedList Member Functions:
All of the following member functions are used in the downloadable demo project.
````
            Constructor(...)                // The constructor for the class described above.
            int	    dladd( void* data )	    // Add a node to the list.
            int     dlAddHead( void *data)  // Adds a node to the beginning of the list.
            int     dlAddTail( void *data)  // Same as dladd, adds a node to the end of the list.
            int	    dladdins( void* data )  // Add a node to the list with an insertion sort.
            int     dlatmark( void )        // Test to see if at a previously marked node in the list.
            void*   dlbsearch( void* data ) // Perform a binary search of the list.  Return a pointer to the found data.
            int     dlclear( void )	    // Empties the list of all nodes, retaining an empty list.
            long    dlcount( void)	    // Return the number of nodes in the list.
            int     dldelete( void )	    // Delete the current node.
            void*   dlfind( void* data )    // Find a node in the list with this data. Return a pointer to the data.
            void*   dlget( void )	    // Get currency, a "fresh" copy of the data in the current node. Return a pointer to the data.
            void*   dlgoback( void )	    // Travel backwards through the list to the previous node.  Return a pointer to the data.
            void*   dlgofwd( void )	    // Travel forwards through the list to the next node.  Return a pointer to the data.
            int     dlinsert( void* data )  // Insert a node into the list at this point.
            int     dlmark( void )	    // Mark (tag) a particular node in the list.
            long    dlposn( void )	    // Return the node’s position in the list.
            int	    dlqsort( void )	    // Sort the list using the qsort library function.
            int	    dlreplace( void* data ) // Replace the current node with this data.
            int	    dlrewind( void )	    // Rewind to the start of the list.
            int	    dlsetcompare( pfunc )   // Change the user supplied comparison function to another.
            int	    dlsort( void )	    // Sort the list using a primitive swap sort algorithm.
            bool    dlsorted( void )	    // Flag indicating whether the list is sorted or not.
            void*   dltofirst( void )	    // Go to the first node in the list. Return a pointer to the data.
            void*   dltolast( void )	    // Go to the last node in the list. Return a pointer to the data.
            int     dltomark( void )	    // Go to a previously marked node in the list.
            int     dlunmark( void )	    // Unmark a previously marked node.
            int     GetStatus( void )       // Get the current value of the internal error flag.

            // Debugging functions
            int	    DumpMemory( )	    // Dump the contents of some memory to the screen.
            int	    dldumplist( void )	    // Dump the contents of the entire list to the screen.
            int	    dldumpnode( void )	    // Dump the contents of a single node to the screen.

            // Overloaded operators
            int	operator<<( void* )        // The same as dladd()
            void* operator[]( long )       // Array access operator
            void* operator++( int )        // Equates to dlgofwd()
            void* operator--( int )        // Equates to dlgoback()
    
````    
# Conclusion and Points of Interest

This code was originally created in the early 90's in the "C" programming language. Since then and with the advent of C++ as well as the almost continuous changes in C++ compilers, this code has evolved accordingly. You may find it interesting to note that the sorting functions in the class don't touch the user's data at all but simply swaps list node data pointers. It's far quicker to change a pointer than it is to change data. Since this language allowed for functions to be included in a structure (idiocy in the author's opinion), it is now possible to also add classes to a CLinkedList using the Method 1 example above. Anytime you use Method 1, you can't use the CLinkedList sort, search or find functions. You would have to visit each node and test accordingly.  
The code is guaranteed to work as intended with the classic struct paradigm and although I have extensively tested the list code, I cannot say the same for classes but lists of classes appear to work. 
This code was also ported onto various flavours of UNIX but as at the time of writing this code, there wasn't an STL.  However, in 2002 I wrote a CLinkedList<T> container class with iterators to replace this code.  Microsoft has only relatively recently created such a container class (for linked lists).
If you find the class useful, please let me know. It is being used in the real world. If you would like to see any more features included also drop me a line for consideration. Personally, because I come from a previous "C" programming environment, I'm used to pointer arithmetic which is why you see many mandatory typecasted return types. If you inspect the source code, you'll see why. I did come across a gotcha when coding one of the comparison functions, I forgot to test for a comparison result of zero.
