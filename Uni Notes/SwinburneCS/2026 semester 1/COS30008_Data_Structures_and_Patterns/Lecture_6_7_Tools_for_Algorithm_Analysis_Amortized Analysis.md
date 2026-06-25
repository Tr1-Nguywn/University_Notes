# COS30008 - Tools for Algorithm Analysis & Amortized Analysis

## Part 1: Computability and the Limits of Algorithms_

### What is Computability?
**Computability** is the study of what problems can and cannot be solved by a formal computational process like a Turing machine.

In simple terms: some problems can be solved/calculated by a program in finite time and resources, and some simply cannot.

Think of it like: a vending machine that always dispenses something given a valid input. But some requests (like "give me tomorrow's weather") are outside what the machine can ever do, no matter how sophisticated it gets.

Why it matters: before spending effort designing an algorithm, it is essential to know whether a solution even exists. Many real-world problems, like detecting all software bugs in an arbitrary program, are provably unsolvable.

### The Abstract Machine Model
**Computation** is modelled as a mapping from inputs to outputs, carried out by a machine or program that processes its input in a sequence of steps. A function is called **effectively computable** if it can be computed in a finite amount of time using finite resources. ![[Pasted image 20260426170928.png]]

Many problems are **intractable**: no efficient algorithm exists for them. While most have a brute-force solution, that approach is computationally infeasible for anything beyond the smallest inputs. Classic examples include the traveling salesman problem and bin packing.

The TSP asks: _"Given a list of cities and the distances between each pair, what is the shortest possible route that visits every city exactly once and returns to the origin city?"_

The BPP asks: _"Given a set of items of different sizes or weights, and a set of identical bins with a fixed capacity, what is the minimum number of bins required to pack all the items without exceeding any bin's capacity?"_

### The Turing Machine
A **Turing machine** is the canonical abstract model of a computing device. It consists of a read/write head that scans a potentially infinite one-dimensional tape divided into squares, each inscribed with symbols. The machine operates in states, reading and writing symbols, then moving left or right until it reaches a halt state.

Formally, a Turing machine is a septuple (7 tuple) $(Q, \Gamma, \gamma, \Sigma, q_0, F, \sigma)$ where:
- $Q$ is a set of states
- $\Gamma$ is a finite non-empty set of tape symbols
- $\gamma \in \Gamma$ is the blank symbol
- $\Sigma \subseteq \Gamma \setminus {\gamma}$ is the set of input symbols ($\Sigma$ is a subset of $\Gamma$ exclude $\gamma$)
- $q_0$ is the start state
- $F \subseteq Q$ is the set of accepting states
- $\sigma: (Q \setminus F) \times \Gamma \to Q \times \Gamma \times {R, L}$ is the transition function The machine halts either when it enters an accepting state or when $\sigma$ is undefined for the current configuration.

### Addition With Turing Machine
![[Pasted image 20260426174122.png|517]] 
Numbers are encoded in **unary**: 5 = `1 1 1 1 1`, 3 = `1 1 1`, separated by `+`. The tape starts as `1 1 1 1 1 + 1 1 1` and the goal is to produce `1 1 1 1 1 1 1 1`.

The machine works by repeatedly shifting the `+` one position rightward into the second group, overwriting it with a `1` each time. Once the `+` reaches the end of the second group, it gets erased and the machine halts. The two unary groups are effectively merged into one.

Each table cell reads as `nextState/"write"/direction`. For example, `2/"1"/R` means: transition to state 2, write `"1"`, move Right.

### Church's Thesis and the Halting Problem
**Church's Thesis** states that it is impossible to build a computing device more powerful than a Turing machine. This cannot be proven mathematically, since "effectively computable" is an intuitive rather than a formal notion. It can only be refuted by demonstrating a machine that solves a problem no Turing machine can. So far, no such counterexample has been found.

The **Halting Problem** asks: given an arbitrary Turing machine and its input tape, will the machine eventually halt? This is provably **uncomputable**, meaning no algorithm can decide it in general. Under Church's Thesis, this means no real computer can solve it either.

### The Ackermann Function
The **Ackermann function** is a classic mathematical algorithm that defines a rapidly growing sequence that is computable* because you can always compute $A(m,n)$ by following its recursive definition, it just take an extraordinarily long time for large input. It's not primitive recursive function, meaning it cannot be expressed using only bounded loops (for-loops) . It is defined as: 
$$A(0, n) = n + 1$$
$$A(m+1, 0) = A(m, 1)$$
$$A(m+1, n+1) = A(m, A(m+1, n))$$
Its growth is again, extraordinary: $A(4, 2)$ is a number with 19,729 decimal digits. This illustrates that Turing computability does not imply practical computability. ![[Pasted image 20260426182809.png]] ![[Pasted image 20260426183042.png]]

### Key Takeaways
1. Computability distinguishes what is solvable in principle from what is not.
2. The Turing machine is the universal model of computation; all known formalisms are equivalent to it.
3. The Halting Problem is the canonical example of an uncomputable problem.
4. Computable does not imply efficient: the Ackermann function grows faster than any primitive recursive function.

**Remember this:** Church's Thesis sets the ceiling on what computation can ever do, and the Halting Problem proves that ceiling has real cracks.

**One analogy to remember it by:** A Turing machine is like an infinitely patient worker with an infinite notepad and a single pencil. Most jobs it can do given enough time. But asking it whether any arbitrary job will ever finish is a question it can never answer reliably.

---

## Part 2: Measuring Algorithm Complexity

### What is Complexity?
**Algorithm complexity** is a measure of the resources, primarily time and space, that an algorithm requires as a function of its input size $n$.

In simple terms: rather than timing an algorithm on specific hardware, we count the number of basic operations it performs as the input grows.

Think of it like: instead of timing a chef, you count how many cuts they make. A chef who makes $n$ cuts for $n$ ingredients scales linearly; one who checks every pair makes $n^2$ cuts.

Why it matters: selecting the right algorithm for large inputs can be the difference between a program that finishes in seconds and one that would take centuries.

### The Problem with Wall-Clock Timing
Measuring actual runtime is machine-dependent. Algorithm A running 2 minutes and algorithm B running 1 minute 45 seconds on the same input does not prove B is better: A may have been interrupted by another process, B may have run on faster hardware, and a different input might reverse the result. A meaningful measure must be **machine-independent**: it counts basic operations as a function of input size.

### Counting Basic Operations
To analyse complexity, we identify a **basic operation** (a comparison, an arithmetic step, an assignment) and count how many times the algorithm performs it. Complexity is then expressed as $T(n)$, the running time as a function of input size $n$.

**Worked example:** computing $\sum_{i=1}^{n} i^3$ via a loop: ![[Pasted image 20260426190426.png]]
$$T(n)=0+1+1+(n+1)+n+4n+1=6n+4$$
The same sum can be computed in $O(1)$ using the closed form $\left(\frac{n(n+1)}{2}\right)^2$, illustrating that algorithmic insight can collapse linear cost to constant.

### General Counting Rules
- **For-loop:** running time is at most the body cost $C$ times the number of iterations, giving $O(n)$.
- **Nested for-loop:** running time multiplies by each loop's size. A $k$-nested loop has cost $O(n^k)$.
- **Statement sequence:** total cost is the sum of each statement's cost; the dominant term determines asymptotic class.
- **Branching (if-else):** cost is at most the condition cost plus the larger of the two branches. This may overestimate but never underestimates.

### Towers of Hanoi: An Exponential Example
https://www.youtube.com/watch?v=rf6uf3jNjbo ![[Pasted image 20260426220130.png]] 
The Towers of Hanoi recurrence is $T(n) = 2T(n-1) + 1$, which resolves to: 
$$T(n) = 2^{n+1} - 1 \implies O(2^n)$$
This shows that a simple recursive procedure with two self-calls produces exponential cost. Doubling the number of disks more than doubles the work; it squares the effort.

### Key Takeaways
1. Count basic operations rather than clock time to get a machine-independent measure.
2. A for-loop is $O(n)$; nesting adds a multiplicative factor; exponential growth comes from recursive doubling.
3. A closed-form formula can collapse an $O(n)$ algorithm into $O(1)$.
4. The dominant term always determines the asymptotic class.

**Remember this:** complexity counts the operations, not the seconds, and the dominant term is the only thing that matters at scale.

**One analogy to remember it by:** counting operations is like counting letters written, not the time spent writing. Whether you use a quill or a keyboard, the letter count tells you the true workload.

---

## Part 3: Best-Case, Worst-Case, and Average-Case Complexity

### What is Input-Dependent Complexity?
For most algorithms, the number of basic operations differs across inputs of the same size. **Three scenarios** are used to characterise this variation: best-case, worst-case, and average-case.

Let $\mathcal{F}_n$ denote the set of all inputs of size $n$, and $\tau(I)$ the number of basic operations on input $I$.

### Best-Case Complexity
$$B(n) = \min{\tau(I) \mid I \in \mathcal{F}_n}$$
Best-case complexity identifies the most favourable input. It is easy to compute but of limited use on its own; it serves mainly as a lower bound when bounding average complexity.

### Worst-Case Complexity
$$W(n) = \max{\tau(I) \mid I \in \mathcal{F}_n}$$
Worst-case complexity is the most practically useful measure. It guarantees the algorithm never exceeds $W(n)$ operations, enabling reliable performance guarantees. This matters especially when deadlines must be met, as in real-time systems.

### Average-Case Complexity
$$A(n) = \sum_{I \in \mathcal{F}_n} \tau(I) \cdot p(I) = E[\tau]$$
where $p(I)$ is the probability of input $I$ occurring. A practical variant sums over operation counts weighted by their probability:
$$A(n) = \sum_{i=1}^{W(n)} i \cdot p_i$$
Average-case analysis requires assumptions about input distributions and is harder to compute than worst-case.

### Bubble Sort as a Case Study
Bubble Sort (Knuth's optimised version)

**Worst case** (reverse-sorted input): every comparison triggers a swap and the outer loop runs fully.
$$T^W_{\text{BubbleSort}}(N) = c_1 + c_2 n + (c_3+c_4)(n-1) + c_5\left(\frac{n(n+1)}{2}-1\right) + (c_6+c_7+c_8+c_9)\frac{n(n-1)}{2}$$
The quadratic terms dominate: $W(n) = O(n^2)$.

**Best case** (already sorted): no swaps occur; the inner loop runs once and exits because $t$ stays 0.
$$T^B_{\text{BubbleSort}}(N) = c_1 + c_2 n + (c_3+c_4)(n-1) + c_5 n + (c_6+c_9)(n-1)$$
The linear term dominates: $B(n) = \Omega(n)$.

Donald Knuth famously concluded that Bubble Sort has nothing to recommend it except a catchy name. Insertion Sort, also $O(n^2)$ and $\Omega(n)$, consistently outperforms it in practice.

### Algorithm Comparison Table

|Algorithm|B(n)|A(n)|W(n)|
|---|---|---|---|
|Towers of Hanoi|$2^n$|$2^n$|$2^n$|
|Linear Search|$1$|$n$|$n$|
|Binary Search|$1$|$\log n$|$\log n$|
|Min/Max/MinMax|$n$|$n$|$n$|
|Insertion Sort|$n$|$n^2$|$n^2$|
|Merge Sort|$n \log n$|$n \log n$|$n \log n$|
|Heap Sort|$n \log n$|$n \log n$|$n \log n$|
|Quick Sort|$n \log n$|$n \log n$|$n^2$|

### Key Takeaways
1. Best-case is a lower bound; worst-case is an upper bound; average-case describes typical behaviour.
2. Worst-case is the most practically important for algorithm selection and real-time guarantees.
3. Two algorithms with the same asymptotic class can differ significantly in practice (Bubble Sort vs Insertion Sort).
4. The same algorithm can behave very differently depending on input order.

**Remember this:** always ask which case matters for your application: if you need a deadline guarantee, use worst-case; if you care about typical loads, use average-case.

**One analogy to remember it by:** best-case is a tailwind, worst-case is a headwind, and average-case is the usual commute. You plan for headwinds, but you budget for the commute.

---

## Part 4: Asymptotic Analysis

### What is Asymptotic Analysis?
**Asymptotic analysis** characterises the growth rate of an algorithm's running time $T(n)$ as $n \to \infty$, ignoring machine-dependent constants and lower-order terms.

In simple terms: we care about the shape of the cost curve as input gets large, not the exact number of operations for small inputs.

Think of it like: comparing two companies' revenue trajectories. One growing at $n^2$ per year will eventually dominate one growing at $1000n$, regardless of the initial gap.

Why it matters: asymptotic notation gives a machine-independent, input-independent characterisation of an algorithm that enables fair comparison across implementations and hardware.

### The Three Asymptotic Notations
**Big-Theta $\Theta$:** tight bound. $f(n) = \Theta(g(n))$ if and only if there exist positive constants $c_1$, $c_2$, and $n_0$ such that:
$$c_1 g(n) \leq f(n) \leq c_2 g(n) \quad \text{for all } n \geq n_0$$
This means $f$ and $g$ grow at the same rate. $\Theta$ is the most informative notation and should be used when both bounds are known.

**Big-Oh $O$:** upper bound. $f(n) = O(g(n))$ if and only if there exist positive constants $c$ and $n_0$ such that:
$$f(n) \leq c \cdot g(n) \quad \text{for all } n \geq n_0$$

**Big-Omega $\Omega$:** lower bound. $f(n) = \Omega(g(n))$ if and only if there exist positive constants $c$ and $n_0$ such that:
$$c \cdot g(n) \leq f(n) \quad \text{for all } n \geq n_0$$

Key relationships:
- $f(n) = O(g(n)) \iff g(n) = \Omega(f(n))$
- $f(n) = \Theta(g(n)) \iff f(n) = O(g(n))$ and $f(n) = \Omega(g(n))$
- $\Theta$ is symmetric: $f(n) = \Theta(g(n)) \iff g(n) = \Theta(f(n))$

### Simplification Rules
Asymptotic notation drops lower-order terms and constant coefficients:
- $4n^2 + 3n + 1 = O(n^2)$: drop $3n + 1$, drop coefficient 4.
- $a_k n^k + \ldots + a_0 = O(n^k)$: keep only the dominant power.
- $\log_{10} n = O(\log n)$: base changes are constant factors; logarithm base is irrelevant.
- $345n^{4536} + n \log n + 2^n = O(2^n)$: exponential dominates everything polynomial.

### The Limit Method
To compare growth rates of $f$ and $g$, examine:
$$L = \lim_{n \to \infty} \frac{f(n)}{g(n)}$$
- $L = 0$: $g$ grows faster than $f$, so $f = O(g)$.
- $L = \infty$: $f$ grows faster than $g$, so $f = \Omega(g)$.
- $L$ is a finite non-zero constant: they grow at the same rate, $f = \Theta(g)$.
- $L$ does not exist: the limit method cannot be applied.

### Hierarchy of Orders (Fastest to Slowest)

|Complexity|Example|
|---|---|
|$O(1)$|constant-size lookup table|
|$O(\log n)$|binary search|
|$O(n)$|finding min/max|
|$O(n \log n)$|merge sort|
|$O(n^2)$|insertion sort|
|$O(n^2 \log n)$|naive suffix array construction|
|$O(n^3)$|standard polygon triangulation|
|$O(2^n)$|recursive Fibonacci|
|$O(n!)$|traveling salesman brute force|

### Key Takeaways
1. Asymptotic notation captures growth rate independent of machine constants or lower-order clutter.
2. $\Theta$ is the tightest and most informative; $O$ gives an upper bound; $\Omega$ gives a lower bound.
3. Drop constants and lower-order terms; keep only the dominant term.
4. The limit method provides a mechanical way to compare two functions' growth rates.

**Remember this:** asymptotic analysis asks "how does cost scale?" not "how fast is it today?"

**One analogy to remember it by:** $O$, $\Theta$, and $\Omega$ are like a speed limit sign, an actual speedometer reading, and a minimum speed sign, all describing the same stretch of road from different angles.

---

## Part 5: Amortized Analysis

### What is Amortized Analysis?
**Amortized (phân bổ dần) analysis** is a technique for finding the worst-case average cost per operation across a sequence of operations, even when individual operations within that sequence have wildly different costs.

In simple terms: one expensive operation does not mean every operation is expensive; what matters is the total cost spread across the whole sequence.

Think of it like a shared printer at an office. Every time someone prints, the job goes through instantly because the tray is already loaded. But occasionally someone has to stop and refill the entire tray, which takes much longer. If you naively said "refilling the tray is the worst case, so every single print job costs that much," you would massively overestimate the true cost. In reality the refill cost is shared across all the prints that used up that tray, so the average cost per print is still cheap.

Why it matters: the naive approach takes the worst-case cost of a single operation and multiplies by $n$, producing a loose bound that overstates the true total. Amortized analysis looks at the total cost of the whole sequence instead, giving a tighter per-operation bound. Crucially, this is still a worst-case guarantee: no adversary can choose a sequence of $n$ operations that exceeds this cost. It is not an assumption about typical inputs or probability.

Formally, amortized analysis characterises:
$$C(n) = \frac{1}{n} \cdot (\text{worst-case total cost of a sequence of } n \text{ operations})$$

### The Three Methods

#### Aggregate Method
The simplest approach. Compute the total worst-case cost $T(n)$ of the entire sequence of $n$ operations, then divide by $n$ to get the average upper bound per operation:
$$\text{amortized cost} = \frac{T(n)}{n}$$
Every operation gets charged the same amortised cost regardless of whether it was cheap or expensive individually. Think of it like splitting a restaurant bill equally: some people ordered more, but everyone pays the same share. 
![[Pasted image 20260520230010.png]]
The key insight is that you ask one question across the whole sequence rather than per operation: what is the total cost, and how does it divide out? This often reveals that expensive operations are too rare to significantly raise the average.

#### Accounting Method
Unlike the aggregate method, the accounting method assigns each operation a custom **amortised cost** $\hat{c}_i$ that may differ from its actual cost $c_i$. The constraint is that the total amortised cost must always be at least as large as the total actual cost across any sequence:
$$\sum_{i=1}^{n} \hat{c}_i \geq \sum_{i=1}^{n} c_i$$
Operations charged more than their actual cost bank the surplus as **credit** on the data structure. When an expensive operation arrives, it draws on that stored credit rather than appearing to exceed the amortised rate. The difference between the two sums at any point is the total credit stored, and it must never go negative. If it did, it would mean some operations borrowed credit that was never actually saved, which would make the amortised cost an underestimate rather than a valid upper bound.

A useful analogy is pre-stamped envelopes. You pay upfront for both the envelope and the postage when you buy it, so when you actually mail the letter, the cost is already covered. The overpayment at purchase time is the credit that makes the mailing step free.

#### Potential Method
Similar in spirit to the accounting method but instead of tracking credit on individual elements, you assign a single **potential energy** $\Phi(D_i)$ to the entire data structure after the $i$-th operation. The amortised cost of each operation is its actual cost plus the change in potential:
$$\hat{c}_i = c_i + \Phi(D_i) - \Phi(D_{i-1})$$
Think of it like conservation of energy: the surplus or deficit between amortised and actual cost must equal the change in the data structure's potential. An operation that increases potential is being overcharged and storing energy for later. An operation that decreases potential is drawing on stored energy and appearing cheaper than it actually was.

Summing over all $n$ operations telescopes to:
$$\sum_{i=1}^{n} \hat{c}_i = \sum_{i=1}^{n} c_i + \Phi(D_n) - \Phi(D_0)$$

For the amortised cost to be a valid upper bound, we need $\Phi(D_n) - \Phi(D_0) \geq 0$ at all times. The simplest way to guarantee this is to set $\Phi(D_0) = 0$ and ensure $\Phi$ never goes negative.

Think of it like a phone battery. Cheap operations charge it, expensive operations drain it. The battery level is the potential. As long as it never drops below its starting charge, the accounting is valid.

### Reverse Polish Notation (RPN)
**Reverse Polish Notation** is a way of writing arithmetic expressions where operators come after their operands rather than between them. For example `3 * 5 + 29` in standard notation becomes `3 5 * 29 +` in RPN.

Why it matters: RPN eliminates the need for brackets entirely. The order of operations is unambiguous just from the position of the operators. It is also a classic stack application because it can be evaluated in a single left-to-right pass.

**Algorithm:** read the expression left to right. If you see a number, push it. If you see an operator, pop the top two numbers, apply the operator, and push the result back.
![[Pasted image 20260521123447.png]]
```cpp
// evaluate "3 5 * 29 +"
push(3)         // stack: [3]
push(5)         // stack: [3, 5]
// see *: pop 5 and 3, push 3*5=15
push(15)        // stack: [15]
push(29)        // stack: [15, 29]
// see +: pop 29 and 15, push 15+29=44
push(44)        // stack: [44]
// result: x = 44
```
Each token is processed exactly once, so the algorithm runs in $\Theta(n)$ where $n$ is the number of tokens.
### Case Study: Stack with Multipop
![[Pasted image 20260521123122.png]]
A **stack** is a last-in first-out structure:
```cpp
template<typename T, size_t N>
class Stack
{
public:
    Stack() noexcept;
    std::optional<T> top() noexcept;
    void push( const T& aValue );
    void pop();
    void pop( size_t k ); // multipop

private:
    T fElements[N];
    size_t fStackPointer;
};
```

The constructor initialises all elements to their default value and the stack pointer to 0:
```cpp
Stack() noexcept :
    fElements(),      // value-initialises every slot in the array
    fStackPointer(0)  // stack is empty
{}
```
- `fElements()` is C++ value initialisation: the compiler expands it into a loop calling `T()` on every element. 
- `fStackPointer(0)` uses member initialiser syntax to set the pointer before the constructor body runs.

You can only interact with the top element. The four operations are:

`top()` returns the most recently inserted element if it exists. Since the stack may be empty, the return type is `std::optional<T>`, a wrapper that either holds a value or holds nothing, so the caller can safely check before using it rather than relying on a sentinel value like `-1` that only works for certain types:
```cpp
std::optional<T> top() noexcept
{
    if ( fStackPointer > 0 )
        return std::optional<T>( fElements[fStackPointer - 1] );
    else
        return std::optional<T>{};  // empty, nothing to return
}
```

`push(x)` adds element $x$ to the top. Cost: $\Theta(1)$.
```cpp
void push( const T& aValue )
{
    assert( fStackPointer < N );
    fElements[fStackPointer++] = aValue;
}
```

`pop()` removes the top element by decrementing the stack pointer. Cost: $\Theta(1)$.
```cpp
void pop()
{
    assert( fStackPointer > 0 );
    fStackPointer--;
}
```

`multipop(k)` removes the top $k$ elements one by one, stopping early if the stack empties first. The actual number of removals is $\min(|s|, k)$, meaning whichever is smaller: the number you asked to remove $k$, or the number of elements currently on the stack $|s|$. So the cost is $\Theta(\min(|s|, k))$.
```cpp
void multipop( size_t k )
{
    while ( fStackPointer > 0 && k > 0 )
    {
        pop();
        k--;
    }
}
```

**The naive bound:** the naive approach takes the single worst-case cost of one operation, which is $\Theta(n)$ for a multipop when the stack is full, and multiplies by $n$ total operations, giving $\Theta(n^2)$. But this is too loose. Each time an expensive multipop happens, the stack must have been refilled by pushes first. So between any two expensive multipops there must be enough cheap pushes to refill what was drained. Expensive multipops cannot happen back to back without cheap operations in between paying for them.

**Aggregate analysis:** each element is pushed exactly once and popped at most once across the entire sequence. So across any $n$ operations starting from an empty stack, the total number of pops across all multipop calls is bounded by the total number of pushes, which is at most $n$. The total cost is $O(2n)$, simplified to $O(n)$, giving an amortised cost of $O(1)$ per operation.

**Accounting analysis:** the idea is to make each push pre-pay for its own eventual removal. Assign these amortised costs:

|Operation|Actual Cost|Amortised Cost|Credit|
|---|---|---|---|
|push|1|2 (overcharged by 1 so store 1 as credit)|1|
|pop|1|0 (use that 1 credit)|0|
|multipop(k)|$k'$|0 (each removed element uses its credit)|0|

Each push pays 1 for itself and deposits 1 credit onto the element it just pushed. When that element is later removed by either `pop` or `multipop`, it uses its stored credit to cover the removal cost. Pop or multipop appear free because the work is already paid for. Credit never goes negative because every element on the stack has exactly 1 credit sitting on it, and you cannot remove an element that was never pushed.

**Potential analysis:** define the potential as $\Phi(D_i) = |s|$, the number of elements currently on the stack. An empty stack has $\Phi = 0$. Potential is always non-negative. The amortised costs work out as:

|Operation|$\Phi(D_i) - \Phi(D_{i-1})$|Actual Cost|Amortised Cost|
|---|---|---|---|
|push|$+1$|1|$1 + 1 = 2$|
|pop|$-1$|1|$1 - 1 = 0$|
|multipop(k)|$-k'$|$k'$|$k' - k' = 0$|

All three methods agree: every operation costs $O(1)$ amortised, so $n$ operations cost $O(n)$ in total.

### Case Study: Dynamic Stack (Expansion and Contraction)
A dynamic stack does not have a fixed size. Instead it tracks a **load factor** defined as elements currently stored divided by total available capacity. Two rules govern when it resizes:
- **Expansion:** when load factor hits 1 (stack completely full), double the capacity. After doubling, load factor drops to $1/2$.
- **Contraction:** when load factor drops to $1/4$ (three quarters of the array is empty), halve the capacity. After halving, load factor rises back to $1/2$.

The threshold for contraction is $1/4$ rather than $1/2$ deliberately. If you contracted at $1/2$, a sequence of alternating push and pop right at the boundary would trigger a resize on every single operation. The gap between the expansion threshold ($1$) and the contraction threshold ($1/4$) ensures at least $n/2$ cheap operations must happen before another expensive resize is possible.

#### DynamicStack Class
```cpp
template<typename T>
class DynamicStack
{
public:
    DynamicStack(); // constructor
    ~DynamicStack(); // destructor

    DynamicStack( const DynamicStack& ) = delete;            // not copyable
    DynamicStack& operator=( const DynamicStack& ) = delete; // not copyable

    std::optional<T> top() const noexcept;
    void push( const T& aValue );
    void pop();

private:
    T* fElements;
    size_t fStackPointer;
    size_t fCapacity;

    void resize( size_t aNewCapacity );
    void ensure_capacity();
    void adjust_capacity();
};
```
- `template<typename T>` makes the stack generic so it works with any type, whether `int`, `std::string`, or a custom struct.
- `T* fElements` is a raw pointer to a heap-allocated array. Unlike the fixed stack where the array size is baked in at compile time with `T fElements[N]`, here the pointer can be redirected to a new larger or smaller array at runtime when resizing happens.
- `size_t fStackPointer` tracks how many elements are currently on the stack. It doubles as the index of the next free slot.
- `size_t fCapacity` tracks the total number of slots currently allocated. The load factor at any point is `fStackPointer / fCapacity`.
- `DynamicStack()` is the constructor, which sets `fElements` to a small initial array, `fStackPointer` to 0, and `fCapacity` to the initial size.
- `~DynamicStack()` is the destructor, which calls `delete[] fElements` to free the heap array when the object goes out of scope. Without this every resize would leak the old array and the final array would never be freed.
- `= delete` on the copy constructor and copy-assignment operator prevents copying entirely. If copying were allowed, both the original and the copy would hold a pointer to the same heap array. When one is destroyed it would free the array the other still needs, causing a crash.
- `resize`, `ensure_capacity`, and `adjust_capacity` are private because they are internal implementation details. No code outside the class should be triggering resizes directly.

#### ensure_capacity (expansion)
Called before every push. If the stack is full (load factor = 1), double the capacity.
```cpp
void ensure_capacity()
{
    // is load factor 1?
    if ( fStackPointer == fCapacity )
        resize( fCapacity * 2 );
}
```

#### adjust_capacity (contraction)
Called after every pop. If load factor drops to $1/4$, halve the capacity.
```cpp
void adjust_capacity()
{
    // is load factor 1/4?
    if ( fStackPointer <= fCapacity / 4 )
        resize( fCapacity / 2 );
}
```

#### resize
Starts with a safety check that the new capacity is large enough to hold the current content. The existing elements are moved (not copied) into the new array using `std::move`, which transfers ownership rather than duplicating data. The old array is then freed and the member variables updated. The running time is $O(n)$; the for-loop dominates.
```cpp
void resize( size_t aNewCapacity )
{
    assert( fStackPointer <= aNewCapacity ); // safety check

    T* lNewElements = new T[aNewCapacity];

    for ( size_t i = 0; i < fStackPointer; i++ )
        lNewElements[i] = std::move( fElements[i] ); // move, not copy

    delete[] fElements;         // free old storage
    fElements = lNewElements;
    fCapacity = aNewCapacity;
}
```

#### push and pop with resizing
```cpp
void push( const T& aValue )
{
    ensure_capacity();                    // expand if needed
    fElements[fStackPointer++] = aValue;
}

void pop()
{
    assert( fStackPointer > 0 );
    fStackPointer--;
    adjust_capacity();                    // contract if needed
}
```

#### Amortized Analysis of push
The cost of the $i$-th push is:
$$c_i = \begin{cases} i & \text{if } i - 1 \text{ is a power of 2 (expansion triggered)} \ 1 & \text{otherwise} \end{cases}$$

**Aggregate analysis:** expansions happen only at pushes $2, 3, 5, 9, 17, \ldots$ Each expansion at position $2^k + 1$ costs $2^k$ to move the existing elements. The total cost of all $n$ pushes is:
$$\sum_{i=1}^{n} c_i = n + \sum_{k=0}^{\lfloor \log_2 n \rfloor} 2^k < n + 2n = 3n$$
Since the total is less than $3n$, the amortised cost per push is $\Theta(1)$, even though individual pushes can cost $\Theta(n)$ when expansion occurs.

**Accounting view:** charge each push 3 credits. 1 pays for the push itself. 1 is saved on the new element to pay for moving it in the next expansion. 1 is donated to an older element in the bottom half of the array, which arrived in a previous expansion and has no saved credit of its own. When the next expansion fires, every element has exactly 1 credit, so the entire move is paid for.

**Potential view:** define $\Phi(D_i) = 2 \cdot \text{fStackPointer} - \text{fCapacity}$. Right after an expansion $\Phi = 0$. As elements are pushed $\Phi$ grows. Right before the next expansion $\Phi = \text{fCapacity}$, which exactly covers the move cost. The amortised cost of every push is 3 in both cases. A symmetric argument gives $\Theta(1)$ amortised for pop with contraction.
### Key Takeaways
1. Amortized analysis finds the worst-case average cost per operation over a sequence, with no probability assumptions.
2. The aggregate method is simplest: compute total cost, divide by $n$.
3. The accounting method assigns per-operation credits; the potential method assigns energy to the whole structure. Both must ensure the stored credit or potential never goes negative.
4. All three methods often yield the same amortised costs, just arrived at differently.
5. Dynamic resizing with doubling on expansion and halving at load factor $1/4$ gives $\Theta(1)$ amortised push and pop.

**Remember this:** amortized analysis asks "what is the worst-case average cost per operation across the whole sequence?" not "what is the worst single operation?"

**One analogy to remember it by:** amortized analysis is like a shared taxi fare. One person lives far away and the ride costs more to reach them, but splitting the total fare equally across all passengers gives everyone a fair and predictable cost per trip.