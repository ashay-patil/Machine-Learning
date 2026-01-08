## Why am I getting two values of precision, recall and F1 score for binary classification model in classification report

You’re getting **two values** because you have **two classes**.
Binary classification does **not** mean “one precision, one recall”. That’s a beginner misconception.

Let’s break this cleanly and brutally.

---

## Look at your confusion matrix first (this tells the truth)

```
[[118  17]
 [ 29 136]]
```

By convention in sklearn:

```
           Pred 0   Pred 1
Actual 0    118      17
Actual 1     29     136
```

So your classes are:

* **Class 0**
* **Class 1**

Sklearn treats **both as important**, not just class `1`.

---

## Key rule you must understand (no exceptions)

> **Precision, recall, F1 are ALWAYS computed PER CLASS first.**

Even in binary classification.

That’s why you see **two rows**:

```
class 0 → precision, recall, f1
class 1 → precision, recall, f1
```

---

## What those two rows actually mean

### 🔹 Metrics for **class 0**

Here sklearn pretends:

* **Positive = class 0**
* **Negative = class 1**

---

### 🔹 Metrics for **class 1**

Now sklearn flips the perspective:

* **Positive = class 1**
* **Negative = class 0**


This is usually what people *informally* mean when they say
“precision and recall of a binary classifier”.

---

## Why sklearn does this (important)

Because:

* In **multiclass**, there is no single “positive”
* Even in binary, **which class is important depends on the problem**

So sklearn refuses to assume:

* class 1 is always positive
* class 0 is unimportant

That assumption would be **wrong in many real systems**.

---

## Then what are these?

### 🔸 Macro average

```
macro avg
```

* Simple average of metrics of class 0 and class 1
* Treats both classes **equally**
* Ignores class imbalance

Use when:

* Both classes matter equally

---

### 🔸 Weighted average

```
weighted avg
```

* Average weighted by number of samples (support)
* Dominated by majority class

Use when:

* Dataset is imbalanced
* You want realistic overall performance

---

## Which numbers should YOU care about?

That depends on **your problem**, not sklearn.

Ask yourself:

* What is the **positive class in real life**?
* Which error hurts more?

Examples:

* Disease detection → look at **class = disease**
* Fraud detection → look at **class = fraud**
* Spam → look at **class = spam**

👉 You usually focus on **precision / recall of ONE class**, not both.

---

## Final mental model (lock this in)

* Binary classification → **2 classes**
* Metrics → **computed per class**
* sklearn shows everything → **you decide what matters**

If you blindly quote “accuracy = 0.85” or “F1 = 0.85” without saying **for which class**, you don’t understand your model yet.

---

After finding the confusion matrix we need to write the Positive, negative and category names as per domain knowledge so that we would be able to understand which row of classification_report to consider.

* As per domain knowledge :- 
```
                                     Prediction
                                negative  | positive
                                category1 | category2
                              |      0    |     1     |
                          |---| --------- | --------- |
Actual category1 negative | 0 |   118     |    17     |
       category2 positive | 1 |    29     |    136    |
```

* From here we get to know that class 1 is considered positive. Thus second line of classification report is useful to us.

---
