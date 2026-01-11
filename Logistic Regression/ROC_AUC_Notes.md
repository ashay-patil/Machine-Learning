# ROC

---

## 1️⃣ First: What Logistic Regression *actually* outputs (important)

Logistic Regression **does NOT output classes**.

It outputs **probabilities**:

```
P(y = 1 | x) = 0.0  →  1.0
```

Example:

```
0.91  → very confident positive
0.55  → weak positive
0.49  → weak negative
0.12  → very confident negative
```

To convert this into a class, **you choose a threshold** (usually 0.5):

```
if prob ≥ threshold → class = 1
else → class = 0
```

⚠️ **This threshold choice is where ROC comes in.**

---

## 2️⃣ The core problem ROC solves

Accuracy, precision, recall, F1…
👉 **ALL of them depend on ONE fixed threshold**.

ROC answers a different question:

> “How good is my model at separating positives from negatives **across ALL possible thresholds**?”

Not at 0.5
Not at 0.7
Not at 0.2

👉 **Across every threshold from 0 to 1**

---

## 3️⃣ ROC Curve: what it really is

ROC = **Receiver Operating Characteristic**

It is a **curve**, not a single number.

### Axes (don’t memorize — understand):

**X-axis:**
❌ False Positive Rate (FPR)

```
FPR = FP / (FP + TN)
```

Meaning:

> “Out of all actual negatives, how many did I incorrectly mark as positive?”

---

**Y-axis:**
✅ True Positive Rate (TPR) = Recall

```
TPR = TP / (TP + FN)
```

Meaning:

> “Out of all actual positives, how many did I correctly catch?”

---

## 4️⃣ How the ROC curve is actually built (step-by-step)

Assume model outputs these probabilities:

| Probability | True Label |
| ----------- | ---------- |
| 0.95        | 1          |
| 0.85        | 1          |
| 0.70        | 0          |
| 0.55        | 1          |
| 0.40        | 0          |
| 0.20        | 0          |

Now vary threshold:

### Threshold = 0.9

Very strict
Only one positive predicted

→ Low FP
→ Low TP

### Threshold = 0.5

Balanced
More TP
Some FP

### Threshold = 0.1

Very loose
Almost everything positive

→ High TP
→ Very high FP

For **each threshold**, calculate:

```
(FPR, TPR)
```

Plot all those points
👉 Join them
👉 That’s the **ROC curve**

---

## 5️⃣ What a “good” ROC curve looks like

* Random model → diagonal line
* Perfect model → hugs top-left corner

Why top-left?

* **TPR = 1** (caught all positives)
* **FPR = 0** (no false alarms)

---

# AUC


## 1️⃣ What AUC is **NOT**

Let’s clear garbage first.

* ❌ It is **not accuracy**
* ❌ It is **not % correct predictions**
* ❌ It is **not about threshold = 0.5**
* ❌ It is **not a metric you “calculate from confusion matrix”**

If this is how you were thinking — that’s the mistake.

---

## 2️⃣ The ONLY thing AUC cares about

AUC answers **one single question**:

> **Does the model give higher scores to positive samples than negative samples?**

That’s it.
No more. No less.

---

## 3️⃣ Think in real life terms (no ML)

Imagine this situation:

You are a **bouncer at a club**.

* Positive class = **drunk people** (should NOT enter)
* Negative class = **sober people** (can enter)

Your model gives a **“drunk score”** from 0 to 1.

You don’t decide yet who enters.
You just **rank people** by how drunk they look.

---

## 4️⃣ One positive vs one negative (core idea)

Pick **one drunk person** and **one sober person**.

Now compare their scores.

### Case 1 ✅

```
Drunk person score  = 0.82
Sober person score  = 0.31
```

Model did a **good job**.

---

### Case 2 ❌

```
Drunk person score  = 0.45
Sober person score  = 0.67
```

Model failed — ranked wrong.

---

### Case 3 😐

```
Drunk person score = 0.50
Sober person score = 0.50
```

Model is clueless — tie.

---

## 5️⃣ THIS is how AUC is computed (mentally)

You **don’t need a curve** to understand AUC.

### Mental experiment:

1. Randomly pick **1 positive**

2. Randomly pick **1 negative**

3. Check:

   * Positive score > Negative score → win
   * Positive score < Negative score → loss
   * Equal → half win

4. Repeat for **all possible pairs**

---

## 6️⃣ AUC = percentage of wins

That’s it.

If out of 100 such comparisons:

* 87 times positive scored higher
* 10 times negative scored higher
* 3 times tie

Then:

```
AUC = (87 + 0.5×3) / 100 = 0.885
```

👉 **AUC ≈ 0.89**

This is the **real meaning** of AUC.

---

## 7️⃣ Concrete numeric example (no hand-waving)

### Dataset

| Sample | Score | True Label |
| ------ | ----- | ---------- |
| A      | 0.9   | 1          |
| B      | 0.8   | 1          |
| C      | 0.6   | 0          |
| D      | 0.4   | 0          |

### All positive–negative pairs

Positives: A, B
Negatives: C, D

Total pairs = 2 × 2 = 4

| Positive | Negative | Correct? |
| -------- | -------- | -------- |
| A(0.9)   | C(0.6)   | ✅        |
| A(0.9)   | D(0.4)   | ✅        |
| B(0.8)   | C(0.6)   | ✅        |
| B(0.8)   | D(0.4)   | ✅        |

```
AUC = 4 / 4 = 1.0 (perfect)
```

---

Now a bad model:

| Sample | Score | Label |
| ------ | ----- | ----- |
| A      | 0.6   | 1     |
| B      | 0.4   | 1     |
| C      | 0.9   | 0     |
| D      | 0.2   | 0     |

Pairs:

| Positive | Negative | Correct? |
| -------- | -------- | -------- |
| A(0.6)   | C(0.9)   | ❌        |
| A(0.6)   | D(0.2)   | ✅        |
| B(0.4)   | C(0.9)   | ❌        |
| B(0.4)   | D(0.2)   | ✅        |

```
AUC = 2 / 4 = 0.5
```

👉 **Random guessing**

---

## 8️⃣ Why the curve exists at all (now it’ll make sense)

ROC curve is just a **geometric trick** to compute this ranking score efficiently.

But conceptually:

> **AUC = how often positives outrank negatives**

That’s the soul.

---

## 9️⃣ Why AUC ignores threshold (important insight)

Because **ranking doesn’t need threshold**.

You’re only asking:

> “Who got a higher score?”

Not:

> “Above 0.5 or not?”

That’s why AUC is stable.

---

## 🔟 When your brain should say “Use AUC”

Use AUC when:

* Model outputs **scores/probabilities**
* You care about **ordering**, not final decision
* Threshold is flexible or undecided

---

## Final one-liner (burn this in memory)

> **AUC is the probability that your model ranks a random positive higher than a random negative.**

---

The threshold where we will find High TPR and Less FPR will be selected for our logistic regression model.

ROC is NOT a decision tool. It’s a visualization tool.

Threshold selection depends on:

Cost of FP vs FN

Legal / medical / financial risk

User experience

---

How thresholds are ACTUALLY chosen using ROC

There are three legit ways. Anything else is hand-waving.

1️⃣ Distance to top-left (what you’re thinking)

We define distance:

    distance=sqrt((FPR−0)^2+(TPR−1)^2)
​

Pick threshold with minimum distance.

✔ Works when:

FP and FN are equally bad

No business preference

⚠ Weak in real life because costs are rarely equal.

2️⃣ Youden’s J statistic (cleaner and common)

J=TPR−FPR

Pick threshold that maximizes J.

Why this works:

Maximizes separation between classes

Same intuition as top-left, but cleaner

This is the most commonly cited ROC-based threshold rule.

3️⃣ Business-driven threshold (most realistic)

Example:

Fraud detection:

FN is deadly

FP is acceptable

Then you don’t chase top-left.

You choose:

TPR ≥ 0.95

Even if FPR becomes high

ROC just helps you see what threshold achieves that.