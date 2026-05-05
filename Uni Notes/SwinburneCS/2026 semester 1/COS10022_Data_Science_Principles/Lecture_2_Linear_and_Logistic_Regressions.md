## Part 1: Linear Regression

---

### 1. Concept Definition & Context
**Linear Regression** is one of the oldest supervised/predictive models (200+ years old) that finds and quantifies the relationship between one or more numerical input variables and a single numerical output variable, by fitting a straight line through the data.

**In simple terms:** given some data points, draw the single best straight line through them, then use that line to predict new values.

**Think of it like:** a rubber band stretched between two nails on a board covered in scattered pins. Linear regression finds the position where the rubber band sits closest to all the pins at once - the "best fit" across all points, not perfectly touching any one.

**Why it matters:** it is the foundational building block of predictive modelling. Many advanced ML techniques (including neural networks and logistic regression) build directly on its ideas. In finance, it underpins stock return models, risk factor analysis, and pricing models.

### 2. Key Principles & Core Theory
**Parametric learning approach.** Linear Regression is a parametric model: the structure of the model is fixed in advance (a linear equation), and the goal of training is to find the best numerical values for the parameters (weights) from the data. This is in contrast to non-parametric models that let structure emerge from data.

**The model equation:**
$$y = w_0 + w_1 \times x_1 + w_2 \times x_2 + ...$$
Where:
- $y$ is the predicted output (dependent variable)
- $w_0$ is the bias / intercept - the value of y when all inputs are zero
- $w_1, w_2, ...$ are the weights / coefficients learned from training data
	- In a more simple example like $y = a + bx$. We can use the following formula to find the equation:
	- ![[Pasted image 20260416013604.png]]
- $x_1, x_2, ...$ are the input variable values

**Why we need both $w_0$ and $w_1$:** 
- $w_1$ (the slope) controls how steeply the line rises or falls.
- $w_0$ (the intercept) shifts the entire line up or down. Without $w_0$, every line would be forced through the origin, making the model far less flexible.

**Gradient formula:**
$$w_1 = r \times \frac{s_y}{s_x}$$

Pearson's r is defined as:
$r = \frac{\sum(x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum(x_i - \bar{x})^2} \times \sqrt{\sum(y_i - \bar{y})^2}}$

Standard deviations:
$s_x = \sqrt{\frac{\sum(x_i - \bar{x})^2}{n-1}}$ and $s_y = \sqrt{\frac{\sum(y_i - \bar{y})^2}{n-1}}$

So the ratio $\frac{s_y}{s_x}$ becomes (n-1 cancels):
$\frac{s_y}{s_x} = \frac{\sqrt{\sum(y_i - \bar{y})^2}}{\sqrt{\sum(x_i - \bar{x})^2}}$

So:
$w_1 = \frac{\sum(x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum(x_i - \bar{x})^2} \times \sqrt{\sum(y_i - \bar{y})^2}} \times \frac{\sqrt{\sum(y_i - \bar{y})^2}}{\sqrt{\sum(x_i - \bar{x})^2}}$

The $\sqrt{\sum(y_i - \bar{y})^2}$ cancels top and bottom:
$w_1 = \frac{\sum(x_i - \bar{x})(y_i - \bar{y})}{\sum(x_i - \bar{x})^2}$

$b = \frac{\sum(x - \bar{x})(y - \bar{y})}{\sum(x - \bar{x})^2}$

Same formula as the simplified version, different notation.

**Supervised learning.** Because the model learns from labeled training data (known input-output pairs), it is a supervised model. The goal after training is to generalise - to predict correctly on unseen test data.

**The optimization objective.** The "best" line is defined as the one that minimizes prediction error across all training points, measured by the **Sum of Squared Errors (SSE)**:
$$SSE = \sum (Y - Y')^2$$
Where Y is the actual value and Y' is the predicted value. Squaring errors penalizes large deviations more heavily.

### 3. Examples & Applications
**Example 1: Simple Linear Regression (single input)**
Given data: $X = [1, 2, 3, 4, 5]$, $Y = [1.00, 2.00, 1.30, 3.75, 2.25]$

Step 1 - Compute 5 statistics:
- Mean of $X$: $mu_x = \frac{1+2+3+4+5}{5} = 3.00$
- Mean of $Y$: $mu_y = \frac{1+2+1.3+3.75+2.25}{5} = 2.06$
- Standard deviation of $X$: $s_x = 1.581$
- Standard deviation of $Y$: $s_y = 1.072$
- Pearson's correlation coefficient: $r_{xy} = 0.627$

Step 2 - Compute the slope ($w_1$):
$w_1 = r_{xy} \times \frac{s_y}{s_x} = 0.627 \times \frac{1.072}{1.581} = 0.425$

Step 3 - Compute the intercept ($w_0$):
$w_0 = mu_y - w_1 \times mu_x = 2.06 - 0.425 \times 3 = 0.785$

Final model:
$y = 0.785 + 0.425 \times x$

Predictions: for $x=1, y=1.21$; for $x=2, y=1.64$

**Example 2: Height predicting Weight**
A scatter plot of height (cm) vs weight (kg) shows a roughly linear trend. A linear regression model fits a straight line through it. If the model learns $y = -100 + 0.9x$, then for a height of 180cm it predicts weight = 62kg. This works because taller people tend (on average) to weigh more - a genuine linear relationship in the data.

### 4. Core Statistical Building Blocks
**Mean (mu)**
The arithmetic average. For variable x with N values:
$$mu_x = \frac{\sum x_i}{N}$$

**Standard Deviation (s)**
Measures how spread out the values are from the mean. A small s means values cluster tightly; a large s means they are widely spread.
$$s_x = \sqrt{\frac{\sum(x_i - mu_x)^2}{N-1}}$$
The part inside the square root is the **sample variance**.

**Pearson's Correlation Coefficient (r)**
Measures the strength and direction of the linear relationship between two variables. Ranges from -1 to +1:
- r close to 1: strong positive linear relationship
- r close to -1: strong negative linear relationship
- r close to 0: little to no linear relationship
$$r_{xy} = \frac{\sum(x_i - mu_x)(y_i - mu_y)}{\sqrt{\sum{(x_i-mu_x)^2} \times \sum{(y_i-mu_y)^2}}}$$
Think of it like: a correlation of 0.9 means if you plotted the data, the points would form a tight upward-sloping band. A correlation of 0.1 would be a scattered cloud.

### 5. Preparing Data for Linear Regression
Before applying Linear Regression, data must be checked and cleaned across five dimensions:
- **Linear assumption.** The model only works when input-output relationships are genuinely linear. If the data is curved (e.g., exponential growth), apply a log transform to linearize it before fitting.
- **Remove noise and outliers.** Outliers are data points that differ significantly from others. A single extreme outlier can drag the regression line far from the true trend. Anscombe's Quartet illustrates this: four datasets with nearly identical means, variances, and even the same regression line - but completely different shapes when plotted, including one driven entirely by a single outlier.
- **Remove collinearity.** Collinearity occurs when two or more input variables are strongly correlated with each other (e.g., "credit limit" and "credit rating" moving almost in lockstep). This makes it impossible to separate their individual contributions to y. Fix it by computing pairwise correlations and removing the redundant variable - follow Occam's Razor: simpler models are better.
- **Gaussian (normal) distributions.** Linear Regression performs best when inputs and outputs follow a bell-curve distribution. If data is skewed, apply transformation techniques to make it more Gaussian.
- **Rescale inputs.** Variables measured on very different scales (e.g., age in years vs. salary in dollars) can cause the model to over-weight large-scale variables. Use either:
	- **Normalization** - rescales values to $[0, 1]$ range:
		- $$x' = (x - x_{mean}) / (x_{max} - x_{min})$$
	- **Standardization (z-score normalization)** - rescales so mean=0, std=1:
		- $$x' = \frac{(x - x_{mean})}{\sigma}$$

### 6. Ordinary Least Squares (OLS) for Multiple Inputs
When there are multiple input variables, **Ordinary Least Squares (OLS)** regression generalises the single-variable approach. The principle is the same - minimise SSE - but now across all input dimensions simultaneously. In practice, statistical software (Python's `scikit-learn`, R, SPSS etc.) handles this optimisation automatically. You rarely compute it by hand for multi-variable problems.

## Part 2: Logistic Regression

### 1. Concept Definition & Context
**Logistic Regression** is a classification model that predicts the probability that an input belongs to a particular category. It extends linear regression by wrapping the linear output through a **sigmoid function**, which squashes any real number into a value between 0 and 1 - interpretable as a probability.

**In simple terms:** instead of predicting "how much", it predicts "how likely" - outputting a probability score that can then be thresholded into a class label (e.g., yes/no, spam/not spam).

**Think of it like:** a dimmer switch. Linear regression is a dial that can spin infinitely in either direction. Logistic regression clamps that dial to the range $[0,1]$, so the output always reads as a probability.

**Why it matters:** most real-world prediction tasks have categorical outputs (fraud/not fraud, default/no default, disease/healthy). Logistic regression is the workhorse for these binary and multi-class problems across medicine, finance, and marketing.

### 2. The Sigmoid Function
The sigmoid (or logistic) function is the mathematical core of logistic regression:
$$S(x) = \frac{1}{(1 + e^{-x})}$$
Key properties:
- Output is always between 0 and 1
- S(0) = 0.5 (the midpoint)
- As x -> +infinity, S(x) -> 1
- As x -> -infinity, S(x) -> 0
- It produces the characteristic S-shaped curve

### 3. The Logistic Regression Model
Logistic Regression takes the linear regression function $z = a_1 \times x_1 + a_2 \times x_2 + ... + b$ and feeds it into the sigmoid:

The result $f(X)$ is the estimated probability that the input X belongs to the positive class. A common decision rule is: if $f(X) >= 0.5$, classify as class 1; otherwise classify as class 0.

**Check lost function $\nabla{l}$***
$$\omega_{tnew} = \omega_{told} - y \nabla{l}$$
After getting $\omega_{tnew}$ put the coeff inside x to find the new more accurate model

### 4. Types & Variations
**Simple Linear Regression**
Single input variable. Best for exploring the relationship between one predictor and a numerical outcome. Example: predicting weight from height.

**Best for:** exploratory analysis, building intuition, simple relationships.

**Multiple Linear Regression**
Multiple input variables. Uses OLS to fit a hyperplane through multidimensional data. Example: predicting house price from size, location, and age.

**Best for:** real-world modelling where outcomes depend on many factors.

**Logistic Regression (Binary)**
Predicts one of two classes. Output is a probability 0-1 thresholded at 0.5. Example: loan default (yes/no), email spam (yes/no).

**Best for:** binary classification, when you need probability estimates alongside predictions.

**Logistic Regression (Multi-class)**
Dependent variable has more than two nominal or ordinal categories. Example: predicting election winner across three parties, or disease severity across levels.

**Best for:** problems with ordered or unordered categorical outputs beyond two classes.

### 5. Comparison

| Feature          | Linear Regression          | Logistic Regression                    |
| ---------------- | -------------------------- | -------------------------------------- |
| Output type      | Numerical (continuous)     | Probability (0 to 1), then class label |
| Use case         | Predict a quantity         | Predict a category                     |
| Output range     | Unbounded (-inf to +inf)   | Bounded [0, 1]                         |
| Core function    | Linear equation            | Sigmoid of linear equation             |
| Error metric     | SSE / RMSE                 | Log-loss / accuracy                    |
| Interpretability | High (slope = unit change) | High (log-odds interpretation)         |
| Non-linear data  | Requires transformation    | Handles via sigmoid wrapping           |


### 6. Applications
- **Finance - credit risk modelling.** Logistic regression predicts whether a borrower will default (yes/no) based on income, credit history, and debt-to-income ratio. Linear regression predicts the exact loss amount given a default.
- **Healthcare - disease diagnosis.** Logistic regression classifies a patient as having or not having a condition based on biomarkers. Linear regression might predict a patient's blood pressure value from lifestyle inputs.
- **Marketing - customer subscription.** Logistic regression predicts whether a customer will subscribe to a product, enabling targeted campaigns. Linear regression could predict the expected lifetime value of a customer in dollars.
- **Data Analytics Lifecycle context.** Both models sit in Phase 4 (Model Building) of the DAL. Phase 3 (Model Planning) identifies that the problem is numerical prediction (linear) or classification (logistic). The team iterates between phases 3 and 4 before finalising the model.

### 7. Key Takeaways & Summary
1. **Linear Regression predicts numbers; Logistic Regression predicts categories.** The distinction comes down to whether your output variable is continuous (salary, weight) or categorical (yes/no, class A/B/C).
2. **Both are parametric and supervised.** They learn weights from labeled training data and store knowledge in those weights - not in the data itself.
3. **The regression line is the best-fit, not the perfect-fit.** A line that passes exactly through all training points is overfitting. The regression line minimises total error, accepting imperfect fit on individual points.
4. **Data quality is as important as model choice.** Outliers, collinearity, and scale differences can break a linear model completely. Cleaning data before fitting is not optional - it is the bulk of the work.
5. **Logistic Regression is linear regression with a sigmoid wrapper.** Understanding linear regression fully means you already understand the mechanics of logistic regression; the sigmoid is just a final transformation step.

**Remember this:** Linear Regression draws the best straight line through data to predict a number; Logistic Regression bends that line into an S-curve to predict a probability.

**One analogy to remember it by:** Linear Regression is like a ruler laid across a scatter of dots - it finds the straightest path through the noise. Logistic Regression is like a thermostat: it takes continuous temperature readings (the linear score) and converts them to a clean binary signal - on or off, yes or no.

---

## Appendix: Phase 4 - Model Building Context

This lecture sits in **Phase 4 (Model Building)** of the Data Analytics Lifecycle (DAL):
- The team develops training, test, and validation datasets.
- Models are fit on training data, then evaluated on test data.
- Phases 3 (Model Planning) and 4 can overlap - teams iterate back and forth before finalizing.
- "Developing" a model usually means selecting and tuning existing implementations, not coding from scratch.
- **Documentation is critical** at this phase: record all assumptions, parameter choices, and transformation decisions. Small decisions made mid-build are easily forgotten post-project.

**Key tools:**
- Commercial: SAS Enterprise Miner, IBM SPSS Modeler, MATLAB
- Open-source: Python (scikit-learn, pandas, NumPy), R, WEKA, KNIME