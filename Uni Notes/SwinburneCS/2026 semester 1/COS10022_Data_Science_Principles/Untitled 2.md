# COS10022 Week 8 - Data Preparation

---

## Part 1: Data Quality and the Preparation Pipeline

_Reference: Dietrich, D. ed. (2015) Data Science & Big Data Analytics, EMC Education Services_

---

### What is Data Preparation?

**Data preparation** is the process of transforming raw data into a clean, consistent, and usable form before analysis or modelling begins.

In simple terms: it is the work of making messy real-world data good enough to learn from.

Think of it like: mise en place in cooking — before you can cook anything useful, you have to wash, cut, and organise every ingredient. Skipping this step ruins the meal regardless of how good your recipe is.

Why it matters: garbage in, garbage out. Even the most sophisticated model will produce misleading results if the input data is incomplete, noisy, or inconsistent. Data preparation is estimated to consume 60–80% of a data scientist's time on any real project.

Data preparation is also known as **data wrangling** or **data munging**. In the Data Analytics Lifecycle it sits at Phase 2, after discovery, and directly precedes model planning. The team uses an analytics sandbox environment and performs **ETLT** (Extract, Transform, Load, Transform) to bring data in and familiarise themselves with its structure before building anything.

### The Six Dimensions of Data Quality

Data has **quality** when it satisfies the requirements of its intended use. The six dimensions used to assess this are accuracy, completeness, consistency, timeliness, believability, and interpretability.

**Accuracy** is the degree to which data represents reality. An incorrect salary value like `-100` is inaccurate. **Completeness** is the degree to which necessary data is available for use — a missing occupation field is an incompleteness problem. **Consistency** is the degree to which data is equal within and between datasets; a record where `Age = 36` but `Birthday = 31/08/1984` (implying age 41) is inconsistent. **Timeliness** means data is available when it is needed. **Believability** is whether users actually trust the data. **Interpretability** is the ease with which the data can be understood.

### The Four Major Tasks in Data Preparation

The four tasks form a logical pipeline, often applied iteratively.

**Data Cleaning** fills in missing values, smooths noisy data, identifies or removes outliers, and resolves inconsistencies. **Data Integration** merges data from multiple sources to reduce redundancies and inconsistencies in the resulting dataset. **Data Reduction** obtains a smaller-volume representation that still produces the same or near-same analytical results. **Data Transformation** converts the data into appropriate formats and scales for mining, making output easier to interpret.

### Key Takeaways

1. Data preparation is Phase 2 of the Data Analytics Lifecycle and is the most time-consuming phase in practice.
2. Quality is always measured relative to intended use — data that is good enough for one task may be inadequate for another.
3. The four tasks (cleaning, integration, reduction, transformation) are distinct but often applied together in a feedback loop.

**Remember this:** You cannot build a reliable model on unreliable data; preparation is the foundation, not a formality.

**One analogy to remember it by:** Raw data is like unprocessed ore — valuable minerals are in there, but you have to smelt and refine it before you can build anything with it.

---

## Part 2: Data Cleaning

_Reference: Dietrich, D. ed. (2015) Data Science & Big Data Analytics, EMC Education Services_

---

### What is Data Cleaning?

**Data cleaning** is the process of detecting and correcting (or removing) corrupt, inaccurate, or otherwise problematic records in a dataset.

In simple terms: it is fixing what is wrong with your data before you use it.

Think of it like: proofreading a document — you go through line by line looking for errors, missing words, and contradictions before publishing.

Why it matters: real-world data is almost always dirty. Feeding dirty data into a model produces results that look valid but are actually misleading.

### The Three Types of Dirty Data

**Incomplete data** has missing attribute values or lacks attributes of interest entirely. A tuple where `Occupation = ""` is incomplete. This can happen because attributes were not considered important at time of entry, due to equipment malfunctions, because inconsistent records were deleted, or because data history was not tracked. Missing data often needs to be inferred rather than simply discarded.

**Noisy data** contains random errors or variance in measured values. A record with `Salary = -100` or an income of `10000K` (plausible typo for a regular income) exemplifies this. Noise arises from faulty collection instruments, data entry problems, data transmission errors, technology limitations, and inconsistent naming conventions. Noise can also appear as **class noise** (contradictory or mislabelled examples) or **attribute noise** (erroneous or missing values).

**Inconsistent data** contains discrepancies in codes or names. A dataset where the rating system changed from `1, 2, 3` to `A, B, C` mid-collection, or duplicate records where the same person is listed with different marital statuses, are inconsistent. So is any record where two logically related fields contradict each other.

### Handling Missing Data

There are six standard approaches, each with trade-offs.

The first is to **ignore the tuple**, typically done when the class label is missing in a classification task. This is not very effective unless the tuple has many missing values, as it discards potentially useful information in other fields.

The second is to **fill in missing values with side information** from an external source. This is the most accurate approach but is time-consuming and impractical at scale.

The third is to **use a global constant** such as `"Unknown"` or `"N/A"`. This is simple but dangerous: a mining algorithm may mistakenly treat all unknown values as sharing a meaningful common property.

The fourth is to **use a measure of central tendency** for the attribute. For normally distributed data, use the mean; for skewed distributions, use the median. If `Download Speed` has missing values, compute the mean of all known values and substitute it.

The fifth is a refinement of the fourth: **use the class-conditional mean or median**, that is, the average computed only from tuples in the same class as the tuple with the missing value. For example, replace missing income values with the average income of customers in the same credit risk category.

The sixth and most sophisticated is to **use the most probable value**, determined via regression, Bayesian inference, or decision tree induction. For example, a decision tree built on other Titanic passenger attributes can predict the missing `sibsp` (siblings/spouses aboard) value for a given passenger.

### Handling Noisy Data

**Binning** smooths data by sorting values and partitioning them into equal-frequency buckets, then replacing each value with either the bin mean (smoothing by bin means) or the nearest boundary value (smoothing by bin boundaries). Given sorted prices `4, 8, 9, 15 | 21, 21, 24, 25 | 26, 28, 29, 34`, smoothing by bin means replaces each bin with `9, 9, 9, 9` and `23, 23, 23, 23` etc.

**Regression** conforms data values to a function. Linear regression fits a straight line `y = x + 1` to two attributes so one can predict the other, which has the effect of smoothing outlier values toward the fitted line.

**Sliding window (moving average)** computes the average of each point's neighbourhood and replaces the point with that average. Given `2, 5, 1, 7, 4, 3, 6, 6, 8` with a window of size 3, the processed output is `3, 4, 4, 5, 4, 5, 7`. This is also called convolution.

**Outlier analysis** uses clustering to detect values that fall outside the main cluster groups. Each cluster centroid represents the average position of a cluster; points that lie far from all centroids are flagged as outliers.

### Detecting Discrepancies

The first step in data cleaning as a process is **discrepancy detection**. Discrepancies are caused by poorly designed entry forms, human errors, deliberate errors (e.g. respondents withholding information), data decay (e.g. outdated addresses), instrumentation faults, system errors, and inconsistencies from data integration.

Detection methods include: using **metadata** (check expected ranges, data types, and valid domains for each attribute); applying **uniqueness, consecutive, and null rules** (the unique rule requires every value of a primary key to differ from all others; the consecutive rule requires no gaps between the lowest and highest values; the null rule specifies how null conditions are represented); using **commercial tools** such as Power BI that perform data scrubbing (using domain knowledge for corrections) and data auditing (using correlation and clustering to find rule violators); and using **ETL tools** that allow transformation specifications through a graphical interface.

### Key Takeaways

1. Real-world data is dirty by default; incomplete, noisy, and inconsistent data must each be addressed differently.
2. The choice of imputation method for missing data depends on the distribution of the data and the downstream task.
3. Discrepancy detection is the prerequisite for any cleaning action — you must find the problem before you can fix it.

**Remember this:** Data cleaning is not just removing bad rows; it is a structured process of detection, decision, and repair applied to each type of data quality problem.

**One analogy to remember it by:** Cleaning data is like restoring a damaged painting — you identify what is missing, what is distorted, and what does not belong, then apply the least-destructive correction that preserves the original intent.

---

## Part 3: Data Integration

_Reference: Dietrich, D. ed. (2015) Data Science & Big Data Analytics, EMC Education Services_

---

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

For **numerical data**, the **Pearson Correlation Coefficient** $r_{A,B}$ is used:

$$r_{A,B} = \frac{\sum_{i=1}^{n}(a_i - \bar{A})(b_i - \bar{B})}{(n-1)\sigma_A \sigma_B}$$

Where $n$ is the number of tuples, $\bar{A}$ and $\bar{B}$ are the means, and $\sigma_A$, $\sigma_B$ are the standard deviations. The result lies in $[-1, 1]$: a positive value indicates attributes move together, zero indicates independence, and a negative value indicates they move in opposite directions.

**Covariance** is a related measure:

$$Cov(A,B) = E((A - \bar{A})(B - \bar{B})) = \frac{\sum_{i=1}^{n}(a_i - \bar{A})(b_i - \bar{B})}{n}$$

which simplifies to $Cov(A,B) = E(A \cdot B) - \bar{A}\bar{B}$. A positive covariance means attributes tend to rise together; negative means opposite; zero suggests independence, though note that zero covariance does not guarantee independence since non-linear relationships can still exist.

**Worked example:** For stock prices of AllElectronics `(6, 5, 4, 3, 2)` and HighTech `(20, 10, 14, 5, 5)` across five time points:

$$E(\text{AllElectronics}) = \frac{20}{5} = $4, \quad E(\text{HighTech}) = \frac{54}{5} = $10.8$$

$$Cov = \frac{6 \times 20 + 5 \times 10 + 4 \times 14 + 3 \times 5 + 2 \times 5}{5} - 4 \times 10.8 = 50.2 - 43.2 = 7$$

Since $Cov > 0$, both stocks tend to rise together.

For **categorical data**, the **Chi-Squared test** ($\chi^2$) is used:

$$\chi^2 = \sum_{i=1}^{c}\sum_{j=1}^{r} \frac{(o_{ij} - e_{ij})^2}{e_{ij}}, \quad e_{ij} = \frac{count(A=a_i) \times count(B=b_j)}{n}$$

A larger $\chi^2$ value means the variables are more likely to be correlated. To interpret it, compare the computed value against the critical value from a $\chi^2$ table at the chosen significance level with $(r-1)(c-1)$ degrees of freedom. If the computed value exceeds the critical value, the null hypothesis of independence is rejected.

**Worked example:** In a 1500-person survey of gender and reading preference:

$$e_{11} = \frac{300 \times 450}{1500} = 90, \quad \chi^2 = \frac{(250-90)^2}{90} + \frac{(50-210)^2}{210} + \frac{(200-360)^2}{360} + \frac{(1000-840)^2}{840} = 507.93$$

With 1 degree of freedom, the critical value at the 0.001 significance level is 10.83. Since $507.93 > 10.83$, gender and preferred reading are strongly correlated.

### Key Takeaways

1. Entity identification is a prerequisite for integration: you must confirm that two records refer to the same real-world object before merging them.
2. Redundancy detection via correlation analysis prevents bloated, misleading datasets from entering the pipeline.
3. The right correlation measure depends on data type: Pearson/covariance for numerical, chi-squared for categorical.

**Remember this:** Integration does not just combine data — it introduces new errors; treat it as another source of data quality problems that must be actively managed.

**One analogy to remember it by:** Merging datasets is like translating two books written in different dialects into one — you must reconcile different names for the same concepts, resolve contradictions, and eliminate chapters that repeat each other.

---

## Part 4: Data Reduction

_Reference: Dietrich, D. ed. (2015) Data Science & Big Data Analytics, EMC Education Services_

---

### What is Data Reduction?

**Data reduction** produces a smaller-volume representation of a dataset that preserves the same (or nearly the same) analytical results as the original.

In simple terms: it makes the dataset smaller so analysis runs faster, without throwing away information that matters.

Think of it like: summarising a 500-page book into a 20-page study guide — the key ideas are preserved but the volume is dramatically reduced.

Why it matters: data warehouses can hold terabytes. Complex mining algorithms run for prohibitively long times on full datasets. Reduction makes analysis computationally tractable.

### The Five Data Reduction Strategies

**Data cube aggregation** applies aggregation operations when constructing a data cube. For example, quarterly sales data for 2002–2004 can be aggregated to annual totals without losing the analytical conclusion about year-over-year trends. Data cubes store multidimensional aggregate values and provide fast access for online analytical processing.

**Attribute subset selection** removes irrelevant, weakly relevant, or redundant attributes. Heuristic search methods are used since exhaustive search over all subsets is infeasible. The four common methods are: stepwise forward selection (start empty, add the best attribute at each step), stepwise backward elimination (start full, remove the worst at each step), combined forward-backward selection (add the best and remove the worst simultaneously), and decision tree induction (build a tree; attributes not appearing in the tree are treated as irrelevant, so the tree's attribute set becomes the reduced subset).

**Dimensionality reduction** encodes data into a compressed form. If the original data can be fully reconstructed, the compression is **lossless**; if only an approximation can be reconstructed, it is **lossy**. The canonical technique is **Principal Component Analysis (PCA)**, which projects data onto a lower-dimensional space (the principal components) while retaining as much variance as possible. For example, gene expression data measured across three genes can be projected to two dimensions (PC1, PC2) while preserving the separability of experimental conditions.

**Numerosity reduction** replaces the original data with a smaller alternative representation. **Parametric methods** assume the data fits a model and store only the model parameters. Linear regression fits $Y = b_0 + b_1 X_1$, multiple linear regression fits $Y = b_0 + b_1 X_1 + b_2 X_2$, and the log-linear model fits $\ln Y = b_0 + b_1 X_1 + \varepsilon$. **Non-parametric methods** do not assume a model: binning, histograms, clustering, and sampling all store reduced representations without fitting a parametric form.

**Discretisation and concept hierarchy generation** replaces raw values with range labels or higher-level concepts, covered in detail in Part 5.

### Sampling Methods

Sampling allows a large dataset $D$ of $N$ tuples to be represented by a much smaller random subset of size $s$. The four common methods are:

**SRSWOR** (Simple Random Sample Without Replacement): all tuples are equally likely; once selected, a tuple is not returned to the pool. **SRSWR** (Simple Random Sample With Replacement): after a tuple is drawn it is returned to $D$, so it may be selected again. **Cluster sampling**: $D$ is partitioned into $M$ disjoint clusters; $s$ clusters are randomly selected and all tuples within them are included. **Stratified sampling**: $D$ is divided into disjoint strata (e.g. by age group); an SRS is drawn from each stratum, which is especially useful when the overall data distribution is skewed.

### Key Takeaways

1. The five strategies address different dimensions of data volume: aggregation reduces temporal resolution, attribute selection removes dimensions, dimensionality reduction compresses features, numerosity reduction models the distribution, and discretisation abstracts values.
2. PCA is the dominant dimensionality reduction method and is lossless in the number of components kept, but lossy if you drop low-variance components.
3. Stratified sampling is preferred over simple random sampling when classes are imbalanced, because it ensures minority classes are represented.

**Remember this:** Reduction is not about losing data — it is about retaining the analytical signal while discarding redundant volume.

**One analogy to remember it by:** Data reduction is like compressing an image file — the smaller JPEG still looks like the photo, but unnecessary pixel detail has been discarded to make the file manageable.

---

## Part 5: Data Transformation

_Reference: Dietrich, D. ed. (2015) Data Science & Big Data Analytics, EMC Education Services_

---

### What is Data Transformation?

**Data transformation** converts source data into formats and scales that are more suitable for mining and make outputs easier to interpret.

In simple terms: it reshapes and rescales the data so algorithms can work with it effectively.

Think of it like: converting ingredients into standard units before cooking — grams, millilitres, and degrees Celsius instead of a pinch, a splash, and a moderate heat.

Why it matters: many algorithms are sensitive to scale differences (e.g. distance-based clustering), data type assumptions, or require values in a specific range to converge efficiently (e.g. neural network backpropagation).

### The Six Transformation Strategies

**Smoothing** removes noise using binning, regression, or clustering as described in Part 2.

**Attribute/feature construction** creates new attributes from existing ones. For example, deriving a `profit_margin` attribute from `revenue` and `cost`.

**Aggregation** constructs data cubes, rolling up data to higher levels of granularity (also discussed under data reduction).

**Normalisation** scales attribute values to a specified range so that attributes with large natural ranges do not dominate those with small ones. Three methods are in common use.

**Discretisation** replaces continuous numeric values with interval labels or concept labels (e.g. replacing raw age with `youth`, `adult`, `senior`). Techniques include binning, histogram analysis, cluster analysis, decision tree analysis, and correlation analysis.

**Concept hierarchy generation for nominal data** generalises lower-level attribute values to higher-level concepts (e.g. city to province to country).

### Normalisation in Detail

Three normalisation methods are standard.

**Min-Max normalisation** maps each value linearly into a target range $[new_min_A, new_max_A]$:

$$v' = \frac{v - min_A}{max_A - min_A}(new_max_A - new_min_A) + new_min_A$$

Example: Income ranges $$12{,}000$–$$98{,}000$ normalised to $[0.0, 1.0]$. Then $$73{,}600$ maps to:

$$v' = \frac{73600 - 12000}{98000 - 12000}(1.0 - 0) + 0 = 0.716$$

**Z-score normalisation** is useful when the actual min and max are unknown:

$$v' = \frac{v - \mu_A}{\sigma_A}$$

Example: With $\mu = $54{,}000$ and $\sigma = $16{,}000$, then $$73{,}600$ transforms to $\frac{73600 - 54000}{16000} = 1.225$.

**Decimal scaling** normalises by dividing each value by a power of 10, choosing the smallest $j$ such that $\max(|v'|) < 1$:

$$v' = \frac{v}{10^j}$$

Example: Values range from $-986$ to $917$; dividing by $1000$ ($j = 3$) gives $-0.986$ to $0.917$.

|Method|Formula|Best Used When|Trade-offs|
|---|---|---|---|
|Min-Max|$\frac{v - min}{max - min}$|Range is known and fixed|Sensitive to outliers shifting the bounds|
|Z-score|$\frac{v - \mu}{\sigma}$|True min/max unknown; near-normal distribution|Does not bound output to a fixed range|
|Decimal scaling|$\frac{v}{10^j}$|Quick approximation needed|Rough; does not centre data|

### Discretisation in Detail

**Binning** groups continuous values into intervals. Equi-width binning uses fixed interval sizes; equi-depth binning uses a fixed number of values per bin. The number of bins determines discretisation resolution — fewer bins means coarser categories.

**Decision tree analysis** for discretisation uses a top-down, supervised splitting approach. Entropy is used to find the split point that produces partitions with the most class-homogeneous intervals:

$$Ent(U) = -\sum_{i=1}^{k} p_i \cdot \log_2 p_i$$

where $p_i$ is the proportion of values in the $i$-th interval. The algorithm recursively finds the split that minimises the weighted total entropy $E_T$.

**Correlation analysis (ChiMerge)** uses a bottom-up supervised merging approach. Neighbouring intervals with similar class distributions (low $\chi^2$ values) are merged iteratively until a stopping criterion is reached.

**Clustering** applies k-means or similar algorithms to partition continuous values into $k$ discrete sectors, which become the discretised categories.

### Concept Hierarchy Generation

For **nominal attributes** (finite sets of unordered values such as city names), concept hierarchies allow generalisation to higher-level concepts. Four methods exist: explicit partial ordering specified by domain experts at the schema level (e.g. `street < city < state < country`); explicit data grouping (e.g. `{Urbana, Champaign, Chicago} < Illinois`); automatic generation by counting distinct values (attributes with more distinct values are placed lower in the hierarchy); and partial specification where only some levels are defined.

### Key Takeaways

1. Normalisation is essential for distance-based and gradient-based algorithms; without it, high-magnitude attributes dominate.
2. Discretisation trades fine-grained precision for interpretability and reduced complexity; the right number of bins is task-dependent.
3. Concept hierarchies enable multi-granularity analysis and are essential for OLAP-style queries over categorical dimensions.

**Remember this:** Transformation is not about changing what the data means — it is about changing how it is expressed so algorithms and humans can work with it more effectively.

**One analogy to remember it by:** Transforming data is like converting a recipe from imperial to metric — the dish is the same, but the instructions are now in a form your tools can actually measure.