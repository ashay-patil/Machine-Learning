I have rewritten your notes with a single consistent dataset and added more depth around **why each decision happens**. I will use a **Loan Approval dataset** because it naturally covers classification, numerical features, categorical features, one-hot encoding, ordinal encoding, and repeated feature usage.

---

# Decision Tree Hyperparameters + Binary Splitting (Complete Notes)

A Decision Tree learns rules by repeatedly asking questions:

```
Feature <= Threshold ?
        |
   ----------------
   |              |
 Yes              No
Left Node      Right Node
```

Every split is **binary** in sklearn.

The hyperparameters control:

> "How should the tree grow?"

The important parameters:

```python
criterion
splitter
max_depth
max_features
```

---

# Example Dataset

Consider a bank deciding whether to approve a loan.

| Age | Income | Credit_Score | Education     | Job_Type | Loan_Approved |
| --- | ------ | ------------ | ------------- | -------- | ------------- |
| 25  | 30k    | 600          | Graduate      | Salaried | No            |
| 35  | 80k    | 750          | Graduate      | Salaried | Yes           |
| 45  | 90k    | 800          | Postgraduate  | Business | Yes           |
| 23  | 25k    | 550          | Undergraduate | Salaried | No            |
| 50  | 100k   | 820          | Postgraduate  | Business | Yes           |
| 30  | 40k    | 650          | Graduate      | Business | No            |

Target:

```
Loan_Approved

Yes
No
```

This is a **classification problem**.

---

# PART 1: Decision Tree Hyperparameters

---

# 1. criterion

## Meaning:

`criterion` decides:

> "How should the tree measure the quality of a split?"

Suppose the tree considers:

```
Credit_Score <= 700
```

Before split:

```
Loan Approved:

Yes: 3
No : 3
```

Mixed node.

After split:

Left:

```
Credit <= 700

Yes: 0
No : 3
```

Right:

```
Yes: 3
No : 0
```

This split is perfect because both children are pure.

But how does the tree know this?

Using `criterion`.

---

# Classification Criteria

## 1. Gini Impurity

Formula:

[
Gini = 1-\sum p_i^2
]

It measures:

> Probability of incorrect classification if we randomly assign a label.

Example:

Node:

```
Yes Yes No No
```

Probability:

```
Yes = 2/4 = 0.5
No  = 2/4 = 0.5
```

Gini:

```
1 - (0.5² + 0.5²)

= 1 - (0.25+0.25)

= 0.5
```

High impurity.

---

Pure node:

```
Yes Yes Yes Yes
```

Probability:

```
Yes = 1
No = 0
```

Gini:

```
1-(1²+0²)

=0
```

Goal:

```
Minimize Gini
```

Default in sklearn:

```python
criterion="gini"
```

---

## 2. Entropy

Measures disorder.

Formula:

[
Entropy=-\sum p_i log_2(p_i)
]

Example:

```
Yes Yes No No
```

Entropy:

```
=1
```

Maximum disorder.

Pure node:

```
Yes Yes Yes Yes
```

Entropy:

```
=0
```

Goal:

```
Reduce entropy
```

---

## 3. log_loss

Used when probability estimates matter.

Example:

Instead of only:

```
Prediction = Yes
```

Tree considers:

```
Probability:

Yes = 0.95
No = 0.05
```

Similar behaviour to entropy.

---

# Regression Criteria

Now target is continuous.

Example:

Predict salary:

| Age | Salary |
| --- | ------ |
| 25  | 30     |
| 35  | 80     |
| 45  | 90     |

The tree tries to create groups with similar salary values.

---

## 1. squared_error

Uses Mean Squared Error:

[
MSE=\frac{1}{n}\sum(y-\bar y)^2
]

Example:

Node:

```
Salary:

30
80
90
```

Mean:

```
66.6
```

Errors:

```
(-36.6)^2
(13.4)^2
(23.4)^2
```

Large errors are heavily punished.

Sensitive to outliers.

---

## 2. absolute_error

Uses:

[
MAE=\frac{1}{n}\sum |y-\bar y|
]

Example:

Salary:

```
30
80
90
```

Errors are absolute.

Less affected by extreme values.

---

## 3. friedman_mse

Modified MSE.

Mostly useful internally in boosting algorithms.

Rarely manually selected.

---

# 2. splitter

Controls:

> "How does the tree search for the split?"

---

## splitter="best"

Checks all possible splits.

Example:

Feature:

```
Age:

20
25
35
40
```

Possible splits:

```
Age <=22.5

Age <=30

Age <=37.5
```

Tree evaluates all.

Suppose:

```
Age <=30
```

gives maximum purity.

It selects that.

Advantages:

* Better accuracy
* Deterministic

---

## splitter="random"

Instead of checking everything:

Randomly tries some splits.

Example:

Only checks:

```
Age <=22.5

Age <=37.5
```

May miss:

```
Age <=30
```

Advantages:

* Faster
* Adds randomness

Mostly useful in:

```
Random Forest
```

---

# 3. max_depth

Controls:

> Maximum number of levels in the tree.

Example:

```
                 Credit Score?
                      |
             ----------------
             |
          Income?
             |
        --------------
        |
     Age?
```

Depth:

```
Root = 0

Credit Score = 0
Income = 1
Age = 2
```

If:

```python
max_depth=2
```

Tree stops at Age.

---

## Effect

Small depth:

```
Underfitting
```

Example:

```
Only Credit Score
```

Cannot learn complex patterns.

Large depth:

```
Overfitting
```

Example:

Tree memorizes:

```
Age=25 AND Income=30000 AND Credit=600 → No
```

---

# 4. max_features

Controls:

> How many features can compete for a split?

Dataset features:

```
Age
Income
Credit Score
Education
Job Type
```

Total:

```
5 features
```

---

## max_features=None

Every split checks all features.

```
Age
Income
Credit
Education
Job
```

---

## max_features="sqrt"

Uses:

[
\sqrt{number\ of\ features}
]

Here:

```
sqrt(5)=2
```

Randomly selects 2 features.

Example:

```
Income
Credit Score
```

Only these compete.

---

## Why?

Randomness creates different trees.

This is the main idea behind Random Forest.

---

# PART 2: Binary Splitting Behaviour

---

# Case 1: Numerical Feature Classification

Feature:

```
Credit Score
```

Dataset:

| Credit | Approved |
| ------ | -------- |
| 550    | No       |
| 600    | No       |
| 750    | Yes      |
| 800    | Yes      |

Possible splits:

```
Credit <=575

Credit <=675

Credit <=775
```

Best:

```
Credit <=675
```

Tree:

```
          Credit <=675
          /          \
        Yes          No
```

Actually:

Left:

```
550,600

No,No
```

Right:

```
750,800

Yes,Yes
```

Perfect.

---

# Case 2: Numerical Regression

Predict salary:

| Experience | Salary |
| ---------- | ------ |
| 1          | 30     |
| 5          | 70     |
| 10         | 120    |
| 15         | 150    |

Split:

```
Experience <=7
```

Creates:

Left:

```
30,70
Mean=50
```

Right:

```
120,150
Mean=135
```

Leaf prediction:

```
Experience <=7 → 50

Experience >7 → 135
```

---

# Case 3: One Hot Encoding

Original feature:

```
Education
```

Values:

```
Graduate
Postgraduate
Undergraduate
```

After OHE:

| Graduate | Postgraduate | Undergraduate |
| -------- | ------------ | ------------- |
| 1        | 0            | 0             |
| 0        | 1            | 0             |
| 0        | 0            | 1             |

Tree can split:

```
Graduate <=0.5
```

Meaning:

```
Is Graduate?
```

Then:

```
Postgraduate <=0.5
```

Same original feature can appear through different columns.

---

# Case 4: Ordinal Encoding

Education:

```
Undergraduate = 0
Graduate = 1
Postgraduate = 2
```

Tree sees:

```
0,1,2
```

Possible split:

```
Education <=1.5
```

Means:

Left:

```
Undergraduate
Graduate
```

Right:

```
Postgraduate
```

Then it can split again:

```
Education <=0.5
```

Left:

```
Undergraduate
```

Right:

```
Graduate
```

---

# Can a feature with >2 categories be used multiple times?

YES.

Example:

Feature:

```
Education

0 = Undergraduate
1 = Graduate
2 = Postgraduate
```

Tree:

```
Education <=1.5

        |
 ----------------
 |              |
UG+G           PG


Education <=0.5

        |
 -------------
 |           |
UG          G
```

Same feature used twice.

This is completely normal.

---

It also happens with numerical features:

Example:

```
Age
```

Tree:

```
Age <=50

Left:
   Age <=30

Right:
   Age <=70
```

A decision tree can reuse the same feature at different nodes.

---

# Final Mental Model

A decision tree is like repeatedly asking questions:

```
Is credit score <= 675?
        |
        |
Is income <= 50000?
        |
        |
Is age <= 30?
```

Every question:

* Uses one feature
* Creates two branches
* Can reuse the same feature later

The hyperparameters decide:

* `criterion` → How to judge a good question
* `splitter` → How to search for questions
* `max_depth` → How many questions to ask
* `max_features` → How many features are allowed to compete for each question

This is the complete intuition behind Decision Tree training.
