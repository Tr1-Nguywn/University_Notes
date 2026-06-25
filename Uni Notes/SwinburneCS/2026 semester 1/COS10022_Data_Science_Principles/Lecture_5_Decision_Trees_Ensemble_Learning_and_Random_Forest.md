## Part 1: Decision Trees

### What is a Decision Tree?
![[Pasted image 20260325154819.png]]
A **decision tree** is a supervised learning model that predicts or classifies outcomes based on input features by recursively splitting a dataset into progressively purer subsets.

In simple terms: it is a flowchart-like structure where each internal node asks a question about a feature, each branch represents an answer, and each leaf holds a final prediction.

Think of it like: a game of Twenty Questions where every answer narrows down the possibilities until only one remains.

Why it matters: decision trees are one of the most interpretable ML models and form the backbone of powerful ensemble methods like Random Forest and gradient boosting, which appear everywhere in industry and competitions.

### Tree Anatomy
![[Pasted image 20260325155122.png]]
A decision tree is built from three kinds of nodes:
- The **root node** is the top-level split representing the most informative feature in the dataset.
- **Decision nodes** (also called internal nodes) sit in the middle and represent further splits on features.
- **Leaf nodes** (also called terminal nodes) sit at the bottom and contain the final output class or value. Every path from root to leaf encodes a decision rule.

### Splitting Criteria
![[Pasted image 20260325161947.png]]
The central challenge in building a tree is deciding which feature to split on at each node. The goal is to produce child subsets that are as **pure** as possible, meaning they contain predominantly one class. Three main criteria are used.

#### Entropy
**Entropy** measures the level of uncertainty or impurity in a dataset. A node containing only one class has entropy 0 (perfectly pure), while a node with an even mix of classes has maximum entropy.

In this formula, $Η$ (Greek capital letter eta) defines the entropy, $pi$ indicates the probability mass function, and $n$ is the number of output states.
$$\text{H}(p) = -\sum_{i=1}^{n} p_i \log_2 p_i \quad \text{for } p \in \mathbb{Q}^n$$

![[Pasted image 20260325155918.png]]
For example, a group of 7 positives and 6 negatives out of 13 has:
$$\text{H}(p) = -\frac{7}{13}\log_2\frac{7}{13} - \frac{6}{13}\log_2\frac{6}{13} \approx 0.995$$

A perfectly pure set gives $\text{H}(p) = 0$. Note that entropy can exceed 1 when there are more than two classes, as shown in the three-class ball example where $\text{H}_0 = 1.55$.
![[Pasted image 20260325160244.png]]
#### Information Gain
**Information Gain** (used in the [**ID3**](https://en.wikipedia.org/wiki/ID3_algorithm) algorithm) is the difference between original entropy and weighted entropy after split. It is calculated as:
$$\text{Gain} = \text{H}_0 - (w_1 \text{H}_1 + w_2 \text{H}_2)$$
where $w_1$ and $w_2$ are the proportional sizes of the two subsets. **The algorithm always selects the feature with the highest gain for the next split**. A known disadvantage of ID3 is that it prefers features with many distinct values (high cardinality), which can overfit.

#### Gain Ratio (C4.5)
**Gain Ratio** is an extension of information gain that corrects for the bias toward high-cardinality features. It normalises information gain by the split information, which penalises features that produce many small branches.
$$\text{Gain Ratio} = \frac{\text{Gain}}{\text{SplitInfo}}$$

The **C4.5** algorithm uses gain ratio, making it more reliable when features vary widely in the number of distinct values.

#### Gini Index (CART)
The **Gini Index** measures impurity using squared probabilities rather than logarithms, making it computationally cheaper as it only use multiplication and addition.

$$\text{Gini} = 1 - \sum_i p_i^2$$

A Gini of 0 means the node is pure. The **CART** algorithm selects splits that minimise the weighted Gini index across child nodes. It has no obvious bias toward high-cardinality features.
![[Pasted image 20260325161642.png]]
[**CART**](https://www.geeksforgeeks.org/machine-learning/cart-classification-and-regression-tree-in-machine-learning/) selects the split with the lowest Gini index. In the computer games example, splitting by gender gave a Gini index of 0.42, while splitting by class gave 0.48 - so gender was the better split.

|Aspect|ID3|C4.5|CART|
|---|---|---|---|
|Split Criterion|Information Gain|Gain Ratio|Gini Index|
|Core Idea|Maximise information gain|Correct bias of information gain|Minimise impurity|
|Bias Problem|Biased toward high-cardinality features|Corrected|No obvious bias|
|Formula|Gain = H(parent) - sum of weighted child entropies|Gain / SplitInfo|Gini = 1 - sum of squared probabilities|

### Handling Numerical Features
![[Pasted image 20260325162035.png]]
While categorical features split naturally into one branch per value. Numerical features are continuous (e.g., wind speed, temperature), you cannot create a separate branch for every possible value. That would produce one-sample leaves with zero impurity but no generalisation power.

![[Pasted image 20260325162046.png]]
The solution is **binary splits**: it tests a threshold (e.g. Age < 30) and places each record in the left or right branch. The threshold is typically chosen as the midpoint between adjacent sorted values that best reduces impurity.

### Tree Construction Process
Building a tree proceeds recursively. At each node, the algorithm evaluates all candidate features and selects the one that best reduces impurity. It then partitions the data and recurses on each subset. The process stops when a stopping condition is met: all records in a node belong to one class, no features remain, or the node has too few records.

### Decision Tree Example
**Problem:** Predict if a person buys a product based on Age and Income.

|Age|Income|Buys|
|---|---|---|
|Young|High|Yes|
|Young|Low|No|
|Middle|High|Yes|
|Middle|Low|Yes|
|Senior|High|No|
|Senior|Low|No|

**Tree construction:**
- Root node splits on Age (highest information gain)
- Young -> splits on Income -> High = Yes, Low = No
- Middle -> always Yes (pure leaf, no further split needed)
- Senior -> always No (pure leaf)

This produces a tree with depth 2, correctly classifying all training records.

### Overfitting and Pruning
![[Pasted image 20260325162108.png]]
A very deep tree will eventually place one record per leaf, achieving zero training error but failing to generalise to unseen data. This is **overfitting**: the model memorises training noise instead of learning general patterns. Vice versa, a tree that is too shallow underfits and misses important patterns.

The solution is **pruning**, which removes branches that appear to have arisen from noise rather than signal. There are 2 types of pruning:
- **Pre-pruning** applies stopping conditions during construction, such as requiring a minimum number of samples in any node, or limiting the maximum depth.
- **Post-pruning** lets the tree grow fully, then removes branches from bottom-up using one of two common methods.
	- **Reduced Error Pruning** evaluates each internal node on a held-out **validation set (mini test set)**. Does removing this branch hurt accuracy on the validation set? If no, prune it.
	- **Minimum Description Length (MDL) Pruning** frames pruning as a compression problem. Does removing this branch decrease the total bits needed to describe the tree plus its errors? If yes, prune it. No validation set needed - it is purely a mathematical cost calculation. The total cost of a tree is defined as:
$$\text{Total Cost} = \text{bits}(\text{tree}) + \text{bits}(\text{errors})$$

| Method                | Criterion                                    |
| --------------------- | -------------------------------------------- |
| Reduced Error Pruning | Prune if validation accuracy does not drop   |
| MDL Pruning           | Prune if bits(tree) + bits(errors) decreases |

### Key Takeaways
1. A decision tree splits data recursively using questions about features, aiming to make each subset as pure as possible.
2. ID3 uses Information Gain; C4.5 improves on this with Gain Ratio to penalise high-cardinality features; CART uses Gini impurity.
3. Numerical features require binary splits at candidate threshold values.
4. Trees can overfit (too deep) or underfit (too shallow); pruning controls this tradeoff.

**Remember this:** a decision tree learns by repeatedly asking the question that reduces uncertainty the most, until each group contains only one kind of answer.

**One analogy to remember it by:** building a decision tree is like writing a quiz where each question eliminates wrong answers - the better your questions, the sooner you reach the right answer.

---

## Part 2: Ensemble Learning

### What is Ensemble Learning?
![[Pasted image 20260325162228.png]]
**Ensemble learning** is a machine learning strategy that combines multiple models (called **weak learners**) to produce a prediction that is stronger and more reliable than any single model could achieve alone.

In simple terms: instead of asking one expert, you ask many and take the majority opinion.

Think of it like: the blind men and the elephant - each person only perceives part of the picture, but combining all their perspectives gives a more accurate description of the whole animal.

Why it matters: a single decision tree is high-variance and easy to overfit; ensemble methods dramatically reduce error by aggregating many trees, each trained on different subsets or features of the data.

### Bootstrapping
![[Pasted image 20260325162243.png]]
**Bootstrapping** is the statistical foundation of ensemble methods. It refers to **random sampling with replacement**: you draw a subset of your data, put each sample back into the pool before the next draw, so the same data point can appear more than once in a subsample.

This matters when your original dataset is small and a single mean or statistic could be misleading. By generating $m$ random subsamples and computing the statistic on each, then averaging the results, you get a **calibrated estimate** that is closer to the true population value. 
![[Pasted image 20260325162331.png]]
The slides demonstrate this with a sample of 8 values whose direct mean is 14.88, compared to a population mean of 12.4. After bootstrapping five subsamples and averaging their means, the calibrated estimate converges to 14.34, closer to the true value.

### Bagging (Bootstrap Aggregating)
![[Pasted image 20260325162408.png]]
**Bagging** (Bootstrap Aggregation) applies bootstrapping directly to a machine learning algorithm. Multiple versions of the model are trained on different bootstrap samples drawn from the training set.

![[Pasted image 20260325162426.png]]
Each model makes its own prediction, and the final output is determined by an **aggregation function**: typically the **statistical mode** (most frequent prediction) for classification tasks, or the **mean** for regression tasks.

Bagging reduces both bias and variance because the averaging process smooths out the individual errors of each model. Training instances can appear in multiple bootstrap samples, which is intentional since it introduces diversity between the models.

**Random Forest** is a direct application of bagging to decision trees. It builds many trees, each trained on a bootstrap sample and each splitting on a random subset of features at every node

### Boosting
![[Pasted image 20260325162447.png]]
**Boosting** is a sequential ensemble approach where each new model focuses on the mistakes of the previous one. Rather than treating all data points equally, boosting assigns **higher weights** to points that were misclassified, forcing the next model to pay more attention to them.
![[Pasted image 20260325162504.png]]
The slides illustrate this with two-class data and three decision stumps (D1, D2, D3). D1 draws a vertical boundary but misclassifies three positive points; those three receive higher weight going into D2. D2 corrects those, but misclassifies three negatives; those gain weight for D3. D3 draws a horizontal boundary targeting those negatives. Combining D1, D2, and D3 (Box 4) produces a complex, strong classifier that none of the weak stumps could achieve alone.

The key difference between bagging and boosting is that bagging trains models independently in parallel, while boosting trains them sequentially with each model correcting the last.

### Stacking
![[Pasted image 20260325162529.png]]
**Stacking** trains multiple heterogeneous base models in parallel, then trains a **meta-model** (also called the blender) on their predictions to produce the final output.
![[Pasted image 20260325162810.png]]
The base models can be fundamentally different types (neural network, decision tree, kernel method), unlike bagging which uses one algorithm type. The winning solution for the 2015 KDD Cup competition used a three-stage stacked approach with 64 single models combined across multiple ensemble stages.

### Comparing the Three Methods

| Method   | Model type    | Training order | Combination    | Best for            |
| -------- | ------------- | -------------- | -------------- | ------------------- |
| Bagging  | Homogeneous   | Parallel       | Mode / Average | Reducing variance   |
| Boosting | Homogeneous   | Sequential     | Weighted       | Reducing bias       |
| Stacking | Heterogeneous | Parallel       | Meta-model     | Maximising accuracy |

- **Accuracy (Goal):** A qualitative measure of how close your model's predictions are to the true values, encompassing all errors.
- **Bias (Systematic Error):** The difference between average predictions and the true target, caused by simplifying assumptions (e.g., linear model on non-linear data). High bias leads to underfitting.
- **Variance (Unstable Error):** How much predictions fluctuate based on different training data samples. High variance means the model is too complex and sensitive, leading to overfitting.

### Ensemble Learning Example
**Problem:** Classify an email as spam or not spam. Three weak learners are trained:
- Tree 1 (trained on sample A): predicts Spam
- Tree 2 (trained on sample B): predicts Not Spam
- Tree 3 (trained on sample C): predicts Spam

Majority vote: 2 out of 3 say Spam, so final prediction = **Spam**.

No single tree was reliable alone, but the ensemble corrects individual mistakes.

### Key Takeaways
1. No single weak learner needs to be perfect; the power comes from aggregating many imperfect models.
2. Bootstrapping enables diverse training sets from a single dataset by sampling with replacement.
3. Bagging reduces variance through parallel averaging; boosting reduces bias through sequential error correction; stacking layers models to learn from each other.

**Remember this:** ensemble methods work on the same principle as a panel of judges - individual biases cancel out and the collective verdict is more reliable than any single opinion.

**One analogy to remember it by:** boosting is like a relay race where each runner knows exactly where the previous runner stumbled, and trains specifically to recover that lost ground.

---

## Part 3: Random Forest

### What is a Random Forest?
A **Random Forest** (or Random Decision Forest) is a supervised learning method that constructs multiple decision trees during training and combines their outputs through majority voting to produce a final classification or regression result.

In simple terms: instead of relying on one tree that might overfit, you grow many trees on different subsets and let them vote.

Think of it like: asking multiple slightly different versions of the same expert, each having studied a different slice of the evidence, then taking the answer that the majority agrees on.

Why it matters: it inherits the interpretability of decision trees while dramatically improving accuracy and robustness, and it works well on large datasets with mixed feature types.

### Where Does Random Forest Fit?
![[Pasted image 20260325162959.png]]
Random Forest sits within supervised learning, under both classification and regression. It is a concrete application of the **stacking** ensemble strategy from Part 2, where each base learner is a decision tree.

### Random Forest Example
**Problem:** Predict survival in a road accident using Age, Speed, Helmet_Used, Seatbelt_Used.
- Tree 1 splits on Speed first, then Helmet_Used
- Tree 2 splits on Age first, then Seatbelt_Used
- Tree 3 splits on Helmet_Used first, then Speed

For a new record (Age=25, Speed=90, Helmet=No, Seatbelt=Yes):
- Tree 1 predicts: No
- Tree 2 predicts: No
- Tree 3 predicts: Yes

Majority vote = **No** (2 out of 3). Final prediction: did not survive.

The diversity in split criteria across trees makes the forest more robust than any individual tree.

### Advantages of Random Forest
Random Forest addresses the three major weaknesses of single decision trees. First, by averaging many trees with different structures, it substantially reduces **overfitting**. Second, it is computationally efficient and **scales well to large datasets** while maintaining high accuracy. Third, it handles missing data by using the available features across multiple trees, effectively **estimating missing values** through the structure of other trees.

### How Random Forest Works
![[Pasted image 20260325163029.png]]
The slides walk through a three-tree example classifying balls (golf ball, basketball, football) by colour, diameter, and shape.

Tree 1 starts with "Diameter $\leq 8$?" and then asks "Shape == Oval?". Tree 2 starts with "Colour == Brown?" followed by "Shape == Oval?". Tree 3 starts with "Colour == White?" followed by "Shape == Oval?". Each tree uses a different initial split criterion drawn from the available features.

When a test data point arrives with no colour information, each tree defaults to false on the colour-based question and continues its remaining logic. Tree 1 and Tree 3 predict basketball; Tree 2 predicts golf ball. The **majority vote** produces basketball as the final answer. This resilience to missing features is one of the key practical advantages of Random Forest.

The forest output probability is formally:
$$p(c|\mathbf{v}) = \frac{1}{T} \sum_{t} p_t(c|\mathbf{v})$$

where $T$ is the number of trees and $p_t(c|\mathbf{v})$ is the probability that tree $t$ assigns class $c$ to input $\mathbf{v}$.

### Real-World Applications
Random Forest has been applied in three broad domains highlighted in the slides. In **remote sensing**, it processes satellite imagery (using Enhanced Thematic Mapping devices) with higher accuracy and shorter training time than many other methods. In **object detection**, it supports multi-class detection in complex environments such as traffic monitoring. In **gaming**, the Microsoft Kinect console uses Random Forest to identify body parts in real time, mapping skeletal movements into game actions.

### Key Takeaways
1. Random Forest builds multiple trees each starting with a different root split, creating diversity in the ensemble.
2. Final classification is determined by majority vote across all trees, which smooths out individual tree errors.
3. It is robust to missing features because trees that do not rely on a missing attribute can still contribute a valid vote.
4. It inherits the interpretability benefits of individual trees while achieving significantly lower variance.

**Remember this:** a random forest is simply democracy applied to machine learning - no single tree is trusted absolutely, but the majority decision is far more reliable than any individual vote.

**One analogy to remember it by:** each tree in the forest is like a detective who saw different parts of the crime scene; the one conclusion they all independently point to is almost certainly correct.

## Model Comparison Summary

| Feature               | Linear Regression            | Logistic Regression              | Naive Bayes                                | Decision Tree                 | Random Forest                   |
| --------------------- | ---------------------------- | -------------------------------- | ------------------------------------------ | ----------------------------- | ------------------------------- |
| Task type             | Regression                   | Classification                   | Classification                             | Both                          | Both                            |
| Output                | Continuous number            | Probability (0-1) -> class       | Class probability                          | Class label                   | Class label (majority vote)     |
| Input data            | Numerical only               | Numerical (encode strings)       | Mixed (numerical + categorical)            | Mixed                         | Numerical preferred             |
| Core idea             | Best fit line minimising SSE | Sigmoid of linear score          | Bayes theorem with independence assumption | Recursive splitting by purity | Many trees, majority vote       |
| Handles non-linearity | No (needs transformation)    | Partially (via sigmoid)          | Yes                                        | Yes                           | Yes                             |
| Overfitting risk      | Low                          | Low                              | Low                                        | High (deep trees)             | Low (averaging reduces it)      |
| Interpretability      | High                         | High                             | Medium                                     | High                          | Low (many trees)                |
| Training speed        | Fast                         | Fast                             | Very fast                                  | Fast                          | Slower (many trees)             |
| Missing data          | Sensitive                    | Sensitive                        | Sensitive                                  | Handles via other branches    | Robust (other trees compensate) |
| Best used when        | Predicting a quantity        | Binary or multi-class prediction | Text, categorical data, small datasets     | Interpretability is needed    | High accuracy is priority       |

### Key distinctions to remember
- Linear vs Logistic: same mechanics, different output. Linear predicts a number, Logistic predicts a class via sigmoid.
- Naive Bayes vs Logistic Regression: both are classifiers, but Naive Bayes uses probability theory and handles raw categorical data natively; Logistic Regression needs numerical input and uses a linear boundary.
- Decision Tree vs Random Forest: a single tree is interpretable but overfits easily; Random Forest trades interpretability for much better accuracy and generalization through bagging.
- Naive Bayes handles categorical (string) attributes natively. Raw string columns do not need to be encoded to numbers before being fed into the model. Only encode if the algorithm requires strictly numerical input (e.g. Logistic Regression, Random Forest).