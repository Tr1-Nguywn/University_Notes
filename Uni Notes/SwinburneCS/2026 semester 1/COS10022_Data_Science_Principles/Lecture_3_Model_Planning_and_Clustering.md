## Part 1: Model Planning and Model Selection

### What is Model Planning?
![[Pasted image 20260323160733.png]]
**Model planning** is Phase 3 of the Data Analytics Lifecycle, where the data science team determines the methods, techniques, and workflow it intends to follow before building any model.

In simple terms: it is the step where you decide what kind of analysis to run before you actually run it.

Think of it like: planning a road trip before leaving - you figure out the route, the tools you need, and potential detours before you get in the car.

Why it matters: choosing the wrong model wastes time and produces misleading results. Planning first ensures the model you build can actually answer the question you are asking.

### What is a Model?
![[Pasted image 20260323160857.png]]
A **model** is an abstraction from reality; an attempt to understand and represent real-world behaviour through a set of rules and conditions.

Modeling involves observing events in a real-world situation and constructing simplified representations that emulate that behaviour. Because models strip out extraneous detail, it is important to revisit those abstracted elements after analysis to check what may have been overlooked.

In statistical modeling, the terms *model, algorithm, analytical technique, and analytical method are used interchangeably*. Mathematically, the convention is to use Greek letters for **parameters** (coefficients) and Latin letters for data or variables. A simple linear model with two variables $x$ and $y$ is written:
$$y = \beta_0 + \beta_1 x$$
where $\beta_0$ and $\beta_1$ are parameters *(beta)*, and $x$ and $y$ are data variables.
**Example:** $y = 2x$
![[Pasted image 20260323161308.png]]
When thinking about a model it helps to ask: what comes first, what influences what, what causes what, and what is a test of what.

### Key Considerations for Model Selection
![[Pasted image 20260323161650.png]]
*List of algorithms* 
![[Pasted image 20260323161945.png]]
*Algorithms used for data science-related applications*

Three primary factors guide which model to choose in Phase 3:
![[Pasted image 20260323161959.png]]
The **structure of the data** matters because different models suit different data types: structured, semi-structured, quasi-structured, and unstructured data each call for different approaches.

The **suitability for hypothesis testing** asks whether the chosen model can prove or disprove the hypotheses the team has formed during discovery.

The **need for a series of models** acknowledges that some analytical problems are complex enough to require chaining multiple models together rather than relying on a single technique.

### Supervised, Unsupervised, and Semi-supervised Models
The broadest classification of models in machine learning distinguishes how the algorithm relates to labelled output.

![[Pasted image 20260323170403.png]]
**Supervised models** take a set of input variables $X$ and an output variable $Y$ and learn the mapping function from inputs to output. Training uses data where correct answers are already known; the test set presents new inputs and the model must predict the output. 
![[Pasted image 20260323170253.png]]
There are two main categories: classification (categorical output such as "Pass" or "Fail") and regression (numerical output such as a price or probability).

![[Pasted image 20260323170541.png]]
**Unsupervised models** receive only input variables $X$ with no known output. The goal is to discover hidden structure or natural groupings in the data. There is no training set, no correct answer, and no teacher.
![[Pasted image 20260323170556.png]]
The two main categories are clustering and association.

![[Pasted image 20260323170700.png]]
**Semi-supervised models** address situations where output labels are only partially available. A common strategy is to first train a supervised model on the few labelled examples, use it to label the unlabeled data, then retrain on the combined set to produce a better final model. This is useful in domains like image classification where labelling large archives manually is impractical.

### Key Takeaways
1. Model planning is about deciding what to build before building it - the output is an analytics plan, not a model itself.
2. A model is always an abstraction; the details stripped away must be revisited after analysis.
3. The supervised/unsupervised distinction is fundamental: supervised learning requires labelled output, unsupervised does not.
4. Model selection is guided by data structure, hypothesis fit, and whether a single model suffices.
5. Semi-supervised learning is a practical bridge when labelled data is scarce.

**Remember this:** choosing the right kind of model before touching the data is what separates a structured analysis from a fishing expedition.

**One analogy to remember it by:** supervised learning is like studying with an answer key; unsupervised learning is like sorting a pile of unknown objects into groups by feel alone.

---

## Part 2: k-Means Clustering

### What is k-Means Clustering?
![[Pasted image 20260323170824.png]]
**k-Means clustering** is an unsupervised (exploratory) algorithm that partitions a dataset into $k$ natural groups by finding similarities between objects based on their attributes.

In simple terms: the algorithm sorts data points into $k$ buckets so that points in the same bucket are as similar to each other as possible.

Think of it like: automatically sorting a bag of mixed marbles into $k$ jars by colour and size - you do not know what the jars represent beforehand, you let the data decide.

![[Pasted image 20260323170909.png]]
Why it matters: clustering is the second most used algorithm family among data scientists (after regression) and drives real applications from customer segmentation to anomaly detection in security footage.

### The k-Means Algorithm
![[Pasted image 20260323171658.png]]
The algorithm assumes a dataset of $M$ objects, each described by $n$ attributes. A **centroid** is the arithmetic mean position of all points currently assigned to a cluster.

The algorithm runs in four steps:
![[Pasted image 20260323171308.png]]
- **Step 1 - Initialize:** Choose the value of $k$ and place $k$ initial centroids. Two common methods exist:
	- The **Forgy method** sets each centroid to a randomly chosen existing data point. This method is usually better because it starts centroids exactly where data exists.
	- The **Random Partition method** randomly assigns every observation to a cluster first, then computes the initial centroids as the means of those assignments. This method tends to put centroids close to the center of the whole dataset, which can make the algorithm take longer to "spread out."

![[Pasted image 20260323171414.png]]
- **Step 2 - Assign:** Compute the Euclidean (/_yü-ˈkli-dē-ən_/) (Ơ-clít) distance from every data point $(x_i, y_i)$ to each centroid $(x_C, y_C)$. 
	- It is also called the **L2 norm** because it squares each difference (power of 2). The expression inside the double bars $\| \cdot \|$ **(see step 4)** is a vector, which on its own is just a direction and magnitude combined, not a single usable number. In this case, the $\cdot$ is an placeholder like in $f(\cdot)$, not multiplication like in $a \cdot b$. The bars are the operation that extracts just the magnitude as a scalar.
	- The formula comes from **Pythagoras' theorem** (Pytago). The horizontal gap $(x_i - x_C)$ and vertical gap $(y_i - y_C)$ between a point and a centroid form the two legs of a right triangle, and the straight-line distance $d$ is the hypotenuse. Squaring both legs, summing them, then taking the square root gives that hypotenuse:
$$d = \sqrt{(x_i - x_C)^2 + (y_i - y_C)^2}$$
	- Square each coordinate difference, sum them, then take the square root to get back to original units. For $n$-dimensional data this extends to:
$$d = \sqrt{\sum_{j=1}^{n} (x_{ij} - x_{Cj})^2}$$
	- The n-dimensional formula above sums gaps across all $n$ attributes equally, so **feature scaling** matters. An attribute in the thousands will dominate one in single digits, the distance will be almost entirely determined by the larger attribute. A single outlier point can also pull a centroid strongly toward it as shown in Step 3.
	- Each point will then be assigned to the nearest centroid (smallest $d$). This defines the first $k$ clusters.

![[Pasted image 20260323171450.png|518]]
- **Step 3 - Update:** Recalculate each centroid as the mean of all points currently in that cluster. The centroid is defined as the "middle" of its members, and the most natural middle of a set of numbers is their arithmetic mean. So for each dimension, sum all values and divide by the number of points:
$$(x_{new_C},\ y_{new_C}) = \left(\frac{\sum_{i=1}^{m} x_i}{m},\ \frac{\sum_{i=1}^{m} y_i}{m}\right)$$
	- The centroid physically moves to the average position of its members. If assignments changed from the previous iteration, centroids will shift. If nothing changed, centroids stay in place and the algorithm has converged.
	- **Outlier sensitivity** - because the centroid is a simple arithmetic mean, one far point shifts it dramatically. For example, a cluster with points at $2, 3, 4, 5$:
$$\text{centroid} = \frac{2+3+4+5}{4} = 3.5$$
	- Add one outlier at $100$:
$$\text{centroid} = \frac{2+3+4+5+100}{5} = 22.8$$
One point moved the centroid from $3.5$ to $22.8$, pulling it completely away from the actual group. This is where the damage happens, not in the distance formula itself.

![[Pasted image 20260323171521.png]]
- **Step 4 - Repeat:** Go back to Step 2 and repeat until **convergence**, meaning the centroids no longer move. In practice, convergence is declared when the total accumulated centroid movement $\delta$ falls below a predefined threshold such as 0.002 using the following formula:
$$\delta = \sum_{j=1}^{k} \left\| \mu_j^{(t)} - \mu_j^{(t-1)} \right\|^2$$
	- Where $\mu_j^{(t)}$ (mu) is the position of centroid $j$ (cluster index, not a power) at the current iteration and $\mu_j^{(t-1)}$ is its position at the previous iteration. The superscript $(t)$ denotes the iteration number, parentheses distinguish it from an exponent.
	- The difference $\mu_j^{(t)} - \mu_j^{(t-1)}$ is a vector representing how much centroid $j$ moved. The $\|\cdot\|^2$ then extracts the magnitude of that movement vector as a scalar and squares it, the same L2 operation from Step 2, just applied to centroid movement rather than point-to-centroid distance. The algorithm uses the same ruler for everything.
	- Convergence is guaranteed but not always to the global optimum. Different starting centroids can produce different final clusters. Running the algorithm multiple times with different random seeds and keeping the result with the lowest total within-cluster variance is standard practice.
### k-Means Example
![[Pasted image 20260324144851.png]]
**Input**: Number of Objects = 6, Number of Clusters = 2

| No  | X   | Y   |
| --- | --- | --- |
| 1   | 1   | 1   |
| 2   | 2   | 3   |
| 3   | 1   | 2   |
| 4   | 3   | 3   |
| 5   | 2   | 2   |
| 6   | 3   | 1   |

- **Step 1** - Choose random K points using the **Forgy method** and set as cluster centers
	- $C_1 = (2,2)$
	- $C_2 = (3,3)$

- **Step 2** - Calculating the distance between objects into cluster centroids by using Euclidean Distance:
	- $d$ to $C_1$
		- $D1 = {(\underline{1}, 1),\ (2, 2)} = \sqrt{(2-1)^2 + (2-1)^2} = 1.41$
		- $D1 = {(\underline{2}, 3),\ (2, 2)} = \sqrt{(2-2)^2 + (2-3)^2} = 1$
		- $D1 = {(\underline{1}, 2),\ (2, 2)} = \sqrt{(2-1)^2 + (2-2)^2} = 1$
		- $D1 = {(3, 3),\ (2, 2)} = \sqrt{(2-3)^2 + (2-3)^2} = 1.41$
		- $D1 = {(2, 2),\ (2, 2)} = \sqrt{(2-2)^2 + (2-2)^2} = 0$
		- $D1 = {(3, 1),\ (2, 2)} = \sqrt{(2-3)^2 + (2-1)^2} = 1.41$
	- $d$ to $C_2$
		- $D2 = {(1, 1),\ (3, 3)} = \sqrt{(3-1)^2 + (3-1)^2} = 2.82$
		- $D2 = {(2, 3),\ (3, 3)} = \sqrt{(3-2)^2 + (3-3)^2} = 1$
		- $D2 = {(1, 2),\ (3, 3)} = \sqrt{(3-1)^2 + (3-2)^2} = \sqrt{5} \approx 2.236$
		- $D2 = {(3, 3),\ (3, 3)} = \sqrt{(3-3)^2 + (3-3)^2} = 0$
		- $D2 = {(2, 2),\ (3, 3)} = \sqrt{(3-2)^2 + (3-2)^2} = 1.41$
		- $D2 = {(3, 1),\ (3, 3)} = \sqrt{(3-3)^2 + (3-1)^2} = 2$
	=> $C_1 = \{(\underline{1}, 1),\ (1, 2),\ (2, 2),\ (3, 1)\}$
	=> $C_2 = \{(2, 3),\ (3, 3)\}$

- **Step 3** - Recalculating the position of the centroid:
	- $$C_1 = (\frac{1 + 1 + 2 + 3}{4},\frac{1 + 2 + 2 + 1}{4}) = (1.75, 1.5)$$
	- $$C_2 = (\frac{2 + 3}{2},\frac{3 + 3}{2}) = (2.5, 3)$$
- **Step 4** - Go back to Step 2, unless the centroids are not changing*.
### Generalizing to Higher Dimensions
When each data point has $n$ attributes (columns) rather than just two like $x$ or $y$, the Euclidean distance generalizes to:
$$d(p_i, q) = \sqrt{\sum_{j=1}^{n} (p_{ij} - q_j)^2}$$

| Point | $j=1$ Age | $j=2$ Income | $j=3$ Spending Score |
| ----- | --------- | ------------ | -------------------- |
| $p_1$ | 25        | 50000        | 72                   |
| $p_2$ | 40        | 80000        | 45                   |
| $p_3$ | 31        | 62000        | 60                   |

- $p_i$ is a data point (a row), $q$ is its assigned centroid, renamed from the Step 2 notation to generalize beyond $x$ and $y$.
- $p_{ij}$ means "the $j$-th attribute of point $i$", so as $j$ runs from $1$ to $n$, it steps through every column of that point and computes the gap against the corresponding centroid attribute $q_j$. Note that $C$ (the centroid) does not change as $j$ increases - $j$ only indexes the attribute dimension, not a different centroid.
- **Attributes** are just the columns of your dataset, the measured properties that describe each point. For example, if your data is customers with age, income, and spending score then $n = 3$, and $p_{1,2}$ means "customer 1's income" (point 1, attribute 2).
- It is the exact same operation as Step 2, just extended across all $n$ columns instead of only $x$ and $y$.

The centroid update becomes the mean across all $n$ dimensions:
$$(q_1, q_2, \ldots, q_n) = \left(\frac{\sum_{i=1}^{m} p_{i1}}{m},\ \frac{\sum_{i=1}^{m} p_{i2}}{m},\ \ldots,\ \frac{\sum_{i=1}^{m} p_{in}}{m}\right)$$
Each $q_j$ is just the average of all points in the cluster along attribute $j$.

### Choosing k: WSS and the Elbow Method
Because k-means requires you to specify $k$ in advance, choosing it well is critical. Three approaches exist: a reasonable prior guess, a predefined business requirement (e.g., a company always segments into five customer types), or the **Within Sum of Squares (WSS)** heuristic.

**WSS** is the sum of squared distances between each point and its closest centroid:
$$WSS = \sum_{i=1}^{M} d(p_i, q^{(i)})^2 = \sum_{i=1}^{M} \sum_{j=1}^{n} (p_{ij} - q_j^{(i)})^2$$
- The outer sum $\sum_{i=1}^{M}$ loops over every data point. The inner sum $\sum_{j=1}^{n}$ loops over every attribute of that point. Together: for each point, compute the squared gap in every dimension between that point and its assigned centroid, then add everything up.
- The superscript $(i)$ on $q^{(i)}$ means "the centroid that point $i$ is currently assigned to", same $(t)$ vs exponent distinction as before.
- Squaring serves two purposes: removes negatives so gaps do not cancel out, and penalises large distances more than small ones.

![[Pasted image 20260323171838.png]]
Adding more clusters always reduces WSS, but at some point the reduction becomes marginal. Note that WSS hits zero when $k = M$ (every point is its own cluster), so a lower WSS is not automatically better. The appropriate $k$ is identified by finding the **elbow** of the WSS curve: the point where the rate of improvement sharply drops off, meaning adding one more cluster stops being worth it.

When evaluating a candidate clustering visually, ask: are clusters well separated, does any cluster contain only a few points, and do any centroids sit too close together?

### Choosing k: Silhouette Analysis
![[plot_kmeans_silhouette_analysis_001.png]]
**Silhouette analysis** measures how well each point fits its assigned cluster compared to neighboring clusters. For each point $i$, compute:
- $a^i$ - mean distance to all other points in the same cluster (how tight is its own cluster)
- $b^i$ - mean distance to all points in the nearest other cluster (how far is the next closest cluster)

Then calculate the silhouette coefficient:
$$s^i = \frac{b^i - a^i}{\max(a^i,\ b^i)}$$
The $\max(a^i, b^i)$ in the denominator normalizes the result to the $[-1, 1]$ range by dividing by whichever distance is larger, keeping the score bounded regardless of the data's scale.

**Example:**
Say for point $i$:
- $a^i = 3$ (average distance to its own cluster members)
- $b^i = 7$ (average distance to the nearest other cluster)
$$s^i = \frac{7 - 3}{\max(3, 7)} = \frac{4}{7} \approx 0.57$$
The $\max$ picks $7$, so you divide by the larger of the two. This keeps the result capped at $1$ no matter how large the gap is.
Now flip it, a poorly assigned point where $a^i = 8$ and $b^i = 2$:
$$s^i = \frac{2 - 8}{\max(8, 2)} = \frac{-6}{8} = -0.75$$
Negative because $b < a$, meaning the point is closer to another cluster than its own. The $\max$ again picks the larger value ($8$) to keep the result bounded above $-1$.

Without the $\max$ you could get scores outside $[-1, 1]$ depending on the data scale, making scores from different datasets incomparable.

The coefficient ranges from $-1$ to $1$. A value near $1$ means the point is well within its cluster and far from others. A value near $0$ means the point sits on a boundary. A value of $-1$ signals likely misassignment. A good clustering has an average silhouette score close to $1$; use the $k$ that maximizes this score.

### Practical Cautions
**Attribute selection** is a key practitioner decision. Only attributes that will be available when assigning new objects should be included. Too many attributes dilute the influence of the most important ones; highly correlated attributes should be reduced to one representative, or combined into a derived ratio such as debt/asset ratio.

![[Pasted image 20260323172821.png]]
**Units of measure** matter because k-means is distance-based. Measuring height in centimeters versus meters produces entirely different cluster boundaries over the same data. Attributes with vastly different numeric scales should be normalized or standardized before clustering.

**Data shape** constraints mean k-means works best when clusters are spherical and have similar variance. Non-spherical shapes (such as crescent or ring distributions) cause k-means to fail, since it cannot handle clusters whose boundary-to-centroid distance is non-uniform.

**Nominal attributes** (e.g., color categories) are not suited to k-means because Euclidean distance between category labels is meaningless.

Other distance metrics such as Manhattan or Mahalanobis can be substituted for Euclidean distance in specific situations.

### Key Takeaways
1. k-Means is an unsupervised, iterative algorithm that assigns points to the nearest centroid and then updates centroids until convergence.
2. The value of $k$ must be chosen before the algorithm runs; WSS and silhouette analysis both guide this choice.
3. Attribute scale dominates Euclidean distance; always normalize attributes with very different ranges.
4. k-Means assumes spherical, similarly-sized clusters; violations lead to poor results.
5. Nominal attributes cannot be used directly with k-means.

**Remember this:** k-means finds the most compact grouping it can given $k$, but it has no way of knowing whether $k$ was the right number to start with.

**One analogy to remember it by:** k-means is like sorting people into $k$ seats by repeatedly moving each person to the nearest chair and then sliding each chair to the centre of its group - it keeps adjusting until no one moves.

---

## Part 3: DBSCAN

### What is DBSCAN?
**DBSCAN** (Density-Based Spatial Clustering of Applications with Noise) is an unsupervised clustering algorithm that groups points based on local density rather than distance to a centroid.
![[Pasted image 20260323173123.png]]
In simple terms: instead of forcing all data into $k$ round bubbles, DBSCAN grows clusters by connecting nearby dense regions and marks isolated points as noise.

Think of it like: identifying populated towns on a map by tracing connected settlements, while labelling isolated farmhouses as outliers.

Why it matters: many real-world datasets have irregular cluster shapes that k-means cannot handle. DBSCAN discovers arbitrary cluster shapes and explicitly identifies outliers, making it useful for anomaly detection and geospatial analysis.

### DBSCAN Parameters and Definitions
DBSCAN has two parameters:
- $\varepsilon$ (Epsilon) defines the neighborhood radius around any point
- $minPts$ (Minimum number of points) sets the minimum number of neighbors within that radius for a point to be considered a **core point**.

DBSCAN definitions:
- A **core point** has at least $minPts$ neighbors within distance $\varepsilon$.
- A **directly reachable** point lies within distance $\varepsilon$ of a core point but is not a core point itself.
- A **density-reachable** point is not directly reachable from a core point, but can be reached by hopping through a chain of core points. This is what allows clusters to grow beyond a single core point's neighbourhood.
- A **border point** is directly reachable from a core point but has fewer than $minPts$ neighbours of its own, so it cannot extend the cluster further.
- An **outlier** (noise point) has no core point within distance $\varepsilon$ of it and belongs to no cluster.

Clusters are formed by connecting core points to all points reachable from them, either directly or through density-reachability.
![[Pasted image 20260323230640.png]]

### DBSCAN vs k-Means
![[Pasted image 20260323173911.png]]

| Criterion                | k-Means                               | DBSCAN                                                        |
| ------------------------ | ------------------------------------- | ------------------------------------------------------------- |
| Number of clusters       | Must specify $k$ in advance           | Determined automatically                                      |
| Cluster shape assumption | Spherical, similar variance           | Arbitrary shape                                               |
| Outlier handling         | All points assigned to a cluster      | Noise points explicitly identified                            |
| Key parameters           | $k$                                   | $\varepsilon$, $minPts$                                       |
| Best used when           | Clusters are compact and round        | Data has irregular shapes or outliers                         |
| Trade-offs               | Sensitive to initialisation and scale | Sensitive to parameter choice; struggles with varying density |

### Key Takeaways
1. DBSCAN grows clusters from dense regions outward, requiring no pre-specified cluster count.
2. $\varepsilon$ and $minPts$ together define what "dense" means; poorly chosen values produce poor clusters.
3. Outliers are first-class outputs: DBSCAN labels them explicitly rather than forcing them into the nearest cluster.
4. DBSCAN handles non-spherical clusters where k-means fails.
5. As with k-means, the concept of distance must be meaningful for the chosen attributes.

**Remember this:** DBSCAN does not look for centres - it looks for crowds, and anything standing alone is noise.

**One analogy to remember it by:** DBSCAN is like a city planner drawing suburb boundaries by tracing areas where houses are densely packed, leaving isolated rural buildings off the map entirely.