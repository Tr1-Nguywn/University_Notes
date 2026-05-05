# COS30008 - Tools for Algorithm Analysis & Amortized Analysis

## Part 1: Computability and the Limits of Algorithms_

### What is Computability?
**Computability** is the study of what problems can and cannot be solved by a formal computational process.

In simple terms: some problems can be solved by a program in finite time and resources, and some simply cannot.

Think of it like: a vending machine that always dispenses something given a valid input. But some requests (like "give me tomorrow's weather") are outside what the machine can ever do, no matter how sophisticated it gets.

Why it matters: before spending effort designing an algorithm, it is essential to know whether a solution even exists. Many real-world problems, like detecting all software bugs in an arbitrary program, are provably unsolvable.

### The Abstract Machine Model
**Computation** is modelled as a mapping from inputs to outputs, carried out by a machine or program that processes its input in a sequence of steps. A function is called **effectively computable** if it can be computed in a finite amount of time using finite resources.
![[Pasted image 20260426170928.png]]

Many problems are **intractable**: no efficient algorithm exists for them. While most have a brute-force solution, that approach is computationally infeasible for anything beyond the smallest inputs. Classic examples include the traveling salesman problem and bin packing.

### The Turing Machine
A **Turing machine** is the canonical abstract model of a computing device. It consists of a read/write head that scans a potentially infinite one-dimensional tape divided into squares, each inscribed with symbols. The machine operates in states, reading and writing symbols, then moving left or right until it reaches a halt state.

Formally, a Turing machine is a septuple (7 tuple) $(Q, \Gamma, \gamma, \Sigma, q_0, F, \sigma)$ where:
- $Q$ is a set of states
- $\Gamma$ is a finite non-empty set of tape symbols
- $\gamma \in \Gamma$ is the blank symbol
- $\Sigma \subseteq \Gamma \setminus {\gamma}$ is the set of input symbols ($\Sigma$ is a subset of $\Gamma$ exclude $\gamma$)
- $q_0$ is the start state
- $F \subseteq Q$ is the set of accepting states
- $\sigma: (Q \setminus F) \times \Gamma \to Q \times \Gamma \times {R, L}$ is the transition function
The machine halts either when it enters an accepting state or when $\sigma$ is undefined for the current configuration.

### Addition With Turing Machine
![[Pasted image 20260426174122.png|517]]
Numbers are encoded in **unary**: 5 = `1 1 1 1 1`, 3 = `1 1 1`, separated by `+`. The tape starts as `1 1 1 1 1 + 1 1 1` and the goal is to produce `1 1 1 1 1 1 1 1`.

The machine works by repeatedly shifting the `+` one position rightward into the second group, overwriting it with a `1` each time. Once the `+` reaches the end of the second group, it gets erased and the machine halts. The two unary groups are effectively merged into one.

Each table cell reads as `nextState/"write"/direction`. For example, `2/"1"/R` means: transition to state 2, write `"1"`, move Right.
### Church's Thesis and the Halting Problem
**Church's Thesis** states that it is impossible to build a computing device more powerful than a Turing machine. This cannot be proven mathematically, since "effectively computable" is an intuitive rather than a formal notion. It can only be refuted by demonstrating a machine that solves a problem no Turing machine can. So far, no such counterexample has been found.

The **Halting Problem** asks: given an arbitrary Turing machine and its input tape, will the machine eventually halt? This is provably **uncomputable**, meaning no algorithm can decide it in general. Under Church's Thesis, this means no real computer can solve it either.

### The Ackermann Function
The **Ackermann function** is a computable* but not primitive recursive function, meaning it cannot be expressed using only bounded loops (for-loops) . It is defined as:
$$A(0, n) = n + 1$$
$$A(m+1, 0) = A(m, 1)$$
$$A(m+1, n+1) = A(m, A(m+1, n))$$
Its growth is extraordinary: $A(4, 2)$ is a number with 19,729 decimal digits. This illustrates that Turing computability does not imply practical computability.
![[Pasted image 20260426182809.png]]
![[Pasted image 20260426183042.png]]
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

**Worked example:** computing $\sum_{i=1}^{n} i^3$ via a loop:
![[Pasted image 20260426190426.png]]
$$T(n)=0+1+1+(n+1)+n+4n+1=6n+4$$
The same sum can be computed in $O(1)$ using the closed form $\left(\frac{n(n+1)}{2}\right)^2$, illustrating that algorithmic insight can collapse linear cost to constant.

### General Counting Rules
- **For-loop:** running time is at most the body cost $C$ times the number of iterations, giving $O(n)$.
- **Nested for-loop:** running time multiplies by each loop's size. A $k$-nested loop has cost $O(n^k)$.
- **Statement sequence:** total cost is the sum of each statement's cost; the dominant term determines asymptotic class.
- **Branching (if-else):** cost is at most the condition cost plus the larger of the two branches. This may overestimate but never underestimates.

### Towers of Hanoi: An Exponential Example
https://www.youtube.com/watch?v=rf6uf3jNjbo
![[Pasted image 20260426220130.png]]
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

Let $\mathcal{F}_n$ denote the set of all inputs of size $n$, and $\tau(I)$ the number of basic operations on input $I$ which is is a single specific input from the set $\mathcal{F}_n$ ($I \in \mathcal{F}_n$).

*read intro of part 4 for better understanding*

### Best-Case Complexity
$$B(n) = \min{\tau(I) \mid I \in \mathcal{F}_n}$$
*Best case of input size n = the minimum number of basic operations over all possible inputs I of size n*

Best-case complexity identifies the most favorable input. It is easy to compute but of limited use on its own; it serves mainly as a lower bound when bounding average complexity.

### Worst-Case Complexity
$$W(n) = \max{\tau(I) \mid I \in \mathcal{F}_n}$$
*Worst case of input size n = the maximum number of basic operations over all possible inputs I of size n*

Worst-case complexity is the most practically useful measure. It guarantees the algorithm never exceeds $W(n)$ operations, enabling reliable performance guarantees. This matters especially when deadlines must be met, as in real-time systems.

### Average-Case Complexity
$$A(n) = \sum_{I \in \mathcal{F}_n} \tau(I) \cdot p(I) = E[\tau]$$
*For every possible input $I$ of size $n$, multiply its operation count $\tau(I)$ by how likely it is to appear $p(I)$, then sum everything up*.

More frequent inputs contribute more to the average than rare ones. $E[\tau]$ is the **expected value** of $\tau$, a statistics term meaning the long-run average operation count if you ran the algorithm on randomly drawn inputs many times. 

A practical equivalent groups inputs by their operation count instead:
$$A(n) = \sum_{i=1}^{W(n)} i \cdot p_i$$
*For every possible operation count $i$ (ranging from 1 up to the worst case $W(n)$), multiply $i$ by $p_i$, the probability that the algorithm takes exactly $i$ steps on a random input, then sum everything up.*

Instead of thinking about individual inputs, you are thinking about outcomes. Multiple inputs can share the same operation count, so their probabilities are pooled into a single $p_i$​: the combined probability that the algorithm takes exactly ii i steps regardless of which specific input caused it. Both formulas give the same result, just from different angles. The first sums over inputs, the second sums over possible costs.

### Bubble Sort as a Case Study
![[Bubble-sort-example-300px.gif]]
Bubble Sort (Knuth's optimised version) illustrates all three scenarios:
![[Pasted image 20260503130708.png]]

**Worst case** (reverse-sorted input): every comparison triggers a swap and the outer loop runs fully.
$$T^W_{\text{BubbleSort}}(N) = c_1 + c_2 n + (c_3+c_4)(n-1) + c_5\left(\frac{n(n+1)}{2}-1\right) + (c_6+c_7+c_8+c_9)\frac{n(n-1)}{2}$$
The quadratic terms dominate: $W(n) = O(n^2)$.

**Best case** (already sorted): no swaps occur; the inner loop runs once and exits because $t$ stays 0.
$$T^B_{\text{BubbleSort}}(N) = c_1 + c_2 n + (c_3+c_4)(n-1) + c_5 n + (c_6+c_9)(n-1)$$
The linear term dominates: $B(n) = \Omega(n)$.

Donald Knuth famously concluded that Bubble Sort has nothing to recommend it except a catchy name.
![[Insertion-sort-example.gif]]
Insertion Sort, also $O(n^2)$ and $\Omega(n)$, consistently outperforms it in practice.

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

### Growth Rate of f(n) and g(n)
![[Pasted image 20260503175252.png]]
When comparing two algorithms with running times $T(n) = f(n)$ and $T(n) = g(n)$, one may be faster for small inputs but slower for large ones. 

The relevant question is behaviour for large $n$, specifically for all $n \geq n_0$ where $n_0$ is the crossover point. Beyond $n_0$, whichever function grows more slowly is the better algorithm regardless of any initial advantage the other may have had.

This is why we care about growth rate rather than raw operation counts, and why the next step is to formalise how we express and compare those growth rates.

### Conventions on Notation
To express growth rates precisely, we use asymptotic notations $\Theta$, $O$, $\Omega$.

Technically these are set-valued functions: $\Theta(n^2)$ is the set of all functions that grow at the same rate as $n^2$. Formally correct notation would be $(3n^2 + 2) \in \Theta(n^2)$, read as "3n squared plus 2 is theta of n squared".

However, the standard convention is to write $(3n^2 + 2) = \Theta(n^2)$, which is not mathematically precise but is universally accepted. The goal is always to use the simplest and most representative function inside the notation, which means stripping all coefficients and lower-order terms.

### Why Lower-Order Terms and Constants Are Ignored
This stripping is justified for two reasons:
- Lower-order terms like initialisation and loop overhead become negligible as $n$ grows large because the dominant term grows so much faster that the rest barely matters. 
	- For example in $4n^2 + 3n + 1$, at $n = 1000$ the $4n^2$ term is 4,000,000 while $3n + 1$ is only 3,001, so it is effectively noise and gets dropped. 
- Constant multipliers are ignored because they depend on the machine:
	- A faster CPU might turn $4n^2$ into $2n^2$, a slower one into $8n^2$, but the shape of how cost grows with $n$ stays the same regardless.

That shape is what we actually care about, so the coefficient goes too, leaving just $n^2$ and giving $T(n) = O(n^2)$.

### Worked Example: f(t) = 5 + sin(t)
![[Pasted image 20260503184929.png]]
To see these ideas applied to a non-obvious case, consider $f(t) = 5 + \sin(t)$ and $g(t) = 1$. Because $\sin(t)$ is bounded between $-1$ and $1$, we have $4 \leq 5 + \sin(t) \leq 6$ for all $t$. 
This means there exist constants $c_1 = 4$ and $c_2 = 6$ for $g(t)$ such that:
- $c_1 \cdot g(t) \leq f(t) \leq c_2 \cdot g(t)$
- $=>f(t) = \Theta(1)$

It is also true that $f(t) = O(1)$ and $f(t) = \Omega(1)$, but $\Theta$ is the best choice because it communicates both bounds simultaneously. When both bounds are known and equal, always prefer $\Theta$ over $O$ or $\Omega$ alone.

### Key Takeaways
1. Best-case is a lower bound; worst-case is an upper bound; average-case describes typical behaviour.
2. Worst-case is the most practically important for algorithm selection and real-time guarantees.
3. Two algorithms with the same asymptotic class can differ significantly in practice (Bubble Sort vs Insertion Sort).
4. The same algorithm can behave very differently depending on input order.

**Remember this:** always ask which case matters for your application: if you need a deadline guarantee, use worst-case; if you care about typical loads, use average-case.

**One analogy to remember it by:** best-case is a tailwind, worst-case is a headwind, and average-case is the usual commute. You plan for headwinds, but you budget for the commute.

---

## Part 4: Asymptotic Analysis (Detailed)

### What is Asymptotic Analysis?
**Asymptotic (tiệm cận) analysis** characterises the growth rate of an algorithm's running time $T(n)$ as $n \to \infty$, ignoring machine-dependent constants and lower-order terms.

In simple terms: we care about the shape of the cost curve as input gets large, not the exact number of operations for small inputs.

Think of it like: comparing two companies' revenue trajectories. One growing at $n^2$ per year will eventually dominate one growing at $1000n$, regardless of the initial gap.

Why it matters: asymptotic notation gives a machine-independent, input-independent characterisation of an algorithm that enables fair comparison across implementations and hardware.

### The Three Asymptotic Notations
![[Pasted image 20260503192553.png]]
**Big-Theta $\Theta$:** tight bound. $f(n) = \Theta(g(n))$ if and only if there exist positive constants $c_1$, $c_2$, and $n_0$ such that:
$$c_1 g(n) \leq f(n) \leq c_2 g(n) \quad \text{for all } n \geq n_0$$
This means $f$ and $g$ grow at the same rate. $\Theta$ is the most informative notation and should be used when both bounds are known.

![[Pasted image 20260503192604.png]]
**Big-Oh $O$:** upper bound. $f(n) = O(g(n))$ if and only if there exist positive constants $c$ and $n_0$ such that:
$$f(n) \leq c \cdot g(n) \quad \text{for all } n \geq n_0$$

![[Pasted image 20260503192618.png]]
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
**Amortized analysis** averages the time required to perform a sequence of operations over all operations in that sequence, even when individual operations have wildly different costs.

In simple terms: one expensive operation doesn't mean every operation is expensive; the cost is spread (amortised) across the whole sequence.

Think of it like: a gym membership. Most days you pay $0 upfront and simply show up, but on joining day you pay the annual fee. Your per-visit cost amortised over a year is low, even though day one was expensive.

Why it matters: naive worst-case analysis of individual operations can produce a loose bound that overstates the true cost of a sequence. Amortized analysis gives a tighter, guaranteed average per-operation cost in the worst case, without assuming anything about probability.

### The Three Methods

#### Aggregate Method

In the aggregate method, we compute the total worst-case cost $T(n)$ of a sequence of $n$ operations, then divide by $n$ to get the amortized cost per operation.

$$\text{amortized cost} = \frac{T(n)}{n}$$

This assigns the same amortized cost to every operation in the sequence.

#### Accounting Method

The accounting method assigns each operation an **amortized cost** that may differ from its actual cost. Operations charged more than their actual cost accumulate **credit** on data structure elements. Future expensive operations draw on this stored credit. The key constraint is that the total credit must never go negative, ensuring the amortized cost is always an upper bound on actual cost.

#### Potential Method

The potential method models prepaid work as **potential energy** $\Phi(D_i)$ associated with the entire data structure after the $i$-th operation. The amortized cost of the $i$-th operation is:

$$\hat{c}_i = c_i + \Phi(D_i) - \Phi(D_{i-1})$$

For the total amortized cost to be an upper bound on actual cost, we require $\Phi(D_i) \geq \Phi(D_0)$ for all $i$. Starting with $\Phi(D_0) = 0$ and maintaining non-negative potential suffices.

### Case Study: Stack with Multipop

A stack supports push, pop, top (each $O(1)$) and a `pop(k)` operation that removes the top $k$ elements, costing $O(\min(|s|, k))$.

Naively: `pop(k)` costs $O(n)$, so $n$ operations could cost $O(n^2)$. But this bound is not tight.

**Aggregate analysis:** each element can be popped at most once per push. Therefore, across any sequence of $n$ push/pop/top/pop(k) operations starting from an empty stack, total pops equal total pushes, which is at most $n$. Total cost is $O(n)$; amortized cost per operation is $O(1)$.

**Accounting analysis:** assign amortized costs as:

|Operation|Actual Cost|Amortized Cost|
|---|---|---|
|push|1|2|
|pop|1|0|
|top|1|1|
|pop(k)|$k' = \min(\|s\|, k)$|0|

Each push deposits 1 unit of credit on the pushed element. Pop and pop(k) spend that credit, so their amortized cost is 0. Total credit never goes negative.

**Potential analysis:** define $\Phi(D_i) = |s|$ (number of elements on the stack). Starting empty gives $\Phi(D_0) = 0 \leq \Phi(D_i)$ always.

|Operation|$\Phi(D_i) - \Phi(D_{i-1})$|Actual Cost|Amortized Cost|
|---|---|---|---|
|push|$+1$|1|2|
|pop|$-1$|1|0|
|top|$0$|1|1|
|pop(k)|$-k'$|$k'$|0|

All amortized costs are $O(1)$, so $n$ operations cost $O(n)$ in total.

### Case Study: Dynamic Stack (Expansion and Contraction)

A dynamic stack resizes automatically using a **load factor** (elements stored / available capacity):

- **Expansion:** when load factor reaches 1, double capacity. After expansion, load factor is $\geq 1/2$.
- **Contraction:** when load factor drops to $1/4$, halve capacity. After contraction, load factor is $\geq 1/2$.

This policy ensures wasted space never exceeds half the total allocation.

The cost of the $i$-th push is:

$$c_i = \begin{cases} 2^k + 1 & \text{if } i = 2^k + 1 \text{ (expansion triggered)} \ 1 & \text{otherwise} \end{cases}$$

Total cost of $n$ pushes:

$$\sum_{i=1}^{n} c_i = n + \sum_{k=0}^{\lfloor \log_2 n \rfloor} 2^k = n + 2n - 1 < 3n$$

Since the total is less than $3n$, the amortized cost of a single push is $3 = O(1)$, even though individual pushes can cost $O(n)$ when expansion occurs. A symmetric argument applies to pop with contraction: the amortized cost is also $O(1)$.

### Key Takeaways

1. Amortized analysis averages cost over a sequence; it guarantees worst-case average performance without probability assumptions.
2. The aggregate method is simplest: total cost divided by number of operations.
3. The accounting method assigns credits to operations; the potential method stores energy in the structure itself.
4. Dynamic resizing with doubling on expansion and halving at load factor $1/4$ gives $O(1)$ amortized push and pop.

**Remember this:** amortized analysis asks "how expensive is each step on average, in the worst case?" not "what is the worst single step?"

**One analogy to remember it by:** amortized analysis is like a coffee loyalty card. Most coffees cost $5, but every tenth is free. The amortized cost per coffee is $4.50 regardless of which specific coffee triggered the free one.