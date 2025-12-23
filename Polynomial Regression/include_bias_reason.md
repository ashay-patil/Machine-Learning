## What happens if you don’t include bias?
### Case 1: You’re using LinearRegression(fit_intercept=True)

Then including bias is pointless and sometimes harmful.

Why?

Because:
```
PolynomialFeatures(include_bias=True) # adds a bias column
LinearRegression(fit_intercept=True) # adds another bias
``` 
Now you have two intercepts → redundancy → numerical instability.

### 👉 Best practice:
```
PolynomialFeatures(include_bias=False)
LinearRegression(fit_intercept=True)  ## By Default the fit_intercept is True
```

This is what professionals actually do.

## Case 2: You’re using a model with fit_intercept=False

Then you must include bias, otherwise:

The model has no intercept at all

It will underfit

Predictions will be systematically shifted