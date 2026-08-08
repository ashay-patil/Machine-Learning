# AdaBoost Regression — Complete Dry Run Using Random-Number Resampling

We'll use this small dataset:

| Student | Hours Studied (X) | Marks (Y) |
| ------- | ----------------: | --------: |
| A       |                 1 |        10 |
| B       |                 2 |        20 |
| C       |                 3 |        30 |
| D       |                 4 |        40 |
| E       |                 5 |        50 |

We'll build two weak regression trees:

`Tree_1, Tree_2`

For the dry run, we'll use the AdaBoost.R2 formulation:

`beta_t = epsilon_t / (1 - epsilon_t)`

and

`alpha_t = ln(1 / beta_t)`

The final AdaBoost.R2 prediction is obtained using a **weighted median** of the predictions of the weak learners, where the weights are their alpha values.

---

## STEP 1 — Give Every Observation Equal Weight

There are 5 observations.

Therefore:

`w_i = 1 / 5 = 0.2`

| Student |  X |  Y | Weight |
| ------- | -: | -: | -----: |
| A       |  1 | 10 |   0.20 |
| B       |  2 | 20 |   0.20 |
| C       |  3 | 30 |   0.20 |
| D       |  4 | 40 |   0.20 |
| E       |  5 | 50 |   0.20 |

So initially:

```text
A → 20%
B → 20%
C → 20%
D → 20%
E → 20%
```

---

## STEP 2 — Train Tree 1

The first weak regression tree is trained on the original dataset.

Suppose Tree 1 gives these predictions:

| Student | Actual | Tree 1 Prediction |
| ------- | -----: | ----------------: |
| A       |     10 |                12 |
| B       |     20 |                18 |
| C       |     30 |                32 |
| D       |     40 |                38 |
| E       |     50 |                30 |

Calculate absolute errors:

`e_i = |y_i - y_hat_i|`

| Student | Actual | Prediction |  Error |
| ------- | -----: | ---------: | -----: |
| A       |     10 |         12 |      2 |
| B       |     20 |         18 |      2 |
| C       |     30 |         32 |      2 |
| D       |     40 |         38 |      2 |
| E       |     50 |         30 | **20** |

Clearly, Tree 1 has a large error on E.

---

## STEP 3 — Normalize the Errors

AdaBoost.R2 converts the errors to values between 0 and 1.

Maximum error:

`e_max = 20`

Normalized error:

`L_i = e_i / e_max`

Therefore:

| Student | Error | Normalized Error (L_i) |
| ------- | ----: | ---------------------: |
| A       |     2 |                    0.1 |
| B       |     2 |                    0.1 |
| C       |     2 |                    0.1 |
| D       |     2 |                    0.1 |
| E       |    20 |                    1.0 |

---

## STEP 4 — Calculate Tree 1's Weighted Error

The weighted error is:

`epsilon_1 = sum(w_i * L_i)`

Therefore:

`epsilon_1 = (0.2)(0.1) + (0.2)(0.1) + (0.2)(0.1) + (0.2)(0.1) + (0.2)(1)`

`epsilon_1 = 0.02 + 0.02 + 0.02 + 0.02 + 0.20`

`epsilon_1 = 0.28`

So Tree 1's weighted error is:

`epsilon_1 = 0.28`

---

## STEP 5 — Calculate beta for Tree 1

AdaBoost.R2 calculates:

`beta_1 = epsilon_1 / (1 - epsilon_1)`

Therefore:

`beta_1 = 0.28 / (1 - 0.28)`

`beta_1 = 0.28 / 0.72`

`beta_1 ≈ 0.3889`

---

## STEP 6 — Calculate alpha for Tree 1

This is the learner's importance.

`alpha_1 = ln(1 / beta_1)`

Therefore:

`alpha_1 = ln(1 / 0.3889)`

`alpha_1 = ln(2.5714)`

`alpha_1 ≈ 0.944`

So:

`alpha_1 = 0.944`

This means Tree 1 gets an importance of approximately 0.944 in the final model.

---

## STEP 7 — Update the Observation Weights

Now we update the weights of the individual observations.

The AdaBoost.R2 update is:

`w_i_new = w_i * beta_1^(1 - L_i)`

Remember:

`beta_1 = 0.3889`

### For A

`L_A = 0.1`

Therefore:

`w_A_new = 0.2 * (0.3889)^(1 - 0.1)`

`w_A_new = 0.2 * (0.3889)^0.9`

`w_A_new ≈ 0.088`

Similarly:

`w_B_new ≈ 0.088`

`w_C_new ≈ 0.088`

`w_D_new ≈ 0.088`

### For E

`L_E = 1`

Therefore:

`w_E_new = 0.2 * (0.3889)^(1 - 1)`

`w_E_new = 0.2 * (0.3889)^0`

`w_E_new = 0.2`

Before normalization:

| Student | Old Weight | New Weight |
| ------- | ---------: | ---------: |
| A       |       0.20 |      0.088 |
| B       |       0.20 |      0.088 |
| C       |       0.20 |      0.088 |
| D       |       0.20 |      0.088 |
| E       |       0.20 |      0.200 |

Total:

`0.088 + 0.088 + 0.088 + 0.088 + 0.2 = 0.552`

---

## STEP 8 — Normalize the New Weights

We divide each weight by 0.552.

For A:

`w_A = 0.088 / 0.552 ≈ 0.159`

Similarly:

`w_B ≈ 0.159`

`w_C ≈ 0.159`

`w_D ≈ 0.159`

For E:

`w_E = 0.2 / 0.552 ≈ 0.362`

Therefore:

| Student | New Weight |
| ------- | ---------: |
| A       |      0.159 |
| B       |      0.159 |
| C       |      0.159 |
| D       |      0.159 |
| E       |  **0.362** |

Check:

`0.159 + 0.159 + 0.159 + 0.159 + 0.362 ≈ 1`

So now:

```text
A → 15.9%
B → 15.9%
C → 15.9%
D → 15.9%
E → 36.2%
```

E has become much more important because Tree 1 made a large error on E.

---

## STEP 9 — Create the Probability Intervals

Now comes the exact random-number technique.

Use the new weights as probabilities.

| Student | Probability | Cumulative Probability |
| ------- | ----------: | ---------------------: |
| A       |       0.159 |                  0.159 |
| B       |       0.159 |                  0.318 |
| C       |       0.159 |                  0.477 |
| D       |       0.159 |                  0.636 |
| E       |       0.362 |          0.998 ≈ 1.000 |

So the random-number intervals are approximately:

| Random Number | Select |
| ------------- | ------ |
| 0.000 – 0.159 | A      |
| 0.159 – 0.318 | B      |
| 0.318 – 0.477 | C      |
| 0.477 – 0.636 | D      |
| 0.636 – 1.000 | E      |

Notice E gets the largest interval:

`0.636 → 1.000`

which is about 36.2% of the range.

Therefore E has the highest probability of being selected.

---

## STEP 10 — Generate Random Numbers

Suppose we want Dataset 2 to contain 5 records.

Generate five random numbers:

`0.10, 0.72, 0.85, 0.40, 0.67`

Now map each random number to an observation.

### Random number = 0.10

0.10 falls in:

`0.000 – 0.159`

Therefore:

`0.10 → A`

### Random number = 0.72

0.72 falls in:

`0.636 – 1.000`

Therefore:

`0.72 → E`

### Random number = 0.85

Again:

`0.85 → E`

### Random number = 0.40

0.40 falls in:

`0.318 – 0.477`

Therefore:

`0.40 → C`

### Random number = 0.67

0.67 falls in:

`0.636 – 1.000`

Therefore:

`0.67 → E`

So our newly created Dataset 2 is:

| Record |  X |  Y |
| ------ | -: | -: |
| A      |  1 | 10 |
| E      |  5 | 50 |
| E      |  5 | 50 |
| C      |  3 | 30 |
| E      |  5 | 50 |

Notice:

`E appears 3 times`

This happened because E had the highest weight.

This is exactly the same random-number mechanism that you learned for classification.

---

## STEP 11 — STOP: Train Tree 2 on Dataset 2

Now this is the point you specifically asked not to skip.

**Tree 2 is now trained on Dataset 2:**

| Record |  X |  Y |
| ------ | -: | -: |
| A      |  1 | 10 |
| E      |  5 | 50 |
| E      |  5 | 50 |
| C      |  3 | 30 |
| E      |  5 | 50 |

The reason E appears three times is that Tree 1 performed badly on E.

Therefore Tree 2 gets more exposure to E.

**This is where the dataset creation for Tree 2 ends.**

---

## STEP 12 — Assume Tree 2 is Trained

Now Tree 2 has been trained on Dataset 2.

Suppose, for our dry run, Tree 2 gives the following predictions on the original observations:

| Student | Actual | Tree 1 | Tree 2 |
| ------- | -----: | -----: | -----: |
| A       |     10 |     12 |     11 |
| B       |     20 |     18 |     21 |
| C       |     30 |     32 |     31 |
| D       |     40 |     38 |     41 |
| E       |     50 |     30 |     47 |

Notice that Tree 2 has improved dramatically on E.

Tree 1:

`50 → 30`

Error:

`20`

Tree 2:

`50 → 47`

Error:

`3`

That is exactly what boosting is trying to achieve.

---

## STEP 13 — Assume alpha_2

We could now calculate Tree 2's error and alpha_2 exactly.

But for understanding the final prediction, let's assume:

`alpha_2 = 1.20`

Therefore we now have:

`alpha_1 = 0.944`

and

`alpha_2 = 1.20`

Tree 2 has a larger alpha, meaning Tree 2 is considered more important than Tree 1.
