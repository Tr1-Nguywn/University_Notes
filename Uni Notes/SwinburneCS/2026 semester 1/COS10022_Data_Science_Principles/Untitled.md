# COS10022 Week 6 - Decision Trees, Ensemble Learning, and Association Rules

---

## Part 1: Decision Trees

_Reference: Han, Pei & Kamber (2011) Data Mining: Concepts and Techniques; COS10022 Week 6 Online Session_

---

### What is a Decision Tree?

A **decision tree** is a supervised learning model that predicts or classifies outcomes based on input features by recursively splitting a dataset into progressively purer subsets.

In simple terms: it is a flowchart-like structure where each internal node asks a question about a feature, each branch represents an answer, and each leaf holds a final prediction.

Think of it like: a game of Twenty Questions where every answer narrows down the possibilities until only one remains.

Why it matters: decision trees are one of the most interpretable ML models and form the backbone of powerful ensemble methods like Random Forest and gradient boosting, which appear everywhere in industry and competitions.

### Node Types

A decision tree is built from three kinds of nodes. The **root node** is the top-level split representing the most informative feature in the dataset. **Decision nodes** (also called internal nodes) sit in the middle and represent further splits on features. **Leaf nodes** (also called terminal nodes) sit at the bottom and contain the final output class or value. Every path from root to leaf encodes a decision rule.

### Splitting Criteria

The central challenge in building a tree is deciding which feature to split on at each node. The goal is to produce child subsets that are as **pure** as possible, meaning they contain predominantly one class. Three main criteria are used.

#### Entropy and Information Gain (ID3)

**Entropy** measures the level of uncertainty or impurity in a dataset. A node containing only one class has entropy 0 (perfectly pure), while a node with an even mix of classes has maximum entropy.

$$H(p) = -\sum_{i=1}^{n} p_i \log_2 p_i$$

where $p_i$ is the proportion of class $i$ in the node.

**Example:** A node with 7 positives and 6 negatives out of 13 samples gives:

$$H = -\frac{7}{13}\log_2\frac{7}{13} - \frac{6}{13}\log_2\frac{6}{13} \approx 0.995$$

A node with 13 positives and 0 negatives gives $H = 0$ (pure).

**Information Gain** is then the reduction in entropy achieved by splitting on a feature. It is calculated as the difference between the original entropy of the parent node and the weighted average entropy of the child nodes after the split.

$$\text{Gain} = H(\text{parent}) - \sum_i w_i H(\text{child}_i)$$

The **ID3** algorithm selects the feature with the highest information gain at each step. However, ID3 is biased toward features with many distinct values. For example, splitting on Student ID gives every leaf a single sample, so entropy in each child is 0 and information gain is maximised, but the split is useless for generalisation.

#### Gain Ratio (C4.5)

**Gain Ratio** is an extension of information gain that corrects for the bias toward high-cardinality features. It normalises information gain by the split information, which penalises features that produce many small branches.

$$\text{Gain Ratio} = \frac{\text{Gain}}{\text{SplitInfo}}$$

The **C4.5** algorithm uses gain ratio, making it more reliable when features vary widely in the number of distinct values.

#### Gini Index (CART)

The **Gini Index** measures impurity using squared probabilities rather than logarithms, making it computationally cheaper.

$$\text{Gini} = 1 - \sum_i p_i^2$$

A Gini of 0 means the node is pure. The **CART** algorithm selects splits that minimise the weighted Gini index across child nodes. It has no obvious bias toward high-cardinality features.

|Aspect|ID3|C4.5|CART|
|---|---|---|---|
|Split Criterion|Information Gain|Gain Ratio|Gini Index|
|Core Idea|Maximise information gain|Correct bias of information gain|Minimise impurity|
|Bias Problem|Biased toward high-cardinality features|Corrected|No obvious bias|
|Formula|Gain = H(parent) - sum of weighted child entropies|Gain / SplitInfo|Gini = 1 - sum of squared probabilities|

### Handling Numerical Features

Categorical features split naturally into one branch per value. Numerical features are continuous, so the tree uses **binary splitting**: it tests a threshold (e.g. Age < 30) and places each record in the left or right branch. The threshold is typically chosen as the midpoint between adjacent sorted values that best reduces impurity.

### Tree Construction Process

Building a tree proceeds recursively. At each node, the algorithm evaluates all candidate features and selects the one that best reduces impurity. It then partitions the data and recurses on each subset. The process stops when a stopping condition is met: all records in a node belong to one class, no features remain, or the node has too few records.

### Overfitting and Pruning

A very deep tree will eventually place one record per leaf, achieving zero training error but failing to generalise to unseen data. This is **overfitting**: the model memorises training noise instead of learning general patterns.

**Pre-pruning** prevents overfitting by stopping growth early, for example by setting a minimum node size or maximum depth threshold. It is computationally efficient but may stop growing before reaching the best structure.

**Post-pruning** lets the tree grow fully and then removes branches that do not improve performance on a held-out validation set. **Reduced Error Pruning** removes a branch if the model's accuracy on the validation set does not decrease after the removal. The **Minimum Description Length (MDL)** principle prunes when the total cost of describing the tree plus its errors decreases: Total Cost = bits(tree) + bits(errors), prune if total cost decreases.

Post-pruning tends to find better trees but is more computationally expensive.

### Key Takeaways

1. Entropy and Gini index both measure impurity; entropy uses logarithms while Gini uses squared probabilities.
2. ID3 is biased toward high-cardinality features; C4.5 corrects this with gain ratio; CART uses Gini and avoids the issue naturally.
3. Overfitting occurs when a tree is too deep and memorises training noise; pruning controls this by removing unhelpful branches.
4. Numerical features are handled with binary threshold splits.
5. Leaf nodes at the bottom hold the final class prediction.

**Remember this:** a decision tree recursively asks questions about features to split data into pure groups, and pruning stops it from memorising noise.

**One analogy to remember it by:** building a decision tree is like an elimination-round tournament bracket: at each round you split the field based on the most informative test, and the final matchup is your leaf prediction.

---

## Part 2: Random Forest and Ensemble Learning

_Reference: COS10022 Week 6 Online Session; Han, Pei & Kamber (2011)_

---

### What is Ensemble Learning?

**Ensemble learning** is a strategy that improves model performance by combining the predictions of multiple models into a stronger, more robust predictor.

In simple terms: instead of relying on one expert, you consult many experts and take their combined judgement.

Think of it like: a jury reaching a verdict by majority vote rather than relying on one juror's opinion.

Why it matters: individual models are prone to overfitting or bias; ensembles average out individual weaknesses, producing models that generalise better to new data.

### Why Weak Models Combine Well

A single strong model is hard to build because it must simultaneously manage complexity, bias, and variance. Weak or base models are simpler and individually less prone to overfitting. When many such models make errors in different places due to being trained on different subsets of data or using different features, their errors cancel out on average, improving collective accuracy and stability.

### Bagging (Bootstrap Aggregating)

**Bagging** trains multiple models independently on different bootstrap samples drawn with replacement from the training data. Because sampling with replacement creates diverse datasets, each model learns slightly different patterns. Final predictions are combined by majority vote for classification or by averaging for regression.

The key effect of bagging is reduced **variance**: individual trees may overfit their specific bootstrap sample, but averaging across many trees smooths out those fluctuations. Bagging models are trained in parallel and do not depend on each other.

**Random Forest** is a direct application of bagging to decision trees. It builds many trees, each trained on a bootstrap sample and each splitting on a random subset of features at every node. The feature randomisation forces the trees to be diverse even beyond what bootstrap sampling alone achieves. Final predictions are made by majority vote across all trees. Random Forest reduces overfitting compared to a single deep decision tree because no individual tree can dominate the result.

### Boosting

**Boosting** trains models sequentially, where each new model focuses on the mistakes of the previous ones. Misclassified samples receive higher weights so the next model pays more attention to them. This makes boosting a "teamwork" approach: each model depends on what came before and compensates for its errors. The result is often a very accurate model, but boosting is more prone to overfitting noisy data than bagging because it iteratively amplifies hard cases.

The key difference between bagging and boosting is that bagging trains models independently in parallel, while boosting trains them sequentially with each model correcting the last.

### Stacking

**Stacking** combines predictions from multiple heterogeneous models using a **meta-model** (also called a blender). The base models are trained on the training data, their predictions are collected as new features, and the meta-model is trained on those features to produce the final output. Unlike bagging, which uses simple aggregation like voting or averaging, stacking learns how best to weight and combine the base models.

|Method|Training Order|Combination|Primary Benefit|
|---|---|---|---|
|Bagging|Parallel, independent|Voting or averaging|Reduces variance|
|Boosting|Sequential, dependent|Weighted combination|Reduces bias|
|Stacking|Parallel, then meta-model|Learned combination|Flexible blending|

### Key Takeaways

1. Ensemble learning combines multiple weak models to form a stronger collective predictor.
2. Bagging reduces variance by training on diverse bootstrap samples and averaging predictions.
3. Boosting reduces bias by sequentially correcting errors, but can overfit noisy data.
4. Random Forest is bagging applied to decision trees with additional feature randomisation.
5. Stacking uses a meta-model to learn the optimal combination of base model outputs.

**Remember this:** bagging is independent trees voting; boosting is sequential specialists fixing each other's mistakes.

**One analogy to remember it by:** bagging is like polling thousands of independent citizens and taking the majority; boosting is like a relay race where each runner corrects the route deviation of the one before them.

---

## Part 3: Association Rules

_Reference: Han, Pei & Kamber (2011) Data Mining: Concepts and Techniques, Chapter on Association Rules; COS10022 Week 6 DSP Lecture Slides_

---

### What are Association Rules?

**Association rules** are an unsupervised, descriptive learning method used to discover interesting hidden relationships in large datasets. The discovered relationships are represented as rules of the form $X \rightarrow Y$, meaning when itemset $X$ is observed, itemset $Y$ tends to also be observed.

In simple terms: if you buy nappies, you probably also buy beer.

Think of it like: a grocery store noticing, after analysing millions of receipts, that cereal and milk appear together in the basket far more often than chance would predict.

Why it matters: association rules drive recommender systems (Amazon's "customers also bought"), store layout decisions, cross-selling strategies, and even medical diagnosis support systems.

### Core Terminology

An **itemset** is a collection of items that co-occur in a transaction. A k-itemset contains exactly $k$ items, for example ${\text{Milk}, \text{Diaper}, \text{Beer}}$ is a 3-itemset. A **transaction** is a single record such as one shopping basket. A **frequent itemset** is an itemset that appears in at least a predefined minimum fraction of all transactions.

**Rules** take the form $X \rightarrow Y$ where $X$ (the antecedent) and $Y$ (the consequent) are non-overlapping itemsets. The rule ${\text{Milk}, \text{Diaper}} \rightarrow {\text{Beer}}$ reads: when a customer buys milk and nappies, they also tend to buy beer.

Generating strong association rules is a two-step process. First, find all frequent itemsets that meet a minimum support threshold. Second, derive rules from those itemsets that also meet a minimum confidence threshold.

### Rule Evaluation Metrics

#### Support

**Support** is the proportion of all transactions that contain both $X$ and $Y$.

$$\text{Support}(X \rightarrow Y) = \frac{\text{transactions containing } X \cup Y}{\text{total transactions}}$$

A support of 2% means that 2% of all transactions contain both sides of the rule. Support filters out rare combinations.

#### Confidence

**Confidence** is the proportion of transactions containing $X$ that also contain $Y$.

$$\text{Confidence}(X \rightarrow Y) = \frac{\text{Support}(X \cup Y)}{\text{Support}(X)}$$

A confidence of 60% means that 60% of customers who bought the antecedent also bought the consequent. Confidence measures the trustworthiness of a rule, but it cannot detect coincidence: a high-confidence rule may exist simply because $Y$ is purchased very frequently overall, not because it is genuinely linked to $X$.

#### Lift

**Lift** corrects for that coincidence problem. It measures how many times more often $X$ and $Y$ appear together than would be expected if they were statistically independent.

$$\text{Lift}(X \rightarrow Y) = \frac{\text{Support}(X \cup Y)}{\text{Support}(X) \times \text{Support}(Y)}$$

A lift of 1 means $X$ and $Y$ are independent. A lift greater than 1 indicates a genuine positive association; the larger the value, the stronger the link.

#### Leverage

**Leverage** measures the absolute difference between the observed co-occurrence and the expected co-occurrence under independence.

$$\text{Leverage}(X \rightarrow Y) = \text{Support}(X \cup Y) - \text{Support}(X) \times \text{Support}(Y)$$

Leverage of 0 means independence. Larger positive values indicate stronger relationships. Leverage and lift generally agree on relative ranking.

**Worked example:** Assuming 1000 transactions, where ${\text{milk, eggs}}$ appears in 300, ${\text{milk}}$ in 500, and ${\text{eggs}}$ in 400; ${\text{milk, bread}}$ appears in 400, ${\text{milk}}$ in 500, ${\text{bread}}$ in 400:

|Metric|Milk to Egg|Milk to Bread|
|---|---|---|
|Confidence|$0.3/0.5 = 0.6$|$0.4/0.5 = 0.8$|
|Lift|$0.3/(0.5 \times 0.4) = 1.5$|$0.4/(0.5 \times 0.4) = 2.0$|
|Leverage|$0.3 - 0.5 \times 0.4 = 0.1$|$0.4 - 0.5 \times 0.4 = 0.2$|

All three metrics confirm that milk and bread share a stronger association than milk and eggs.

### The Apriori Algorithm

The **Apriori algorithm** is the foundational method for generating frequent itemsets. It exploits the **Apriori property** (also called the downward closure property): if an itemset is frequent, every subset of it must also be frequent. Equivalently, if an itemset is infrequent, every superset of it is also infrequent and need not be tested.

The algorithm uses an iterative level-wise search. It defines $C_k$ as the candidate itemsets of size $k$ and $L_k$ as the frequent itemsets of size $k$.

**Step 1:** Scan the entire database to count every individual item and form $L_1$, the frequent 1-itemsets, by removing items below the minimum support threshold.

**Step 2 (Join):** Generate $C_{k+1}$ by computing the Cartesian product $L_k \times L_k$, joining frequent $k$-itemsets with themselves to produce candidate $(k+1)$-itemsets.

**Step 3 (Prune):** Remove any candidate from $C_{k+1}$ if any of its $k$-item subsets is not in $L_k$. This is the application of the Apriori property and avoids scanning for itemsets that cannot possibly be frequent.

**Step 4:** Scan the database to count the remaining candidates in $C_{k+1}$ and retain those meeting minimum support to form $L_{k+1}$.

Repeat until no new frequent itemsets are found.

**Worked example (Example 3 from slides):** Given 5 transactions with items I1 through I5, min support count = 2 and min confidence = 50%:

- $C_1$ counts all items; $L_1$ retains I1 (3), I2 (4), I3 (3) after dropping I4 and I5.
- $C_2 = L_1 \times L_1$ produces ${I1,I2}$, ${I1,I3}$, ${I2,I3}$; after scanning, $L_2$ keeps ${I1,I2}$ (2) and ${I2,I3}$ (3).
- $C_3 = L_2 \times L_2$ produces ${I1,I2,I3}$ with support count 1, which fails minimum support, so $L_3$ is empty and the algorithm terminates.

From $L_2$, candidate rules are generated and confidence is calculated. For ${I2,I3}$: ${I2} \rightarrow {I3}$ has confidence $3/4 = 75%$ (accepted) and ${I3} \rightarrow {I2}$ has confidence $3/3 = 100%$ (accepted). Both satisfy min confidence.

### Generating Association Rules from Frequent Itemsets

Once all frequent itemsets are found, rules are generated by taking each frequent itemset, enumerating all non-empty subsets, and for each subset $s$ creating the rule $s \rightarrow (\text{itemset} \setminus s)$. Each candidate rule is then evaluated against the minimum confidence threshold. Only those meeting both minimum support (inherited from the frequent itemset) and minimum confidence are selected as strong association rules.

### Applications

Association rules are most commonly applied in **market basket analysis**: understanding which products are purchased together to inform shelf placement, cross-selling promotions, and inventory management. Beyond retail, they appear in recommender systems (Amazon and Netflix use clickstream patterns), medical diagnosis support (linking symptoms to diagnoses), and genomics (linking genes to functions).

### Validation and Diagnostics

Confidence alone is insufficient for selecting meaningful rules because high confidence can arise from coincidence. Lift and leverage provide a genuine relationship test. However, even after filtering on all four metrics, domain experts are needed to judge whether a rule is actionable, since mathematically strong rules can still be obvious or impractical (e.g. ${\text{paper}} \rightarrow {\text{pencil}}$).

The Apriori algorithm also requires the analyst to set minimum support before execution. Too high a threshold yields too few rules; too low a threshold produces an unmanageable number. Variants of the algorithm can adapt the threshold dynamically to target a desired number of rules.

Computationally, each level of the algorithm requires a full scan of the database, making it slow on large datasets. Several efficiency improvements exist: partitioning (running Apriori on partitions separately), sampling (running on a data subset with a lower threshold), transaction reduction (dropping transactions that contain no frequent k-itemsets in later scans), hash-based counting, and dynamic itemset counting.

### Key Takeaways

1. Association rules discover co-occurrence patterns of the form $X \rightarrow Y$ in transaction databases without any predefined target variable.
2. Support filters for frequency; confidence filters for reliability; lift and leverage filter for genuine association beyond coincidence.
3. The Apriori algorithm uses level-wise search and the downward closure property to prune the search space and find all frequent itemsets efficiently.
4. Rule generation takes every frequent itemset, enumerates its non-empty subsets, and evaluates each candidate rule against minimum confidence.
5. Domain expertise is essential to filter mathematically valid but practically useless rules.

**Remember this:** association rules mining first finds what co-occurs frequently, then tests whether the co-occurrence is strong, genuine, and non-coincidental.

**One analogy to remember it by:** think of Apriori like building a pyramid from bricks: you only add the next level if all the bricks beneath it are stable (frequent), and you discard any brick whose lower layers are missing.