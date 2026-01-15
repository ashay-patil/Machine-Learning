## What happens if you don’t include bias?

* LinearRegression(fit_intercept=True) means to include theta0 in the equation of Best Fit line. Thus the equation of Bestfit line will be y = theta0 + theta1 * x1 + theta2 * x2 + .....
* LinearRegression(fit_intercept=False) means not to include theta0 in the equation of Best Fit line. Thus the equation of Best Fit line will be y = theta1 * x1 + theta2 * x2 + ....
* In sklearn by default the fit_intercept is set to True

### Case 1: You’re using LinearRegression(fit_intercept=True)

Then including bias is pointless and sometimes harmful.

Why?

Because:
```
PolynomialFeatures(include_bias=True) # adds a bias column
LinearRegression(fit_intercept=True) # adds another bias
``` 

PolynomialFeatures(include_bias=True) includes an extra x column whose value is always 1. 
Since fit_intercept = True our best fit line will turn out to be y = theta0 + theta1 * 1 + theta2 * x2 + .....
Notice that theta0 + theta1 * 1 is constant. 
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