## Part 1: Classification Models

### What is Classification?
![[image 30.png|image 30.png]]
**Classification** is a supervised machine learning technique that assigns known labels (class labels) to objects based on their input attributes.

In simple terms: given some information about an object, the model predicts which category it belongs to.

Think of it like: a doctor reviewing a patient's symptoms and deciding whether they have a particular condition or not. The doctor has seen many labelled cases before and uses that experience to classify a new patient.

Why it matters: Classification enables automation of decision-making at scale. Spam filters, fraud detection systems, medical diagnosis tools, and recommendation engines all depend on classification models.
### Where Classification Fits in Data Science
Classification sits within supervised learning under machine learning. The broader technique landscape for data science problems includes clustering (grouping by similarity), association rules (relationships between items), regression (predicting continuous outcomes), and time series analysis (temporal forecasting). Classification is the right technique when the goal is to assign an object to a known, discrete category.

In the Data Analytics Lifecycle, model building occurs in Phase 4. The team develops training and test datasets, builds and executes models, and evaluates their performance. A model may be iterated on before moving to deployment, or a team may move forward even if the model is only marginally sufficient.

### Key Takeaways
1. Classification is supervised learning because it requires labelled training data.
2. The correct technique to select depends on the nature of the business problem.
3. Model building in Phase 4 always requires both fitting on training data and evaluating on test data.

**Remember this:** Classification assigns known labels; it does not discover hidden structure - that is clustering.

**One analogy to remember it by:** A spam filter is a classifier that has been trained on thousands of labelled emails. Every new email it judges is a classification decision.

---

## Part 2: Naive Bayes Classifier

### What is the Naive Bayes Classifier?

The **Naive Bayes Classifier** is a probabilistic supervised classification algorithm based on Bayes' Theorem, which describes the relationship between conditional probabilities of events.

In simple terms: it calculates the probability that a data point belongs to each class, then picks the class with the highest probability.

Think of it like: a detective who computes the probability of each suspect being guilty based on the available evidence, updating their belief as each new piece of evidence is considered independently.

Why it matters: Despite its simplicity, Naive Bayes performs surprisingly well on text classification problems like spam filtering, sentiment analysis, and news categorization.


![[Pasted image 20260322180859.png]]
### Bayes' Theorem: The Foundation
**Bayes' Theorem** describes how to compute the conditional probability of an event A given that event B has already occurred:
$$P(A|B) = \frac{P(B|A) . P(A)}{P(B)}$$

Where:
- **P(A | B)** is the posterior probability - the probability of A given B.
- **P(B | A)** is the likelihood - the probability of observing B if A is true.
- **P(A)** is the prior probability of A.
- **P(B)** is the marginal probability of B.

Named after Thomas Bayes (1700s), this theorem is derived from the definition of conditional probability:
	$P(A|B) = \frac{P(A \cap B)}{P(B)}$

By symmetry, the same applies flipped:
	$P(B|A) = \frac{P(A \cap B)}{P(A)}$

Rearrange the second one to isolate the joint probability:
	$P(A \cap B) = P(B|A) \times P(A)$

Substitute into the first formula:
	$P(A|B) = \frac{P(B|A) \times P(A)}{P(B)}$

### Basic Probability Warm-Up
Consider tossing two coins. The sample space is {HH, HT, TH, TT}, each with probability 1/4. The conditional probability that the second coin is H given the first is T can be found by narrowing down the sample space to outcomes where the first coin is T: {TH, TT}. Of these, only TH has the second coin as H, so $P(second = H | first = T) = \frac{1}{2}$.

Bayes' Theorem confirms this formally:
$$P(\text{second}=H \mid \text{first}=T) = \frac{P(\text{first}=T \mid \text{second}=H) \times P(\text{second}=H)}{P(\text{first}=T)} = \frac{\frac{1}{2} \times \frac{1}{2}}{\frac{1}{2}} = 0.5$$

### Worked Example: John's Flight Upgrade
John checks in at least two hours early 40% of the time. If early, his upgrade probability is 0.75. If not early, it is 0.35. Given that John did not receive an upgrade, what is the probability he did not arrive early?

**Define events:**
- C = arrived at least 2 hours early; C' = did not arrive early
- A = received upgrade; A' = did not receive upgrade

**Given:**
- $P(C) = 0.40$, therefore $P(C') = 0.60$
- $P(A | C) = 0.75$, therefore $P(A' | C) = 0.25$
- $P(A | C') = 0.35$, therefore $P(A' | C') = 0.65$

**Step 1 - Find P(A) using the law of total probability:**
	$P(A) = P(C) \times P(A | C) + P(C') \times P(A | C')$
    $= 0.4 \times 0.75 + 0.6 \times 0.35$
    $= 0.30 + 0.21$
    $= 0.51$
Therefore $P(A') = 1 - 0.51 = 0.49$.

**Step 2 - Apply Bayes' Theorem:**
	$P(C' | A') = P(A' | C') \times \frac{P(C')}{P(A')}$
    $= 0.65 \times \frac{0.60}{0.49}$
    $≈ 0.796$
John had roughly a 79.6% chance of not arriving early, given he did not get an upgrade.
### Generalizing Bayes' Theorem to Classification
For a dataset with class labels $c_1, c_2, ..., c_n$ and observed attributes $A = {a_1, a_2, ..., a_m}$, the generalized Bayes' Theorem assigns class $c_i$ to each record such that $P(c_i | A)$ is maximized:
$$P(c_i | A) = \frac{P(a_1, a_2, ..., a_m | c_i) \times P(c_i)}{P(a_1, a_2, ..., a_m)}$$

The denominator $P(a_1, ..., a_m)$ is constant across all classes, so in practice the model just compares the numerators across classes and picks the largest.

### The "Naive" Assumption
The "naive" part of Naive Bayes is the assumption of **conditional independence** among attributes given the class label. This means:
$$P(a_1, a_2, ..., a_m | c_i) ≈ P(a_1 | c_i) \times P(a_2 | c_i) +\times ... \times P(a_m | c_i)$$

This assumption makes the computation tractable: instead of estimating one large joint probability (which would require enormous data), you only need to estimate small individual probabilities for each attribute. In reality, attributes are rarely truly independent, but the model often works well despite this.

### Worked Example: Shopping Purchase Prediction
**Problem:** Predict whether a customer will purchase a product given the combination Day = Holiday, Discount = Yes, Free Delivery = Yes.

**Dataset:** 30 records with three predictors (Day, Discount, Free Delivery) and one target (Purchase: Buy/No Buy). From the frequency tables:
- Total Buy = 24/30, Total No Buy = 6/30.

**Likelihood tables (from frequency counts):**

| Attribute     | Value   | Buy   | No Buy |
| ------------- | ------- | ----- | ------ |
| Day           | Weekday | 9/24  | 2/6    |
| Day           | Weekend | 7/24  | 1/6    |
| Day           | Holiday | 8/24  | 3/6    |
| Discount      | Yes     | 19/24 | 1/6    |
| Discount      | No      | 5/24  | 5/6    |
| Free Delivery | Yes     | 21/24 | 2/6    |
| Free Delivery | No      | 3/24  | 4/6    |

**Compute P(No Buy | Holiday, Discount=Yes, Free Delivery=Yes):**
Numerator (No Buy side):
	$P(Discount=Yes | No Buy) \times P(Free Delivery=Yes | No Buy) \times P(Day=Holiday | No Buy) \times P(No Buy)$
	$= (1/6) \times (2/6) \times (3/6) \times (6/30)$

Denominator (marginal):
	$P(Discount=Yes) \times P(Free Delivery=Yes) \times P(Day=Holiday)$
	$= (20/30) \times (23/30) \times (11/30)$
	$\approx0.0296$

**Compute P(Buy | Holiday, Discount=Yes, Free Delivery=Yes):**
Numerator (Buy side):
	$(19/24) \times (21/24) \times (8/24) \times (24/30) \approx 0.9857$
	
Same denominator.

**Normalise:**
Likelihood of Buy = $\frac{0.9857}{0.9857 + 0.0296} \approx 97.05\%$
Likelihood of No Buy = $\frac{0.0296}{(0.9857 + 0.0296)} \approx 2.95\%$

**Conclusion:** The model predicts purchase on a Holiday with discount and free delivery with ~97% probability.

The calculation for a single attribute (e.g. Day alone) works the same way: compute $P(Buy | Weekday) \approx 0.82$ vs $P(No Buy | Weekday) \approx 0.18$ using Bayes' Theorem, then conclude the customer is likely to buy on a weekday.

### Advantages and Disadvantages of Naive Bayes

| Aspect                               | Detail                                                             |
| ------------------------------------ | ------------------------------------------------------------------ |
| Very simple and fast                 | Easy to implement; suitable for real-time prediction               |
| Low data requirement                 | Works well even with relatively small training sets                |
| Handles mixed data                   | Supports both continuous and discrete attributes                   |
| Scalable                             | Scales well with more predictors and large datasets                |
| Not sensitive to irrelevant features | Irrelevant attributes have limited impact                          |
| Independence assumption              | Often violated in practice; can degrade accuracy                   |
| Discretisation loss                  | Converting continuous values to discrete buckets loses information |

Naive Bayes handles categorical (string) attributes natively, so raw string columns do not need to be encoded to numbers before being fed into the model. Only encode if the algorithm requires strictly numerical input (e.g. Logistic Regression, Random Forest).

The key weakness is the naive independence assumption. When attributes are correlated (which is common in real data), the probability estimates become inaccurate. Despite this, Naive Bayes remains a popular baseline model and performs well on text tasks.

### Real-World Applications
Naive Bayes is used in spam filtering (classifying emails as spam or not), social media sentiment analysis (positive, negative, neutral), news text classification (technology, sports, entertainment), face recognition, medical diagnosis, and weather prediction. Its speed makes it practical for real-time systems.

### Key Takeaways
1. Bayes' Theorem computes the posterior probability of a class given evidence using prior probabilities and likelihoods.
2. The naive independence assumption allows each attribute to be evaluated separately, which makes computation feasible.
3. To make a prediction, compute the posterior for each class and pick the highest one. Normalize to get interpretable probabilities.
4. The model excels at text classification despite the simplifying assumption often being wrong in practice.

**Remember this:** Naive Bayes is "**naive**" because it assumes all features are independent given the class - which is rarely true, but the model is fast and often good enough.

**One analogy to remember it by:** Judging a restaurant by the independent ratings of its food, service, and ambience and then adding them up, rather than considering how they interact - that is Naive Bayes.

---

## Part 3: Model Evaluation

### What is Model Evaluation?
**Model evaluation** (also called model validation or model diagnostics) is the process of measuring how well a built model performs on data, particularly data it has never seen during training.

In simple terms: after training a model, you test it against a labelled dataset to see how many predictions it gets right and wrong.

Think of it like: a driving test. A learner driver practices (trains) on roads they know, but the examiner tests them on an unfamiliar route to check if the skills generalize.

Why it matters: A model that performs well on training data but fails on new data is not useful. Evaluation catches this and gives the team objective evidence of whether the model meets business requirements.

### The Confusion Matrix
![[Pasted image 20260322184840.png]]
The **confusion matrix** is the central tool for evaluating a two-class (binary) supervised model. It cross-tabulates predicted class labels against actual class labels:
- **True Positives (TP):** correctly identified as positive. Also called Sensitivity or Recall.
- **True Negatives (TN):** correctly identified as negative. Also called Specificity or Selectivity.
- **False Positives (FP):** predicted positive but actually negative (Type I error). Also called Fall-out.
- **False Negatives (FN):** predicted negative but actually positive (Type II error). Also called Missing rate.

A good classifier maximizes TP and TN while minimizing FP and FN.

### Evaluation Metrics
![[Pasted image 20260322185832.png]]
**Accuracy** is the overall success rate - the fraction of all records that were correctly classified:
$Accuracy = \frac{TP + TN}{TP + TN + FP + FN} \times 100\%$

A threshold of 80% or above is generally considered acceptable. However, accuracy alone can be misleading in imbalanced datasets (e.g. if 95% of records are negative, a model that always predicts negative achieves 95% accuracy but is useless).

**True Positive Rate (TPR / Recall / Sensitivity)** measures how many *actual positives* were correctly caught:
$TPR = \frac{TP}{TP + FN}$

**False Positive Rate (FPR / Fall-out / Type I error rate)** measures how many *actual negatives* were incorrectly flagged:
![[Pasted image 20260322185220.png]]
$FPR = \frac{FP}{FP + TN}$

**False Negative Rate (FNR / Miss rate / Type II error rate)** measures how many *actual positives were missed*:
![[Pasted image 20260322185440.png]]
$FNR = \frac{FN}{FN + TP}$
So TPR + FNR = 100% of actual positive by definition.

**Precision** measures how trustworthy the positive predictions are - of all records predicted positive, how many were truly positive:
![[Pasted image 20260322185302.png]]
$Precision = \frac{TP}{TP + FP}$

**Area Under the Curve (AUC)** summarizes classifier performance across all classification thresholds. It measures the area under the ROC (Receiver Operating Characteristic) curve, which plots TPR against FPR. Higher AUC means better performance:
![[Pasted image 20260322185617.png]]

### Worked Example: Bank Subscription Classifier
A confusion matrix of Naïve Bayes classifier for 100 customers in predicting whether they would subscribe to the term deposit.

|                            | Predicted: Subscribed | Predicted: Not Subscribed | Total |
| -------------------------- | --------------------- | ------------------------- | ----- |
| **Actual: Subscribed**     | 3 (TP)                | 8 (FN)                    | 11    |
| **Actual: Not Subscribed** | 2 (FP)                | 87 (TN)                   | 89    |
| **Total**                  | 5                     | 95                        | 100   |

Computing the metrics:
$Accuracy = \frac{3 + 87}{100}  \times 100\% = 90\%$
$TPR = \frac{3}{3 + 8} = 0.273$  **(poor - misses most subscribers)**
$FPR = \frac{2}{2 + 87} \approx 0.022$
$FNR = \frac{8}{8 + 3} \approx 0.727$
$Precision = \frac{3}{3 + 2} = 0.60$

Despite 90% accuracy, the TPR of 0.273 reveals the classifier is poor at identifying the minority positive class (subscribers). This is a common trap with imbalanced datasets.

### Cross-Validation Methods
**Cross-validation** is the method by which evaluation metrics are computed to give a reliable, unbiased estimate of model performance. The core idea is always to evaluate the model on data it has not seen during training.

**Holdout (Percentage Split):** The simplest method. The dataset is split into a training set (e.g. 70-80%) and a test set (20-30%). The model is trained on the training set and evaluated on the test set. The weakness is that results can vary depending on the specific split.
![[Pasted image 20260322190011.png]]

**K-Fold Cross Validation:** The dataset is divided into k equally-sized folds. The model is trained on k-1 folds and tested on the remaining fold. This is repeated k times, each time using a different fold as the test set. The final performance is the average across all k iterations. This gives more stable estimates than the holdout method, though it is more computationally expensive. A common choice is k = 10.
![[Pasted image 20260322190046.png]]

**Leave-One-Out Cross Validation (LOOCV):** *A special case of k-fold* where k equals the number of records N. Each record is used as the test set once. This gives the least biased estimate but is the most expensive to run, especially for large datasets.
![[Pasted image 20260322190056.png]]

**Validation Set:** A more sophisticated approach introduces a third partition alongside training and test sets (e.g. 60% training / 20% validation / 20% test). 
![[Pasted image 20260322190123.png]]
The validation set is used to tune model hyperparameters (parameters set before training, such as the number of neighbours in KNN). Once the model is optimised using the validation set, its final performance is measured on the held-out test set. This prevents the test set from being "peeked at" during tuning.

### Practical Considerations: Choosing the Right Metric
What counts as a "good enough" model depends on the business context established in Phase 1 (Discovery). The acceptable trade-off between Type I and Type II errors is a business decision, not a purely statistical one.

Consider a spam filter where "positive" means spam:
- **A busy executive who wants no spam in her Inbox, even if some legitimate emails are mis-routed to Spam:** She cares most about keeping spam out. The failure mode she cannot tolerate is a spam email reaching her Inbox (False Negative - missed spam). Therefore, minimise **FNR**.
- **A less busy executive who cannot miss any important email, even if it means tolerating some spam in his Inbox:** He cares most about not losing real emails. The failure mode he cannot tolerate is an important email going to Spam (False Positive - legitimate email classified as spam). Therefore, minimise **FPR**.

In medical screening, FNR is often more critical (missing a disease is dangerous). In fraud detection, FPR may be more costly (falsely flagging legitimate customers damages trust). There is always a trade-off.

### Overfitting
**Overfitting** is when a model learns the training data too closely - including its noise - and fails to generalise to new data. It manifests as high training accuracy but poor test accuracy. Cross-validation is the primary way to detect overfitting, because it forces the model to be evaluated on data it has never seen. A well-generalising model should show similar performance on both training and test sets.

### Evaluating Unsupervised Models
Supervised models have ground-truth labels to compare against, making confusion matrix metrics straightforward. Unsupervised models (like K-Means Clustering) do not have labels, so evaluation is more subjective and model-specific. Methods include domain expert review, and objective metrics such as the Silhouette score or Weighted Sum of Squares for clustering, and support, confidence, and lift for association rules.

### Key Takeaways
1. The confusion matrix is the foundation for all supervised classification metrics: TP, TN, FP, FN.
2. Accuracy alone is insufficient for imbalanced datasets - always check TPR, FPR, and Precision as well.
3. The appropriate metric depends on the business cost of each error type (Type I vs Type II).
4. Cross-validation (k-fold or holdout) ensures evaluation is done on unseen data.
5. AUC provides a single threshold-independent measure of overall classifier quality.
6. Overfitting is detected by a large gap between training and test performance.
**Remember this:** A 90% accurate model can still be terrible - always decompose performance using the confusion matrix and pick metrics that match what the business actually cares about.

**One analogy to remember it by:** A thermometer that always reads 36.6 degrees is 100% consistent but tells you nothing useful. Similarly, a classifier that always predicts the majority class looks accurate but misses everything important.