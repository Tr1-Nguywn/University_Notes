## Part 1: C++ Foundations

### What is C++?
**C++** is a compiled, statically-typed, multi-paradigm programming language that exposes low-level details such as memory layout, performance tradeoffs, and design decisions that most other languages abstract away.

In simple terms: C++ gives you direct control over how the computer works. You decide how data is stored, how it moves, and when it gets destroyed. Nothing happens behind your back.

Think of it like: driving a manual car versus an automatic. Python and Java are automatic; the engine manages itself. C++ is manual. You control the gears, the clutch, and the engine directly. More work, but nothing is hidden.

Why it matters: understanding C++ forces you to understand what every other language is quietly doing for you. It is the foundation language for systems programming, game engines, embedded software, and high-performance applications.

### The C++ Programming Model
C++ was developed by Bjarne Stroustrup at Bell Labs in 1983, initially as "C with Classes." This unit uses **C++20**, which adds concepts, expands `constexpr`, and eliminates the need for `typename` in certain circumstances.

C++ is **compiled**: source code is translated into machine code before it runs. Types are checked at compile time, not at runtime. Type errors are caught early and the resulting binary runs without interpreter overhead.

C++ is **multi-paradigm**: it directly supports multiple programming styles, and non-trivial programs mix all of them depending on what fits the problem locally.
- **Procedural:** a sequence of explicit instructions. The oldest style, aligned with the von Neumann architecture, and the foundation of most C++ code at the function level.
- **Object-oriented:** data and behavior bundled into classes. Objects communicate via methods; inheritance allows new classes to extend existing ones.
- **Functional:** computation as the application and composition of functions, supported through lambdas and higher-order functions (takes one or more callback functions and returns a function as its result). Side effects (any observable change outside a function's own scope) are minimized.
- **Generic:** templates write algorithms and data structures that work for any type. The compiler generates the concrete version per type; no runtime overhead, no code duplication.

**Namespaces** group names to prevent clashes. `std::` is the prefix for everything in the C++ standard library. Argument-dependent lookup (ADL) enables `<<` and `>>` to resolve to their `std::` definitions without explicit qualification.

### lvalues and rvalues
Every C++ expression has a **type** and a **value category**:
- An **lvalue** (locator value) is an expression that refers to a named, addressable location in memory. You can take its address with `&`. Variable names, array subscripts, and dereferenced pointers are lvalues.
- An **rvalue** (right values) is everything else: literals, results of most operators, and function calls that return non-references. No persistent storage, and cannot appear on the left side of an assignment.
```cpp
int score;
score = 42;  // score is an lvalue (has address); 42 is an rvalue (just a value)
```

### lvalue References
A **reference** is an alias for an existing object. It shares the same memory location; no copy is made.
**Example:**
```cpp
int blockSize = 512;
int& bufferSize = blockSize;             // another name for the same memory location
const int& fixedBufferSize = blockSize;  // same location, read-only through this alias
```

`const` restricts access through that alias, not the original variable. The `&` symbol has two distinct meanings depending on context:
```cpp
int& bufferSize = blockSize;  // preceded by a type: reference declaration
&blockSize;                   // not preceded by a type: address-of operator
```

|Mechanism|Syntax|Copies?|Caller's variable changed?|Use when|
|---|---|---|---|---|
|By value|`int aScore`|Yes|No|Small, cheap-to-copy types|
|By reference|`int& aScore`|No|Yes|Need to modify caller's variable|
|By const reference|`const int& aScore`|No|No|Large objects, read-only|

### const and constexpr
Magic numbers in code have no readable meaning. Named constants fix this. C++ offers three mechanisms, each with different tradeoffs:
```cpp
#define PI 3.14159              // text substitution before compilation; no type, no address
const int maxPlayers = getMaxPlayers();  // fixed after initialization; can be a runtime value
constexpr int screenWidth = 1920;        // must be known at compile time; baked into the binary
```
- `#define` is a preprocessor directive. Before* compilation even begins, the preprocessor does a find-and-replace: every occurrence of `PI` is replaced with `3.14159`. It has no type, no memory address, and no scope. The compiler never sees the name, only the substituted value, which makes debugging harder and errors less readable.
- `const` is a proper C++ variable. It has a type, a memory address, and obeys scope rules. It fixes a value *after initialization*, meaning the value can come from a runtime computation like a function call. Once set, it cannot be changed.
- `constexpr` goes further: the value must be fully computable at compile time and is baked directly into the binary. All `constexpr` variables are implicitly `const`, but not all `const` variables are `constexpr`. Use `constexpr` when the value is truly fixed and *known before* the program runs.

**Example:**
![[Pasted image 20260326024737.png]]
**Constant member functions** append `const` to a method signature, making the function read-only with respect to the object it belongs to.

Every non-static member function receives a hidden, implicit `this` pointer - the compiler internally passes the address of the calling object as a hidden first argument. Inside the function body, `this` points to that specific object instance, which is how member functions know which object's data they should operate on.

Without `const`, `this` is `MyClass*` and the function can freely modify fields. With `const`, `this` becomes `const MyClass* const`, meaning all fields of that particular object instance are treated as read-only for the duration of the call - any attempt to modify them becomes a compile error.

It also allows the function to be called on `const` objects, which would otherwise be rejected by the compiler:
```cpp
float x() const noexcept { return fXCoord; }  // reads fXCoord, cannot change it
```
Rule of thumb: everything that should or must not change is marked `const`. In short:

| Keyword     | Evaluated at            | Use when                                  |
| ----------- | ----------------------- | ----------------------------------------- |
| `const`     | Runtime or compile time | Value may only be known at runtime        |
| `constexpr` | Compile time only       | Value is fixed and known before execution |
**EVERYTHING THAT SHOULD OR MUST NOT CHANGE IS MARKED WITH THE CONST KEYWORD.**
### noexcept
`noexcept` is a promise to the compiler that a function will never throw an exception. The compiler skips generating exception-handling paths, producing leaner and faster code.
```cpp
float x() const noexcept { return fXCoord; }
```
If a `noexcept` function does throw despite the promise, `std::terminate()` is called immediately. Apply only where exceptions genuinely cannot or should not occur; default constructors, destructors, move operations, and swap functions should never throw.

### Object Construction
**Constructors** are special member functions that initialize an object. C++ offers several forms.

A **default constructor** takes no arguments, or all arguments have defaults. The compiler synthesizes one if you define none, but do not rely on it for `float` or `int` fields as they will contain garbage values.

A **constructor with initializer list** initializes fields directly before the body runs. This is preferred over assigning inside `{}` because it initializes in one step rather than default-constructing then overwriting:
```cpp
Vector2D::Vector2D( float aXCoord, float aYCoord ) noexcept :
    fXCoord(aXCoord),  // fXCoord initialized directly
    fYCoord(aYCoord)   // fYCoord initialized directly
{}
```
- Vector2D:: This indicates the scope, meaning we are looking inside the class or namespace named Vector2D.
- Vector2D (second one): This is the constructor function, which has the same name as the class. It is responsible for initializing new instances of the Vector2D class.

A **conversion constructor** is a single-argument constructor that implicitly defines a conversion from that parameter type to the class type. Use `explicit` to require manual conversion and prevent silent mistakes:
```cpp
// Without explicit: silent conversion allowed
Vector2D( std::istream& aInput ) : Vector2D() { aInput >> *this; }

// With explicit: conversion must be written manually; compile error otherwise
explicit Particle2D( float aMass = 0.0f, float aRadius = 10.0f, ... ) noexcept;
```

### Operator Overloading
Operator overloading means giving a new meaning to an operator (e.g. +, -, , []) when it is used with objects. 
**Syntax**: `return_type operator op (parameters) { ... }`

The following operators cannot be overloaded in C++:
1. `sizeof`
2. `typeid`
3. Scope resolution (`::`)
4. Class member access operators (. and .)
5. Ternary / conditional operator (?:)

A **member operator** treats the left-hand operand implicitly as `this`. Only the right-hand argument is declared explicitly:
```cpp
Vector2D Vector2D::operator*( const float aScale ) const noexcept
{
    return Vector2D( x() * aScale, y() * aScale );
}
// v * 2.0f  =>  v.operator*(2.0f)
```

An **ad hoc (non-member) operator** is a free function required when the left-hand side is not of the class type.
Commutativity is not automatic in C++; both directions must be defined manually:
```cpp
Vector2D operator*( const float aScale, const Vector2D& aVec ) noexcept
{
    return aVec * aScale;  // delegates to the member operator with sides flipped
}
// 2.0f * v  now works; without this it is a compile error
```

`friend` grants a free function access to private members. The standard idiom for stream I/O operators:
```cpp
friend std::istream& operator>>( std::istream& aInput, Vector2D& aVec );
friend std::ostream& operator<<( std::ostream& aOutput, const Vector2D& aVec );
```

|Criterion|Member Operator|Ad Hoc Operator|
|---|---|---|
|Left-hand access|Implicit `this`|Explicitly specified|
|Private member access|Direct|Requires `friend`|
|LHS constraint|Must be class type|Can be any type|
|Typical use|`v * scalar`|`scalar * v`|

### Imperative vs OOP
Both styles are valid and are routinely mixed in real C++ programs. The insertion sort example shows the same algorithm in both:

|Criterion|Imperative Function|OOP Class|
|---|---|---|
|Data ownership|Caller|Object|
|State across calls|No|Yes|
|Reusability|Lower|Higher|
|Best used when|Stateless, one-off algorithm|State must be managed over time|

### Code Organization and Conventions

|Location|Purpose|
|---|---|
|`*.h` / `*.hpp`|Class declarations, function prototypes, inline functions|
|`*.cpp`|Definitions; the actual implementation code|
|Header only|Small inline functions; all template classes (no `.cpp` allowed)|

`#pragma once` at the top of every header prevents duplicate inclusion during compilation.

COS30008 uses a Borland-inspired naming convention:

| Prefix        | Meaning                    | Example                   |
| ------------- | -------------------------- | ------------------------- |
| `f`           | Field (class member)       | `fNumbers`                |
| `l`           | Local variable             | `lIndex`                  |
| `a`           | Parameter (argument)       | `aOutput`                 |
| `g`           | Global variable            | `gConfig`                 |
| `i`, `j`, `k` | Loop variables (exception) | `for (size_t i = 0; ...)` |

Indentation style: opening brace on the next line aligned with the control statement; closing brace aligns with opening brace.

### Key Takeaways
1. C++ exposes what other languages hide; memory, performance, and design decisions are all visible and intentional.
2. Mark everything that should not change with `const`; this communicates intent to the compiler and to other developers.
3. Constructor initializer lists are preferred over body assignment; they initialize fields directly rather than default-constructing then overwriting.
4. Commutativity is not free; `v * 3.0f` and `3.0f * v` are two different operator calls requiring separate definitions.
5. `friend` grants precise, declared exceptions to encapsulation; it is not object-oriented but is a deliberate and standard C++ idiom.

**Remember this:** In C++, nothing happens by accident. Every object is initialized by a constructor, every copy is deliberate, every mutation is visible; that control is exactly the point.

**One analogy to remember it by:** C++ is a manual car. You control the gears, the clutch, and the engine. It demands more from you, but nothing is hidden; and understanding it tells you what every automatic car is doing underneath.

---

## Part 2: Basic Concepts I

### What is a Programming Paradigm?
A **programming paradigm** is a fundamental style for structuring and expressing computation. It shapes how a programmer breaks down a problem and writes a solution; essentially, it is the lens you use to decide what a program is made of and how its pieces fit together.

In simple terms: a paradigm is not a language feature but a way of thinking about problems before writing any code.

Think of it like: architectural styles. A brutalist architect works with raw concrete blocks stacked precisely (imperative); a modular builder snaps pre-built rooms together (object-oriented); a client who hands an architect a requirements document and says "make it work" (declarative/logic).

Why it matters: different paradigms suit different problems. Knowing them lets you pick the right approach and makes your design intent readable to others.

### The Four Core Paradigms

|Paradigm|Formula|Key Idea|
|---|---|---|
|Imperative|program = algorithms + data|Describe **how** to do something, step by step|
|Functional|program = function composed with function|Computation is the application of **pure functions**|
|Logic|program = facts + rules|Describe **what is true**; the system derives answers|
|Object-Oriented|program = objects + messages|Data and behavior **bundled into objects** that communicate|

Other paradigms exist: blackboard, event-driven, pipes and filters, constraint-based, list-based. In any non-trivial C++ project, the imperative and object-oriented paradigms must be mixed.

**Imperative programming** is one of the oldest styles. The algorithm for computation is clearly expressed through a series of instructions including assignments, tests, and conditional branching. This style aligns naturally with the von Neumann architecture.

**Object-oriented programming** is based on the concepts of objects and classes, comparable to variables and types in Pascal and C. Instead of applying global procedures to variables, we invoke methods on instances, a process known as "message passing." The core concept of **inheritance** allows creating new classes from existing ones by modifying or extending them.

### Object-Oriented Software Development
**OOP** is both a programming style and a software development methodology where a program is modeled as a collection of interacting objects. It is described as an evolutionary step that refines earlier imperative techniques and a revolutionary idea in how we model reality in software.

Why OOP is popular: it naturally captures real life, scales well from trivial to complex tasks, and focuses on responsibilities, reuse, and composition.

### Concrete vs Abstract
OOP design moves along two axes: the application domain and the solution domain.

The **application domain** is the real world the software is trying to model; the problem space. It is described in the language of the user, not the programmer. You think in terms of entities and types: "we have employees, departments, salaries." No code yet, just understanding what exists and what matters.

The **solution domain** is the code world; how you implement the solution in a programming language. Entities become objects (runtime instances in memory) and types become classes (blueprints in code).

**Domain analysis** is the crossing point: the process of translating the application domain into the solution domain. You study the real-world problem, identify its key entities and relationships, then decide how to represent them as classes and objects in code.

**Entities** are the real, specific things you observe in the world: a particular employee named John, a specific bank account with balance $500. **Types** are the abstraction of those entities: "Employee" and "BankAccount" as general concepts, stripped of any specific instance. Similarly, **objects** are concrete runtime instances living in memory, while **classes** are their abstract blueprint, defining structure and behavior without being any one specific thing.

### The Visitor Pattern
A naive design might say "Shape has a `draw()` method." But shapes cannot draw themselves; drawing depends on the rendering context.

The **Visitor Pattern** resolves this: the `Shape` knows its geometry; a `Renderer` knows how to render each concrete type. Each shape accepts a `Renderer` and delegates the rendering:
```
Shape.draw(Renderer) --delegates--> Renderer.render(Rectangle)
                                    Renderer.render(Circle)
                                    ...
```

This is a real design problem that shows why naive OOP design can fail and why patterns matter.

### Values
In computer science, a **value** is anything that can be evaluated, stored in a data structure, passed as an argument, or returned from a function.

**Constants** are named abstractions of values that assign user-defined meaning. They have no address and are substituted at compile time:
```cpp
const int EOF = -1;
const double PI = 3.1415927;
```

**Primitive values** cannot be further decomposed. They are the atoms of a type system. Examples: truth values, integers, characters, real numbers, strings, enumerators. Some are implementation and platform-dependent (e.g. size of `int`).

**Composite values** are built from primitives and other composites. Layout is generally implementation-dependent:
```cpp
struct Point { int x; int y; } gPoint = { 1, 2 };  // record
enum class Color { Red, Blue, Green };               // enumeration
```

Other composite types: arrays, sets, lists, tuples, files, maps.

**Pointers** store the address (location in memory) of another value. C++ allows obtaining the address of any lvalue and function. A pointer to a function was the only way to pass a callable as an argument before lambda expressions.

Key pointer operations: `&x` (take address of x), `*ptr` (dereference pointer), `ptr++` (pointer arithmetic).

### Sets
A **set** is an unordered collection of elements satisfying some characterizing property P(x):
$${ x \mid P(x) = \text{True} }$$

The foundational axiom of set theory: there exists an empty set $\emptyset$ where no element belongs. Formally, $\forall x, x \notin \emptyset$.

### Inductive Specification
When defining a set by listing elements is impractical, we use **inductive (recursive) specification**: a finite recipe that defines a possibly infinite set.

It has two parts:
- **Base clause:** the seed element(s) that definitely belong.
- **Inductive/recursive clause:** if element x is in the set, some transformation of x is also in the set.

Example: the set S of natural numbers divisible by 3:
- $0 \in S$ (base clause)
- If $x \in S$, then $x + 3 \in S$ (inductive clause)

Inductive specification always defines the **smallest** such set; it is free of redundancy. Proof: if S1 and S2 both satisfy all properties and are both smallest, then $S1 \subseteq S2$ and $S2 \subseteq S1$, so $S1 = S2$.

### Indexed Sets and Maps
Sets are unordered. To impose order, we assign each element a unique index from a totally ordered index set I:
$$S_I = { a_i \mid a \in S, i \in I }$$

**Arrays are indexed sets** where the index set is a range of integers.

The **Cartesian product** $A \times B$ is the set of all ordered pairs $(a, b)$. A **map** is an associative container where elements are key-value pairs. An **associative array (dictionary)** is a map where elements are indexed by key rather than position; formally a partial function. Accessing a missing key returns $\perp$ (undefined).

### Key Takeaways
1. Paradigms shape how you think before you write a single line of code; OOP is popular because it mirrors real-world structure naturally.
2. Domain analysis is the bridge between what the user wants (application domain) and what the code does (solution domain).
3. Entities and objects are concrete; types and classes are abstract. Always move from concrete observations to abstract blueprints.
4. Inductive specification defines the smallest set satisfying all given properties; this guarantee of minimality is what makes it useful.
5. Arrays are a special case of indexed sets; understanding this connection clarifies why indexing starts at 0 in most languages.

**Remember this:** Everything in OOP starts from observing real things, abstracting them into types, and deciding how those types communicate; the code is just the formal expression of that observation.

**One analogy to remember it by:** Domain analysis is like writing a job description before hiring. You start by watching what the job actually involves (entities), abstract it into a role (type), then decide what tools and responsibilities come with it (class). The employee who fills that role is the object.

---

## Part 3: Basic Concepts II

### What are Templates?
A **template** is a parameterized abstraction in C++: instead of taking a value like `int x` as a parameter, it takes a type as the parameter. You write the logic once in a general form without committing to any specific type, and the compiler generates the concrete version for whichever types you actually use.

In simple terms: write once, use for any type.

Think of it like: a cookie cutter. The cutter (template) defines the shape; you pick the dough (type) each time you use it. Without templates, you would need to rewrite the same algorithm (e.g. insertion sort) for every type (`int`, `double`, `string`).

Why it matters: templates are the foundation of the C++ Standard Library. Every container (`vector`, `map`, `set`) and algorithm (`sort`, `find`) is a template.

From a theoretical perspective, templates are **second-order functions**: they map types to functions, classes, variables, or other types.

### Types of Templates
**Function templates** define a generic function. The compiler deduces the types from the call arguments (CTAD in C++17):
```cpp
template<typename T, typename Op = std::greater<T>>
void performInsertionSort( std::vector<T>& aArray, Op aTest = Op{} )
{
    // sorting logic using T generically; Op defaults to descending order
}
```

**Class templates** define a generic class:
```cpp
template<typename T>
class InsertionSorter
{
public:
    using array_type = std::vector<T>;  // alias: array_type means std::vector<T>
    InsertionSorter( const array_type& aData ) : fData(aData) {}
private:
    array_type fData;
};
```

**Variable templates** allow typed constants:
```cpp
template<typename T>
T pi_v = T(3.14159265358979323846);

constexpr float pif = pi_v<float>;  // correct precision for float automatically
```

**Alias templates** create parameterized type aliases:
```cpp
template<typename T>
using array_type = std::vector<T>;
```

|Kind|What it parameterizes|Common use|
|---|---|---|
|Function template|A function|Algorithms (sort, swap)|
|Class template|A class|Containers (vector, map)|
|Variable template|A variable|Typed constants (pi_v)|
|Alias template|A type alias|Shorthand for complex types|

### Key Rules for Templates
The compiler generates a new specialization for each distinct set of type parameters. The full implementation (declaration and methods) must be in the header file; templates are blueprints, not compiled classes, so the compiler needs to see all code to generate the right version for each `T`.

**Type traits** are compile-time meta-functions that inspect or transform types: `std::is_same_v<T, U>` returns true if T and U are the same type.

**Concepts** (C++20) are named sets of constraints on template parameters: `std::copyable<T>` ensures T supports copy and move operations. They improve error messages, readability, and compilation times.

### Indexer
An **indexer** is a class that overloads `operator[]` to make a user-defined data type behave like an array.

Think of it like: a smart receptionist. Instead of rummaging through a filing cabinet directly, you hand a label to the receptionist (the indexer) and get back the right file.

Why use an indexer: bounds checking on access, support for non-integer keys, read-only semantics via `const`.

`operator[]` must return an **lvalue reference** so the indexed element can be used on the left-hand side of assignment:
```cpp
template<typename T, size_t N = 20>
class BasicIndexer
{
private:
    T fElements[N];
public:
    using value_type = T;
    using difference_type = std::ptrdiff_t;

    T& operator[]( size_t aIndex )
    {
        assert( aIndex < N );       // debug range check; crashes on out-of-bounds
        return fElements[aIndex];   // return reference so caller can assign
    }
};
```

The `using` declarations are **nested types** (member types). They describe the types used internally and play an important role in traits and concepts so users can query the type without guessing.

![[Pasted image 20260409161616.png]]
`IndexerByString` inherits from `BasicIndexer` and adds an `operator[](const std::string&)` overload that converts a string key to a numeric index, then forwards to the base class integer operator.

### Lambda Expressions
A **lambda expression** is an anonymous function object (closure) that can be defined inline inside another function.

Think of it like: a sticky note with instructions. Instead of writing a whole named function elsewhere, you write a small note right where you need it.

Before C++11 lambdas, the only way to pass a callable as an argument was a pointer to a named, statically defined function. Lambdas eliminate that restriction.

**Syntax:** `[capture] (params) mutable throw() -> return_type { body }`

**Examples:**
```cpp
// Simple Addition
auto sum = [](int a, int b) { return a + b; };
int result = sum(5, 3); // result = 8

// Using Captures
int multiplier = 3;
auto times = [multiplier](int x) { return x * multiplier; };
// multiplier is captured by value; the lambda uses a copy of it.

// Generic Lambda (C++14)
auto generic_add = [](auto a, auto b) { return a + b; };
```

| Part                | Required | Purpose                                     |
| ------------------- | -------- | ------------------------------------------- |
| Capture clause `[]` | Yes      | Which outer variables the lambda can access |
| Parameter list `()` | No       | Input arguments                             |
| `mutable`           | No       | Allow modifying captured-by-value variables |
| Return type `->`    | No       | Explicit return type; usually deduced       |
| Body `{}`           | Yes      | The code to execute                         |

### Capture Modes

| Capture   | Meaning                                                  |
| --------- | -------------------------------------------------------- |
| `[]`      | No captures; outer variables inaccessible                |
| `[x, y]`  | Capture `x` and `y` by value (copies)                    |
| `[&]`     | Capture all by reference; outer updates affect lambda    |
| `[=]`     | Capture all by value; outer updates do not affect lambda |
| `[this]`  | Capture current object by reference                      |
| `[&, x]`  | All by reference except `x` (captured by value)          |
| `[=, &x]` | All by value except `x` (captured by reference)          |

Lambdas solve the hard-coded conversion problem in `IndexerByString`. The call operator `operator()` is used instead of `operator[]` because `operator[]` only accepts one argument:
```cpp
T& operator()( const std::string& aKey,
    auto AtoI = [] (const std::string& aKey) {
        size_t lIndex = 0;
        for ( size_t i = 0; i < aKey.size(); i++ )
            lIndex = lIndex * 10 + (static_cast<size_t>(aKey[i]) - '0');
        return lIndex;
    })
{
    return BasicIndexer<T, N>::operator[]( AtoI(aKey) );
    // AtoI converts the string key to a numeric index; caller can override it
}
```

`auto` lets the compiler deduce the type of a variable from its initializer. It is useful for complex lambda types and iterator types, but incorrect use can produce undefined behavior even if the code compiles.

### Iterators
An **iterator** is an object that implements the infrastructure needed to visit elements of a collection one by one through a common interface.
![[Pasted image 20260407162245.png]]

Think of it like: a bookmark in a book. The bookmark knows where you are (position), can move forward (or backward), and lets you read or write the current page (element access) without exposing how the book's internal binding works.

Why iterators matter: not all data types are arrays. Simple integer indexing does not work for linked lists, trees, or streams. Iterators provide a data-structure-agnostic traversal mechanism.

### Iterator Hierarchy
Each level adds capabilities to the one above it:
![[Pasted image 20260407145118.png]]

**Abilities of iterators:**
![[Pasted image 20260407151408.png]]

**Input iterator:**
![[Pasted image 20260407151442.png]]
![[Pasted image 20260407152034.png]]

**Output iterator:**
![[Pasted image 20260407155538.png]]

**Forward iterator:**
![[Pasted image 20260407155901.png]]
![[Pasted image 20260407155938.png]]

**Bidirectional iterator:**
![[Pasted image 20260407162119.png]]

**Random access iterator:**
![[Pasted image 20260407162147.png]]

### Why is the Dereference Operator const?
The dereference operator is declared `const` because the iterator itself being constant should not prevent access to the underlying element. An `int* const` (constant pointer to int) still yields `int&` when dereferenced; the top-level constness does not propagate. Iterators follow the same rule:
```cpp
T& operator*() const noexcept
{
    return const_cast<T&>( fElements[fPosition] );
    // const_cast strips the iterator's own constness to return a read-write reference
}
```

Prefix (`++iter`) advances and returns a reference to `this` (efficient). Postfix (`iter++`) saves a copy of the old state, advances, then returns the old copy; one extra copy.

### C++ Range Loop
The traditional for statement in C++ reads:
```cpp
for ( init-statement; condition; expression ) statement 
```
This form uses explicit loop variables, conditions, and increments over loop variables.

C++11 introduces a simpler form, called range for statement, to iterate through the elements of a container or other sequence:
```cpp
for ( declaration : expression ) statement
```

The range-for statement is syntactic sugar over the iterator interface:
![[Pasted image 20260407171117.png]]
This form is called range-loop, and the element expression denotes a sequence used to define a range-loop variable in declaration advanced in each iteration
Always use a reference variable to avoid unnecessary copies. Use `const auto&` for read-only access and `auto&` for read-write access.

### Recursion
**Recursion** is a technique where a procedure calls itself to solve a problem by reducing it to smaller instances of the same problem.

Think of it like: Russian nesting dolls. To open the outermost doll, you apply the same "open" operation to the next-smaller doll inside, and so on, until you reach the smallest doll that needs no further opening.

Two requirements for a valid recursive definition:

1. At least one **base case** that is solved directly (no further recursive call).
2. Every recursive call must move toward the base case, or the procedure will not terminate.

### Classic Recursive Problems
**Factorial:**
```cpp
size_t factorial( size_t n ) noexcept
{
    if ( n == 0 ) return 1;          // base case
    else return n * factorial(n-1);  // inductive case; reduces n toward 0
}
```

**Fibonacci:** naive recursive Fibonacci has exponential time complexity. The call tree roughly doubles at each level; `fib(n)` generates approximately $2^n$ calls. This is the classic illustration of why recursion alone is not always efficient.

**Tail recursion:** a function is **tail-recursive** if the recursive call is the very last operation; no deferred computation remains after it returns. Tail recursion can be mechanically converted to a loop:
```cpp
long gcd( long x, long y ) noexcept
{
    if ( y == 0 ) return x;
    else return gcd( y, x % y );  // tail call; no pending work after return
}
```

**Towers of Hanoi:** move n disks from Start to Target using Middle as buffer. Number of moves $= 2^n - 1$.

**Merge sort:** divide-and-conquer recursion. Recursively splits a list, sorts each half, and merges results. Time complexity: $O(n \log n)$. Space complexity: $O(n)$.

The midpoint is calculated as `low + (high - low) / 2` to **prevent integer overflow** that would occur with `(low + high) / 2` when indices are large.

### Mutual Recursion and Forward Declarations
Mutually recursive classes in C++ require **forward declarations** to resolve the circular dependency. Unlike Java or C#, C++ does not resolve mutual dependencies automatically:
```cpp
class ClassB;  // forward declare ClassB before ClassA uses it

class ClassA {
    // can now reference ClassB
};

class ClassB {
    // ClassA is already fully defined
};
```

### Key Takeaways
1. Templates enable type-agnostic reuse: write once, instantiate for any type. The full definition must live in headers because templates are blueprints, not precompiled code.
2. Indexers wrap raw array access with bounds checking and flexible key types; `operator[]` must return an lvalue reference.
3. Lambdas are anonymous closures that can be defined inline; the capture clause controls exactly which outer variables the lambda can see and how.
4. Iterators decouple algorithms from data structures; the iterator hierarchy lets you express exactly how much capability a function requires.
5. Every recursive definition needs a base case and must progress toward it; tail recursion is convertible to a loop, and divide-and-conquer recursion gives provably efficient algorithms.

**Remember this:** A well-designed C++ program is like a well-run library. The shelves (data structures) are organized by a cataloguing scheme (indexed sets/maps), librarians (iterators) know how to walk the shelves, all books follow a standard template (class/function templates), and finding any book always terminates because there are finitely many shelves (recursion base case).

**One analogy to remember it by:** Templates are cookie cutters, lambdas are sticky notes, and iterators are bookmarks. Each one is a small, focused tool that eliminates an entire class of repetition from your code.

Here is the new Part, which would slot in as **Part 4** in the existing doc since Parts 1-3 are already taken:

---

## Part 4: Matrices and Vector Transformations

### What is a Matrix?
A **matrix** is a rectangular array of numbers arranged in rows and columns. An $n \times m$ matrix $\mathbf{M}$ has $n$ rows and $m$ columns, where each entry $M_{ij}$ is identified by its row $i$ and column $j$:
$$\mathbf{M}_{3 \times 4} = \begin{bmatrix} M_{11} & M_{12} & M_{13} & M_{14} \\ M_{21} & M_{22} & M_{23} & M_{24} \\ M_{31} & M_{32} & M_{33} & M_{34} \end{bmatrix}$$

In simple terms: a matrix is a structured grid of numbers that encodes a transformation. Where a single number scales one thing, a matrix can simultaneously scale, rotate, and translate entire spaces.

Think of it like: a recipe card that takes a vector as input and tells you exactly where each component ends up after the operation.

Why it matters: matrices are the mathematical foundation of 2D and 3D graphics, physics simulations, robotics, and machine learning. Every transformation applied to a game object or a neural network weight is ultimately a matrix operation.

If $n = m$, the matrix is called a **square matrix**. Individual rows and columns can be extracted:
$$row(\mathbf{M}_{3 \times 4}, 2) = \begin{bmatrix} M_{21} & M_{22} & M_{23} & M_{24} \end{bmatrix} \qquad column(\mathbf{M}_{3 \times 4}, 3) = \begin{bmatrix} M_{13} \\ M_{23} \\ M_{33} \end{bmatrix}$$

### Matrix Operations
**Scalar multiplication** distributes the scalar $\alpha$ across every entry in the matrix:
$$\alpha \mathbf{M} = \mathbf{M}\alpha = \begin{bmatrix} \alpha M_{11} & \alpha M_{12} & \cdots & \alpha M_{1m} \\ \alpha M_{21} & \alpha M_{22} & \cdots & \alpha M_{2m} \\ \vdots & \vdots & \ddots & \vdots \\ \alpha M_{n1} & \alpha M_{n2} & \cdots & \alpha M_{nm} \end{bmatrix}$$

**Matrix addition** is element-wise and requires both matrices to have the same dimensions:
$$(\mathbf{F} + \mathbf{G})_{ij} = F_{ij} + G_{ij}$$

**Matrix multiplication** requires the number of columns in $\mathbf{F}$ to equal the number of rows in $\mathbf{G}$. If $\mathbf{F}$ is $n \times m$ and $\mathbf{G}$ is $m \times p$, the product $\mathbf{FG}$ is $n \times p$. Each $(i, j)$ entry is the dot product of row $i$ of $\mathbf{F}$ and column $j$ of $\mathbf{G}$:
$$(\mathbf{FG})_{ij} = \sum_{k=1}^{m} F_{ik} G_{kj}$$

An $n$-dimensional vector can be treated as an $n \times 1$ matrix. Multiplying an $m \times n$ matrix $\mathbf{M}$ by an $n$-dimensional vector $\mathbf{V}$ yields an $m$-dimensional vector $\mathbf{V}'$.

### 2D Vector Transformations

The three fundamental 2D transformations are scaling, rotation, and translation. The first two can be expressed as $2 \times 2$ matrix multiplications; translation requires a special treatment.

**Scaling** by factor $\alpha$ stretches or shrinks a vector uniformly:

$$\mathbf{V}' = \begin{bmatrix} V'_x \\ V'_y \end{bmatrix} = \begin{bmatrix} \alpha & 0 \\ 0 & \alpha \end{bmatrix} \begin{bmatrix} V_x \\ V_y \end{bmatrix}$$

**Example:** the vector $[1.0, 2.0]$ scaled by $3.2$ yields $[3.2, 6.4]$:

$$\begin{bmatrix} 3.2 \\ 6.4 \end{bmatrix} = \begin{bmatrix} 3.2 & 0 \\ 0 & 3.2 \end{bmatrix} \begin{bmatrix} 1.0 \\ 2.0 \end{bmatrix}$$

**Rotation** by angle $\theta$ counterclockwise uses the rotation matrix $\mathbf{R}$:

$$\mathbf{R} = \begin{bmatrix} \cos\theta & -\sin\theta \\ \sin\theta & \cos\theta \end{bmatrix}$$

**Example:** the vector $[1.0, 4.0]$ rotated by $90°$ yields $[-4.0, 1.0]$:

$$\begin{bmatrix} -4.0 \\ 1.0 \end{bmatrix} = \begin{bmatrix} 0 & -1 \\ 1 & 0 \end{bmatrix} \begin{bmatrix} 1.0 \\ 4.0 \end{bmatrix}$$

**Translation** shifts a vector by an offset $[\Delta_x, \Delta_y]$:

$$\mathbf{V}' = \begin{bmatrix} V_x \\ V_y \end{bmatrix} + \begin{bmatrix} \Delta_x \\ \Delta_y \end{bmatrix}$$

Translation does not affect the orientation or scale of the axes. The problem is that translation cannot be expressed as a $2 \times 2$ matrix multiplication; only as matrix addition, which is computationally undesirable when combining multiple transformations.

### Homogeneous Coordinates

**Homogeneous coordinates** solve the translation problem by extending 2D vectors to 3D and using $3 \times 3$ matrices for all transformations, including translation. A $w$-coordinate with value 1 is added:

$$\begin{bmatrix} V_x \\ V_y \end{bmatrix} \Rightarrow \mathbf{V}_3 = \begin{bmatrix} V_x \\ V_y \\ 1 \end{bmatrix}$$

A general $3 \times 3$ transformation matrix $\mathbf{F}$ embeds a $2 \times 2$ matrix $\mathbf{M}$ and a translation vector $\mathbf{T}$:

$$\mathbf{F} = \begin{bmatrix} \mathbf{M} & \mathbf{T} \\ \mathbf{0} & 1 \end{bmatrix} = \begin{bmatrix} M_{11} & M_{12} & T_x \\ M_{21} & M_{22} & T_y \\ 0 & 0 & 1 \end{bmatrix}$$

To return to 2D after transformation, divide all coordinates by $V_w$ and drop it:

$$\begin{bmatrix} V'_x / V_w \\ V'_y / V_w \\ V_w / V_w \end{bmatrix} = \begin{bmatrix} V_x \\ V_y \\ 1 \end{bmatrix} \Rightarrow \begin{bmatrix} V_x \\ V_y \end{bmatrix}$$

The three standard $3 \times 3$ transformation matrices in homogeneous coordinates are:

$$\mathbf{S} = \begin{bmatrix} s_x & 0 & 0 \\ 0 & s_y & 0 \\ 0 & 0 & 1 \end{bmatrix} \qquad \mathbf{R} = \begin{bmatrix} \cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 \end{bmatrix} \qquad \mathbf{T} = \begin{bmatrix} 1 & 0 & T_x \\ 0 & 1 & T_y \\ 0 & 0 & 1 \end{bmatrix}$$

Note that $\mathbf{T}$ contains a $2 \times 2$ identity sub-matrix as $\mathbf{M}$. This means under translation, the scale and rotation of the object remain unchanged.

### Vector3D and Matrix3x3 in C++
`Vector3D` extends `Vector2D` to homogeneous coordinates by composing a `Vector2D fBaseVector` with a `float fW` field. It supports implicit conversion from `Vector2D` (adding $w = 1$) and explicit conversion back to `Vector2D` (dropping $w$):
```cpp
class Vector3D
{
private:
    Vector2D fBaseVector;  // composition: reuses Vector2D for x and y
    float fW;

public:
    Vector3D( float aX = 1.0f, float aY = 0.0f, float aW = 1.0f ) noexcept;
    Vector3D( const Vector2D aVector ) noexcept;  // implicit: Vector2D -> Vector3D

    float x() const noexcept { return fBaseVector.x(); }
    float y() const noexcept { return fBaseVector.y(); }
    float w() const noexcept { return fW; }

    float operator[]( size_t aIndex ) const noexcept;   // index-based access
    explicit operator Vector2D() const noexcept;         // explicit: Vector3D -> Vector2D

    Vector3D operator*( const float aScalar ) const noexcept;
    Vector3D operator+( const Vector3D& aOther ) const noexcept;
    float dot( const Vector3D& aOther ) const noexcept;
};
```

`Matrix3x3` stores three `Vector3D` row vectors and provides static factory methods to build the standard transformation matrices directly:
```cpp
class Matrix3x3
{
private:
    Vector3D fRows[3];  // three row vectors, each a Vector3D

public:
    static Matrix3x3 getS( const float aX = 1.0f, const float aY = 1.0f ); // scaling
    static Matrix3x3 getT( const float aX = 0.0f, const float aY = 0.0f ); // translation
    static Matrix3x3 getR( const float aAngleInDegree = 0.0f );             // rotation

    Matrix3x3 operator*( const float aScalar ) const noexcept;
    Matrix3x3 operator+( const Matrix3x3& aOther ) const noexcept;
    Vector3D  operator*( const Vector3D& aVector ) const noexcept; // transform a vector

    const Vector3D& row( size_t aRowIndex ) const;
    const Vector3D  column( size_t aColumnIndex ) const;
};
```

### Object Layout
The memory layout of a C++ class follows declaration order. C++ places non-static member variables with the same access level adjacent in memory, preserving declaration order. The result is a record structure where the fields are the member variables.

For classes with no virtual members, the object record solely contains the member variables. This means `Vector2D` and `Vector3D` lay out in memory as packed arrays of `float` values:
```
Vector2D:          Vector3D:
  0: float fX;       Vector2D fBaseVector:
  1: float fY;         0: float fX;
                       1: float fY;
                     2: float fW;
```

This layout is why `operator[]` on `Vector3D` can index coordinates directly by offset.

### Key Takeaways
1. Matrix multiplication is not commutative; order matters. $\mathbf{FG} \neq \mathbf{GF}$ in general.
2. Translation cannot be expressed as a $2 \times 2$ matrix multiplication; homogeneous coordinates solve this by lifting 2D vectors into 3D.
3. All three standard transformations (scale, rotation, translation) become $3 \times 3$ matrix multiplications in homogeneous coordinates, allowing them to be composed by multiplying their matrices together.
4. C++ object layout follows declaration order; for simple classes with no virtual members, fields are laid out as a packed record, which enables index-based coordinate access via `operator[]`.
5. `Vector3D` uses class composition (embedding a `Vector2D`) rather than inheritance, demonstrating that building complex types from simpler ones is often cleaner than extending them.

**Remember this:** Homogeneous coordinates are the trick that unifies all geometric transformations into one consistent operation: matrix multiplication. Once everything is a matrix multiply, combining transformations is just multiplying matrices together.

**One analogy to remember it by:** Translation in 2D is like trying to slide a piece of paper using only a rubber stamp -- you can rotate and scale with the stamp, but sliding requires a different motion entirely. Homogeneous coordinates add a third dimension that turns the slide into a rotation, so one tool handles everything.


