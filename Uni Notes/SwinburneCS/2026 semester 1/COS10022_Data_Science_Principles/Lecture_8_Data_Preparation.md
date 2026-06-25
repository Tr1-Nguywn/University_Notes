## Part 1: Data Quality and the Preparation Pipeline

### What is Data Preparation?
**Data preparation** is the process of transforming raw data into a clean, consistent, and usable form before analysis or modelling begins.

In simple terms: it is the work of making messy real-world data good enough to learn from.

Think of it like: set up in cooking - before you can cook anything useful, you have to wash, cut, and organise every ingredient. Skipping this step ruins the meal regardless of how good your recipe is.

Why it matters: garbage in, garbage out. Even the most sophisticated model will produce misleading results if the input data is incomplete, noisy, or inconsistent. Data preparation is estimated to consume 60–80% of a data scientist's time on any real project.

![[Pasted image 20260511193019.png]]
Data preparation is also known as **data wrangling** or **data munging**. In the Data Analytics Lifecycle it sits at Phase 2, after discovery, and directly precedes model planning.

![[Pasted image 20260511195132.png]]
The team uses an analytics sandbox environment and performs **ETLT** (Extract, Transform, Load, Transform) a combined approach drawing on both ETL and ELT to bring data in and familiarise themselves with its structure before building anything.

### The Six Dimensions of Data Quality
Data has **quality** when it satisfies the requirements of its intended use. The six dimensions used to assess this are accuracy, completeness, consistency, timeliness, believability, and interpretability.
1. **Accuracy** is the degree to which data represents reality. An incorrect salary value like `-100` is inaccurate.
2. **Completeness** is the degree to which necessary data is available for use - a missing occupation field is an incompleteness problem.
3. **Consistency** is the degree to which data is equal within and between datasets; a record where `Age = 36` but `Birthday = 31/08/1984` (implying age 41) is inconsistent.
4. **Timeliness** means data is available when it is needed.
5. **Believability** is whether users actually trust the data.
6. **Interpretability** is the ease with which the data can be understood.

### The Four Major Tasks in Data Preparation
![[Pasted image 20260515002338.png]]
The four tasks form a logical pipeline, often applied iteratively:
1. **Data Cleaning** fills in missing values, smooths noisy data, identifies or removes outliers, and resolves inconsistencies.
2. **Data Integration** merges data from multiple sources to reduce redundancies and inconsistencies in the resulting dataset.
3. **Data Reduction** obtains a smaller-volume representation that still produces the same or near-same analytical results.
4. **Data Transformation** converts the data into appropriate formats and scales for mining, making output easier to interpret.

### Key Takeaways
1. Data preparation is Phase 2 of the Data Analytics Lifecycle and is the most time-consuming phase in practice.
2. Quality is always measured relative to intended use - data that is good enough for one task may be inadequate for another.
3. The four tasks (cleaning, integration, reduction, transformation) are distinct but often applied together in a feedback loop.

**Remember this:** You cannot build a reliable model on unreliable data; preparation is the foundation, not a formality.

**One analogy to remember it by:** Raw data is like unprocessed ore - valuable minerals are in there, but you have to smelt and refine it before you can build anything with it.

---

## Part 2: Data Cleaning

### What is Data Cleaning?
**Data cleaning** is the process of detecting and correcting (or removing) corrupt, inaccurate, or otherwise problematic records in a dataset.

In simple terms: it is fixing what is wrong with your data before you use it.

Think of it like: proofreading a document - you go through line by line looking for errors, missing words, and contradictions before publishing.

Why it matters: real-world data is almost always dirty. Feeding dirty data into a model produces results that look valid but are actually misleading.

### The Three Types of Dirty Data
![[Pasted image 20260515002615.png]]
In short, **discrepancies**.

**Incomplete data** has missing attribute values or lacks attributes of interest like `Age` entirely. A tuple where `Gender = ""` is incomplete. This can happen because attributes were not considered important at time of entry, due to equipment malfunctions, because inconsistent records were deleted, or because data history was not tracked. Missing data often needs to be inferred rather than simply discarded.

**Noisy data** contains random errors or variance in measured values. A record with `Salary = -100` or an income of `10000K` (plausible typo for a regular income) exemplifies (minh hoạ) this. Noise arises from faulty collection instruments, data entry problems, data transmission errors, technology limitations, and inconsistent naming conventions. Noise can also appear as **class noise** (contradictory or mislabelled examples) or **attribute noise** (erroneous or missing values).

**Inconsistent data** contains discrepancies in codes or names. A dataset where the rating system changed from `1, 2, 3` to `A, B, C` mid-collection, or duplicate records where the same person is listed with different marital statuses, are inconsistent. So is any record where two logically related fields contradict each other. 

### Handling Missing Data
There are six standard approaches, each with trade-offs:
1. **Ignore the tuple**, typically done when the class label is missing in a classification task. This is not very effective unless the tuple has many missing values, as it discards potentially useful information in other fields.
2. **Fill in missing values with side information** from an external source. This is the most accurate approach but is time-consuming and impractical at scale.
3. **Use a global constant** such as `"Unknown"` or `"N/A"`. This is simple but dangerous: a mining algorithm may mistakenly treat all unknown values as sharing a meaningful common property.
4. **Use a measure of central tendency** for the attribute. For normally distributed data, use the mean (average); for skewed distributions, use the median (middle value). If `Download Speed` has missing values, compute the mean of all known values and substitute it.
	- ![[Pasted image 20260515004658.png]]
5. The fifth is a refinement of the fourth: **use the class-conditional mean or median**, that is, the average computed only from tuples in the same class as the tuple with the missing value. For example, replace missing income values with the average download speed in the same mobile package category.
6. The sixth and most sophisticated is to **use the most probable value**, determined via regression, Bayesian inference, or decision tree induction. For example, a decision tree built on other Titanic passenger attributes can predict the missing `sibsp` (siblings/spouses aboard) value for a given passenger.
	- ![[Pasted image 20260515004914.png]]

### Handling Noisy Data

#### Binning
**Binning** smooths data by sorting values and partitioning them into equal-frequency buckets/bins, then replacing each value with either the bin mean (smoothing by bin means) or the nearest boundary value (smoothing by bin boundaries). Given sorted prices into 3 bins `4, 8, 9, 15 | 21, 21, 24, 25 | 26, 28, 29, 34`. Smoothing by bin means replaces each bin with `9, 9, 9, 9`, `23, 23, 23, 23`, etc. While smoothing by bin boundaries replaces each value with whichever boundary (min or max of the bin) it is closest to.

#### Regression
![[Pasted image 20260515010352.png]]
**Regression** conforms data values to a function. Linear regression fits a straight line `y = x + 1` to two attributes so one can predict the other, which has the effect of smoothing outlier values toward the fitted line.

#### Sliding Window
![[Pasted image 20260515010623.png]]
**Sliding window** (moving average) computes the average of each point's neighbourhood by sliding a fixed-size window across the data one step at a time, replacing each point with the average of the values the window covers. Given `2, 5, 1, 7, 4, 3, 6, 6, 8` with a window of size 3, the processed output is `3, 4, 4, 5, 4, 5, 7`. Note the output is shorter than the input because the window requires a full set of neighbours at each position.

This operation is also called **[Convolution](https://www.youtube.com/watch?v=KuXjwB4LzSA) (Tích chập)**, where the window acts as a kernel with equal weights [1/3, 1/3, 1/3] applied at each position meaning each of the 3 values in the window contributes equally to the average.

#### Outlier Analysis
![[Pasted image 20260515010645.png]]
**Outlier analysis** uses clustering to detect values that fall outside the main cluster groups. Each cluster centroid represents the average position of a cluster; points that lie far from all centroids are flagged as outliers.

### Detecting Discrepancies
**Discrepancies** are just any unexpected or conflicting values in the data that suggest something is wrong.
That includes all three dirty data types:
- missing values (incomplete)
- wrong or weird values like Salary = -100 (noisy)
- contradicting values like Age = 36 but birthday says 41 (inconsistent)

The first step in data cleaning as a process is **discrepancy detection**. Discrepancies are caused by poorly designed entry forms, human errors, deliberate errors (e.g. respondents withholding information), data decay (e.g. outdated addresses), instrumentation faults, system errors, and inconsistencies from data integration.

Detection methods include: 
- Using **metadata** (check expected ranges, data types, and valid domains for each attribute).
- Applying **uniqueness, consecutive, and null rules** (the unique rule requires every value of a primary key to differ from all others; the consecutive rule requires no gaps between the lowest and highest values; the null rule specifies how null conditions are represented).
	- ![[Pasted image 20260515014955.png]]
- Using **commercial tools** such as Power BI that perform data scrubbing (using domain knowledge for **corrections**) and data auditing (using correlation and clustering to **find** rule violators).
- Using **ETL tools** that allow transformation specifications through a graphical interface.

### Key Takeaways
1. Real-world data is dirty by default; incomplete, noisy, and inconsistent data must each be addressed differently.
2. The choice of imputation method for missing data depends on the distribution of the data and the downstream task.
3. Discrepancy detection is the prerequisite for any cleaning action - you must find the problem before you can fix it.

**Remember this:** Data cleaning is not just removing bad rows; it is a structured process of detection, decision, and repair applied to each type of data quality problem.

**One analogy to remember it by:** Cleaning data is like restoring a damaged painting - you identify what is missing, what is distorted, and what does not belong, then apply the least-destructive correction that preserves the original intent.

---

## Part 3: Data Integration

### What is Data Integration?
**Data integration** combines data from multiple sources (databases, data cubes, flat files) into a coherent unified store, as in data warehousing.

In simple terms: it is the work of merging separate datasets into one consistent whole.

Think of it like: merging two different contact lists where the same person may be saved under slightly different names in each list.

Why it matters: organisations store data in many separate systems. Meaningful analysis usually requires combining them, and the integration process itself introduces new data quality problems that must be managed.

### The Entity Identification Problem
When merging sources, matching equivalent real-world entities is a core challenge. `Bill Clinton` and `William Clinton` refer to the same person. `customer_id` in one database may be the same as `cust_number` in another. Resolving these equivalences is the **entity identification problem**.

Beyond naming, the same entity may have different attribute values across sources due to different representations or measurement scales (e.g. metric vs. imperial units). Integration must detect and resolve these **data value conflicts**.

### Detecting Redundant Attributes with Correlation Analysis
**Redundant data** arises frequently during integration. Two sources may have given the same attribute different names (object identification redundancy), or one source may store a value that is derivable from another (e.g. annual revenue derived from monthly sales). Redundant attributes can be detected using **correlation analysis**, which measures how strongly one attribute implies another.

#### Pearson Correlation Coefficient
![[Pasted image 20260515030502.png]]
For **numerical data**, the **Pearson Correlation Coefficient** $r_{A,B}$ is used:
$$r_{A,B} = \frac{\sum_{i=1}^{n}(a_i - \bar{A})(b_i - \bar{B})}{(n-1)\sigma_A \sigma_B} = \frac{\sum_{i=1}^{n}(a_i b_i) - n \bar{A} \bar{B}}{(n-1)\sigma_A \sigma_B}$$
The Pearson Correlation Coefficient $r_{A,B}$ takes $n$ tuples and uses the means $\bar{A}, \bar{B}$ and standard deviations $\sigma_A, \sigma_B$ of both attributes to produce a value in $[-1, 1]$. A positive $r$ means the attributes move together, $r = 0$ indicates no relationship, and a negative $r$ means they move in opposite directions.

**Worked example:** For stock prices of `AllElectronics` `(6, 5, 4, 3, 2)` and `HighTech` `(20, 10, 14, 5, 5)` across five time points:
$$\bar{A} = 4, \quad \bar{B} = 10.8, \quad \sigma_A = 1.58, \quad \sigma_B = 5.74$$
$$r_{A,B} = \frac{(2)(9.2) + (1)(-0.8) + (0)(3.2) + (-1)(-5.8) + (-2)(-5.8)}{(5-1)(1.58)(5.74)} = \frac{30}{36.27} \approx 0.83$$
Since $r = 0.83$ is close to 1, the two stocks are strongly positively correlated, meaning they tend to rise and fall together.

#### Covariance
**Covariance (hiệp phương sai)** is a related measure for numerical data. Covariance and correlation both measure the linear relationship between two variables, but differ significantly in interpretation: covariance only indicates the direction of the relationship (positive or negative) and depends on unit scales, while correlation standardizes this relationship, measuring both direction and strength on a unit-free scale from -1 to +1 :
$$Cov(A,B) = E((A - \bar{A})(B - \bar{B})) = \frac{\sum_{i=1}^{n}(a_i - \bar{A})(b_i - \bar{B})}{n} = \frac{\sum_{i=1}^{n} a_i b_i}{n} - \bar{A}\bar{B}$$
A positive covariance means attributes tend to rise together; negative means opposite; zero suggests independence, though note that zero covariance does not guarantee independence since non-linear relationships can still exist.

**Worked example:** For stock prices of AllElectronics `(6, 5, 4, 3, 2)` and HighTech `(20, 10, 14, 5, 5)` across five time points:
$$E(\text{AllElectronics}) = \frac{20}{5} = $4, \quad E(\text{HighTech}) = \frac{54}{5} = $10.8$$
$$Cov = \frac{6 \times 20 + 5 \times 10 + 4 \times 14 + 3 \times 5 + 2 \times 5}{5} - 4 \times 10.8 = 50.2 - 43.2 = 7$$
Since $Cov > 0$, both stocks tend to rise together.

#### Chi-Squared Test
![[Pasted image 20260515033306.png]]
For categorical data, chi-squared tests whether two attributes are correlated by comparing actual counts against what you would expect if they had no relationship.
$$\chi^2 = \sum_{i=1}^{c}\sum_{j=1}^{r} \frac{(o_{ij} - e_{ij})^2}{e_{ij}}, \quad e_{ij} = \frac{count(A=a_i) \times count(B=b_j)}{n}$$

Where $o_{ij}$ is the observed count, $e_{ij}$ is the expected count if both attributes were independent, A has $c$ distinct values, B has $r$ distinct values, and $n$ is the total tuples. Differences are squared so negatives do not cancel positives, then divided by $e_{ij}$ to scale relative to the expected count. A larger $\chi^2$ means stronger correlation.

**Significance level ($\alpha$)** is a probability threshold set before the test, typically 0.05 or 0.001. It represents the maximum risk you accept of wrongly concluding a correlation exists. A stricter $\alpha$ requires a higher $\chi^2$ to reject independence.

**Degrees of freedom** $df = (r-1)(c-1)$ represents how many cells are free to vary once row and column totals are fixed. In a 2x2 table, knowing one cell forces all others, so $df = 1$. This determines which row of the $\chi^2$ table to use for the critical value.

**Critical value** is the threshold from the $\chi^2$ table at your chosen $\alpha$ (significant level/xắc suất sai) and $df$. If your computed $\chi^2$ exceeds it, the null (original) hypothesis of independence is rejected.

**Worked example:** 1500-person survey on gender and reading preference.
Observed value $o_{ij}$:

|             | Male | Female | Total |
| ----------- | ---- | ------ | ----- |
| Fiction     | 250  | 200    | 450   |
| Non-fiction | 50   | 1000   | 1050  |
| Total       | 300  | 1200   | 1500  |

Expected values using $e_{ij} = (\text{row total} \times \text{col total}) / n$:
- Fiction | Male: $(450×300)/1500=90$
- Fiction | Female: $(450×1200)/1500=360$
- Non-fiction | Male: $(1050×300)/1500=210$
- Non-fiction | Female: $(1050×1200)/1500=840$

|             | Male | Female |
| ----------- | ---- | ------ |
| Fiction     | 90   | 360    |
| Non-fiction | 210  | 840    |

$$\chi^2 = \frac{(250-90)^2}{90} + \frac{(50-210)^2}{210} + \frac{(200-360)^2}{360} + \frac{(1000-840)^2}{840} = 507.93$$
With $df = 1$ and $\alpha = 0.001$, the critical value is 10.83. Since $507.93 > 10.83$, the null hypothesis is rejected - gender and reading preference are strongly correlated/dependent.

### Key Takeaways
1. Entity identification is a prerequisite for integration: you must confirm that two records refer to the same real-world object before merging them.
2. Redundancy detection via correlation analysis prevents bloated, misleading datasets from entering the pipeline.
3. The right correlation measure depends on data type: Pearson/covariance for numerical, chi-squared for categorical.

**Remember this:** Integration does not just combine data - it introduces new errors; treat it as another source of data quality problems that must be actively managed.

**One analogy to remember it by:** Merging datasets is like translating two books written in different dialects into one - you must reconcile different names for the same concepts, resolve contradictions, and eliminate chapters that repeat each other.

---

## Part 4: Data Reduction

### What is Data Reduction?
**Data reduction** produces a smaller-volume representation of a dataset that preserves the same (or nearly the same) analytical results as the original.

In simple terms: it makes the dataset smaller so analysis runs faster, without throwing away information that matters.

Think of it like: summarising a 500-page book into a 20-page study guide - the key ideas are preserved but the volume is dramatically reduced.

Why it matters: data warehouses can hold terabytes. Complex mining algorithms run for prohibitively (quá mức) long times on full datasets. Reduction makes analysis computationally tractable.

### The Five Data Reduction Strategies
#### Data Cube Aggregation
![[Pasted image 20260515175307.png]]
**Data cube aggregation** applies aggregation (tổng hợp) operations when constructing a data cube. For example, quarterly sales data for 2002–2004 can be aggregated to annual totals without losing the analytical conclusion about year-over-year trends. So in short, higher-level. Data cubes store multidimensional aggregate values and provide fast access for online analytical processing.

#### Attribute Subset Selection
**Attribute subset selection** removes irrelevant, weakly relevant, or redundant attributes from the dataset. Irrelevant attributes add noise without contributing predictive power, weakly relevant ones have minimal impact, and redundant ones can be derived from other attributes already present. Removing them reduces the data volume, speeds up mining, and often improves model accuracy.

Exhaustive search over all possible subsets is infeasible because with $n$ attributes there are $2^n$ possible subsets. For even 30 attributes that is over a billion combinations. Heuristic search methods are used instead to find a good enough subset without checking every possibility.

The four common methods are:
1. **Stepwise forward selection** starts with an empty set and at each step adds the single attribute that most improves the model. It continues until adding any remaining attribute no longer improves performance. This is efficient when the relevant subset is small since you only build up what you need.
2. **Stepwise backward elimination** starts with the full set of attributes and at each step removes the single attribute that contributes least to the model. It continues until removing any remaining attribute causes a significant drop in performance. This is better when most attributes are relevant and you only need to trim a few.
3. **Combined forward-backward selection** does both simultaneously at each step, adding the best attribute and removing the worst. This is more flexible than either alone since it can correct earlier decisions, for example removing an attribute that was useful early on but became redundant after a better one was added.
4. **Decision tree induction** builds a decision tree directly from the data where each internal node tests an attribute, each branch corresponds to a test outcome, and each leaf node holds a class prediction. Attributes that never appear as split points in the tree are considered irrelevant to the classification task. The subset of attributes that do appear becomes the reduced attribute set, making the tree itself the selection mechanism.

#### Dimensionality Reduction
![[Pasted image 20260515175553.png]]
**Dimensionality reduction** encodes data into a compressed form with fewer dimensions while preserving as much useful information as possible. If the original data can be fully reconstructed from the compressed form, it is lossless. If only an approximation can be recovered, it is lossy. Most real-world dimensionality reduction is lossy since perfect reconstruction would require storing nearly as much information as the original.
![[Pasted image 20260515180351.png]]
The canonical (chuẩn mực) technique is **Principal Component Analysis (PCA)**. The core idea is that high-dimensional data often has redundancy, meaning many attributes are correlated with each other. PCA finds new axes called principal components that capture the directions of maximum variance in the data, then projects the data onto a smaller number of those axes.

The first principal component (PC1) captures the most variance, the second (PC2) captures the next most, and so on. By keeping only the top $k$ components you retain most of the information while discarding the dimensions that contribute little variance, which are often just noise. For example:
Gene expression data measured across three genes lives in 3D space. PCA can project it down to two dimensions (PC1, PC2) such that the separation between experimental conditions is preserved even though one dimension was dropped. The dropped dimension carried little variance so losing it barely affects the analysis.

The key trade-off is choosing how many components to keep. Keeping more components preserves more information but reduces less. Keeping fewer compresses more aggressively but risks losing meaningful structure. In practice you keep enough components to explain a target percentage of total variance, commonly 95%.

#### Numerosity Reduction
![[Pasted image 20260515181139.png]]
**Numerosity reduction** replaces the original data with a smaller alternative representation that still preserves the analytical results. There are two broad methods.

**Parametric methods** assume the data fits a known model, so instead of storing all the data points you only store the model parameters. Three common models are used:
- Linear regression $Y = b_0 + b_1 X_1$ fits a straight line between two attributes, where $b_0$ is the intercept and $b_1$ is the slope. Given $X$, you can predict $Y$ without storing every data point.
- Multiple linear regression $Y = b_0 + b_1 X_1 + b_2 X_2$ extends this to two or more predictors, capturing more complex relationships.
- Log-linear model $\ln Y = b_0 + b_1 X_1 + \varepsilon$ is used when the relationship between attributes is exponential rather than linear. $\varepsilon$ represents the error term.
In all cases, the data is summarised by just a few parameters rather than thousands of tuples.

**Non-parametric methods** make no assumption about the underlying distribution and store a reduced form of the data directly:
- Binning groups values into intervals and stores the bin summary instead of individual values.
- Histograms partition data into buckets and store the frequency or average per bucket.
- **Clustering** groups similar data points together and stores only the cluster centroids rather than every point.
- Sampling stores a representative subset of the data rather than the full dataset.

#### Discretisation and Concept Hierarchy Generation
**Discretisation and concept hierarchy generation** replaces continuous raw values with interval labels or higher-level concepts, for example replacing a raw age value with youth, adult, or senior. This is covered in detail in Part 5.

### Sampling Methods
Sampling allows a large dataset $D$ of $N$ tuples to be represented by a much smaller random subset of size $s$. The four common methods are:
1. **SRSWOR** (Simple Random Sample Without Replacement): all tuples are equally likely; once selected, a tuple is not returned to the pool.
2. **SRSWR** (Simple Random Sample With Replacement): after a tuple is drawn it is returned to $D$, so it may be selected again.
3. **Cluster sampling**: $D$ is partitioned into $M$ disjoint clusters; $s$ clusters are randomly selected and all tuples within them are included. 
	- ![[Pasted image 20260515183448.png]]
4. **Stratified sampling**: $D$ is divided into disjoint strata (e.g. by age group); an SRS (Simple Random Sample) is drawn from each stratum, which is especially useful when the overall data distribution is skewed.
	- ![[Pasted image 20260515183502.png]]

### Key Takeaways
1. The five strategies address different dimensions of data volume: aggregation reduces temporal resolution, attribute selection removes dimensions, dimensionality reduction compresses features, numerosity reduction models the distribution, and discretisation abstracts values.
2. PCA is the dominant dimensionality reduction method and is lossless in the number of components kept, but lossy if you drop low-variance components.
3. Stratified sampling is preferred over simple random sampling when classes are imbalanced, because it ensures minority classes are represented.

**Remember this:** Reduction is not about losing data - it is about retaining the analytical signal while discarding redundant volume.

**One analogy to remember it by:** Data reduction is like compressing an image file - the smaller JPEG still looks like the photo, but unnecessary pixel detail has been discarded to make the file manageable.

---

## Part 5: Data Transformation

### What is Data Transformation?
**Data transformation** converts source data into formats and scales that are more suitable for mining and make outputs easier to interpret.

In simple terms: it reshapes and rescales the data so algorithms can work with it effectively.

Think of it like: converting ingredients into standard units before cooking - grams, millilitres, and degrees Celsius instead of a pinch, a splash, and a moderate heat.

Why it matters: many algorithms are sensitive to scale differences (e.g. distance-based clustering), data type assumptions, or require values in a specific range to converge efficiently (e.g. neural network backpropagation).

### The Six Transformation Strategies
1. **Smoothing**: removes noise using binning, regression, or clustering as described in Part 2.
2. **Attribute/feature construction**: creates new attributes from existing ones. For example, deriving a `profit_margin` attribute from `revenue` and `cost`.
3. **Aggregation**:constructs data cubes, rolling up data to higher levels of granularity (also discussed under data reduction).
4. **Normalisation**: **scales** attribute values to a specified range so that attributes with large natural ranges do not dominate those with small ones. Three methods are in common use.
5. **Discretisation**: replaces continuous numeric values with interval labels or concept labels (e.g. replacing raw age with `youth`, `adult`, `senior`). Techniques include binning, histogram analysis, cluster analysis, decision tree analysis, and correlation analysis.
6. **Concept hierarchy generation for nominal data**: generalises lower-level attribute values to higher-level concepts (e.g. city to province to country).

### Normalisation in Detail
Three normalisation methods are standard.

#### Min-Max Normalisation
**Min-Max normalisation** maps each value linearly into a target range $[new\_min_A, new\_max_A]$:
$$v' = \frac{v - min_A}{max_A - min_A}(new\_max_A - new\_min_A) + new\_min_A$$
This is the most intuitive method. You subtract the minimum to shift the data to zero, divide by the range to scale it to $[0, 1]$, then stretch and shift it into the target range. The result is a direct linear mapping where the original min becomes $new\_min_A$ and the original max becomes $new\_max_A$.

Example: Income ranges \$12,000–\$98,000 normalised to $[0.0, 1.0]$. Then \$73,600 maps to:
$$v' = \frac{73600 - 12000}{98000 - 12000}(1.0 - 0) + 0 = 0.716$$

The main weakness is sensitivity to outliers. A single extreme value shifts the min or max, compressing all other values into a narrow band.

#### Z-Score Normalisation
**Z-score normalisation** rescales values based on how far they are from the mean in units of standard deviation:
$$v' = \frac{v - \mu_A}{\sigma_A}$$
This is useful when the actual min and max are unknown or when the data follows a roughly normal distribution. The output is not bounded to a fixed range; values can be negative or greater than 1. A result of 0 means the value equals the mean, positive means above, and negative means below.

Example: With $\mu =$ \$54,000 and $\sigma =$ \$16,000, then \$73,600 transforms to:
$$\frac{73600 - 54000}{16000} = 1.225$$

This means \$73,600 is 1.225 standard deviations above the mean income.

#### Decimal Scaling
**Decimal scaling** normalises by dividing each value by a power of 10, choosing the smallest $j$ such that $\max(|v'|) < 1$:
$$v' = \frac{v}{10^j}$$

It is the simplest method and requires no knowledge of the mean or range. You just find the smallest $j$ that brings all values below 1 in absolute terms. It does not centre the data or bound it symmetrically, so it is mainly used for quick approximations.

Example: Values range from $-986$ to $917$. The largest absolute value is $986$, which has 3 digits, so $j = 3$. Dividing by $1000$ gives $-0.986$ to $0.917$.

| Method | Formula | Best Used When | Trade-offs |
|---|---|---|---|
| Min-Max | $\frac{v - min}{max - min}$ | Range is known and fixed | Sensitive to outliers shifting the bounds |
| Z-score | $\frac{v - \mu}{\sigma}$ | True min/max unknown; near-normal distribution | Does not bound output to a fixed range |
| Decimal scaling | $\frac{v}{10^j}$ | Quick approximation needed | Rough; does not centre data |

### Discretisation in Detail
Discretisation (sự rời rạc hoá) replaces continuous numeric values with **discrete interval labels** or category labels, reducing complexity an improving interpretability. There are four common techniques.

#### Binning
Binning groups continuous values into intervals and replaces each value with a label representing its bin. **Equi-width binning** divides the range into intervals of equal size, while **equi-depth binning** divides the data so each bin contains the same number of values. The number of bins controls the resolution of the discretisation; fewer bins produce coarser, more generalised categories while more bins preserve finer distinctions. Binning is unsupervised since it ignores class labels entirely.

#### Decision Tree Analysis
Decision tree analysis for discretisation is a top-down supervised approach, meaning it uses class labels to guide where to split. Entropy measures the class homogeneity of an interval:
$$Ent(U) = -\sum_{i=1}^{k} p_i \cdot \log_2 p_i$$

where $p_i$ is the proportion of values in the $i$-th class. An entropy of 0 means all values in the interval belong to the same class (perfectly homogeneous). The algorithm finds the split point that minimises the weighted total entropy $E_T$ across the resulting partitions, then recurses on each partition until a stopping criterion is met.

#### Correlation Analysis (ChiMerge)
ChiMerge is a bottom-up supervised merging approach, the opposite direction from decision trees. It starts with each distinct value as its own interval, then iteratively merges neighbouring intervals whose class distributions are most similar, measured by a low $\chi^2$ value between them. Merging continues until the $\chi^2$ between all remaining neighbouring intervals exceeds a threshold, meaning further merging would combine intervals with meaningfully different class distributions.

#### Clustering
Clustering applies an algorithm such as k-means to the continuous values, partitioning them into $k$ groups based on similarity. Each resulting cluster becomes a discrete category. Unlike binning, clustering does not require fixed interval sizes and can adapt to the natural groupings in the data.

### Concept Hierarchy Generation
For **nominal attributes** (finite sets of **unordered** values such as city names), concept hierarchies allow values to be generalised to higher-level concepts, enabling analysis at multiple levels of granularity. For example, a city can be rolled up to state, then to country.

Four methods exist for generating these hierarchies. The first is explicit partial ordering specified by domain experts directly at the schema level, for example `street < city < state < country`, which defines the generalisation path upfront. The second is explicit data grouping, where specific values are manually mapped to a higher concept, for example `{Urbana, Champaign, Chicago} < Illinois`. The third is automatic generation by counting distinct values; attributes with more distinct values are placed lower in the hierarchy since they are more specific, and the hierarchy is built by ordering attributes from most to least distinct. The fourth is partial specification, where only some levels of the hierarchy are defined and the rest are inferred or left flexible.

### Key Takeaways
1. Normalisation is essential for distance-based and gradient-based algorithms; without it, high-magnitude attributes dominate.
2. Discretisation trades fine-grained precision for interpretability and reduced complexity; the right number of bins is task-dependent.
3. Concept hierarchies enable multi-granularity analysis and are essential for OLAP-style queries over categorical dimensions.

**Remember this:** Transformation is not about changing what the data means - it is about changing how it is expressed so algorithms and humans can work with it more effectively.

**One analogy to remember it by:** Transforming data is like converting a recipe from imperial to metric - the dish is the same, but the instructions are now in a form your tools can actually measure.