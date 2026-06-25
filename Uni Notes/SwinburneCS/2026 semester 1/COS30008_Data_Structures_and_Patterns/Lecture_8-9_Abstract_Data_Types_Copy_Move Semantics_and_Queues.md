## Part 1: Abstract Data Types

### What is an Abstract Data Type?
![[Pasted image 20260527131752.png]]
**Data abstraction** is a design principle that separates what a data type does from how it does it. It divides a type into two distinct pieces: an **interface** and an **implementation**.

In simple terms: the interface says what operations are available and what they promise; the implementation says how those operations are actually carried out.

Think of it like: a vending machine. You know the buttons and what each button delivers. You have no idea about the internal mechanics, and you do not need to. If the machine's internals are replaced, your behaviour as a user does not change at all.

Why it matters: decoupling (tách rời) interface from implementation allows you to swap representations without breaking any code that uses the type. This is the foundation of modular, maintainable software.

An entity designed this way is called an **abstract data type (ADT)**. A client of an ADT interacts exclusively through the operations defined in the interface. To change the underlying representation, you change the implementation, not the interface.

### Object-Oriented Encapsulation
**Object-oriented encapsulation** is the primary mechanism C++ provides to construct ADTs. The convention is:
- All member variables (the state of an object) have **private** visibility, so no client can access them directly.
- All member functions defining the interface have **public** visibility, so clients interact only through those operations.

This scheme enforces that knowing the implementation gives you no practical means to bypass the interface. Encapsulation does not necessarily hide the source code; it removes any valid way to exploit that knowledge.

### Representation Strategies
![[Pasted image 20260527133940.png]]
Because an interface is fixed independently of its backing store, a single interface can be satisfied by multiple distinct representations. You can start with a simple array-based implementation and later replace it with a linked structure or a tree, without touching any code that uses the interface. This flexibility is one of the core benefits of the ADT design.

### Required Operations
Every ADT needs at minimum three kinds of operations to be usable:

A **constructor** creates a new value of the type. There must be at least one, though there may be several with different parameter lists. A **type predicate** tests whether a given value is a valid representation of the type; in C++ this role is often filled by `assert` statements or `empty()`/`full()` service functions. **Access procedures and manipulators** read information from a representation and update it, respectively.

### Key Takeaways
1. An ADT separates interface (what) from implementation (how).
2. Object-oriented encapsulation enforces this separation by restricting member variable access.
3. A single interface can be backed by multiple representations, making ADTs flexible and swappable.
4. Every ADT needs at minimum a constructor, a type predicate, and access/manipulator operations.

**Remember this:** An ADT is a contract. The client signs it by only calling the interface; the implementer signs it by fulfilling the promised behaviour.

**One analogy to remember it by:** an ATM card interface. You insert, enter a PIN, and choose an action. Whether the bank stores your balance in Oracle or PostgreSQL is completely irrelevant to you.

---

## Part 2: Copy and Move Semantics in C++

### What are Copy and Move Semantics?
**Copy semantics** and **move semantics** govern how data is transferred from one C++ object to another. Both involve transferring state, but they differ in what happens to the source.

In simple terms: copying duplicates the data and leaves the source intact; moving transfers ownership and leaves the source in a valid but empty state.

Think of it like: photocopying a document versus physically handing it over. Copying leaves you with both; moving leaves only the recipient with something useful.

Why it matters: without proper copy and move support, ADTs that manage heap memory will either leak resources, perform unnecessary allocations, or produce dangling pointers.

### The Canonical Methods
C++ defines a set of six **canonical methods** that every well-formed class managing resources should address:

For object creation and deletion: the **default constructor** initialises a new object with sensible values, and the **destructor** releases all resources the object **owns**.

For copy semantics: the **copy constructor** creates **a new object** as a deep copy of an existing one, and the **copy-assignment operator** replaces an **existing object's state** with a deep copy of another.

For move semantics: the **move constructor** transfers ownership from a temporary (expiring) object into a newly created one, and the **move-assignment operator** transfers ownership into an already-existing object.

### Rvalue References
To support move operations, C++ introduced **rvalue references**, written with `&&` instead of `&`.
First, understanding the difference between lvalue and rvalue:
- An **lvalue** is something that has a name and a persistent memory address. You can refer to it again after the current line.
- An **rvalue** is something temporary that exists only for the duration of an expression, like a literal or the result of a calculation. It has no name and disappears after the line.
```cpp
int a = 5;
int& ref = a;   // lvalue reference, a has a name and persists
```

An **lvalue reference** `&` can only bind to named variables. An **rvalue reference** `&&` can only bind to temporaries:
```cpp
#define rvalue 42

int i = rvalue;         // rvalue 42 used to initialise i, then gone
int&& r1 = rvalue;      // rvalue reference bound to a literal
int&& r2 = i * rvalue;  // rvalue reference bound to result of multiplication
```
In `r2 = i * rvalue`, the multiplication produces a temporary result with no name. It exists just long enough to be bound to `r2`, then it would normally be thrown away.

The key insight is that `&&` is a signal to the compiler: "this object is about to expire, you are allowed to steal its resources instead of copying them." This is what makes move semantics possible. Rather than allocating new memory and duplicating data, the move operation just takes ownership of what the expiring object already had.

### Special Member Function Rules
The compiler auto-generates the default constructor, copy constructor, copy-assignment operator, and destructor if none are declared. However, declaring certain members suppresses automatic generation of others:
- Declaring any constructor suppresses the auto-generated default constructor.
- Declaring a virtual destructor suppresses the auto-generated default destructor.
- Declaring a move constructor or move-assignment operator suppresses auto-generation of both copy operations.
- Declaring any of the copy constructor, copy-assignment operator, move constructor, move-assignment operator, or destructor suppresses auto-generation of both move operations.

This is the **Rule of Five** in practice: if you need to define any one of the five special members, you almost certainly need to define all five.

### DynamicStack: Copy and Move in Practice
![[Pasted image 20260527164026.png]]
`DynamicStack<T>` is a heap-managed LIFO stack extended with full copy and move semantics.

**The swap idiom** is central to both move operations. It exchanges every private member field between two objects element-wise using `std::swap`, and is always marked `noexcept` because it cannot fail:
```cpp
void swap( DynamicStack& aOther ) noexcept
{
    std::swap( fElements,     aOther.fElements );
    std::swap( fStackPointer, aOther.fStackPointer );
    std::swap( fCapacity,     aOther.fCapacity );
}
```
Element-wise swapping is necessary to avoid calling `std::swap` on the object itself, which would recurse infinitely.

**The copy constructor** allocates a fresh heap array matching the source capacity, then deep-copies only the live elements (those up to `fStackPointer`):
```cpp
DynamicStack( const DynamicStack& aOther ) :
    fElements( new T[aOther.fCapacity] ),
    fStackPointer( aOther.fStackPointer ),
    fCapacity( aOther.fCapacity )
{
    assert( fStackPointer <= fCapacity );
    for ( size_t i = 0; i < fStackPointer; i++ )
        fElements[i] = aOther.fElements[i];
}
```

**The copy-assignment operator** always begins with a self-assignment check. Skipping it would cause the operator to free its own memory before copying from it. The two-step pattern is: call the destructor on `this` to release current resources (without deleting the object), then use in-place `new` to reinitialise `this` via the copy constructor:
```cpp
DynamicStack& operator=( const DynamicStack<T>& aOther )
{
    if ( this != &aOther )
    {
        this->~DynamicStack();           // free current resources
        new (this) DynamicStack( aOther ); // in-place copy init
    }
    return *this;
}
```

**The move constructor** initialises `this` via the default constructor to establish valid state, then swaps with the expiring source. When the constructor returns, the source is destroyed, taking `this`'s old (empty) state with it:
```cpp
DynamicStack( DynamicStack<T>&& aOther ) noexcept :
    DynamicStack()
{
    swap( aOther ); // swap idiom
}
```

**The move-assignment operator** follows the same logic with a self-assignment guard:
```cpp
DynamicStack& operator=( DynamicStack<T>&& aOther ) noexcept
{
    if ( this != &aOther )
        swap( aOther );
    return *this;
}
```

### std::move()
`std::move(arg)` is a compile-time-only cast that forces move semantics on its argument, even if that argument is an lvalue. It generates no executable code; it simply reinterprets the argument as an rvalue reference so that the compiler selects the move overload:
```cpp
DynamicStack<int> lStackCopy = lStack;           // copy constructor
DynamicStack<int> lStackMove = std::move(lStack); // move constructor
```

After `std::move(lStack)`, `lStack` is in a valid but unspecified state. Using it further triggers a compiler warning about use of a moved-from object.

### Key Takeaways
1. Copy semantics duplicates data, preserving the source; move semantics transfers ownership, leaving the source valid but empty.
2. The six canonical methods cover construction, destruction, copy, and move.
3. The swap idiom is the cleanest way to implement move operations, and it is always `noexcept`.
4. The copy-assignment operator must guard against self-assignment to avoid freeing memory it still needs.
5. Declaring any move operation suppresses auto-generated copy operations, and vice versa.
**Remember this:** If your class owns heap memory, you must write all five special members. The compiler's defaults will silently do the wrong thing.

**One analogy to remember it by:** copy semantics is a backup drive that duplicates your files; move semantics is transferring your files to a new machine and wiping the old one.

---

## Part 3: Template Argument Deduction and Perfect Forwarding

### What is Template Argument Deduction?
**Template argument deduction** is the process by which the C++ compiler infers the type parameters of a function template from the types of the arguments passed at the call site. You do not need to explicitly specify `<int>` as an actual usable type when calling a template that receives an `int`; the compiler figures it out.
```cpp
// without deduction, you would have to write:
DynamicStack<int> lStack;
push<int>( lStack, 5 );

// with deduction, compiler sees 5 is an int and figures out T itself:
push( lStack, 5 ); // compiler deduces T = int
```

In simple terms: the compiler reads the types you pass and uses them to fill in the blanks in the template definition.

Why it matters: without deduction, every template call would require explicit type annotations. Deduction also powers universal references and perfect forwarding, which are essential for writing zero-overhead generic containers.

### lvalue and rvalue References in Overload Resolution
Given a class `Foo<T>` with three overloaded member functions: `f(T&)`, `f(const T&)`, and `f(T&&)`, the compiler picks the best match based on the value category of the argument:
- An lvalue (a named variable) binds to `T&`.
- A constant lvalue reference binds to `const T&`.
- An rvalue (a literal or temporary) binds to `T&&` as the best match.
```cpp
Foo<int> obj;
int v = 20;
const int& cv = v;

obj.f( v );   // f(T&),       T is int
obj.f( cv );  // f(const T&), T is int
obj.f( 20 );  // f(T&&),      T is int
```

### Universal References
A **universal reference** arises when a function template parameter is declared as `U&&` and `U` is a deduced type. Unlike a plain rvalue reference, a universal reference can bind to both lvalues and rvalues. The actual reference type is determined by what is passed:
- An rvalue passed to `U&&` yields `U&&`.
- An lvalue of type `X` passed to `U&&` collapses to `X&` (see Reference Collapsing).

Without `std::forward`, an rvalue passed to a universal reference parameter is treated as an lvalue inside the function body, because the parameter itself has a name.

### Reference Collapsing
When template deduction produces a reference to a reference, C++ applies **reference collapsing**. The rule is simple: any combination involving at least one lvalue reference collapses to an lvalue reference; only `X&& &&` collapses to `X&&`:

|Combination|Collapses to|
|---|---|
|`X& &`|`X&`|
|`X& &&`|`X&`|
|`X&& &`|`X&`|
|`X&& &&`|`X&&`|

This rule is what makes universal references behave correctly: passing an lvalue causes the deduced type to be `X&`, so `X& &&` collapses to `X&`, and the function receives a plain lvalue reference.

### std::forward() and Perfect Forwarding
The problem starts with the fact that once an rvalue is given a name inside a function, it becomes an lvalue. The name is what makes it an lvalue:
```cpp
template<typename T> void f3( T&& p )
{
    // even if 4 was passed in as an rvalue,
    // p has a name now so it is treated as lvalue
    f( p ); // always calls f(const int&), always [l-value]
}
```
So when you call `f3(4)`, the `4` arrives as an rvalue. But inside `f3`, `p` has a name, so it is now treated as an lvalue. If you just pass `p` along, the rvalue nature of `4` is permanently lost.

`std::forward<T>(arg)` fixes this by looking at what `T` was deduced as at the call site, which still remembers whether the original caller passed an lvalue or rvalue:
- If the caller passed an lvalue, `T` was deduced as `int&` which collapses to `int&`, so `std::forward<T>` returns an lvalue reference, preserving it as lvalue.
- If the caller passed an rvalue, `T` was deduced as `int`, so `std::forward<T>` returns an rvalue reference, restoring the rvalue nature that was lost when `p` got a name.

```cpp
void f( const int& p ) { std::cout << "[l-value]"; } // called for lvalues
void f( int&& p )      { std::cout << "[r-value]"; } // called for rvalues

template<typename T> void f3( T&& p )
{
    f( p );                      // p has a name so always lvalue -> [l-value]
    f( std::forward<T>( p ) );  // checks original T to restore true category
}

int a = 2;
f3( a );  // a is lvalue, T = int&,  forward returns lvalue  -> [l-value][l-value]
f3( 4 );  // 4 is rvalue, T = int,   forward returns rvalue  -> [l-value][r-value]
```
The first call `f(p)` always prints `[l-value]` regardless of what was passed because `p` has a name. The second call `f(std::forward<T>(p))` prints the correct category because `forward` looks at `T` to reconstruct what the original caller actually gave.

This is called **perfect forwarding** because the argument reaches the final function `f` in exactly the same state the original caller intended, lvalue stays lvalue, rvalue stays rvalue. Without it everything silently becomes lvalue and the wrong overload gets called, which matters for `emplace()` where you need the arguments to reach the element constructor with their original types intact so the right constructor overload is selected.

It is also a **compile-time only** operation. `std::forward` generates no executable code, it is purely a type cast that tells the compiler how to treat the argument. The decision happens at compile time based on what `T` was deduced as, not at runtime.
### Key Takeaways
1. Template argument deduction infers type parameters from call-site argument types.
2. lvalues bind to `T&`, const lvalues to `const T&`, and rvalues to `T&&` as the best match.
3. A universal reference (`U&&` with deduced `U`) binds to any value category.
4. Reference collapsing resolves reference-to-reference types: `X&& &&` is the only combination that remains `X&&`.
5. `std::forward` preserves the original value category through forwarding chains; without it, all forwarded arguments become lvalues.

**Remember this:** `std::move` always gives you an rvalue; `std::forward` gives you whatever the caller gave you.

**One analogy to remember it by:** `std::move` is handing over your keys and walking away; `std::forward` is passing a sealed envelope along without opening it, so the final recipient gets exactly what was sent.

---

## Part 4: Queue ADT

### What is a Queue?
A **queue** is a dynamic set that enforces a **first-in, first-out (FIFO)** policy: the element removed is always the one that has been in the queue the longest.

In simple terms: elements join at the back and leave from the front, like a supermarket checkout line.

Think of it like: a printer job queue. The first document submitted is the first to print. Any new jobs go to the back.

Why it matters: queues appear throughout computing: process scheduling, breadth-first graph search, network packet buffering, and any system where order of arrival must be respected.

### Queue Operations
![[Pasted image 20260527233825.png]]
A queue interface exposes three core operations:
1. `top()` returns the oldest element (the front of the queue) without removing it. It returns `std::optional<T>` so that the check for emptiness and the retrieval of the value happen as a single atomic operation, which is essential for thread-safe code. The running time is $O(1)$.
2. `enqueue(value)` inserts a new element at the tail of the queue. The tail advances in round-robin fashion over the backing array. The running time is $O(1)$.
3. `dequeue()` removes the oldest element by advancing the head pointer in round-robin fashion. The slot is not immediately zeroed; it will be overwritten on the next enqueue. The running time is $O(1)$.

### Circular Buffer Representation
![[Pasted image 20260527233951.png]]
The fixed-capacity queue (`Queue<T, N>`) uses a **circular buffer**: a statically allocated array of size $N$ with two indices, `fHead` and `fTail`, and a counter `fSize`.
- `fHead` points to the oldest (first-to-be-dequeued) element.
- `fTail` points to the most recently inserted element.
- Initially, `fHead == fTail == 0` and `fSize == 0` (empty).
- When `fHead` or `fTail` reaches index $N$, it wraps back to 0 via modular arithmetic.

The queue can distinguish empty from full using `fSize`:
```cpp
bool empty() const noexcept { return fSize == 0; }
bool full()  const noexcept { return fSize == N; }
```

The enqueue operation advances the tail before inserting (unless the queue was empty, in which case `fHead` and `fTail` remain in sync):
```cpp
void enqueue( const T& aValue )
{
    assert( !full() );          // crash in debug if queue is full

    if ( !empty() )             // only advance tail if queue has elements
    {
        if ( ++fTail == N )     // increment tail, if it hits the end of array
            fTail = 0;          // wrap back to 0 (circular buffer)
    }

    fElements[fTail] = aValue;  // place new element at tail position
    fSize++;                    // one more element in the queue
}
```

The dequeue operation advances the head after decrementing the count:
```cpp
void dequeue()
{
    assert( !empty() );
    fSize--;
    if ( !empty() )
    {
        if ( ++fHead == N ) fHead = 0;
    }
}
```
Both assert statements operate only in Debug builds and are stripped in Release, so there is no production overhead.

### Queue States
The circular buffer passes through four recognisable states:

|State|Condition|
|---|---|
|Empty|`fSize == 0`, `fHead == fTail`|
|One element|`fSize == 1`, `fHead == fTail`|
|Partially filled|`0 < fSize < N`, head and tail separated|
|Full|`fSize == N`, `full() == true`|

Note that empty and single-element states both satisfy `fHead == fTail`, which is why `fSize` is needed as a discriminator rather than relying on pointer equality alone.

### Key Takeaways
1. A queue is FIFO: first in, first out; unlike a stack which is LIFO.
2. A circular buffer implements a queue in $O(1)$ time for all three operations, using index wrapping instead of element shifting.
3. `fSize` is required to distinguish the empty state from the single-element state, since both have `fHead == fTail`.
4. Using `std::optional<T>` for `top()` makes it atomic: test-and-read in one operation, which is safer in concurrent contexts.

**Remember this:** A circular buffer is a fixed ring of slots. Head chases tail; when either hits the end, it wraps to the beginning.

**One analogy to remember it by:** a roundabout (traffic circle) with numbered entry and exit points. Cars join at the tail, leave from the head, and the road itself never changes length.

---

## Part 5: In-place Construction, Variadic Templates, and Revised Containers

### The Performance Problem with Type Mismatches
When inserting an element into a typed container using `enqueue` or `push`, the argument must match the container's element type. If it does not, the compiler creates a temporary object of the correct type, which is then copied in. For example:
```cpp
Queue<std::string> lQueue;
lQueue.enqueue( "Hello, world!" ); // const char[14], not std::string
```

The compiler creates a temporary `std::string` from the c-string literal, then copies it into the queue. For performance, the c-string should be forwarded directly to a `std::string` constructor inside the queue's storage, skipping the temporary entirely.

### emplace() and In-place Construction
**`emplace()`** solves this by constructing the element directly inside the container's memory using a **placement new** expression. The steps are:
1. Destroy the object currently occupying the target slot by calling its destructor explicitly (`~T()`). This frees resources but does not deallocate the memory.
2. Use placement `new` to construct a new `T` in that same memory using the forwarded arguments.
```cpp
template<typename... Args>
void emplace( Args&&... args )
{
    assert( !full() );
    if ( !empty() )
    {
        if ( ++fTail == N ) fTail = 0;
    }
    fElements[fTail].~T();                                  // free old entry
    new (&fElements[fTail]) T( std::forward<Args>(args)... ); // construct in-place
    fSize++;
}
```
Placement `new` does not allocate memory; it initialises an existing memory location. `std::forward<Args>(args)...` uses perfect forwarding so each argument reaches the `T` constructor with its original value category intact.

### Variadic Templates
**Variadic templates** allow a function or class template to accept a parameter pack of zero or more type arguments. In `emplace`, `typename... Args` is the **template parameter pack** and `Args&&... args` is the **function parameter pack**:
```cpp
template<typename... Args>
void emplace( Args&&... args )
{ /* ... */ }
```
The ellipsis `...` indicates expansion. When the compiler instantiates the function, it generates a version with exactly the types and number of arguments provided by the caller. This makes `emplace` work for any constructor signature of `T`, with no code duplication.

### Revised Stack and Queue
`StackRevised<T>` and `QueueRevised<T>` are fully-featured dynamic containers that combine copy semantics, move semantics, the swap idiom, `emplace()`, and automatic capacity management (expand on overflow, contract on underuse) into a single coherent class. `QueueRevised<T>` additionally manages head, tail, and size on a heap-allocated circular buffer rather than a fixed-size stack array.

Both revised containers demonstrate that in C++, the interface of an ADT remains stable while its internal representation can be replaced (from fixed-size array to dynamic heap allocation) without changing any client code.

### Stack, Queue, and the Standard Library
The standard library's `std::stack` and `std::queue` are **object adapters**: they do not maintain their own storage. Instead, they hold an instance of `std::deque` (a double-ended queue) and forward all calls to its methods. This means the stack and queue interfaces are backed by a shared representation.

`std::deque` can be viewed as a circular buffer, but the standard library uses a more sophisticated segmented approach that avoids copying elements when the buffer must grow or shrink, unlike the doubling strategy used in this course's `DynamicStack`.

A **priority queue** extends the basic queue concept by ordering elements rather than serving them strictly by arrival time. A naive implementation could use sorted priority-value pairs, but the canonical efficient approach is a **heap**, which combines the structural simplicity of an array with the access pattern of a binary tree.

### Key Takeaways
1. Type mismatches between call-site arguments and container element types silently create temporary copies; `emplace` eliminates this cost.
2. `emplace` uses placement `new` to construct directly inside existing memory, preceded by an explicit destructor call to release the old object's resources.
3. Variadic templates allow `emplace` to forward any number of arguments of any type to the element constructor with zero overhead.
4. `std::stack` and `std::queue` are thin adapters over `std::deque`; they share a representation.
5. Priority queues are best implemented with a heap rather than sorted lists.

**Remember this:** `push` copies a ready-made object into the container; `emplace` builds the object inside the container from raw ingredients.

**One analogy to remember it by:** `push` is delivering a pre-packaged burger to a restaurant and putting it on the shelf; `emplace` is sending the raw ingredients and having the kitchen assemble the burger directly on the shelf.