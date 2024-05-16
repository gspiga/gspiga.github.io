---
title: "Counting on the Right Regression (Pt. 1): Poisson vs. Linear for Count Data"
show_date: true 
toc: true
toc_label: "Navigation"
toc_sticky: true
---

Count data, which represents the number of times an event occurs, is common in many fields, from the number of customer visits to a store to the number of accidents occurring at a specific location. Often, the gut reaction is to apply linear regression given the numerical outcomes, but it usually falls short with count data. In part one of this three-part series, I'll explore why Poisson regression is often more suitable for modeling counts and demonstrate its advantages over linear regression.

## Why Linear Regression is not Ideal

Count data measures the frequency of an event. Because of this, count data is always going to be discrete as well as non-negative. Linear regression however often assumes the data is continuous and can include negative values. 

Linear regression assumes that the relationship between the independent and dependent variables is linear and that the dependent variable is normally distributed with constant variance (homoscedasticity). These assumptions often do not hold true for count data, leading to several issues:

1. Non-negative Predictions: Linear regression can produce negative predictions, which are not meaningful for count data.

2. Heteroscedasticity: Count data often exhibits heteroscedasticity, where the variance increases with the mean. Recall we need *homoscedasticity* (equal variance) to be met for linear regression to be valid.
 
3. Non-normality: Linear regression assumes that the residuals (differences between observed and predicted values) are normally distributed, not often seen in count data. 

Hopefully, by now, you're thinking "Well Gianni, you have made it clear linear regression is a terrible idea for my count data, but what do I use instead?" 

## Introducing: Poisson Regression

Poisson regression is designed explicitly for count data and assumes that the counts follow a Poisson distribution. The Poisson distribution models the probability of a given number of events happening in a fixed interval of time or space, given a known average rate of occurrence.

In Poisson regression, we have the following assumptions:

1. Each observation is independent of the others (i.e. the number of ice cream cones sold today will not influence the number sold tomorrow).
2. The mean and the variance of your model are identical. Formally, $$E(Y_i) = Var(Y_i)$$. I will introduce this concept more in depth as well as what to do when this is violated in Part 2. 

### Nerdy Math Notation

If you are familiar with the formula for linear regression model, you will find the Poisson is quite familiar: 

$$log(\lambda_i) = \beta_0 + \beta x_{i1} + \beta_2 x_{i2} + \ldots + \beta_p x_{ip}$$ where $$\lambda_i$$ is the expected count for the $i$-th observation and $$\beta_0, \beta_1, \ldots, \beta_p$$ are our regression coefficients. 

### Implementation in Python

Let's show a simple example of how to apply Poisson regression in Python. We will use a simple data set, modeling the number of awards given to high school students. Our two predictors are the score on their math final and a categorical variable of three levels for which program they are in: Vocational, General, and Academic. Here is a preview of our data set, which we will call `awards`: 

|id | num_awards | prog | math |
|---|---|---|---|
45 | 0 | Vocational | 41 |
108 | 0 | General | 41 |
15 | 0 | Vocational | 44 |
67 | 0 | Vocational | 42 |
153 | 0 | Vocational | 40 |

We can visualize our counts using a histogram to get a better idea of the distribution of our counts:
![Counts Separated by Program Type](https://github.com/gspiga/gspiga.github.io/blob/master/code_books/Poisson_Regression/Part_1/histogram_of_num_awards.png?raw=true)

Our response variable does not have any hint of normality. Seeing there is a right skew, Poisson regression seems to be the most fitting choice. 

We can use the following Python code:

```
import statsmodels.formula.api as smf

# Poisson regression model
poisson_model = smf.poisson('num_awards ~ C(prog) + math', data=awards).fit()
```
We now have the following model:

<table class="simpletable">
<caption>Poisson Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>      <td>num_awards</td>    <th>  No. Observations:  </th>  <td>   200</td>  
</tr>
<tr>
  <th>Model:</th>                <td>Poisson</td>     <th>  Df Residuals:      </th>  <td>   196</td>  
</tr>
<tr>
  <th>Method:</th>                 <td>MLE</td>       <th>  Df Model:          </th>  <td>     3</td>  
</tr>
<tr>
  <th>Date:</th>            <td>Thu, 16 May 2024</td> <th>  Pseudo R-squ.:     </th>  <td>0.2118</td>  
</tr>
<tr>
  <th>Time:</th>                <td>10:38:47</td>     <th>  Log-Likelihood:    </th> <td> -182.75</td> 
</tr>
<tr>
  <th>converged:</th>             <td>True</td>       <th>  LL-Null:           </th> <td> -231.86</td> 
</tr>
<tr>
  <th>Covariance Type:</th>     <td>nonrobust</td>    <th>  LLR p-value:       </th> <td>3.747e-21</td>
</tr>
</table>
<table class="simpletable">
<tr>
            <td></td>               <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>             <td>   -5.2471</td> <td>    0.658</td> <td>   -7.969</td> <td> 0.000</td> <td>   -6.538</td> <td>   -3.957</td>
</tr>
<tr>
  <th>C(prog)[T.Academic]</th>   <td>    1.0839</td> <td>    0.358</td> <td>    3.025</td> <td> 0.002</td> <td>    0.382</td> <td>    1.786</td>
</tr>
<tr>
  <th>C(prog)[T.Vocational]</th> <td>    0.3698</td> <td>    0.441</td> <td>    0.838</td> <td> 0.402</td> <td>   -0.495</td> <td>    1.234</td>
</tr>
<tr>
  <th>math</th>                  <td>    0.0702</td> <td>    0.011</td> <td>    6.619</td> <td> 0.000</td> <td>    0.049</td> <td>    0.091</td>
</tr>
</table>

One thing to keep in mind when interpreting coefficients. Recall that we are building a model for the *log* of the expected count. So for example: for every additional point a student receives on the math final, the difference in the logs of expected counts is expected to change by 0.0702. 

References: 
- [POISSON REGRESSION | STATA ANNOTATED OUTPUT](https://stats.oarc.ucla.edu/stata/output/poisson-regression/)
