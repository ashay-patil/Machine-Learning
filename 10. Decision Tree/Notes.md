1. Meaning of the hyperparameters you gave.
2. Binary splitting behavior in:

   * classification
   * regression
   * one hot encoding
   * ordinal encoding
3. If a feature with >2 unique categories can be used multiple times.

That’s it.

---

## PART 1: HYPERPARAMETERS

Your params:

```python
criterion
splitter
max_depth
max_features
```

These are all training-time controls for Decision Tree.

They tell the tree:
“How should I grow?”

---

1. criterion

---

Meaning:
“How do I decide which split is best?”

Suppose current node has:

```text
[Yes, Yes, No, No]
```

You try two possible splits.

Split A:
Left -> [Yes, Yes]
Right -> [No, No]

Split B:
Left -> [Yes, No]
Right -> [Yes, No]

Clearly A is better because children are purer.

But how do we measure "better"?

That is criterion.

---

## For classification:

a) gini

Gini = 1 - \sum p_i^2

Interpretation:
probability of misclassification.

Example:

[Yes, Yes, No, No]

P(Yes)=0.5
P(No)=0.5

Gini = 1 - (0.25+0.25)=0.5

Pure node:
[Yes, Yes]

Gini = 0

Goal:
minimize gini.

---

b) entropy

Entropy = -\sum p_i \log_2 p_i

Interpretation:
measure of disorder.

More mixed → higher entropy.

Pure → 0.

Goal:
minimize entropy.

---

c) log_loss

Very similar to entropy.

Used when probability estimation matters.

Usually behaves close to entropy.

---

## For regression:

Now labels are numbers:

[10, 12, 14, 100]

Need children with similar values.

a) squared_error

MSE = \frac{1}{n}\sum (y_i-\bar y)^2

Large errors punished heavily.

Default choice.

---

b) absolute_error

MAE = \frac{1}{n}\sum |y_i-\bar y|

Less sensitive to outliers.

---

c) friedman_mse

Modified MSE.
Mostly useful in boosting.

Rarely used directly.

---

2. splitter

---

Options:

* best
* random

Suppose feature = Age

Values:
20, 25, 35, 40

Possible thresholds:
22.5
30
37.5

best:
checks all.

```text
Age<=22.5
Age<=30
Age<=37.5
```

Chooses best.

More accurate.

---

random:
picks random thresholds.

Example:
only tests:

```text
Age<=22.5
Age<=37.5
```

Maybe misses 30.

Faster.
Adds randomness.

Useful in Random Forest.

---

3. max_depth

---

“How many levels can tree grow?”

Example:

```text
          root
         /   \
       n1     n2
      /
    n3
```

Depth:
root = 0
n1 = 1
n3 = 2

If max_depth=2:
stop at n3.

If no limit:
keeps splitting.

Too deep:
overfitting

Too shallow:
underfitting

---

4. max_features

---

At each node:
how many features are allowed to compete?

Suppose:

features:
Age
Salary
City
Marks

total = 4

If None:
all 4 checked

---

If sqrt:
√4 = 2

randomly choose:
maybe [Age, Marks]

Only these can split.

---

If log2:
log₂4 = 2

Again only 2 features checked.

Purpose:
inject randomness.

---

## PART 2: BINARY SPLITTING

Rule in sklearn:

Every node asks only one binary question:

```text
feature <= threshold ?
```

Answers:
Yes → left
No → right

Always 2 branches.

Now let’s see how this behaves in different data types.

==================================================
CASE 1: NUMERICAL FEATURE (classification)
==================================================

Example:

| Age | Buy |
| --- | --- |
| 20  | Yes |
| 25  | Yes |
| 35  | No  |
| 40  | No  |

Possible thresholds:
22.5, 30, 37.5

Best:
Age <= 30

Tree:

```text
Age <= 30
 /      \
Yes      No
```

Done.

One split.

==================================================
CASE 2: NUMERICAL FEATURE (regression)
==================================================

| Age | Salary |
| --- | ------ |
| 20  | 10     |
| 25  | 12     |
| 35  | 80     |
| 40  | 90     |

Best split:
Age <= 30

Left:
[10,12] mean=11

Right:
[80,90] mean=85

Prediction:
left leaf = 11
right leaf = 85

Same binary split.

Only impurity formula changes.

==================================================
CASE 3: ONE HOT ENCODING
==================================================

Original:

Color:
Red
Blue
Green

After One-hot encoding:

| is_red | is_blue | is_green |
| ------ | ------- | -------- |
| 1      | 0       | 0        |
| 0      | 1       | 0        |
| 0      | 0       | 1        |

Now split:

```text
is_red <= 0.5
```

Left:
not red

Right:
red

Then maybe:

```text
is_blue <= 0.5
```

Left:
not blue

Right:
blue

So yes:
same original feature is represented by multiple columns.

Each can be used independently.

This is why OHE works very naturally.

==================================================
CASE 4: ORDINAL / LABEL ENCODING
==================================================

Suppose:

Red=0
Blue=1
Green=2
Yellow=3

Now values:

[0,1,2,3]

Possible thresholds:
0.5
1.5
2.5

Example split:

```text
Color <= 1.5
```

Left:
Red, Blue

Right:
Green, Yellow

Then left child can split again:

```text
Color <= 0.5
```

Left:
Red

Right:
Blue

Then right child:

```text
Color <= 2.5
```

Left:
Green

Right:
Yellow

So yes — same feature reused multiple times.

Important:
Tree gradually isolates categories.

Problem:
it assumes order:

Red < Blue < Green < Yellow

which may be fake.

That is why ordinal encoding can be dangerous.

---

FINAL DOUBT:
Can feature with >2 unique categories be used multiple times?
-------------------------------------------------------------

YES. Absolutely.

Very important.

Suppose:

Color encoded ordinally:
0,1,2,3

Tree can do:

Node 1:

```text
Color <= 1.5
```

Node 2:

```text
Color <= 0.5
```

Node 3:

```text
Color <= 2.5
```

Same feature reused multiple times.

No restriction.

Same happens with numerical features too.

Example:

Age

root:
Age <= 50

left child:
Age <= 30

right child:
Age <= 70

Same feature repeated.

This is normal.

---

## Mental model

Think of tree like repeatedly cutting a cake.

First cut:
left/right

Then cut left piece again.

Then cut right piece again.

Can use same knife (same feature) many times.

That is exactly how decision trees work.
