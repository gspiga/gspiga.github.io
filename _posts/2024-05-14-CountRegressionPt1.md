---
title: "Counting on the Right Regression (Pt. 1): Poisson vs. Linear for Count Data"
show_date: true 
---

Count data, which represents the number of times an event occurs, is common in many fields, from the number of customer visits to a store to the number of accidents occurring at a specific location. Often, the gut reaction is to apply linear regression given the numerical outcomes, but it usually falls short with count data. In part one of this three-part series, I'll explore why Poisson regression is often more suitable for modeling counts and demonstrate its advantages over linear regression.

### Why Linear Regression is not Ideal

Count data measures the frequency of an event. Because of this, count data is always going to be discrete as well as non-negative. Linear regression however often assumes the data is continuous and can include negative values. 

Linear regression assumes that the relationship between the independent and dependent variables is linear and that the dependent variable is normally distributed with constant variance (homoscedasticity). These assumptions often do not hold true for count data, leading to several issues:

1 Non-negative Predictions: Linear regression can produce negative predictions, which are not meaningful for count data.

2. Heteroscedasticity: Count data often exhibits heteroscedasticity, where the variance increases with the mean. Recall we need *homoscedasticity* (equal variance) to be met for linear regression to be valid.
 
3. Non-normality: Linear regression assumes that the residuals (differences between observed and predicted values) are normally distributed, not often seen in count data. 

Hopefully, by now, you're thinking "Well Gianni, you have made it clear linear regression is a terrible idea for my count data, but what do I use instead?" 

## Introducing: Poisson Regression

Poisson regression is designed explicitly for count data and assumes that the counts follow a Poisson distribution. The Poisson distribution models the probability of a given number of events happening in a fixed interval of time or space, given a known average rate of occurrence.

In Poisson regression, we have the following assumptions:

1. Each observation is independent of the others (i.e. the number of ice cream cones sold today will not influence the number sold tomorrow).
2. The mean and the variance of your model are identical. Formally, $E(Y_i) = Var(Y_i)$. I will introduce this concept more in depth as well as what to do when this is violated in Part 2. 

### Nerdy Math Notation

If you are familiar with the formula for linear regression model, you will find the Poisson is quite familiar: 

$log(\lambda_i) = \beta_0 + \beta x_{i1} + \beta_2 x_{i2} + \ldots + \beta_p x_ip$ where $\lambda_i$ is the expected count for the $i$-th observation and $\beta_0, \beta_1, \ldots, \beta_p$ are our regression coefficients. 


