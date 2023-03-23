- Memory Layout of C Program
  id:: 63bcf13c-61cc-46b4-921a-9932889a716d
  collapsed:: true
	- ![](https://he-s3.s3.amazonaws.com/media/uploads/383f472.png)
	- Stack
	  collapsed:: true
		- Store
			- Automatic variables
			- Function call information
				- Return address
				- Previous frame pointer (EBP)
		- Growth toward address zero (Backward)
		- A stack pointer register tracks the top of the stack
	- Heap
	  collapsed:: true
		- Store
			- Dynamic memory
		- C Library Functions (Is not syscall)
			- malloc()
			- realloc()
			- free()
		- Syscall (Implement the C Library Functions)
			- brk()
			- sbrk()
			- Break is the address boundary of the Heap, these two syscall would manipulate that address
	- Uninitialized Data Segment (or bss segment)
	  collapsed:: true
		- Store
			- Global variables and static variable that
				- initialized to zero
				- do not have explicit initialization in source code
		- Initialized by the kernel to arithmetic 0 before the program start executing
		- `bss` is named after an ancient assembler operator that stood for "block started by symbol"
	- Initialized Data Segment (or simply data segment)
	  collapsed:: true
		- Store
			- Global variables
			- Static variables
		- Can be further classified into
			- initialized read-only
			- initialized read-write area
	- Test Segment
	  collapsed:: true
		- Store
			- Code or simply as text
		- Place below the heap or stack in order to prevent overflow from overwriting it
		- Usually it is sharable and read-only
-
- Static Variable
  id:: 62a397ed-bf86-4568-85b0-7ebdc316ff30
  collapsed:: true
	- Static variables have a property of preserving their value even after they are out of their scope
	- Example
	  collapsed:: true
		- #+BEGIN_SRC C
		  #include<stdio.h>
		  int fun()
		  {
		    static int count = 0; # HERE
		    count++;
		    return count;
		  }
		    
		  int main()
		  {
		    printf("%d ", fun());
		    printf("%d ", fun());
		    return 0;
		  }
		  #+END_SRC
		- Output would be : `1 2`
		- The content of static variable `count` always preserve
	- Facts
	  collapsed:: true
		- A static variable remains in memory while the program is running.
		- Static variables (like global variables) are initialized as 0 if not initialized explicitly.
		- Static variables are allocated memory in initialized data segment, not stack segment. See ((63bcf13c-61cc-46b4-921a-9932889a716d))
		- Static global variables and function are possible. The purpose is to limit scope of them to a single file. See ((63bcf13c-cff8-40e1-9ed7-6bf765561f41))
		- Static variables should not be declared inside structure.
			- The reason is C compiler requires the entire structure elements to be placed together
				- Such as memory allocation for structure members should be contiguous
				- Structure member may belongs to stack or heap if allocated dynamically. It is possible to have an entire structure as static.
- Static Function
  collapsed:: true
  id:: 63bcf13c-cff8-40e1-9ed7-6bf765561f41
	- In C, functions are global by default
	- Access to static functions is restricted to the file where they are declared
	- Example
	  collapsed:: true
		- In `file_1.c`
			- #+BEGIN_SRC C
			  static void fun1(void) # HERE
			  {
			    puts("fun1 called");
			  }
			  #+END_SRC
		- In `file_2.c`
			- #+BEGIN_SRC C
			  int main(void)
			  {
			    fun1(); 
			    return 0;  
			  }
			  #+END_SRC
			- We would get a compile error, because fun1() is declared static in`file_1.c`  and cannot be used in `file_2.c`
- Volatile Variable
  collapsed:: true
	- ![未命名繪圖 (16).jpg](../assets/未命名繪圖_(16)_1655717304292_0.jpg)
	- Explanation
		- Before dig into the keyword volatile, first we should know how does an *optimizing compiler* works.
		- An optimizing Compiler would avoid accessing some variables again if it isn't changed in the code
			- Example
			- #+BEGIN_SRC C
			  uint32 *statusPtr = 0xF1230000;
			  
			  while(*statusPtr == 0){
			    // Assume status does not change in this loop
			  }
			  #+END_SRC
			- An optimizing Compiler would see that ~status~ isn't being changed by while loop, so there's no need to access ~status~ variable again after each iteration of loop
			- So the compile may convert this loop to a infinite loop, such as ~while(1)~ or read the status from register without verify it's true status
		- If we want to tell the compiler that ~status~ is a special variable so that no such optimization are allowed on this variable
			- Example
			- #+BEGIN_SRC C
			  volatile uint32 *statusPtr = 0xF1230000;
			  #+END_SRC
		- Here, ~statusPtr~ is pointing to a memory location (e.g. for some IO port) at which the content can change at any point of time from some peripheral device.
	- Usuage
		- A question will be raised. How can value of a variable change in such a way that compiler cannot predict?
			- 1) Global variables modified by an interrupt service routine outside the scope
				- For example, a MMIO data port
			- 2) Global variable within a multi-threaded application
- Extern Variable
  collapsed:: true
	- Used when a particular file need to access a variable from another file
		- Declared only, not defined (No memory)
		- Meaning "Go outside my scope and you will find the definition of the variable that I declared"
			- Compiler would trust you
				- Compiler would not prompt error
					- But linker would throw error, if you use it and cannot find it
	- Special Example
		- #+BEGIN_SRC C
		  extern int var = 0;
		  int main(void){
		    var = 10;
		    return 0;
		  }
		  #+END_SRC
		- This program still works according to C standards
			- If a variable is only declared and an initializer is also provided with that declaration
				- Memory will be allocated
-
- Array vs. &Array
  collapsed:: true
	- #+BEGIN_SRC C++
	  int array[5];
	  cout << array << " " << &array << endl;
	  // e.g. 0x7fffce606b90 0x7fffce606b90
	  #+END_SRC
	- So you might think that ~array~ and ~&array~ are the same
	- Well, they aren't not!!
	- #+BEGIN_SRC C++
	  int array[5];
	  cout << array << " " << array + 1 << endl;
	  cout << &array << " " << &array + 1 << endl;
	  // e.g. 0x7ffc6ab5daa0 0x7ffc6ab5daa4 
	  // e.g. 0x7ffc6ab5daa0 0x7ffc6ab5dab4
	  #+END_SRC
	- With pointer arithmetic, we can see the difference
		- `array` is a pointer to the first element of array
			- `array + 1` would be the pointer to the second element
		- `&array` is a pointer to whole array of 5 integers
			- `&array + 1` results in addition of 5 * 4B = 20 = 0x14
			- Which is address right after the ~array~
-
- Range-based for loop in C++ 11
  collapsed:: true
	- Example
	- #+BEGIN_SRC C++
	  std::vector<int> v = {0, 1, 2, 3, 4, 5};
	  
	  // Iterating over whole vector
	  for (auto i : v)
	          std::cout << i << ' ';
	  
	  // the initializer may be a braced-init-list
	  for (int n : {0, 1, 2, 3, 4, 5})
	          std::cout << n << ' ';
	  
	  // Iterating over array
	  int a[] = {0, 1, 2, 3, 4, 5};    
	  for (int n : a)
	          std::cout << n << ' ';
	  
	  // Printing keys and values of a map
	  std::map <int, int> MAP({{1, 1}, {2, 2}, {3, 3}});
	  for (auto i : MAP)
	    		std::cout << '{' << i.first << ", " 
	                    << i.second << "}\n";
	  #+END_SRC