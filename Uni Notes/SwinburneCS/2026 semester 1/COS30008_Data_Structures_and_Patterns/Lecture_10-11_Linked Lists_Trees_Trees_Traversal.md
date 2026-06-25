## Part 1: Problems with Arrays and Linked Lists
### Initial note:

| Symbol  | Meaning                                                   | Example                               |
| ------- | --------------------------------------------------------- | ------------------------------------- |
| `*`     | dereference - get value (a something) a pointer points to | `*this` = the object `this` points to |
| `&`     | reference - alias for existing variable (no copy)         | `List& x = lList`                     |
| `**`    | pointer to a pointer                                      | rarely used                           |
| `&&`    | rvalue reference - binds to temporaries/moved values      | `List&& aOther`                       |

```cpp
int x = 5;
int* p = &x;   // p is a pointer TO x
*p = 10;       // dereference p → x is now 10
int& r = x;    // r is an alias for x
r = 20;        // x is now 20
```  

```cpp

void foo(List a)        // pass by value - makes a copy

void foo(List& a)       // pass by reference - no copy, can modify

void foo(const List& a) // pass by reference - no copy, read only

void foo(List* a)       // pass by pointer

void foo(List&& a)      // pass rvalue - move semantics (steal from temp)

```

#### In List.h

| Usage                | What it means                                      |
| -------------------- | -------------------------------------------------- |
| `const List& aOther` | read-only reference to source, no copy made        |
| `List& operator=`    | returns reference to current object                |
| `List&& aOther`      | move constructor - steals from a temporary         |
| `*this`              | the actual object (dereference the `this` pointer) |
| `this`               | pointer to current object                          |
| `return *this`       | return the object itself (not a pointer)           |
### What is a Linked List?
A **linked list** is a dynamic data structure where each element (node) stores its data alongside a pointer to the next node, rather than occupying contiguous memory like an array.

In simple terms: instead of elements sitting next to each other in memory, each element has a signpost pointing to where the next one is.

Think of it like: a treasure hunt where each clue tells you where the next clue is, rather than all the clues laid out on a table in order.

Why it matters: arrays are fast for reading but expensive for inserting and deleting in the middle. Linked lists flip that trade-off, making structural changes cheap.

### Problems with Arrays
![[Pasted image 20260528235243.png]]
Arrays store elements in contiguous memory, which gives them strong memory locality (low latency reads). However, this comes at a cost for mutations. Inserting or deleting an element at an arbitrary position requires shifting n/2 elements on average, giving O(n) time complexity. Resizing is also a concern; in real-time systems where timing deadlines must be met, an unexpected resize can be a critical failure.
![[Pasted image 20260528235401.png]]
The key tension is that arrays' greatest strength (contiguous memory, cache-friendly reads) is also the source of their main weakness (costly structural changes).

### Singly Linked List
![[Pasted image 20260528235603.png]]
A **singly linked list** is a sequence of nodes where each node holds data and a `next` pointer to the following node. The final node's `next` pointer is set to a sentinel value (Nil), marking the end. It is a recursive data structure: each node is of the same type and refers to another node of the same type.

In C++, a `struct` is essentially a `class` where members are `public` by default instead of `private`. That is the only real difference. So `SinglyLinkedList` being a struct means `data` and `next` are directly accessible without getters, which is fine here because this is a low-level node type, not an ADT meant to hide its internals.

The struct is **templated over `T`**, meaning the same node definition works for any data type. When you write `SinglyLinkedList<int>`, the compiler stamps out a version where `data` is an `int`. When you write `SinglyLinkedList<std::string>`, `data` becomes a `std::string`. The struct itself does not care what `T` is.

The two constructors handle the two value categories. The first takes `const T& aData`, which is an lvalue reference, so it **copies** the data in. The second takes `T&& aData`, which is an rvalue reference, so it calls `std::move(aData)` to **transfer** ownership of the data rather than copying. Both constructors accept an optional `aNext` pointer defaulting to `nullptr`, so a newly created node starts as a tail by default.

The `next` pointer is a raw pointer to another node of the same type, which is what makes it recursive: `SinglyLinkedList<T>` contains a pointer to `SinglyLinkedList<T>`. The struct refers to itself in its own definition, which is valid in C++ as long as it is a pointer or reference (not a value, which would require infinite size).

The reason you need a type alias or explicit type when declaring pointers is that `SinglyLinkedList` alone is not a complete type without `<T>`. The compiler has no context to infer what `T` should be from a bare pointer declaration, unlike a function call where it could look at the argument types.
```cpp
template<typename T>
struct SinglyLinkedList
{
    T data; // public field access
    SinglyLinkedList* next; // public field access

    SinglyLinkedList( const T& aData, SinglyLinkedList* aNext = nullptr ) noexcept :
        data(aData), // copy data
        next(aNext) 
    {}

    SinglyLinkedList( T&& aData, SinglyLinkedList* aNext = nullptr ) noexcept :
        data(std::move(aData)), // move data
        next(aNext) 
    {}
};
```

![[Pasted image 20260529002950.png]]
A critical note on class template argument deduction: when declaring raw pointers to `SinglyLinkedList`, the compiler cannot deduce template arguments because there are no function arguments to deduce from.
![[Pasted image 20260529003016.png]]
You must use a type alias (e.g., `using StringList = SinglyLinkedList<std::string>`) or specify the type explicitly.

### List Manipulations
#### Insert at Top
![[Pasted image 20260529003553.png]]
```cpp
p = new SinglyLinkedList( 5 );
q = new SinglyLinkedList( 7, p );
```
The new node is created with its `next` already pointing to the current head. The head variable then becomes the new node. No traversal needed because you always know where the top is. Cost: O(1).

**Traversal** is simply walking through every node in the list from head to tail, one node at a time by following next pointers.

#### Insert in the Middle
![[Pasted image 20260529004030.png]]
```cpp
SinglyLinkedList<int>* r = new SinglyLinkedList( 6 );
r->next = p;   // new node points to what comes after
q->next = r;   // predecessor points to new node
```
Order matters critically here. You must set `r->next = p` first before `q->next = r`. If you do it the other way around, `q->next` becomes `r` before `r->next` is set, and you lose the reference to the rest of the chain permanently. The position must already be known before this runs, finding it costs O(n), but the insertion itself once you are there is O(1).

#### Insert at the End: Naive
![[Pasted image 20260529003640.png]]
```cpp
SinglyLinkedList<int>** pInsert = &q;
```
`pInsert` is a pointer-to-pointer, initialised to `&q`, meaning it holds the address of `q` itself. So `*pInsert` gives you `q`, which is the head pointer. This is the starting point of the traversal.

```cpp
while ( *pInsert != nullptr )
{
    pInsert = &((*pInsert)->next);
}
```
Each iteration breaks down as:
- `*pInsert` dereferences once to get the current pointer (starts as `q`)
- checks if it is nullptr, if so the loop ends because we found the end of the list
- if not null, `(*pInsert)->next` accesses the `next` field of the current node
- `&((*pInsert)->next)` takes the address of that `next` field
- `pInsert` is reassigned to that address, so it now points at the `next` field inside the current node

So `pInsert` is not moving from node to node, it is moving from one `next` field to the next `next` field. At the end of the loop it is sitting at the address of the last node's `next` field, which holds `nullptr`.

Visually for the list `q -> 7 -> 5 -> Nil`:
```
Start:    pInsert = &q          *pInsert = q (points to node 7)
Step 1:   pInsert = &(7->next)  *pInsert = pointer to node 5
Step 2:   pInsert = &(5->next)  *pInsert = nullptr  -> loop ends
```

```cpp
*pInsert = new SinglyLinkedList( 9 );
```
At this point `pInsert` holds the address of node 5's `next` field. Assigning to `*pInsert` writes directly into that field, so node 5's `next` now points to the new node 9. No need to track the previous node separately because you are already holding the address of the exact memory slot you want to write into.

**Empty list case:** if `q` is null to begin with, `*pInsert` is null on the very first check and the loop never runs. The assignment `*pInsert = new SinglyLinkedList(9)` then writes directly into `q` itself, making node 9 the new head. This works without any special case because `pInsert` started as `&q`, so writing to `*pInsert` is the same as writing to `q`.

#### Insert at the End: With Tail Pointer
![[Pasted image 20260529003729.png]]
```cpp
pListLast->next = new SinglyLinkedList( 9 );
pListLast = pListLast->next;
```
Maintaining a separate `pListLast` variable that always tracks the last node reduces end-insertion to O(1). You never need to traverse. The trade-off is that every other operation that changes the end of the list (deletion, moving nodes) must also remember to update `pListLast`, otherwise it becomes a dangling or incorrect pointer.

#### Delete a Node
![[Pasted image 20260529003703.png]]
```cpp
q->next = q->next->next;
```
`q` here is the predecessor of the node you want to remove. You skip over the target by making `q->next` jump to the node after the target. The target node is now unreachable from the list. Cost is O(1) once you have the predecessor, but finding it requires O(n) traversal from the head since singly linked lists have no backward pointer.

One important concern with raw pointers: after this operation the removed node is still in memory but nothing in the list points to it anymore. If you do not `delete` it explicitly, that is a memory leak. This is one of the six raw pointer problems mentioned earlier, there is no automatic cleanup.

#### Delete from the End
There is no shortcut here even with a tail pointer. To remove the last node you need to find the second-to-last node and set its `next` to nullptr, then update `pListLast`. Since singly linked nodes have no `previous` pointer, you must traverse from the head to find the new tail. Cost: O(n). This asymmetry (O(1) append but O(n) delete from end) is a fundamental constraint of singly linked lists and is one of the motivations for doubly linked lists.

### Raw Pointers: Why They Are Problematic
Raw pointers carry six fundamental problems:
1. A declaration does not indicate whether it points to a single object or an array.
2. It does not indicate whether you own the pointed-to memory.
3. If you do own it, there is no indication of how to free it (`delete` vs a custom mechanism).
4. Even if `delete` is correct, it may be unclear whether to use `delete` (single) or `delete[]` (array).
5. Ensuring memory is freed exactly once along all code paths is difficult.
6. There is no way to detect a dangling pointer (one pointing to already-freed memory).

### RAII and Smart Pointers
**Resource Acquisition Is Initialization (RAII)** is a programming idiom where resource acquisition and object initialization happen at the same time. The main principle of RAII is to give ownership of any heap-allocated resource to a stack-allocated object whose destructor contains the code to delete or free the resource and also any associated cleanup code. All standard C++ library abstractions follow RAII.

**Smart pointers** are defined in `<memory>` and are the primary RAII tool for heap-allocated memory:
- `std::unique_ptr<T>`: exactly one owner. Cannot be copied, only moved. Use for plain C++ objects (POCO) as the default choice.
- `std::shared_ptr<T>`: reference-counted. The managed object is kept alive as long as any owner holds a reference. Every time a new `shared_ptr` points to the same object, the count goes up. When one is destroyed, it goes down. When it hits zero, the object is deleted. Use when a resource must be shared across multiple owners.
- `std::weak_ptr<T>`: a non-owning observer* of a `shared_ptr`-managed object meaning `weak_ptr` cannot exist without a `shared_ptr`. Does not increment the reference count. Used to break circular reference cycles that would otherwise cause memory leaks.

```cpp
auto a = std::make_shared<Node>(); // a count = 1
auto b = std::make_shared<Node>(); // b count = 1

a->next = b;  // b count = 2
b->prev = a;  // a count STAYS 1, weak_ptr does not increment
```

When scope ends:
```
a destroyed: 1 -> 0, deleted
    destroys a->next, b count: 2 -> 1

b destroyed: 1 -> 0, deleted
no leak
```

Compare with `shared_ptr` for both:
```
a destroyed: 2 -> 1, NOT deleted (b->prev still holds it)
b destroyed: 2 -> 1, NOT deleted (a->next still holds it)
both stuck at 1, both leaked
```

Prefer `std::make_unique` and `std::make_shared` over direct `new`, as they use perfect forwarding to construct objects and are safer in the presence of exceptions.

### Variadic templates
**Variadic templates** allow a function or class template to accept any number of type arguments, including zero.

Without variadic templates you would need a separate overload for every possible number of arguments:
```cpp
// without variadic, must write one for each case
static node makeNode()
static node makeNode( const T& a )
static node makeNode( const T& a, node& b )
static node makeNode( T&& a )
static node makeNode( T&& a, node& b )
// and so on forever...
```

With variadic templates one function handles all cases:
```cpp
template<typename... Args>  // ... means zero or more types
static node makeNode( Args&&... args )  // ... means zero or more arguments
{
    return std::make_shared<SinglyLinkedListSP>( std::forward<Args>(args)... );
}
```

**Breaking down the syntax:**
```cpp
typename... Args
```
`...` after `typename` declares a **template parameter pack**, basically a bunch of types. This means `Args` is not a single type, it is a collection of zero or more types. The compiler figures out what they are from what you pass in.

```cpp
Args&&... args
```
`...` after `&&` declares a **function parameter pack**, means a bunch of arguments of those types. Each argument gets its own `Args&&` universal reference. So if you pass three arguments, there are three separate `Args&&` references.

```cpp
std::forward<Args>(args)...
```
`...` at the end **expands** the pack, means write this out once for each argument. The compiler writes out one `std::forward` call per argument:
```cpp
makeNode( "AAAA" )
// expands to:
std::make_shared<SinglyLinkedListSP>( std::forward<const char*>(arg0) )

makeNode( "BBBB", One )
// expands to:
std::make_shared<SinglyLinkedListSP>( std::forward<const char*>(arg0),
                                      std::forward<node>(arg1) )
```

**What the compiler actually does:**
The compiler generates a completely separate function for every unique combination of argument types you call it with. This happens at compile time, not runtime:
```cpp
makeNode( "AAAA" );
// compiler stamps out:
// static node makeNode( const char*&& arg0 )

makeNode( "BBBB", One );
// compiler stamps out:
// static node makeNode( const char*&& arg0, node& arg1 )
```
Each generated version is fully type safe and has zero runtime overhead because it all happens before the program runs.

**The `...` position matters:**
```cpp
typename... Args    // declaring a pack, put ... after typename
Args&&... args      // declaring a pack, put ... after &&
args...             // expanding a pack, put ... after the name
std::forward<Args>(args)...  // expanding, put ... at the very end
```
A simple way to remember it: `...` on the left of a name means declaring a pack, `...` on the right means expanding it.
### Singly Linked List with Smart Pointers
The raw pointer version of `next` is replaced with a `node` nested type alias, not a member variable. It does not take up any space in an instance:
```cpp
using node = std::shared_ptr<SinglyLinkedList>; // shorthand for shared_ptr to a node
```
`using` is only for types. You cannot use `using` to **define** alias a variable, a function, or a value.

The struct now has four overloaded constructors covering all combinations of copy/move for data and with/without a next pointer:
```cpp
template<typename T>
struct SinglyLinkedListSP
{
    using node = std::shared_ptr<SinglyLinkedListSP>; // shared_ptr replaces raw pointer

    T data;
    node next; // owning pointer to next node, nullptr if tail

    // copy data, no next
    SinglyLinkedListSP( const T& aData ) noexcept : 
        data(aData), next(nullptr) {}
    
    // copy data, with next
    SinglyLinkedListSP( const T& aData, node& aNext ) noexcept : 
        data(aData), next(aNext) {}
    
    // move data, no next
    SinglyLinkedListSP( T&& aData ) noexcept : 
        data(std::move(aData)), next(nullptr) {}
    
    // move data, with next
    SinglyLinkedListSP( T&& aData, node& aNext ) noexcept : 
        data(std::move(aData)), next(aNext) {}
};
```
Everything that exists at runtime lives somewhere in memory and therefore has an address. `&` can take the address of any of it.

**Without the factory method**, creating nodes is verbose:
```cpp
using StringList     = SinglyLinkedListSP<std::string>; // alias for the node type
using StringListNode = StringList::node;                // alias for shared_ptr to node

// must manually wrap with shared_ptr and call new directly
StringListNode One   = std::shared_ptr<StringList>( new StringList( "AAAA" ) );
StringListNode Two   = std::shared_ptr<StringList>( new StringList( "BBBB", One ) );
StringListNode Three = std::shared_ptr<StringList>( new StringList( "CCCC", Two ) );
```
`::` itself just means "look inside this scope". Whether that requires `static` depends on what you are looking for, functions and variables need `static`, but types do not.

The caller must know the full type name, manually wrap with `shared_ptr`, and call `new` directly. This is exactly what `make_shared` and the factory method are designed to eliminate.

**`std::make_unique` and `std::make_shared`** are standard library helpers that construct an object and wrap it in a smart pointer in one step. Both use perfect forwarding to pass arguments directly to the object constructor. Prefer them over `new` because they are exception safe, more efficient (one allocation instead of two for `make_shared`), and cleaner to read. Note that C++17 only supports object creation with these, C++20 extends support to arrays as well.

**With the factory method**, the same list becomes:
```cpp
using StringList     = SinglyLinkedListSP<std::string>; // alias for the node type
using StringListNode = StringList::node;                // alias for shared_ptr to node

// factory method hides shared_ptr and new internally
StringListNode One   = StringList::makeNode( "AAAA" );
StringListNode Two   = StringList::makeNode( "BBBB", One );
StringListNode Three = StringList::makeNode( "CCCC", Two );
```

The factory method is a `static` function inside the struct using variadic templates and perfect forwarding:
```cpp
template<typename... Args>        // accept any number of type arguments
static node makeNode( Args&&... args ) // universal reference parameter pack
{
    // make_shared<T, Args...>
    // allocates node and shared_ptr control block in one allocation
    // forwards each argument with original value category intact
    return std::make_shared<SinglyLinkedListSP>( std::forward<Args>(args)... );
}
```

`static` means it belongs to the class itself, not any instance, so it can be called before any node exists. `typename... Args` and `Args&&... args` accept any number of arguments of any type, matching any constructor signature. `std::forward<Args>(args)...` unpacks the parameter pack and forwards each argument with its original value category intact. `std::make_shared` then allocates the node and wraps it in a `shared_ptr` in one allocation.

The **Factory Method** is a creational design pattern that separates object creation from the code controlling it. The caller asks for a node without knowing how it is built internally. Variadic templates and perfect forwarding make it convenient to define because the same function handles any constructor signature without writing separate overloads.

Traversing the list works the same way regardless of whether raw pointers or shared pointers are used:
```cpp
// start from Three (most recently inserted), walk to nullptr
for ( StringListNode lTop = Three; lTop != nullptr; lTop = lTop->next )
{
    std::cout << "Value: " << lTop->data << std::endl;
}
// Output:
// Value: CCCC  <- Three was inserted last, points to Two
// Value: BBBB  <- Two points to One
// Value: AAAA  <- One points to nullptr, loop ends
```
The list prints in reverse insertion order because each new node is inserted at the front, pointing back to the previous head.
### Doubly Linked List
![[Pasted image 20260530225503.png]]
A **doubly linked list** extends the singly linked list by adding a `previous` pointer to each node, enabling bidirectional traversal. Key advantages over singly linked lists include: deletion at either end is O(1), and reverse iteration is straightforward.

```cpp
template<typename T>
struct DoublyLinkedList
{
	using node = std::shared_ptr<DoublyLinkedList>;
	using node_w = std::weak_ptr<DoublyLinkedList>;
	
	T data;
	node next;
	node_w previous;
	
	DoublyLinkedList( const T& aData ) noexcept : data(aData) {}
	
	DoublyLinkedList( T&& aData ) noexcept : data(std::move(aData)) {}
	
	void link( node& aPrevious, node& aNext ) noexcept; // link adjacent nodes
	void isolate() noexcept; // unlink node
	
	// factory method for list nodes
	template<typename... Args>
	static node makeNode( Args&&... args );
};
```

```cpp
void isolate() noexcept
{
	if ( next ) // Is there a next node?
	{
		next->previous = previous;
	}
	
	Node lNode = previous.lock(); // lock std::weak_ptr
	
	if ( lNode ) // Is there a previous node?
	{
		lNode->next = next;
	}
	
	previous.reset();
	next.reset();
	// lNode goes out of scope
}
```

In the smart pointer implementation, `next` is a `std::shared_ptr` (owning) while `previous` is a `std::weak_ptr` (non-owning). This is deliberate: if both were `shared_ptr`, circular references between adjacent nodes would prevent the reference count from reaching zero, causing a memory leak. The `weak_ptr` breaks the cycle.

To access a `weak_ptr`, call `.lock()` to obtain a temporary `shared_ptr`:
```cpp
Node lNode = previous.lock(); // obtain usable shared_ptr
if ( lNode )
{
    lNode->next = next;
}
```
The `isolate()` method on a doubly linked node unlinks it by updating its neighbors' pointers and then resetting its own smart pointer references.

### List ADT
![[Pasted image 20260530234326.png]]
The `List<T>` class wraps a doubly linked list behind a clean interface. It uses `fHead` and `fTail` nodes (both `shared_ptr`) and a `size_t fSize` counter. Operations include `push_front`, `push_back`, `remove`, `operator[]`, and iterator methods.

An important implementation note: the synthesized destructor for `List` would recursively traverse `next` pointers when destroying `fHead`, potentially causing a stack overflow on large lists. The explicit destructor must instead traverse backwards from `fTail`, isolating each node before its `shared_ptr` goes out of scope.

### Singly Linked List Iterator
![[Pasted image 20260530234450.png]]
A `SinglyLinkedListIterator<T>` provides a forward iterator interface over a singly linked list. It satisfies the C++20 `BasicForwardIterator` concept by supplying:
- `difference_type = std::ptrdiff_t` (satisfies `weakly_incrementable`)
- `value_type = T` (satisfies `indirectly_readable`)
- `operator*`, `operator++` (prefix and postfix), `operator==`
- `begin()` and `end()` for range-based for loops

The constructor takes the head node and sets both `fList` (the collection anchor) and `fPosition` (the current element) to it. `end()` returns an iterator copy where `fPosition` is `nullptr`.

### Doubly Linked List Iterator and DFA
![[Pasted image 20260531010220.png]]
The `DoublyLinkedListIterator<T>` is a bidirectional iterator with four methods: `begin`, `end`, `rbegin`, `rend`.

![[Pasted image 20260530234755.png]]
It models its movement as a **Deterministic Finite Automaton (DFA)** with three states: `BEFORE`, `DATA`, and `AFTER`.

A DFA is formally a quintuple (Σ, Q, q₀, σ, F) where Σ is the input alphabet, Q is the set of states, q₀ is the initial state, σ is the transition function, and F is the set of accepting states. For the list iterator, the "alphabet" is the increment/decrement operations combined with whether the current pointer is NIL or not.

![[Pasted image 20260530234842.png]]
State transitions are implemented as a `switch` statement over the current state. Each case inspects the current position and transitions accordingly. The `break` statement after each case is critical; without it, C++ allows silent fall-through, which is a common source of hard-to-find bugs. A `default` case is generally required unless the case analysis is exhaustive (all enum values are covered).
```cpp
switch ( fState )
{
	case States::BEFORE:
	// BEFORE logic
		break;
	case States::DATA : fPosition = fPosition->next;
		if (!fPosition) {
			fState = State::AFTER
		}
		break;
	case States::AFTER:
	// AFTER logic
	break;
}
```

When advancing forward in state DATA, the iterator moves to `fPosition->next`. If that pointer is null, the state transitions to AFTER; otherwise, it stays in DATA.

### Key Takeaways
1. Arrays are contiguous and cache-friendly, but insertion and deletion are O(n) due to required shifts.
2. Linked lists achieve O(1) insertion and deletion at a known position, at the cost of O(n) traversal and worse cache performance.
3. Raw pointers are dangerous; RAII and smart pointers (`unique_ptr`, `shared_ptr`, `weak_ptr`) should be preferred for safe memory management.
4. In doubly linked lists, the `previous` pointer must be a `weak_ptr` to avoid reference count cycles and memory leaks.
5. The List ADT requires an explicit destructor to avoid stack overflow during iterative destruction of large lists.

**Remember this:** Linked lists solve the array's mutation problem by trading contiguous memory for pointers, and smart pointers solve raw pointer dangers by tying memory lifetime to object scope.

**One analogy to remember it by:** A singly linked list is like a train where each carriage only knows the one behind it; a doubly linked list is like a train where each carriage knows both neighbors; and smart pointers are like the train conductor who automatically uncouples carriages when the journey ends.

---

## Part 2: Trees

### What is a Tree?
A **tree** T is a finite, non-empty set of nodes defined recursively:
$$T = {r} \cup T_1 \cup T_2 \cup \ldots \cup T_n$$
where r is the designated **root** and $T_1, T_2, \ldots, T_n$ are distinct subtrees ($n \geq 0$). A tree is inherently a recursive structure: every subtree is itself a tree.
![[Pasted image 20260530235446.png]]
In simple terms: a tree is a hierarchy where one node is at the top and every other node has exactly one parent.

Think of it like: a company org chart. The CEO is the root, each department head is a child, and employees are leaf nodes at the bottom.

Why it matters: trees underpin nearly every fast data structure in computing, from file systems and databases to compilers and search engines. The recursive structure makes many algorithms elegant and efficient.

### Core Terminology
![[Pasted image 20260531000013.png]]
- **Parent and child:** The root r of tree T is the parent of all subtree roots $r_i$. Each $r_i$ is a child of r. Nodes with the same parent are **siblings**.
- **Leaf:** A node with no subtrees (degree zero). In $T_c = {D, {E, {H}}, {F, {G, {I}}, {J, {K}, {L}}}}$, the leaves are H, I, K, L.
- **Degree:** The number of subtrees a node has. The degree of the tree itself is the maximum degree of any node.
- **Path:** A non-empty sequence of nodes $p = {r_1, r_2, \ldots, r_k}$ where each node is the parent of the next. Path length $|p| = k - 1$.
- **Depth:** The length of the unique path from the root to node $r_i$.
- **Height of a node:** The length of the longest path from $r_i$ to any leaf below it. All leaves have height 0.
- **Height of a tree:** The height of its root node.
- For $T_c$: the tree height is 3, the depth of G is 2, and the height of J is 1 (since $\max(|{J,K}|, |{J,L}|) = 1$).

### N-ary Trees
![[Pasted image 20260531000200.png]]
A general tree allows each node to have a different degree. For trees where every node has the same degree N, we need the concept of an **empty tree** ($T = \emptyset$). Without it, it is impossible to build a finite tree where all nodes have degree N (except for the trivial N=0 case).

Think about why: if every node must have exactly N children, and those children must also have exactly N children, the tree would have to be infinite. The empty tree gives you a base case to stop at, the same way `nullptr` terminates a linked list.

An **N-ary tree** T where $N \geq 1$ is either:
- The empty tree $T = \emptyset$, or
- A root R with exactly N distinct N-ary subtrees: $T = {R, T_1, T_2, \ldots, T_N}$

The empty subtree slots are distinct from "missing" children; they are proper, typed, empty trees. This is why `nullptr` is a poor representation: `nullptr` means "nothing," but an empty subtree is a legitimate, typed value that happens to contain no data.

### Sentinel Nodes
A **sentinel node** is a programming idiom used in place of null to represent an empty subtree. Unlike `nullptr` (which refers to nothing), a sentinel node is a proper object of the same type as a non-empty tree node, carrying no key.

The problem with `nullptr` is a type mismatch. If empty subtrees are represented as `nullptr`, then a subtree slot can be either a real node or `nullptr`, and they are different types. The recursive definition requires all subtrees, empty or not, to be the same type.

In C++, `std::unique_ptr<NTree>` naturally supports this: when the pointer wraps `nullptr`, it acts as a sentinel (empty subtree). When it wraps a heap object, it is a non-empty node. Both cases share the same `unique_ptr` type:
```cpp
std::unique_ptr<NTree> slot; // wraps nullptr, acts as sentinel
slot = NTree::makeNode(5);   // wraps a node, non-empty
// both are unique_ptr, same type, satisfies the definition
/* Before assignment:
slot [ nullptr ] -> nothing
After assignment:
slot [ 0x1234 ] -> [ NTree, key=5 ] on heap */
```

### NTree<T, N>
```cpp
template<typename T, size_t N>
class NTree
{
public:
    using node = std::unique_ptr<NTree>; // exclusive ownership of subtrees

    NTree( const T& aKey = T{} ) noexcept; // copy key, default empty tree
    NTree( T&& aKey ) noexcept;             // move key, default empty tree

    template<typename... Args>
    static node makeNode( Args&&... args ); // factory method

    NTree( const NTree& aOther );                // copy constructor
    NTree& operator=( const NTree& aOther );     // copy assignment

    NTree( NTree&& aOther ) noexcept;            // move constructor
    NTree& operator=( NTree&& aOther ) noexcept; // move assignment

    void swap( NTree& aOther ) noexcept;         // swap helper

    const T& operator*() const noexcept;          // access key
    NTree& operator[]( size_t aIndex ) const;     // access subtree by index

    void attach( size_t aIndex, node& aNode );    // move node in
    node detach( size_t aIndex );                 // move node out

    bool leaf() const noexcept;                   // true if all slots are sentinels
    size_t height() const noexcept;               // longest path to a leaf

private:
    T fKey;          // key stored at this node
    node fNodes[N];  // exactly N subtree slots, sentinels by default
};
```

`NTree<T, N>` is a class template parameterised over key type `T` and tree degree `N`, where `T` is the type of the key and `N` is a compile-time constant for the number of subtrees each node has.

#### Exclusive Ownership
Nodes use `std::unique_ptr` instead of `shared_ptr`. A subtree belongs to exactly one parent: you cannot accidentally copy a subtree, only move it. `shared_ptr` would be wrong here because shared ownership implies the same subtree could appear in two places simultaneously, which violates the tree definition.

#### Constructors
All N subtree slots in `fNodes` default-initialise to `unique_ptr` wrapping `nullptr` (sentinels). Every newly created node is automatically a leaf with no children. The default argument `T{}` on the first constructor means you can create an empty tree node with no key:
```cpp
NTree<int, 3> node( 5 );  // key=5, all 3 slots are sentinels
NTree<int, 3> empty;      // key=T{} (default), all 3 slots are sentinels
```

#### Factory Method
Uses variadic templates and perfect forwarding to call `std::make_unique` with any combination of arguments:
```cpp
template<typename... Args>
static node makeNode( Args&&... args )
{
    return std::make_unique<NTree>( std::forward<Args>(args)... );
}
```
Uses `make_unique` instead of `make_shared` from the linked list version, reflecting the exclusive ownership model. The caller asks for a node without knowing the internal construction details.

#### Attach and Detach
```cpp
void attach( size_t aIndex, Node& aNode )
{
	// transfers ownership from aNode to fNodes[aIndex]
	assert( aIndex < N&& !fNodes[aIndex] );
	fNodes[aIndex] = std::move(aNode);
}
```

```cpp
Node detach( size_t aIndex )
{
	// transfers ownership from fNodes[aIndex] to result
	assert( aIndex < N && fNodes[aIndex] );
	return std::move(fNodes[aIndex]);
}
```
`attach(index, node)` moves a node into the tree at the given slot. The slot must currently be a sentinel because attaching to an occupied slot would lose the existing subtree. `detach(index)` moves the node out, leaving a sentinel behind.

Both use `std::move` since `unique_ptr` cannot be copied:
```cpp
auto child = NTree::makeNode( 5 );   // child owns the node
tree.attach( 0, std::move(child) );  // ownership transfers to tree
// child is now empty (nullptr)
```

#### Key Access
`operator*()` returns a const reference to `fKey`. This is why you see `**this` in BST (Binary Search Tree) code: the first `*` dereferences the `unique_ptr` to get the `NTree` object, the second `*` calls `operator*()` on that object to get the key.

`operator[](index)` returns a reference to the indexed subtree. It asserts two things: the index is in range, and the slot is not a sentinel. The second assert is necessary because returning a reference to `nullptr` is undefined behaviour:
```cpp
NTree& operator[]( size_t index )
{
    assert( index < N );          // in range
    assert( fNodes[index] );      // not a sentinel
    // assert( fNodes[index] != nullptr ); // explicit null check
    return *fNodes[index];        // safe to dereference
}
```

#### leaf() and height()
`leaf()` checks whether all N subtree slots are sentinels. It must check every slot so it runs in O(N) time, not O(1).
`height()` recursively computes the longest path to any leaf by taking the maximum height across all non-sentinel subtrees and adding one. It visits every node in the tree so it runs in O(n) time:
```cpp
bool leaf() const noexcept
{
	for ( size_t i = 0; i < N; i++ ) {
		if ( fNodes[i] ) {
			return false;
		}
	}
	return true;
}
size_t height() const noexcept
{
	size_t Result = 0;
	
	if ( !leaf() ) {
		for ( size_t i = 0; i < N; i++ ) {
			if ( fNodes[i] ) {
				Result = std::max( Result, fNodes[i]->height() + 1 );
			}
		}
	}
	return Result;
}
```

#### Copy Semantics
```cpp
NTree( const NTree& aOther ) : fKey(aOther.fKey) {
	for ( size_t i = 0; i < N; i++ ) {
		if ( aOther.fNodes[i] ) {
			// copy non-empty subtree
			fNodes[i] = std::move(makeNode( *aOther.fNodes[i] ));
		}
	}
}
```
The copy constructor copies `fKey` and recursively copies all non-empty subtrees. Each copied subtree is a new heap allocation giving a completely independent deep copy. Ownership of each copy is transferred into the new node via `std::move(makeNode(...))`.

```cpp
NTree& operator=( const NTree& aOther ) {
	if ( this != &aOther ) {
		this->~NTree();
		
		new (this) NTree( aOther );
	}
	return *this;
}
```
Copy assignment uses the placement new idiom: call the destructor on `this` to release current resources, then use in-place `new` to reinitialise via the copy constructor. This is the same pattern as `DynamicStack`.

#### Move Semantics and swap()
```cpp
NTree( NTree&& aOther ) noexcept : NTree() {
	swap( aOther );
}

NTree& operator=( NTree&& aOther ) noexcept {
	if ( this != &aOther ) {
		swap( aOther );
	}
	return *this;
}
```
Move semantics are implemented via `swap()`. The move constructor default-constructs an empty tree (all sentinels) then swaps with the source. After the swap, `this` has the source's data and the source has an empty tree. When the source is destroyed it takes the empty state with it.

`swap()` exchanges both the key and all N subtree `unique_ptr`s element by element:
```cpp
void swap( NTree& aOther ) noexcept
{
    std::swap( fKey, aOther.fKey );
    for ( size_t i = 0; i < N; i++ )
    {
        std::swap( fNodes[i], aOther.fNodes[i] );
    }
}
```
`std::swap` on `unique_ptr` internally uses move semantics to exchange ownership safely without any reference count overhead. The loop is necessary because `fNodes` is a raw array of `unique_ptr`s, not a single object, so each slot must be swapped individually.

### BTree
```cpp
template<typename T>
class BTree
{
public:
    using node = std::unique_ptr<BTree>; // exclusive ownership of subtrees

    BTree( const T& aKey = T{} ) noexcept; // copy key, default empty tree
    BTree( T&& aKey ) noexcept;             // move key, default empty tree

    template<typename... Args>
    static node makeNode( Args&&... args ); // factory method

    BTree( const BTree& aOther );                // copy constructor
    BTree& operator=( const BTree& aOther );     // copy assignment

    BTree( BTree&& aOther ) noexcept;            // move constructor
    BTree& operator=( BTree&& aOther ) noexcept; // move assignment

    void swap( BTree& aOther ) noexcept;         // swap helper

    const T& operator*() const noexcept;         // access key
    bool hasLeft() const noexcept;               // true if left subtree is not empty
    BTree& left() const;                         // access left subtree
    bool hasRight() const noexcept;              // true if right subtree is not empty
    BTree& right() const;                        // access right subtree

    void attachLeft( node& aNode );              // move node in as left subtree
    void attachRight( node& aNode );             // move node in as right subtree

    node detachLeft();                           // move left subtree out
    node detachRight();                          // move right subtree out

    bool leaf() const noexcept;                  // true if both subtrees are empty
    size_t height() const noexcept;              // longest path to a leaf

private:
    T fKey;      // key stored at this node
    node fLeft;  // left subtree, sentinel by default
    node fRight; // right subtree, sentinel by default
};
```
`BTree<T>` defines a proper binary tree with named left and right subtrees rather than indexed slots. It provides `hasLeft()`, `hasRight()`, `left()`, `right()`, `attachLeft()`, `attachRight()`, `detachLeft()`, and `detachRight()`.
![[Pasted image 20260531011619.png]]

A simple type alias `using BTree<T> = NTree<T, 2>` would technically compile but fails for two reasons. First, it does not communicate the left/right distinction. Index 0 and index 1 have no semantic meaning, whereas `left()` and `right()` do. Second, and more critically, `hasLeft()` and `hasRight()` need to inspect the private subtree array of `NTree` directly. A type alias is not a subclass and cannot access private members. A proper subclass could, but the subtree array would need to be `protected` rather than `private`, which exposes internals unnecessarily. Implementing `BTree<T>` independently keeps `NTree`'s internals private and gives `BTree` exactly the interface it needs:
```cpp
bool hasLeft()  const noexcept { return fLeft  != nullptr; }
bool hasRight() const noexcept { return fRight != nullptr; }

BTree& left()   const noexcept { return *fLeft;  }
BTree& right()  const noexcept { return *fRight; }
```

The named methods make traversal code readable and self-documenting, which matters because every DFS traversal method relies on `hasLeft()`, `hasRight()`, `left()`, and `right()` being clearly named and correct.

### Key Takeaways
1. A tree is defined recursively as a root plus N subtrees; the empty tree is a valid, distinct case requiring sentinel representation.
2. Degree, depth, height, and path are precisely defined properties that form the vocabulary for analyzing tree algorithms.
3. `NTree<T, N>` uses `unique_ptr` to enforce exclusive ownership; attaching and detaching subtrees transfers ownership via move semantics.
4. Sentinel nodes are preferred over `nullptr` for empty subtrees because they maintain a uniform type across empty and non-empty cases.
5. `BTree<T>` should be implemented as a standalone class, not a type alias or subclass of `NTree<T, 2>`, to correctly express left/right semantics and keep `NTree` internals private.

**Remember this:** A tree is a recursive structure: every node is the root of its own subtree, and smart pointer ownership mirrors this: each node exclusively owns its children.

**One analogy to remember it by:** An N-ary tree with `unique_ptr` children is like a folder system where each folder exclusively owns its subfolders. Moving a subfolder to a new parent is a transfer; you cannot clone it without explicitly copying. An empty folder slot is not "nothing" but a valid, named, empty location.

---

## Part 3: Tree Traversal

### What is Tree Traversal?
**Tree traversal** is the systematic process of visiting every node in a tree exactly once in a defined order.

In simple terms: given a tree, traversal is a recipe that decides which node to process next until all nodes have been seen.

Think of it like: reading a book's table of contents in different ways. You could follow chapters depth-first (finish all sub-sections before the next chapter) or breadth-first (scan all chapter titles before looking at any sub-sections).

Why it matters: virtually every useful tree operation, from search to evaluation to deletion, requires visiting nodes. The order in which you visit them changes the result and determines which data structure you need to implement the traversal efficiently.

### Two Families of Traversal
All tree traversal algorithms fall into one of two families.:
1. **Depth-first traversal (DFS)** plunges as deep as possible along a branch before backtracking; it uses a **stack** to track frontier nodes.
2. **Breadth-first traversal (BFS)** visits all nodes at a given depth before proceeding deeper; it uses a **queue** to track the next nodes to visit. 
The choice of **auxiliary data structure (temporary structures used during data manipulation)** is not incidental; it is what mechanically produces the correct ordering in both recursive and iterator-based implementations.

### Depth-First Traversal Variants
There are three named orderings for DFS on a binary tree, differing only in when the root is visited relative to its subtrees.

![[Pasted image 20260601002845.png]]
**Pre-order** visits the root first, then recursively traverses each subtree left to right. The mnemonic is: root, left, right. For the example tree with root D, this yields D-E-H-N-M-F-G-I-J-K-L. Pre-order is natural for copying a tree or serialising it, because the root always appears before its children.

![[Pasted image 20260601011143.png]]
**In-order** traverses the left subtree, visits the root, then traverses the right subtree. The mnemonic is: left, root, right. Result: H-N-E-M-D-I-G-F-K-J-L. In-order traversal of a binary search tree yields keys in sorted ascending order, which is its primary use case.

![[Pasted image 20260601002849.png]]
**Post-order** traverses each subtree left to right first, then visits the root. The mnemonic is: left, right, root. On the same tree: N-H-M-E-I-G-K-L-J-F-D. Post-order is natural for deletion (children must be freed before the parent) and for evaluating expression trees bottom-up, where operands must be known before applying an operator.

### Breadth-First Traversal
![[Pasted image 20260601011324.png]]
**Breadth-first traversal** visits nodes level by level, left to right within each level. On the same tree: D-E-F-H-M-G-J-N-I-K-L. This is also called **level-order traversal**. It is the natural way to find the shortest path from the root to a node, and is used in algorithms that process a tree layer by layer.

### Implementation Strategies
Two high-level strategies exist for implementing traversal:
1. The **Visitor Pattern** is a recursive approach. A `TreeVisitor<T>` base class provides four virtual hooks: `preVisit`, `inVisit`, `postVisit` (for DFS), and `visit` (for BFS). Concrete subclasses override exactly one hook to activate the desired ordering. The tree's `performDepthFirstSearch` method calls all three hooks at the right moments during its recursive walk; whichever hook is active determines the traversal order. This separates the traversal logic from the action performed on each node, a key benefit when new operations need to be added without modifying the tree class.
2. The **iterator-based approach** maps the recursive traversal to a linear sequence, converting the tree walk into something that satisfies C++20 range semantics and can be used directly in range-based for loops. This requires externalising the traversal state into the iterator object itself, which is where the stack and queue come in.

### The Visitor Pattern Structure
![[Pasted image 20260601011352.png]]
The Visitor Pattern (Gamma et al., 1994) separates an operation from the object structure it operates on. Three participants are involved. The **Visitor** interface declares a visit method for each element type. **ConcreteVisitor** classes implement those methods with specific behaviour. **Element** classes implement an `Accept(Visitor)` method that calls the appropriate visitor method back, supplying themselves as the argument.

The base `TreeVisitor<T>` provides default no-op implementations for all three DFS hooks and a default print implementation for the BFS `visit` hook:
```cpp
template<typename T>
class TreeVisitor
{
public:
    virtual ~TreeVisitor() {}
    
    //default dfs traversal
    virtual void preVisit(  const T& aKey ) const noexcept {}
    virtual void postVisit( const T& aKey ) const noexcept {}
    virtual void inVisit(   const T& aKey ) const noexcept {}
    
    //default bfs traversal
    virtual void visit( const T& aKey ) const noexcept
    {
        std::cout << aKey;
    }
};
```

Concrete visitors each override exactly one hook and delegate to the default `visit` implementation:
```cpp
template<typename T>
class PreOrderVisitor : public TreeVisitor<T>
{
public:
    void preVisit( const T& aKey ) const noexcept override
    {
        this->visit( aKey ); // invoke default print behaviour
    }
};
```
`PostOrderVisitor` overrides `postVisit` and `InOrderVisitor` overrides `inVisit` in exactly the same pattern.

### DFS and BFS Implementation on BTree
#### DFS Implementation
```cpp
void performDepthFirstSearch( const TreeVisitor<T>& aVisitor ) const noexcept
{
    aVisitor.preVisit( **this );                      // pre-order: fires here, before any subtree

    if ( hasLeft() )
    {
        left().performDepthFirstSearch( aVisitor );   // recurse into left subtree
    }

    aVisitor.inVisit( **this );                       // in-order: fires here, after left before right

    if ( hasRight() )
    {
        right().performDepthFirstSearch( aVisitor );  // recurse into right subtree
    }

    aVisitor.postVisit( **this );                     // post-order: fires here, after both subtrees
}
```
The method always calls all three hooks on every node. The key is that the base `TreeVisitor` has all three hooks as empty no-ops by default. Only the concrete visitor overrides one of them to actually do something. So the ordering is controlled by which hook is active, not by changing the traversal code.

Walking through each case with the same tree node:
**Pre-order** using `PreOrderVisitor`:
```cpp
aVisitor.preVisit( **this );  // ACTIVE: prints the key here
if ( hasLeft() )  { left().performDepthFirstSearch( aVisitor ); }
aVisitor.inVisit( **this );   // no-op
if ( hasRight() ) { right().performDepthFirstSearch( aVisitor ); }
aVisitor.postVisit( **this ); // no-op
```
Root is printed before either subtree is visited, giving root-left-right order.

**In-order** using `InOrderVisitor`:
```cpp
aVisitor.preVisit( **this );  // no-op
if ( hasLeft() )  { left().performDepthFirstSearch( aVisitor ); }
aVisitor.inVisit( **this );   // ACTIVE: prints the key here
if ( hasRight() ) { right().performDepthFirstSearch( aVisitor ); }
aVisitor.postVisit( **this ); // no-op
```
Root is printed after the left subtree and before the right subtree, giving left-root-right order.

**Post-order** using `PostOrderVisitor`:
```cpp
aVisitor.preVisit( **this );  // no-op
if ( hasLeft() )  { left().performDepthFirstSearch( aVisitor ); }
aVisitor.inVisit( **this );   // no-op
if ( hasRight() ) { right().performDepthFirstSearch( aVisitor ); }
aVisitor.postVisit( **this ); // ACTIVE: prints the key here
```
Root is printed only after both subtrees are fully visited, giving left-right-root order.

The traversal skeleton never changes. The three hooks are always called in the same fixed positions. What changes is which hook fires. This is the core idea of the Visitor Pattern: the structure drives the walk, the visitor decides what happens at each point.

#### BFS Implementation
![[Pasted image 20260601224112.png]]
BFS is handled separately by `performBreadthFirstSearch`. It maintains a `std::queue<const BTree*>` of raw pointers. Raw pointers are appropriate here because the queue does not own the nodes; it only observes them. The tree's `unique_ptr` members retain ownership throughout:
```cpp
void performBreadthFirstSearch( const TreeVisitor<T>& aVisitor ) const noexcept
{
    std::queue<const BTree*> lQueue;
    lQueue.push( this );
    while ( !lQueue.empty() )
    {
        const BTree& lFront = *lQueue.front(); // lvalue reference to front
        lQueue.pop();
        aVisitor.visit( *lFront );
        if ( lFront.hasLeft() )  { lQueue.push( &lFront.left() ); }
        if ( lFront.hasRight() ) { lQueue.push( &lFront.right() ); }
    }
}
```
The pattern is: dequeue the front, visit it, then enqueue its children. The result is level-order traversal.

### Iterator-Based Traversal
Iterators convert traversal into a linear sequence compatible with C++20 range semantics. Both iterator classes expose `operator*`, `operator++`, `operator==`, `begin`, and `end`, along with the required type aliases `difference_type` and `value_type`.

**`DepthFirstTraversal<T>`** holds a raw root pointer (`fRoot`), a `std::stack<Frontier<pointer>>` (`fStack`), and a `Mode` enum. A single class handles all three orderings by switching behaviour based on mode at both initialisation and increment time. The default mode is `PRE_ORDER`:
```cpp
class DepthFirstTraversal
{
public:
	// type alias
    using pointer        = const BTree<T>*;
    using frontier       = DepthFirstTraversal<pointer>;
    using iterator       = DepthFirstTraversal<T>;
    using node           = typename BTree<T>::node;
    using difference_type = std::ptrdiff_t;   // satisfies weakly_incrementable
    using value_type     = T;                 // satisfies indirectly_readable
 
    enum class Mode { PRE_ORDER, POST_ORDER, IN_ORDER };
 
    DepthFirstTraversal( const node& aBTree, Mode aMode = Mode::PRE_ORDER );
 
    const T& operator*() const noexcept;
 
    iterator& operator++();
    iterator  operator++( int );
 
    bool operator==( const iterator& aOther ) const noexcept;
 
    iterator begin() const noexcept;
    iterator end()   const noexcept;
 
private:
    pointer                fRoot;
    std::stack<frontier>   fStack;
    Mode                   fMode;
 
    void pushNode( pointer aNode ) noexcept;
};

```

**`BreadthFirstTraversal<T>`** holds a raw root pointer (`fRoot`) and a `std::queue<pointer>` (`fQueue`). On construction, the root is pushed into the queue. `operator*` dereferences the queue's front to return the current key. `operator++` dequeues the front and enqueues its children if they exist. `end()` returns an iterator whose queue is empty:
```cpp
template<typename T>
class BreadthFirstTraversal
{
public:
	// type alias
    using pointer         = const BTree<T>*;
    using iterator        = BreadthFirstTraversal<T>;
    using node            = typename BTree<T>::Node;
    using difference_type = std::ptrdiff_t;   // satisfies weakly_incrementable
    using value_type      = T;                // satisfies indirectly_readable
 
    BreadthFirstTraversal( const node& aBTree );
 
    const T& operator*() const noexcept;
 
    iterator& operator++();
    iterator  operator++( int );
 
    bool operator==( const iterator& aOther ) const noexcept;
 
    iterator begin() const noexcept;
    iterator end()   const noexcept;
 
private:
    pointer                fRoot;
    std::queue<pointer>    fQueue;
};

```

### The Frontier Abstraction
A **Frontier** is a small struct/auxiliary type pairing a node pointer with a boolean flag `mustExploreRight`. It is the key data structure enabling post-order traversal via a stack.
```cpp
template<typename T>
struct Frontier
{
    bool mustExploreRight;
    T node;

    Frontier( T aNode = nullptr ) :
        mustExploreRight(true), node(aNode) {}

    bool operator==( const Frontier& aOther ) const noexcept
    {
        return mustExploreRight == aOther.mustExploreRight &&
               node == aOther.node;
    }
};
```
In post-order traversal, a node must remain on the stack after its left subtree is processed so that its right subtree can still be explored before the node is finally yielded. Without the `mustExploreRight` flag, you cannot distinguish between "this node is waiting for its right subtree" and "this node is ready to be yielded." Pre-order and in-order do not require this flag because nodes are yielded or discarded before the right branch is pushed.

### Stack-Based Iterator Steps
Understanding how each traversal maps to stack operations is essential for implementing `operator++`.

![[Pasted image 20260602174846.png]]
**Pre-order steps:**
- Initialization: push the root onto the stack.
- Element access: the current element is the top of the stack.
- Move forward: hold the top, pop it. Push its right child if it has one, then push its left child if it has one. Right is pushed first so left is on top and processed first.

![[Pasted image 20260602212918.png]]
**In-order steps:**
- Initialization: follow the leftmost path from the root, pushing every node onto the stack until there are no more left children.
- Element access: the current element is the top of the stack.
- Move forward: hold the top, pop it. If it has a right child, push the right child, then push all left descendants of that right child.

![[Pasted image 20260602212943.png]]
**Post-order steps:**
- Initialization: push all left nodes starting from the root. When the leftmost node is reached, clear its `mustExploreRight` flag. If it has a right child, push that right child and all of its left descendants with `mustExploreRight = true`.
- Element access: the current element is the top of the stack.
- Move forward: pop the top. If the stack is not empty and the new top still has `mustExploreRight = true`, clear it, and if the node has a right child push it along with all its left descendants.

The post-order algorithm ensures that both subtrees are fully processed before a node is yielded, which is why nodes remain on the stack longer and the flag is needed to track exploration state.

### The pushNode Method
The private helper `pushNode(pointer aNode)` abstracts the "push all left nodes from a given starting point" operation. It doubles as the initialisation routine and is shared across all three modes. For post-order it handles the additional right-branch exploration when the leftward path ends:
```cpp
void pushNode( pointer aNode ) noexcept
{
    while ( aNode )
    {
        fStack.push( aNode );
        if ( aNode->hasLeft() )
        {
            aNode = &aNode->left();
        }
        else
        {
            if ( fMode == Mode::POST_ORDER )
            {
                fStack.top().mustExploreRight = false;
                if ( aNode->hasRight() )
                {
                    aNode = &aNode->right();
                    continue; // restart loop with right child
                }
            }
            aNode = nullptr;
        }
    }
}
```
The `continue` statement restarts the while loop with the right child as the new current node, which then gets pushed and its own left descendants explored. This is what produces the fully initialised post-order stack state before the first element is accessed.

### Complete Iterator Usage
The four traversal modes used together on the same tree:
```cpp
// Breadth-first: D E F H M G J N I K L
for ( auto& s : BreadthFirstTraversal<std::string>(aBTree) )
    std::cout << ' ' << s;

// DFS Pre-order: D E H N M F G I J K L
for ( auto& s : DepthFirstTraversal<std::string>(aBTree) )
    std::cout << ' ' << s;

// DFS Post-order: N H M E I G K L J F D
for ( auto& s : DepthFirstTraversal<std::string>(aBTree,
    DepthFirstTraversal<std::string>::Mode::POST_ORDER) )
    std::cout << ' ' << s;

// DFS In-order: H N E M D I G F K J L
for ( auto& s : DepthFirstTraversal<std::string>(aBTree,
    DepthFirstTraversal<std::string>::Mode::IN_ORDER) )
    std::cout << ' ' << s;
```

### M-Way Search Trees
A **binary search tree (BST)** is the special case of a 2-way search tree. The general form is the **M-way (M-ary) search tree**: each node holds up to M-1 keys and exactly M subtrees (some of which may be empty). Keys within a node are distinct and ordered. For any key $k_i$ in a node, all keys in subtree $T_{i-1}$ (its left subtree) are less than $k_i$, and all keys in subtree $T_i$ (its right subtree) are greater than $k_i$.

Formally, an M-ary search tree T is either empty ($T = \emptyset$), or for $2 \leq n \leq M$ it consists of n M-ary subtrees $T_1, T_2, \ldots, T_n$ and n-1 keys $k_1, k_2, \ldots, k_{n-1}$ satisfying $k_i < k_{i+1}$ and the ordering properties above.

![[Pasted image 20260603165549.png]]
A **4-way search tree** has up to 3 keys per node and up to 4 subtree slots. This structure is the precursor to B-trees, which are the foundation of most database index implementations.

### Binary Search Tree Definition
![[Pasted image 20260603165628.png]]
A **2-ary (binary) search tree** T is either empty, or consists of one key r and exactly two binary subtrees $T_L$ and $T_R$ such that:
$$\forall k \in T_L: k < r \qquad \forall k \in T_R: k > r$$
In-order traversal of a BST always yields keys in sorted ascending order. This is why in-order is the natural traversal for search trees.

### BST Operations
![[Pasted image 20260603165724.png]]
The `BTree<T>` class used as a BST exposes `findMax`, `findMin`, `insert`, and `remove`, all implemented on the root node and operating recursively or iteratively.

**findMax** follows right children until there is no right child, then returns the current key. Since larger values are always in the right subtree, the rightmost node is the maximum:
```cpp
const T& findMax() const noexcept
{
    if ( hasRight() ) { return right().findMax(); }
    return **this; // dereferences unique_ptr then operator*() for key
}
```
**findMin** follows left children symmetrically.

![[Pasted image 20260603165733.png]]
**insert** performs an iterative BST search using two pointers: `x` (the current node) and `y` (the parent). The loop descends left or right depending on key comparison, stopping when `x` is null. `y` is then the insertion parent. A new leaf is created and moved into the correct child slot. Duplicate keys return false immediately:
```cpp
bool insert( const T& aKey ) noexcept
{
    BTree* x = this;
    BTree* y = nullptr;
    while ( x )
    {
        y = x;
        if ( aKey == **x ) { return false; } // duplicate key
        x = (aKey < **x ? x->fLeft.get() : x->fRight.get());
    }
    if ( !y ) { return false; }
    node z = BTree::makeNode( aKey );
    if ( aKey < **y ) { y->fLeft  = std::move( z ); }
    else              { y->fRight = std::move( z ); }
    return true;
}
```

![[Pasted image 20260603165841.png]]
**remove** is the most complex BST operation. It uses the same iterative search to find the target node `x` and its parent `y`. Once found, three cases are handled:
- If x has a left child: copy the maximum of x's left subtree into x's key, then recursively remove that maximum from the left subtree. The predecessor (left-subtree maximum) is chosen because it is the largest value still smaller than all right-subtree values, preserving the BST invariant.
- Else if x has a right child: copy the minimum of x's right subtree into x's key, then recursively remove that minimum (successor strategy).
- Else (x is a leaf): set the parent's matching child pointer to nullptr, which destroys the node via `unique_ptr`.
```cpp
bool remove( const T& aKey, BTree* aParent = nullptr ) noexcept
{
    BTree* x = this;
    BTree* y = aParent;
    while ( x )
    {
        if ( aKey == **x ) { break; }
        y = x;
        x = (aKey < **x ? x->fLeft.get() : x->fRight.get());
    }
    if ( !x ) { return false; }
    if ( x->hasLeft() )
    {
        x->fKey = x->left().findMax();     // replace with in-order predecessor
        x->left().remove( **x, x );
    }
    else if ( x->hasRight() )
    {
        x->fKey = x->right().findMin();    // replace with in-order successor
        x->right().remove( **x, x );
    }
    else
    {
        if ( y->fLeft.get() == x )  { y->fLeft  = nullptr; }
        else                         { y->fRight = nullptr; }
    }
    return true;
}
```

### Balanced Tree Variants
An unbalanced BST degrades to O(n) height in the worst case (inserting keys in sorted order produces a linked-list shaped tree). Two self-balancing variants bound the height automatically.

**AVL trees** (Adelson-Velskii and Landis) enforce strict balance: the heights of any node's two subtrees differ by at most one. Height is bounded by $1.44\log_2 n$. Stricter balance means faster lookups but more rotations required on insertion and deletion.

**Red-Black trees** use a colouring invariant (each node is red or black, with constraints on colour sequences along any root-to-leaf path) to bound height at $2\log_2 n$. Less strict than AVL, so insertions and deletions require fewer rebalancing operations, but lookups are marginally slower. Java's `TreeMap` and C++'s `std::map` and `std::set` typically use Red-Black trees.

|Property|AVL Tree|Red-Black Tree|
|---|---|---|
|Height bound|$1.44\log_2 n$|$2\log_2 n$|
|Lookup speed|Faster|Slightly slower|
|Insert / remove|Slower (more rotations)|Faster|
|Balancing strictness|Strict|Relaxed|
|Common use|Read-heavy workloads|General-purpose maps and sets|

Other notable tree variants include **Rose Trees** (used in file systems with variable-arity nodes), **Expression Trees** (used in compilers for internal program representation), **Multi-rooted trees** (used in C++ multiple inheritance hierarchies), and **B-trees** (generalised M-way search trees used in database indices and filesystems).

### Key Takeaways
1. DFS uses a stack and has three orderings (pre, in, post) determined by when the root is visited relative to its subtrees; BFS uses a queue and visits nodes level by level.
2. The Visitor Pattern decouples traversal logic from node operations: the tree drives the recursive walk, and the visitor decides what to do at each hook point.
3. Iterator-based traversal externalises the stack or queue into the iterator object, converting a recursive walk into a linear sequence compatible with C++20 range semantics.
4. The Frontier abstraction is required for post-order iterators because a node must remain on the stack while its right subtree is still being explored, and `mustExploreRight` tracks whether that exploration has happened.
5. In-order traversal of a BST yields sorted order; deletion replaces an interior node's key with its in-order predecessor or successor to preserve the BST invariant.
6. AVL and Red-Black trees are both self-balancing BSTs; AVL is stricter and faster at lookup, Red-Black is more flexible and faster at mutation.

**Remember this:** the shape of the traversal follows the shape of the auxiliary structure; a stack goes deep before wide (DFS), and a queue goes wide before deep (BFS).

---

## Part 4: TernaryTree - Exam Pattern (2022 & 2024)

### What is TernaryTree?
A **TernaryTree** is a 3-ary tree where every node has exactly three subtree slots (`[0]=left`, `[1]=middle`, `[2]=right`). It appeared with an **identical structure** in the 2022 and 2024 COS30008 final exams, making it the highest-priority topic.

The critical difference from `NTree<T,3>` in Lab 12: TernaryTree uses **raw pointers + a static NIL sentinel**, not `unique_ptr`. Every empty subtree slot points to `&NIL` rather than `nullptr`.

### Full Class Skeleton
```cpp
template<typename T>
class TernaryTree
{
public:
    using TTree    = TernaryTree<T>;
    using TSubTree = TTree*;               // raw pointer - NOT unique_ptr

    static TTree NIL;                      // one shared sentinel per type T

    TernaryTree();                         // default: all 3 slots → &NIL
    TernaryTree( const T& aKey );          // copy-key constructor
    TernaryTree( T&& aKey );               // move-key constructor
    ~TernaryTree();                        // must NOT delete NIL slots

    bool     empty()    const;             // is this == &NIL ?
    bool     leaf()     const;             // not empty AND all 3 slots == &NIL
    size_t   height()   const;             // throws if empty
    const T& operator*() const;            // throws domain_error if empty

    const TTree& operator[]( size_t i ) const;
          TTree& operator[]( size_t i );

    void    attachSubTree( size_t i, TTree& aTree );
    TTree&  removeSubTree( size_t i );

    TTree*  clone() const;                 // deep copy, returns heap pointer

    TernaryTree( const TTree& aOther );
    TTree& operator=( const TTree& aOther );
    TernaryTree( TTree&& aOther ) noexcept;
    TTree& operator=( TTree&& aOther ) noexcept;

private:
    T        fKey;
    TSubTree fSubTrees[3];                 // [0]=left  [1]=middle  [2]=right
};

// Static NIL definition (goes in .cpp or at the bottom of the header):
template<typename T>
TernaryTree<T> TernaryTree<T>::NIL;
```

### Key Invariants - Memorise These

| Rule | Detail |
|---|---|
| `empty()` | `return this == &NIL;` - pointer comparison, NOT key check |
| `leaf()` | `!empty()` AND all 3 `fSubTrees[i]->empty()` |
| Default ctor | sets `fSubTrees[0] = fSubTrees[1] = fSubTrees[2] = &NIL` |
| Destructor | only `delete fSubTrees[i]` if `!fSubTrees[i]->empty()` |
| **GOLDEN RULE** | **NEVER delete &NIL** - it is a shared static object |
| `operator[]` | assert `i < 3`, return `*fSubTrees[i]` |
| `clone()` | if leaf → `new TTree(fKey)`, else copy key + recurse subtrees |
| `removeSubTree` | grab slot ptr, reset slot to `&NIL`, return `*old` |
| move ctor | steal `fKey` + all 3 `fSubTrees[]`, set source slots to `&NIL` |

### Method Implementations

#### empty() and leaf()
```cpp
template<typename T>
bool TernaryTree<T>::empty() const
{
    return this == &NIL;
}

template<typename T>
bool TernaryTree<T>::leaf() const
{
    for ( size_t i = 0; i < 3; i++ )
        if ( !fSubTrees[i]->empty() )
            return false;
    return !empty();
}
```

#### operator*() and height()
```cpp
template<typename T>
const T& TernaryTree<T>::operator*() const
{
    if ( empty() )
        throw std::domain_error("Operation not supported");
    return fKey;
}

template<typename T>
size_t TernaryTree<T>::height() const
{
    if ( empty() )
        throw std::domain_error("Operation not supported");

    size_t lHeight = 0;

    for ( size_t i = 0; i < 3; i++ )
    {
        if ( !fSubTrees[i]->empty() )
            lHeight = std::max( lHeight, fSubTrees[i]->height() + 1 );
    }

    return lHeight;
}
```

#### Constructor and Destructor
```cpp
template<typename T>
TernaryTree<T>::TernaryTree()
{
    fSubTrees[0] = fSubTrees[1] = fSubTrees[2] = &NIL;
}

template<typename T>
TernaryTree<T>::TernaryTree( const T& aKey ) : fKey(aKey)
{
    fSubTrees[0] = fSubTrees[1] = fSubTrees[2] = &NIL;
}

template<typename T>
TernaryTree<T>::~TernaryTree()
{
    for ( size_t i = 0; i < 3; i++ )
        if ( !fSubTrees[i]->empty() )   // guard: never delete &NIL
            delete fSubTrees[i];
}
```

#### attachSubTree() and removeSubTree()
```cpp
template<typename T>
void TernaryTree<T>::attachSubTree( size_t i, TTree& aTree )
{
    if ( !fSubTrees[i]->empty() )
        delete fSubTrees[i];            // free existing subtree first
    fSubTrees[i] = &aTree;
}

template<typename T>
TernaryTree<T>& TernaryTree<T>::removeSubTree( size_t i )
{
    TTree& lResult = *fSubTrees[i];
    fSubTrees[i] = &NIL;                // reset slot to sentinel
    return lResult;
}
```

#### clone() - Deep Copy
```cpp
template<typename T>
TernaryTree<T>* TernaryTree<T>::clone() const
{
    if ( empty() )
        return &NIL;

    TTree* lClone = new TTree( fKey );

    for ( size_t i = 0; i < 3; i++ )
        if ( !fSubTrees[i]->empty() )
            lClone->fSubTrees[i] = fSubTrees[i]->clone();

    return lClone;
}
```

#### Copy Semantics
```cpp
template<typename T>
TernaryTree<T>::TernaryTree( const TTree& aOther ) : fKey(aOther.fKey)
{
    for ( size_t i = 0; i < 3; i++ )
        fSubTrees[i] = aOther.fSubTrees[i]->empty()
            ? &NIL
            : aOther.fSubTrees[i]->clone();
}

template<typename T>
TernaryTree<T>& TernaryTree<T>::operator=( const TTree& aOther )
{
    if ( this != &aOther )
    {
        this->~TernaryTree();
        new (this) TernaryTree( aOther );
    }
    return *this;
}
```

#### Move Semantics
```cpp
template<typename T>
TernaryTree<T>::TernaryTree( TTree&& aOther ) noexcept : fKey(std::move(aOther.fKey))
{
    for ( size_t i = 0; i < 3; i++ )
    {
        fSubTrees[i] = aOther.fSubTrees[i];   // steal pointer
        aOther.fSubTrees[i] = &NIL;            // leave source empty
    }
}

template<typename T>
TernaryTree<T>& TernaryTree<T>::operator=( TTree&& aOther ) noexcept
{
    if ( this != &aOther )
    {
        this->~TernaryTree();
        new (this) TernaryTree( std::move(aOther) );
    }
    return *this;
}
```

### Prefix Iterator (Stack-Based Pre-Order)
The iterator walks the tree in pre-order (root → left → middle → right) using a `std::stack` of raw pointers. Push order is **right → middle → left** so that left is on top and visited first.

```cpp
template<typename T>
class TernaryTreePrefixIterator
{
public:
    using TTree    = TernaryTree<T>;
    using iterator = TernaryTreePrefixIterator<T>;

    TernaryTreePrefixIterator( const TTree* aTree )
    {
        if ( !aTree->empty() )
            fStack.push( aTree );
    }

    const T& operator*() const
    {
        return **fStack.top();              // operator*() on TTree
    }

    iterator& operator++()
    {
        const TTree* lTop = fStack.top();
        fStack.pop();
        // push right→middle→left so left comes off first
        for ( int i = 2; i >= 0; i-- )
            if ( !(*lTop)[i].empty() )
                fStack.push( &(*lTop)[i] );
        return *this;
    }

    bool operator==( const iterator& aOther ) const
    {
        return fStack == aOther.fStack;
    }

    iterator begin() const
    {
        return *this;                       // copy of this (already at start)
    }

    iterator end() const
    {
        return TernaryTreePrefixIterator( &TTree::NIL );  // empty stack
    }

private:
    std::stack<const TTree*> fStack;
};
```

### Raw Pointer vs unique_ptr Comparison

| Property | Lab 12 NTree/BTree | Exam TernaryTree |
|---|---|---|
| Subtree pointer type | `unique_ptr<NTree>` | `TTree*` (raw) |
| Empty test | `ptr == nullptr` | `this == &NIL` |
| Memory cleanup | RAII automatic | destructor must guard `&NIL` |
| Subtree array | `fNodes[N]` unique_ptr | `fSubTrees[3]` raw ptr |
| Structural ops | `attach` / `detach` | `attachSubTree` / `removeSubTree` |
| Sentinel | none (nullptr) | `static TTree NIL` |

### Key Takeaways
1. `empty()` is always a pointer comparison: `this == &NIL`, never a key check.
2. The destructor must guard every slot: only `delete fSubTrees[i]` when `!fSubTrees[i]->empty()`.
3. `removeSubTree` resets the slot to `&NIL` - not `nullptr` - and returns the old subtree by reference.
4. The prefix iterator pushes subtrees right→middle→left so the left child is popped (visited) first.
5. `clone()` is the building block for the copy constructor - call it recursively on each non-empty subtree.

**Remember this:** every empty slot must point to `&NIL`, not `nullptr`. The moment you use `nullptr` instead of `&NIL` as a sentinel, `->empty()` becomes a null dereference and the program crashes.

**One analogy:** NIL is like a dead-end sign at the edge of a road network. Every road that leads nowhere points to the same sign. You never demolish the sign itself - you just stop driving when you see it.

**One analogy to remember it by:** pre-order is like a tour guide who announces a city before you enter it; in-order is like one who speaks while you stand in the city square; post-order is like one who summarises only after you leave. BFS is like a guide who photographs every building on one street before moving to the next.