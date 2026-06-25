## Association Rules

### What are Association Rules?
**Association rules** are an unsupervised, descriptive learning method used to discover hidden co-occurrence patterns (relationships) in large transaction datasets. Rules take the form $X \rightarrow Y$: when itemset $X$ appears in a transaction, itemset $Y$ tends to appear alongside it.
![[Pasted image 20260506145710.png]]
In simple terms: they answer "what things tend to get bought together?"

Think of it like: a supermarket manager reviewing millions of receipts and noticing that nappies and beer appear in the same basket far more often than chance would predict.

Why it matters: association rules power product recommendation systems, store layout decisions, cross-selling strategies, and even medical diagnosis support linking symptoms to treatments.

### Core Terminology
An **itemset** is a collection of items that co-occur in a transaction. A k-itemset contains exactly $k$ items, for example $\{\text{Milk, Diaper, Beer}\}$ is a 3-itemset.
![[Pasted image 20260506145733.png]]
A **transaction** is a single record such as one shopping basket. A **frequent itemset** is one that appears in at least a predefined minimum fraction of all transactions.

Rules have a non-overlapping **antecedent** ($X$) and **consequent** ($Y$):
$$\{\text{Milk, Diaper}\} \rightarrow \{\text{Beer}\}$$

Generating strong rules is a two-step process: first find all frequent itemsets meeting a minimum **support** threshold, then derive high-confidence (strong) rules from those itemsets that also meet a minimum **confidence** threshold.

### Rule Evaluation Metrics
Four metrics are used together to find rules that are frequent, trustworthy, and genuinely meaningful rather than coincidental.

#### Support
**Support** is the proportion of all transactions containing both $X$ and $Y$.
$$\text{Support}(X \rightarrow Y) = \frac{\text{transactions containing } X \cup Y}{\text{total transactions}}$$

Support filters out rare, unreliable combinations. A support of 40% means 40% of all transactions contain both sides of the rule.

#### Confidence
**Confidence** is the proportion of transactions containing $X$ that also contain $Y$.
$$\text{Confidence}(X \rightarrow Y) = \frac{\text{Support}(X \cup Y)}{\text{Support}(X)}$$
Confidence measures trustworthiness but has a known limitation: it cannot detect coincidence. A high confidence like 70% for $\{\text{Toothbrush}\} \rightarrow \{\text{Milk}\}$ might exist simply because milk is bought by nearly everyone, not because toothbrushes drive milk purchases.

#### Lift
**Lift** corrects for coincidence by measuring how much more often $X$ and $Y$ co-occur than expected under **statistical** independence.
$$\text{Lift}(X \rightarrow Y) = \frac{\text{Support}(X \cup Y)}{\text{Support}(X) \times \text{Support}(Y)}$$
Lift = 1 means statistically independent. Lift > 1 means a genuine positive association; larger values mean **stronger** links.

#### Leverage
**Leverage** is the absolute difference between observed co-occurrence and the co-occurrence expected under independence.
$$\text{Leverage}(X \rightarrow Y) = \text{Support}(X \cup Y) - \text{Support}(X) \times \text{Support}(Y)$$
Leverage = 0 means independent. Lift and leverage generally agree on ranking; using both together gives a more complete picture.

#### Worked Comparison
**Given:** 1000 transactions. $\{\text{milk, eggs}\}$ appears in 300, $\{\text{milk}\}$ in 500, $\{\text{eggs}\}$ in 400. $\{\text{milk, bread}\}$ appears in 400, $\{\text{bread}\}$ in 400.

| Metric | Milk to Eggs | Milk to Bread |
|---|---|---|
| Confidence | $0.3/0.5 = 0.6$ | $0.4/0.5 = 0.8$ |
| Lift | $0.3/(0.5 \times 0.4) = 1.5$ | $0.4/(0.5 \times 0.4) = 2.0$ |
| Leverage | $0.3 - (0.5 \times 0.4) = 0.1$ | $0.4 - (0.5 \times 0.4) = 0.2$ |

All three metrics confirm milk and bread have a stronger genuine association than milk and eggs. Support and confidence find frequent, trustworthy rules; lift and leverage additionally filter out coincidental ones.

### The Apriori Algorithm
https://www.youtube.com/watch?v=zi_ydmbWfAs
#### Apriori Property
![[Pasted image 20260506232736.png]]
The **Apriori property** (also called the downward closure property) states that if an itemset is frequent, every supersets of it must also be frequent. Conversely, if an itemset is infrequent, every superset is also infrequent and can be pruned without scanning.

This makes the algorithm efficient: it cuts entire branches of the search space the moment any subset fails the support threshold. For example, if $\{A, B\}$ is infrequent, then $\{A,B,C\}$, $\{A,B,D\}$, and so on are all pruned immediately.

#### Level-Wise Search
Apriori employs an iterative approach known as a level-wise search, where $k$-itemsets are used to explore $(k+1)$-itemsets.

Lets use the Youtube video link up there:
![[Pasted image 20260507000551.png]]
First, the set of frequent 1-itemsets is found by scanning the database to count each item and collecting those meeting minimum support (in this case 40% = 2 items), denoted $L_1$.
![[Pasted image 20260507000653.png]]
Next, $L_1$ is used to find $L_2$, which is used to find $L_3$, and so on, until no more frequent itemsets can be found. Each $L_k$ requires exactly one full database scan.

![[Pasted image 20260506234919.png]]
In detail, $C_k$ refers to **candidate itemsets** ("potential" frequent itemsets that haven't been confirmed yet) of size $k$, and $L_k$ refers to the confirmed **frequent itemsets** of size $k$. Each round has 4 steps:
1. **Find:** Start with $L_{k-1}$ from the previous round (begin at $L_1$ prev frequent itemset)
2. **Join:** Generate $C_k$ by computing the Cartesian product $L_{k-1} \times L_{k-1}$. This means pairing every combination of itemsets from $L_{k-1}$ to produce larger candidates of size $k$. For example, from $L_1 = {I1, I2, I3}$
	1. ${I1}+{I2} \rightarrow {I1,I2}$
	2. ${I1}+{I3} \rightarrow {I1,I3}$
	3. ${I2}+{I3} \rightarrow {I2,I3}$
3. **Prune:** Drop any candidate in $C_k$ whose $(k-1)$-size subsets are not all in $L_{k-1}$
4. **Scan:** Count surviving candidates in the database, keep those meeting min support → $L_k$

#### Worked Example (Example 3 from Lecture)
**Given:** 5 transactions, min support count = 2, min confidence = 50%.

| TID | Items |
|---|---|
| 100 | I1, I2 |
| 200 | I2, I3, I4, I5 |
| 300 | I2, I3 |
| 400 | I1 |
| 500 | I1, I2, I3 |

- **Step 1 - Generate L1:** Scan gives I1:3, I2:4, I3:3, I4:1, I5:1. I4 and I5 fail min support and are dropped. $L_1 = \{I1, I2, I3\}$.
- **Step 2 - Generate C2:** Join $L_1 \times L_1$ gives $\{I1,I2\}$, $\{I1,I3\}$, $\{I2,I3\}$.
- **Step 3 - Generate L2:** Scan gives $\{I1,I2\}$: 2, $\{I1,I3\}$: 1 (dropped), $\{I2,I3\}$: 3. $L_2 = \{\{I1,I2\}, \{I2,I3\}\}$.
- **Step 4 - Generate C3:** Join $L_2 \times L_2$ produces $\{I1,I2,I3\}$. Scan gives count 1, which fails min support. $L_3$ is empty; algorithm terminates.

**Rule generation from L2:** For $\{I1,I2\}$, generate all non-empty subset splits and calculate confidence using $\text{Confidence} = \text{Support}(X \cup Y) / \text{Support}(X)$:
- $\{I2\} \rightarrow \{I1\}$: $2/4 = 50\%$, accepted
- $\{I1\} \rightarrow \{I2\}$: $2/3 = 66\%$, accepted

For $\{I2,I3\}$:
- $\{I2\} \rightarrow \{I3\}$: $3/4 = 75\%$, accepted
- $\{I3\} \rightarrow \{I2\}$: $3/3 = 100\%$, accepted

All four rules pass the 50% minimum confidence threshold.

#### Worked Example (Example 4 from Lecture)
**Given:** 9 transactions (T100-T108), min support count = 2, min confidence = 70%. After running Apriori, $L_3 = \{\{I1,I2,I3\}, \{I1,I2,I5\}\}$.

Taking $\{I1,I2,I5\}$ and evaluating all rules:

| Rule | Confidence | Decision |
|---|---|---|
| $\{I1,I2\} \rightarrow \{I5\}$ | $2/4 = 50\%$ | Rejected |
| $\{I1,I5\} \rightarrow \{I2\}$ | $2/2 = 100\%$ | Accepted |
| $\{I2,I5\} \rightarrow \{I1\}$ | $2/2 = 100\%$ | Accepted |
| $\{I1\} \rightarrow \{I2,I5\}$ | $2/6 = 33\%$ | Rejected |
| $\{I2\} \rightarrow \{I1,I5\}$ | $2/7 = 29\%$ | Rejected |
| $\{I5\} \rightarrow \{I1,I2\}$ | $2/2 = 100\%$ | Accepted |

Three strong rules are found from this one frequent itemset.

### Applications
The primary domain is **market basket analysis**: which products are bought together, informing shelf placement, cross-selling promotions, and inventory decisions. For example, "customers who bought a laptop and virus protection software also bought an extended service plan 70% of the time" can drive bundled pricing.

Beyond retail, association rules appear in recommender systems (Amazon, Netflix), medical diagnosis support (linking symptoms to treatments), and genomics (linking genes to functions).

### Validation and Diagnostics
Even after filtering by all four metrics, some surviving rules may be subjectively useless because they yield no unexpected insight, for example $\{\text{paper}\} \rightarrow \{\text{pencil}\}$. Filtering for truly actionable rules requires **domain expertise**.

A practical issue with Apriori is that minimum support must be set before execution. Too high produces too few rules; too low produces an unmanageable number. Computationally, each level requires a full database scan, so runtime grows with data size. Efficiency improvements include partitioning (run on database partitions then combine), sampling (run on a subset with a lower threshold), transaction reduction (drop transactions with no frequent k-itemsets in later scans), hash-based itemset counting, and dynamic itemset counting.

### Key Takeaways
1. Association rules are unsupervised and descriptive, not predictive: they discover what co-occurs, not why.
2. Support filters for frequency; confidence filters for trustworthiness; lift and leverage filter for genuine association beyond coincidence.
3. The Apriori property prunes the search space: any superset of an infrequent itemset is infrequent and can be skipped entirely.
4. Rule generation enumerates all non-empty subset splits of each frequent itemset, then filters by minimum confidence.
5. Domain expertise is needed to distinguish mathematically strong rules from practically useless ones.

**Remember this:** association rule mining finds what co-occurs frequently, then checks whether that co-occurrence is strong, genuine, and not just a coincidence.

**One analogy to remember it by:** Apriori is like building a pyramid: you only lay the next layer of bricks if every brick beneath is solid (frequent). The moment a lower brick is missing, every structure above it collapses and you stop testing.
