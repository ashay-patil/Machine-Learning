### ct=ColumnTransformer(transformers=[('onehot',OneHotEncoder(drop='first'),[3])], remainder='passthrough')
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