# ct=ColumnTransformer(transformers=[('onehot',OneHotEncoder(drop='first'),[3])], remainder='passthrough')
### What `drop='first'` **actually** means

When you use **OneHotEncoder**, a categorical column is converted into multiple binary (0/1) columns.

Example column (index `3`):

```
Color
------
Red
Blue
Green
```

Normally, one-hot encoding creates **one column per category**:

```
Color_Blue  Color_Green  Color_Red
0           0            1
1           0            0
0           1            0
```

Now here’s the **problem** 👇
These columns are **linearly dependent**.

If you know:

```
Color_Blue = 0
Color_Green = 0
```

you **automatically know**:

```
Color_Red = 1
```

This is called **dummy variable trap** (perfect multicollinearity).

---

### So what does `drop='first'` do?

It **removes one category column** (the first one, lexicographically).

Using `drop='first'`, the encoding becomes:

```
Color_Green  Color_Red
0            1
0            0
1            0
```

Now:

* If both columns are `0` → it means **Blue**
* Blue is the **reference category**

👉 The dropped category is **implicitly represented**.

---

### Why this matters (don’t ignore this)

1. **Avoids multicollinearity**

   * Linear Regression, Logistic Regression, SVM (linear kernel) will **break or behave badly** without this.

2. **Cleaner math**

   * Matrix inversion becomes possible.
   * Coefficients are interpretable **relative to the dropped category**.

3. **Industry standard**

   * Any serious ML pipeline uses this for linear models.

---

### Important misconception to kill ❌

> “drop='first' means losing information”

**Wrong.**

No information is lost.
One category is just treated as the **baseline**.

---

### When you should NOT use `drop='first'`

Be precise here:

❌ Don’t use it if:

* You’re using **tree-based models**
  (Decision Trees, Random Forest, XGBoost)

Why?

* Trees **don’t care about multicollinearity**
* Dropping a column can actually reduce split clarity

✔ For trees, use:

```python
OneHotEncoder(drop=None)
```

---

### Your exact code, decoded

```python
ct = ColumnTransformer(
    transformers=[
        ('onehot', OneHotEncoder(drop='first'), [3])
    ],
    remainder='passthrough'
)
```

Means:

* Column **index 3** is categorical
* One category will be **dropped**
* Remaining columns pass through unchanged
* Resulting dataset is safe for **linear models**

---

### Brutal summary (memorize this)

* `drop='first'` → **prevents dummy variable trap**
* One category becomes the **reference**
* Essential for **linear models**
* Optional (often unnecessary) for **tree models**

---

### In Support Vector Regressor we use Kernels so that a non linear data in n dimensions will behave linearly in >n dimensions.



# Hyperparameters - gamma 


`gamma` is one of the most confusing SVM hyperparameters, so let's build the intuition from scratch.

## What exactly is gamma?

Imagine every training data point sends out an invisible "field of influence."

**Gamma controls the size of that field.**

* **Small gamma** → Large influence area
* **Large gamma** → Small influence area

Think of it like dropping a stone into a pond.

### Small gamma

The ripples spread very far.

```
           ***************
        *********************
      ********* X ************
        *********************
           ***************
```

One point affects a large region.

---

### Large gamma

The ripple hardly spreads.

```
      ***
     *X*
      ***
```

The point affects only its immediate neighborhood.

---

## Example 1

Suppose we have these training points.

```
Blue                 Blue



          Red



Blue                 Blue
```

We want to classify the center red point.

---

### Small gamma

Every point influences a large area.

The four blue points and the red point all "pull" the decision boundary.

The SVM creates a smooth boundary.

```
Blue               Blue


     ---------
    /         \
   |   Red     |
    \---------/


Blue               Blue
```

The boundary is smooth because every point influences a large neighborhood.

---

### Large gamma

Now each point influences only nearby locations.

The red point says

> "Only I matter here."

The blue points say

> "We only care about our nearby regions."

The SVM creates a tight boundary around the red point.

```
Blue               Blue


       (Red)


Blue               Blue
```
Notice how the decision boundary hugs the red point.


## One-line interview answer

> **Gamma controls the radius of influence of each training sample in non-linear SVMs. A small gamma gives each point a large influence, producing a smoother decision boundary and reducing overfitting. A large gamma gives each point a small influence, producing a more complex decision boundary that can fit the training data closely but may overfit.**

### Easy way to remember

| Gamma     | Influence Radius | Decision Boundary | Risk         |
| --------- | ---------------- | ----------------- | ------------ |
| **Small** | Large            | Smooth            | Underfitting |
| **Large** | Small            | Complex           | Overfitting  |

A simple memory trick is:

* **Small γ = "See the big picture."** Every point considers many neighbors.
* **Large γ = "Focus on nearby points only."** Every point cares almost exclusively about its immediate surroundings, making the boundary much more intricate.
