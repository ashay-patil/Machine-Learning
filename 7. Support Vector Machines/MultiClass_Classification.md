# How to perform Multiclass Classification using SVC

---

## 1. Tiny example dataset (3 classes, 2 features)

Assume this dataset (already scaled — ignore scaling for now):

| Point | x1 | x2 | Class |
| ----- | -- | -- | ----- |
| P1    | 1  | 1  | 0     |
| P2    | 2  | 1  | 0     |
| P3    | 1  | 2  | 0     |
| P4    | 5  | 5  | 1     |
| P5    | 6  | 5  | 1     |
| P6    | 5  | 6  | 1     |
| P7    | 9  | 1  | 2     |
| P8    | 10 | 2  | 2     |
| P9    | 9  | 2  | 2     |

Classes = `{0, 1, 2}`
So **OVO = 3 choose 2 = 3 classifiers**

---

## 2. OVO: which SVMs are trained?

### Classifier 1: **Class 0 vs Class 1**

Training data:

```
Class 0 → label +1
Class 1 → label -1
(Class 2 data is ignored)
```

Points used:

```
(1,1) (2,1) (1,2)  → +1
(5,5) (6,5) (5,6)  → -1
```

This SVM learns a boundary roughly separating low values from mid values.

---

### Classifier 2: **Class 0 vs Class 2**

Training data:

```
Class 0 → +1
Class 2 → -1
```

Points used:

```
(1,1) (2,1) (1,2)  → +1
(9,1) (10,2) (9,2) → -1
```

This boundary is **very wide apart**, easy separation.

---

### Classifier 3: **Class 1 vs Class 2**

Training data:

```
Class 1 → +1
Class 2 → -1
```

Points used:

```
(5,5) (6,5) (5,6)  → +1
(9,1) (10,2) (9,2) → -1
```

Diagonal-ish separation.

---

## 3. Prediction dry run (this is where people get confused)

### New test point:

```
X_new = (6, 4)
```

Now **all 3 classifiers fire**.

---

### ① Classifier (0 vs 1)

Decision:

```
distance > 0 → class 0
distance < 0 → class 1
```

(6,4) is much closer to class 1 cluster.

➡️ Vote = **Class 1**

---

### ② Classifier (0 vs 2)

(6,4) is closer to class 0 than class 2.

➡️ Vote = **Class 0**

---

### ③ Classifier (1 vs 2)

(6,4) is clearly closer to class 1.

➡️ Vote = **Class 1**

---

### Vote tally:

```
Class 0 → 1 vote
Class 1 → 2 votes
Class 2 → 0 votes
```

### Final prediction = **Class 1**

No probability.
No averaging.
Just **hard voting based on decision margins**.

---

## 4. The actual sklearn code (OVO explicitly)

```python
import numpy as np
from sklearn.svm import SVC

# Dataset
X = np.array([
    [1,1], [2,1], [1,2],      # class 0
    [5,5], [6,5], [5,6],      # class 1
    [9,1], [10,2], [9,2]      # class 2
])

y = np.array([0,0,0, 1,1,1, 2,2,2])

# OVO SVC
model = SVC(kernel='linear')

model.fit(X, y)

# Test point
X_test = np.array([[6, 4]])
print(model.predict(X_test))
```

Output:

```
[1]
```

---

## 5. What sklearn does internally (important)

Internally, this line:

```python
model = SVC(kernel='linear')
```

Creates **3 separate SVC objects**:

| Binary Model | Classes |
| ------------ | ------- |
| SVC_01       | 0 vs 1  |
| SVC_02       | 0 vs 2  |
| SVC_12       | 1 vs 2  |

Each model:

* sees **only its two classes**
* outputs a **signed distance**
* sklearn converts it to a **vote**

---

## 6. One more brutal truth

If you have:

* 50 classes → **1225 SVMs**
* Each SVM is expensive

This is why **SVC dies in large-class problems**.
