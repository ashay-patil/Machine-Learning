# Gradient Boosting for Regression vs Classification

This is an excellent question. In fact, this is the point where many people think:

> "Wait... adding numbers makes sense for regression, but how can adding trees produce a class?"

The answer is:

> **The equation is exactly the same!** The only difference is **what the equation represents**.

Let's compare.

| Regression | Classification |
|------------|----------------|
| Output of equation = prediction | Output of equation = score (log-odds or logits) |
| Final output is continuous | Final output is converted into probabilities |

---

# Regression

You already know this.

Suppose

```text
H0 = 50
```

Tree predictions

```text
H1 = +8
H2 = -3
H3 = +1
```

Learning rate

```text
η = 0.1
```

Final equation

```text
F(x) = H₀ + ηH₁ + ηH₂ + ηH₃
```

Substituting the values:

```text
F(x)

= 50
+ 0.1(8)
+ 0.1(-3)
+ 0.1(1)

= 50.6
```

Prediction

```text
ŷ = 50.6
```

Finished.

---

# Binary Classification

Now suppose the problem is

```text
Spam
Not Spam
```

The equation is **STILL**

```text
F(x) = H0 + ηH1 + ηH2 + ... + ηHM
```

Nothing changes.

The only difference is

**F(x) is NOT a probability.**

It is called a **logit** or **log-odds score**.

Suppose

```text
H0 = -0.6
```

Trees predict

```text
Tree1 = +0.8
Tree2 = -0.2
Tree3 = +0.3
```

Learning rate

```text
η = 0.1
```

Final score

```text
F(x)

= -0.6
+ 0.08
- 0.02
+ 0.03

= -0.51
```

Now we convert this score into a probability using the **sigmoid** function.

```text
sigmoid(x) = 1 / (1 + e^(-x))
```

So

```text
Probability

= sigmoid(-0.51)

≈ 0.375
```

Meaning

```text
37.5% chance of Spam
62.5% chance of Not Spam
```

Then

```text
Probability > 0.5 ?

No

Predict Not Spam
```

So the equation didn't change.

Only the interpretation changed.

