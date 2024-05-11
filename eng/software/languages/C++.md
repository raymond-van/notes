# C++
- statically-typed
	- variable types are determined during compilation
- runs directly on the hardware of the machine, making C++ programs extremely fast
	- comes at cost of binaries (machine code) being tied to a particular OS and hardware platform (e.g. x86 (Intel/AMD), ARM (Mobile, Embedded))
- main() entry point for every C++ program
## Lifecycle
- Source code -> Preprocessing -> Compilation -> Linking -> Loading -> Execution
### Preprocessing
- handle lines starting with `#` which are preprocessor directives that
- `#include`
	- copies contents of the header files into your source code, bringing in external code declarations
- `#macro` / `#define`
	- substitutes defined macros throughout code
	- used for constants and avoiding code repetition
- Conditional Compilation (`#ifdef`, `#else`, `#endif`)
	- Selectively include or exclude code blocks based on defined conditions
	- `#ifdef {symbol}`: include if symbol has been defined using `#define`
	- used for writing for different platforms, debugging, checking C++ versions / code versions
### Compilation
- takes preprocessed code and performs the following:
	- **Analysis**
		- Lexical: Breaks code into tokens (keywords, operators, identifiers)
		- Syntax: Builds Abstract Syntax tree (AST) representing code's grammatical structure, checking for syntax errors
		- Semantic: Checks code for meaning and type correctness
	- **Translates**
		- Translate AST into machine-specific assembly code
	- **Optimizes**
		- Improves assembly code for efficiency using various techniques
- outputs: 
	- object file(`.o`) containing machine code / assembly
	- symbol table
		- **Symbols defined in the object file:** 
			- Functions, global variables defined within that specific source file
	    - **Symbols referenced but not defined in the object file:**
		    - External functions or global variables used in the file but whose implementation lives elsewhere
		- linker uses symbol tables to resolve symbols from all object files and libraries
### Linking
- Combines object files, along with any necessary libraries into a single, ready-to-run executable
- Combines all symbol tables into a global view of all defined and referenced symbols across projects
	- For each referenced symbol the linker looks for the definition in object files and then libraries, if not found -> error
- Takes resolved symbols and assigns a memory address to each element
	- Edit machine code to use these newly assigned addresses
- Static Linking
	- done at build time resulting in a single executable containing all the code
	- larger executable, but self-contained
- Dynamic Linking
	- done at runtime with code from external libraries loaded as needed
	- results in smaller executable
- output:
	- patched-up machine code
	- executable file (`.exe` (windows), ELF file (unix, mac))
#### Libraries
- contain actual implementation of the things that were declared in header files
- pre-compiled object/machine code (assembly)
- Static Libraries
	- end with `.lib` (windows) or `.a` (unix)
	- gets directly copied into your source code during linking
- Dynamic Libraries
	- end with `.dll` (windows) or `.so` (unix)
	- executable only has a reference to a dynamic library
		- code is loaded into memory at runtime as needed
	- multiple programs can share a single dynamic library, reducing memory
	- can update library without recompiling the apps that use it
### Loading
- allocates memory space (stack, heap, code segment, data segment) for the program, see Memory Model below
- loader reads and places executable from disk to memory
- then resolves dynamic libraries
- sets CPU instruction pointer to entry point (`main()`) and begins execution
## C++ Specific 
### Syntax
- prefix * means "value of"
- suffix * means "pointer to"
- prefix & means "address of"
- suffix & means "reference to"
#### Function Prototypes
- allows you to use a function before actually defining it (i.e. no body)
- declare a prototype at the beginning of your code to inform compiler about function signature
- for code organization
- e.g.: place `int multiply(int x, int y);` after includes, then define the function somewhere in your project
#### Forward Declaration
- general term for function prototypes but applied to classes, variables
#### Lambdas
- unnamed function defined in place 
- can be assigned to variables or used as a parameter to another function
- Capture-list
	- list of variables from the surrounding scope that the lambda function can access
	- for code organization
	- `[=]`: captures all variables from surrounding scope by value
	- `[&]`: captures all variables from surrounding scope by reference
```
[capture-list](parameters) -> return_type {
    // function body
};
```
#### Structs
- a class with public access to all members by default
- used to group related data together
#### Unions
- stores different data types in the same memory location
#### Keywords
- `virtual`: Declares a member function that can be overridden in derived classes, enabling polymorphism (OOP)
	- Pure virtual functions
		- append = 0 to the end
		- derived classes must implement but provides no default implementation itself
		- classes containing pure virtual functions are considered **Abstract Base Classes**
			- These classes cannot be directly instantiated
- `const`: doesn't change, unless mutable is used
- `volatile`: signals to compiler that the variable may change in ways the compiler cannot predict due to hardware, multithreading, or OS interaction
	- forces compiler to reread the variable's value **every time** it's used
	- does not guarantee atomicity and can hurt performance, better to use atomics
- `constexpr`: Declares functions or variables that can be evaluated at compile time if provided with constant arguments
	- Calculating results at compile-time takes load off during runtime
- `friend`: Grants a non-member function or class access to private and protected members of another class
	- breaks encapsulation
- `thread_local`: variable that exists for the lifetime of the thread they belong to
- `using`: allows you to import entire namespaces or specific elements into the current scope (e.g.: `using namespace std`)
- `noexcept`: add to a function declaration to indicate that the function is guaranteed not to throw any exceptions
- `decltype`: allows you to determine the exact type of an expression at compile-time
	- useful for templates and auto

#### Explicit Type Conversion
##### Old methods
- C-style: `double x = (double) int_val` 
- Function notation: `double x = double(int_val)`
##### C++ Named Casts
- more explicit and readable
- `static_cast`: standard conversions between compatible types
- `dynamic_cast`: safely converting between pointers or references within an inheritance hierarchy
- `const_cast`: manipulates the const-ness of a variable 
- `reinterpret_cast`: low-level cast between unrelated types, treats memory as raw bytes -> unsafe, try to avoid
### Dynamic Types
- can achieve dynamic type like behavior at runtime at cost of performance
- Polymorphism + Inheritance
	- declare pointers/refs to a base class and have them point to objects of derived classes
	- `virtual` functions allows you to call the overridden function in the derived class even through a base class pointer, providing runtime flexibility
- `void*`
	- generic pointer that can point to any data type, needs to be explicitly casted before use
- `std::any` (C++17)
	- safe, modern way to hold values of different types
	- need to query the type of stored value before use
#### Run-time Type Identification (RTTI)
- obtain type information at run-time (useful for dynamic types)
- `typeid` operator
	-  returns a `type_info` object representing the object's actual type
	- `type_info` lets you compare types, retrieve the type's name, and more
- `dynamic_cast` operator
	- used to perform down casts within inheritance hierarchies (e.g. cast a base class to derived class)
### Header files
- like interfaces in other languages
- declarations but not implementations for things like: functions, classes, macros, data types
- Note: Template functions and classes in header files typically contain the implementation because the compiler will need it to generate a concrete implementation with a specific type at compile type
## OOP
### Inheritance
- a class can be derived from multiple base classes
- can lead to **Diamond Inheritance** 
	- where a class is derived from two or more classes, which in turn, are derived from a common base class
	- use `virtual` on base class to resolve
### Polymorphism
- Static/Compile-time Polymorphism
	- occurs at compile-time
	- e.g.: Function overloading, templates
- Dynamic/Runtime Polymorphism
	- appropriate function gets called at runtime
	- enabled by `virtual`
		- if a class has at least one virtual function, compiler creates a virtual table
	- Virtual tables (vtable)
		- if you have a pointer to a base class, it can point to objects of the base class or any its derived class
			- e.g.: `BaseClass* x = new DerivedClass1()`
		- if the base class has a virtual function, the virtual table tells you which version of the function to call (could be in base class or in one of the derived classes)
		- hidden member `vptr` points to the virtual table
### Classes
- private access to all members by default
- used when you want to create objects with state & behavior and implement OOP paradigms e.g.: inheritance, encapsulation (hiding data)
#### Static Variables
- exist and retain their value throughout the entire program's execution, even across multiple function calls or object creation
- **Within a function:** 
	- gets initialized only once (the first time the function is called) and maintains its (potentially changed) value across subsequent calls
- **Within a class:** 
	- shared by all objects of that class
	- only a single copy of the variable, regardless of how many objects you create
	- accessing: `MyClass::staticVar`
#### Member Functions
- functions defined within a class or struct
##### Special Member Functions
- classes that manage resources (using raw pointers, etc.), compiler generated defaults often won't suffice
- can tell compiler to use default version by setting the special member function to "= default"
###### Copy Constructor
- special member function that **initializes a new object** with data from an existing object of the same class
- **Syntax:** `ClassName(const ClassName& other) { /* copy logic */ }`
- **Default Copy Constructor**
	- generated by compiler iff. you haven't defined any special member functions
	- does a member-wise copy, which **might lead to a shallow copy if you have pointers or references as members**
- consider copy-write idiom, which only makes a deep copy if we're modifying data, otherwise do a shallow copy
###### Copy Assignment Operator
- Modify an existing object with a new value
- **Syntax:** `ClassName& operator=(const ClassName& other)`
- use copy-swap idiom to implement
###### Move Constructor
- special member function that efficiently transfers ownership of resources from one object to another, typically when the original object is a temporary about to be destroyed
- can be crucial for performance when objects hold large or expensive-to-copy resources
- **Syntax:** `ClassName(ClassName&& other) { /* move logic */ }`
	- Rvalue reference && (see below) enables efficient "stealing" of resources
- **Default Move Constructor**
	- generated by compiler iff. you haven't defined any special member functions
	- does a member-wise move. If member types have move constructors themselves, those will be used; otherwise, it falls back to a member-wise copy
###### Move Assignment Operator
- Efficiently **transfer** ownership of resources to an _existing_ object
- **Syntax:** `ClassName& operator=(ClassName&& other)`
###### Destructors
- special member function that is automatically called when an object goes out of scope or is explicitly destroyed
- takes no arguments, has no return type
- used to clean up resources held by the object
- **Syntax**: `~ClassName() { /* cleanup code */ }`
- **Default Destructor**:
	- always provided by compiler if you don't define your own
	- Calling destructors for member objects of class types
	- Not doing anything special if your class only has primitive data members
#### Instance Member Functions
- gets called on instance of the class using .
- `this` refers to the current instance
- e.g. `instance.function()`
#### Static Member Functions
- gets called on class name using ::
- only has access to static variables and other static functions
- e.g. `class::function()`
## Memory Model
- memory of a C++ program is not stored and executed sequentially
- compiler and hardware reorders memory operations for efficiency
	- program should still run as if it was ordered sequentially
	- sometimes doesn't in multithreaded environments leading to unintended outcomes
- Synchronization tools enforce memory ordering constraints by the compiler or explicitly by the programmer (e.g.: `atomic`, `mutex`, `condidition_variable`)
### std::Atomic
### std::Mutex
### Types of Memory
- **Code/Text Segment:** 
	- the program's machine instructions 
- **Data Segment:** 
	- global and static variables, initialized and uninitialized variables are separated
	- lifetime is the entire program execution
- **Stack:** 
	- function calls and parameters, local variables
	- allocated and deallocated automatically as functions are called and return
- **Heap:** 
	- manual dynamic memory allocation (`new`) and deallocation (`delete`)
	- larger than stack, slower access
## Pointers 
- stores memory addresses
- allows direct memory access and forms the basis of arrays and iterators 
- accessing value stored in pointer (indirection)
	- `int value = *ptr; // value contains the value of the variable that 'ptr' points to`
- changing value stored in pointer 
	- `*ptr = 20`
- Function pointers
	- work the same as any other pointer
	- `returnType (*functionPointerName)(parameterTypes); `
	- can be passed to other functions like callbacks
- responsible for allocating (`new`) and deallocating (`delete`) memory
	- forgetting to delete leaks to memory leak
- arrow operator `->` is used to access the members of a pointer pointing to an object / structure
### Smart Pointers
- classes that wrap around raw pointers and automate memory management
- utilizes RAII (Resource Acquisition Is Initialization)
	- Resource
		- could be memory, file handle, network socket, mutex etc...
	- ties the lifetime of a resource to the lifetime of a specific object
	- when object is created, resource is acquired
	- when object is destroyed, resource is released
- when a smart pointer goes out of scope, its destructor automatically calls `delete` on the underlying raw pointer
#### Unique Pointers
- only one `unique_ptr` can point to an object at a time
- can transfer ownership
#### Shared Pointers
- several `shared_ptr`s can point to an object at a time
- keeps track of number of references to an object
- object is deleted when the last `shared_ptr` goes out of scope
- use `make_shared` to create `shared_ptr` instasnce
#### Weak Pointers
- used to break reference cycles that prevent `shared_ptr`s from being destroyed
- Reference Cycles
	- occurs when two or more objects hold `shared_ptr` references to each other, forming a **circular dependency**
## References
- alias for existing variable
- cannot be null, must be initialized when declared and cannot be changed to refer to another variable
- passing large data structures by reference allows you to avoid expensive copying
- don't need to dereference with `*`
	- e.g.:
		- `int x = 5; `
		- `int& ref = x;  std::cout << ref; // Outputs 5 `
		- `ref = 10; // changes the value of x`
### Rvalue References (&&)
- special references that don't store a traditional memory address 
- instead, they provide a special bind to temporary objects or values that are about to be destroyed (Rvalues)
- Rvalues 
	- temporary values or expressions that don't necessarily have a persistent address in memory (e.g., literals, results of functions returning by value).
- primary **purpose is to enable efficient move semantics** (stealing resources from temporary objects)
- can use rvalue references like regular references until it goes out of scope (automatically deleted)
	- however you cannot get address since it's bounded to a temporary
## Templates
- enables generic programming which is just generic classes or functions that work with different data types
### Function Templates
- syntax:
```
template <typename T> // Or 'template <class T>'
T myFunction(T arg1, T arg2) {
    // Code that works with any type 'T'
}
``` 
### Class Templates
- syntax:
```
template <typename T>
class MyContainer {
    T element;
public:
    void setElement(T arg) { element = arg; }
    T getElement() { return element; }
};
```
### Variadic Templates
- allow functions and classes to accept a variable number of arguments of any type
- enables mixed-type tuples
- syntax:
	- `template <typename... Args>`
	- `...Args` is a parameter pack which can contain zero or more arguments
- uses recursion for processing the arbitrary number of arguments
	- Base Case
		- template specialization to handle when the parameter pack is empty, ending the recursion
	- Recursive Case
		- template definition that processes one element of the parameter pack and calls itself with the remaining elements
#### Fold Expressions
- more concise alternative introduced in C++ 17
### Template Specialization
- allows you to create custom versions of a generic template for specific data types that you know will be present
- promotes code reusability
- done with `template<>` syntax
	- then `class ClassName<specific datatype> { ... }`
- compiler knows to search for the most specific template match
### Type Traits
- template classes get information about the type’s properties, behavior, or characteristics
- enable you to adapt your code depending on the properties of a given type, or even enforce specific properties for your type parameters in template code
- `std::is_integral<T>`: Checks if a type T is an integral type (int, char, etc.)
- `std::is_function`: Checks if the given type is a function type
- Can combine type traits together, below type traits can use a type trait as input
	- `std::conditional`: If a given boolean value is true, use type A; otherwise, use type B
	- `std::enable_if`: If a given boolean value is true, use type A; otherwise, there is no nested type
### SFINAE
- Substitution Failure Is Not An Error
- principle in template programming where if the compiler fails to substitute a template parameter, it will silently discard that template from the potential overload set instead of throwing a hard error
- allows for sophisticated selection of function or class template specializations based on the properties of the types provided
- order matters:
	- place specific templates before general templates
	- compiler finds the most specialized viable template for substitution
- can be used with type traits, e.g.:
```
template <typename T, typename = std::enable_if_t<std::is_integral<T>::value>>
void func(T value) {
    // Do something specifically for integral types
}
```
## STL (Standard Template Library)
- collection of header files that provide an optimized set of tools including containers, algorithms and iterators 
### Containers
#### Sequence
- Maintain elements in a strict linear sequence, determined by their insertion order
- e.g.: vector, list (DLL), deque
#### Ordered Associative
- Automatic sorting of elements based on a key
- e.g.: set, map, multiset, multimap
- based on self-balancing BST (e.g.: red-black trees)
- search/insertion/removal is O(log n)
#### Unordered Associative
- Prioritize blazing-fast search and retrieval by key at the cost of being unordered
- e.g.: unordered versions of above
- based on hash table
- search/insertion/removal: average O(1) can degrade to O(n) (hash collisions)
- using custom objects as keys will need a suitable hash function to avoid collisions
#### Adapters
- Provide alternate interfaces (behavior) on top of existing sequence containers
- e.g.: stack, queue, priority queue
- `priority_queue`
	- built on top of another container, typically vector
	- maintains sorted order based on priority, highest priority at top
	- max heap by default
	- for min heap use `std::greater<int>` as the comparator
### Algorithms
- Note: STL algorithms operate on iterators, not containers
- Non-modifying Sequence Operations
	- Examine data without changing it
	- **Searching:** `find`, `find_if`, `search`, `binary_search` (must be sorted)
	- **Counting:** `count`, `count_if`
	- **Analysis:** `all_of`, `any_of`, `none_of`, `adjacent_find`
- Modifying Sequence Operations
	- Alter the contents of containers
	- **Copying:** `copy`, `copy_backward`, `copy_n`, `copy_if`
	- **Moving:** `move`, `move_backward`
	- **Filling:** `fill`, `fill_n`, `generate`
	- **Replacing:** `replace`, `replace_if`
	- **Removing:** `remove`, `remove_if`, `unique`
		- remove/remove_if don't actually remove anything, **they place the elements you want to keep in the front** and give you an iterator to the new end of the valid range
			- see erase-move idiom
	- **Transforming:** `transform` (applies a function to each element)
- Sorting & Related Operations
	- Organize data
	- **Sorting:** `sort`, `stable_sort`, `partial_sort`
	- **Binary Search:** `lower_bound`, `upper_bound`, `equal_range` (assumes data is sorted)
	- **Set Operations:** `set_union`, `set_intersection`, etc. (work on sorted ranges)
- Permutation Operations
	- Rearrange elements in a sequence
	- `reverse`, `rotate`, `shuffle`, `next_permutation`, `prev_permutation`
- Numeric Operations
	- Mathematical computations on sequences of numeric values
	- `accumulate` (sums up values), `adjacent_difference`, `inner_product`, `partial_sum`
### Iterators
- act like smart pointers that point to elements within a container
	- allow you to navigate and manipulate contents
- connect STL algorithms and containers
- **Iterator Invalidation**
	- causes:
		- Insertions and Deletions
			- adding or removing elements in the middle of a container can cause existing iterators to point to invalidated memory as the container may move elements around
			- Except: iterators in `list` remain valid on inserts/deletes
		- Reallocations
			- if a container needs to resize its internal memory (e.g.: `vector`), existing iterators might become invalidated
			- entire data structure could be moved to new location in memory
	- solution:
		- know when your STL container invalidates existing iterators on certain operations 
			- each container has specific rules about iterator invalidation
		- get a new iterator into the modified container
- **Input Iterators**
    - Read-only
    - used with cin and file streams
    - Operations:
        - `++`: Next element
        - `*`: Access the element at the current position (dereference)
        - `->`: Access a member of the object the iterator points to (if it points to a complex object).
		- == and `!=`: Compare iterators
- **Output Iterators**
    - Write-Only
    - Operations: Similar to input iterators, but can't access
- **Bidirectional Iterators**
	- Read and write
	- used in lists
    - Can move forwards and backwards
    - Operations: all of the above, `--` (move to the previous element).
- **Random Access Iterators**
	- Most powerful, behave like regular pointers
    - Random access: can jump directly to a position within the range
	- used in vectors, arrays, deques
    - Operations: All of the above, plus `+=`, `-=` (arithmetic for offsets), `[]` (subscript operator), `<`, `>`, `<=`, `>=` (compare if one iterator refers to an element before/after another)
### Functors
- a struct/class in C++ that overloads the function call operator, `operator()`
- special syntax that allows you to create objects that you can call just like regular functions ("function objects")
- serve as a way to provide adaptable behaviors for STL algorithms, primarily ones involving sorting, comparing, or transforming elements
	- e.g.: comparators
- holds data (state)
- lambda's are a concise alternative to functors
### Streams
- flow of bytes that can be read/written
- e.g.: file i/o, network communication
#### iostream
- `operator>>` (extraction operator)
- `operator<<` (insertion operator)
- `get()`: Read a single character
- `getline()`: Read an entire line of text
	- intended to work with streams
- `eof()`: Check for end-of-file
- `good()`, `bad()`, `fail()`: Check stream state
#### filestream
- inherits from iostream
- `open()`: Opens a file
	- or constructor initialization`std::fstream abc("data.txt", std:ios:in | std:ios:out)
- `close()`: Closes a file
- `is_open()`: Checks if a file is open
- `read()`: Read raw bytes into a buffer
	- **instead use getline() or >> for single word**
- `write()`: Write raw bytes from a buffer
	- **instead use << which provides formatting**
- `seekg()`: Set the position within the input stream
- `seekp()`: Set the position within the output stream
- `tellg()`: Get the current input position
- `tellp()`: Get the current output position
#### stringstream
- inherits from iostream
- lets you treat strings as if it were an input or output stream
	- use `stream1.str()` to convert the stream into a string
- ideal when you need to build or modify complex text formats in memory
- Treat strings as streams for in-memory manipulations
## Exception Handling
- graceful handling of errors: partial recovery, prevent resource leaks, helps debugging
- keywords:`try catch throw noexcept`
- `<stdexcept>` library: provides standard set of exception classes
	- `std::exception`: Base class for all standard exceptions
	- `std::logic_error`
	- `std::runtime_error`
### Access Violations
- errors that occur when a program attempts to access an illegal memory location, e.g.:
	- Trying to access the contents (dereference) of a null / invalid pointer
	- Out of bounds array indexing
	- Reading or writing to memory freed by the user or the operating system
- Use tools: Valgrind, AddressSanitizer
## Build Systems
- automate the process of compiling, linking, testing and packaging your code and manage dependencies
- e.g. Make
### Build file
- tells the build system which cpp files need to be compiled into object files and in what order
- specifies how different parts of your project depend on each other
- instructs the linker on how to combine object files with necessary libraries (static or shared) to produce the final executable or library
- allows you to control the build process with flags and options for the compiler and linker
- e.g.: Makefile
### CMake
- generates build files for a variety of build systems - cross-platform support
- you define targets (e.g.: an executable or library) and their dependencies
- CMakeLists.txt contains instructions CMake's DSL that define:
    - Project name
    - Source files to compile
    - Libraries to link against
    - Headers to include
    - Executables or libraries to create
    - Tests to run
    - Installation instructions
## Best Practices
### Rule of Zero, Three, Five
- see member function notes above for details
- Zero
	- a class or structure does not explicitly manage resources, it should not define any of the special member functions, i.e., destructor, copy constructor, copy assignment operator, move constructor, and move assignment operator, e.g.:
```
struct MyResource {
	std::string name;
	int value;
	};
```
- Three
	- a class or structure that manages resources should define the following three special member functions:
		- Destructor
		- Copy constructor
		- Copy assignment operator
- Five
	- extends the Rule of Three to include two additional special member functions:
		- Move constructor
		- Move assignment operator
### Portability
- can achieve some C++ code portability
- **Standards-Compliant C++:** Adhering to the C++ standard goes a long way. Avoid platform-specific extensions when possible.
- **Conditional Compilation:** Use preprocessor directives (`#ifdef`) to include code blocks specific to a target OS or architecture.
- **Cross-Platform Libraries:** Leverage libraries that abstract away the differences between platforms (e.g., Boost, Qt).
- **Cross-Compilers:** Compilers that can generate machine code for architectures different from the one you're developing on.
## Optimizations
### Separate Compilation
- each source file can be compiled independently into an object file. These object files can then be linked together to form the final executable. This provides faster build times when making changes to a single source file since only that file needs to be recompiled, and the other object files can be reused
- e.g.:
	- `g++ -c main.cpp -o main.o  // source to object
	- `g++ -c example.cpp -o example.o // source to object 
	- `g++ main.o example.o -o my_program // link object files together
## Dangers
- Data Race: 
	- When multiple threads access the same memory location without synchronization, and at least one access is a write, you have a data race – leading to undefined behavior
- Memory Leaks: 
	- If you forget to `delete`, you create a memory leak, with memory remaining allocated but inaccessible
	- Reference cycles
- Dangling Pointers: 
	- Using a pointer after the memory it points to has been deallocated leads to unpredictable errors
- Double Free:
	- Accidentally calling `delete` twice on the same memory block corrupts the memory heap
- Thread Safety
### Undefined Behavior
- when behavior cannot be predicted due to violation of language rules
- e.g.: access violations, trying to use value of uninitialized variables (arbitrary value depends on what's stored in memory)
### Circumvention
- Data Race: atomic types, mutexes
- Memory Leaks: smart pointers
