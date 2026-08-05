### 1. `n_estimators`

```python
"n_estimators": [100, 200, 500, 1000]
```

This is the **number of decision trees** in the random forest.

Example:

```python
RandomForestClassifier(n_estimators=100)
```

means

> Build a forest consisting of **100 decision trees**.

If

```python
n_estimators = 500
```

then the forest has **500 trees**.

Prediction process:

```
Tree 1  ----> Cat
Tree 2  ----> Dog
Tree 3  ----> Cat
Tree 4  ----> Cat
Tree 5  ----> Dog

Majority Vote = Cat
```

More trees generally:

* ✅ Increase accuracy (up to a point)
* ✅ Reduce overfitting
* ❌ Increase training time
* ❌ Increase memory usage

---

### 2. `max_depth`

```python
"max_depth": [5, 8, 15, None, 10]
```

This controls **how deep each decision tree is allowed to grow**.

Example:

```
Depth = 1

           Age > 30?
          /        \
       Yes          No
```

```
Depth = 2

            Age > 30?
          /          \
      Income?      Student?
```

```
Depth = 3

               Age?
             /     \
         Income?   Student?
         /   \
      City?  Gender?
```

If

```python
max_depth = 5
```

the tree cannot grow beyond 5 levels.

If

```python
max_depth = None
```

there is **no depth limit**. The tree keeps splitting until other stopping criteria are met (such as `min_samples_split`).

Effect:

Small depth:

* Faster
* Simpler model
* May underfit

Large depth:

* Learns more patterns
* May overfit

---

### 3. `min_samples_split`

```python
"min_samples_split": [2, 8, 15, 20]
```

This specifies the **minimum number of samples required at a node before it can be split into child nodes**.

Example:

Suppose a node contains

```
50 samples
```

If

```python
min_samples_split = 2
```

the node can split because 50 ≥ 2.

Now suppose another node has

```
10 samples
```

If

```python
min_samples_split = 15
```

then

```
10 < 15
```

so that node **cannot split further** and becomes a leaf node.

Effect:

Small value:

* Deeper trees
* Can capture complex patterns
* Higher risk of overfitting

Large value:

* Shallower trees
* More generalized model
* Lower risk of overfitting

---

### 4. `max_features`

```python
"max_features": [5, 7, "auto", 8]
```

This controls **how many features each tree considers when looking for the best split**.

Suppose your dataset has

```
20 features
```

```
Age
Salary
Height
Weight
Experience
Gender
...
```

If

```python
max_features = 5
```

then at every split, the algorithm randomly picks **5 out of the 20 features** and finds the best split among only those 5.

If

```python
max_features = 7
```

it randomly considers 7 features.

Why?

If every tree always looked at all features, the trees would become very similar.

Randomly choosing a subset makes trees **more diverse**, which is the key idea behind a Random Forest.

Example:

Tree 1 sees

```
Age
Salary
Height
Weight
Experience
```

Tree 2 sees

```
Gender
Income
City
Marks
Branch
```

Different trees learn different patterns, and their combined predictions are usually more robust.

---

### What does `"auto"` mean?

In older versions of scikit-learn,

```python
max_features="auto"
```

meant:

* For classification: use `sqrt(n_features)`

For example, if there are 16 features:

```
sqrt(16) = 4
```

so each split considers 4 randomly selected features.

However, **`"auto"` has been removed in recent versions of scikit-learn**. For `RandomForestClassifier`, you should use:

```python
max_features="sqrt"
```

instead of `"auto"`.

---

### Summary

| Hyperparameter      | Meaning                                     | Higher Value Effect                                                       |
| ------------------- | ------------------------------------------- | ------------------------------------------------------------------------- |
| `n_estimators`      | Number of trees in the forest               | More stable predictions, slower training                                  |
| `max_depth`         | Maximum depth of each tree                  | More complex trees, possible overfitting                                  |
| `min_samples_split` | Minimum samples needed to split a node      | Larger values produce simpler trees                                       |
| `max_features`      | Number of features considered at each split | Larger values make trees more similar; smaller values increase randomness |

A good starting point for a modern `RandomForestClassifier` would be:

```python
rf_params = {
    "n_estimators": [100, 200, 500],
    "max_depth": [None, 10, 20, 30],
    "min_samples_split": [2, 5, 10],
    "max_features": ["sqrt", "log2"]
}
```

This avoids the deprecated `"auto"` option and uses values that are commonly tuned in practice.
