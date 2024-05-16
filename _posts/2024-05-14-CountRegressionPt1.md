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

Let's show a simple example of how to apply Poisson regression in Python. We will use a simple data set, modeling the number of awards given to high school students. Our two predictors are the score on their math final and a categorical variable of three levels for which program they are in: Vocational, General, and Academic. Here is a preview of our data set: 

|id | num_awards | prog | math |
|---|---|---|---|
45 | 0 | Vocational | 41 |
108 | 0 | General | 41 |
15 | 0 | Vocational | 44 |
67 | 0 | Vocational | 42 |
153 | 0 | Vocational | 40 |

We call our data set `awards` and can use the following Python code:

```
import statsmodels.formula.api as smf

# Poisson regression model
poisson_model = smf.poisson('num_awards ~ prog + math', data=scores).fit()

# Summary of the model
print(poisson_model.summary())
```

$$
\begin{center}
\begin{tabular}{lclc}
\toprule
\textbf{Dep. Variable:}   &   num\_awards    & \textbf{  No. Observations:  } &      200    \\
\textbf{Model:}           &     Poisson      & \textbf{  Df Residuals:      } &      197    \\
\textbf{Method:}          &       MLE        & \textbf{  Df Model:          } &        2    \\
\textbf{Date:}            & Thu, 16 May 2024 & \textbf{  Pseudo R-squ.:     } &   0.1816    \\
\textbf{Time:}            &     09:16:13     & \textbf{  Log-Likelihood:    } &   -189.75   \\
\textbf{converged:}       &       True       & \textbf{  LL-Null:           } &   -231.86   \\
\textbf{Covariance Type:} &    nonrobust     & \textbf{  LLR p-value:       } & 5.148e-19   \\
\bottomrule
\end{tabular}
\begin{tabular}{lcccccc}
                   & \textbf{coef} & \textbf{std err} & \textbf{z} & \textbf{P$> |$z$|$} & \textbf{[0.025} & \textbf{0.975]}  \\
\midrule
\textbf{Intercept} &      -5.5781  &        0.677     &    -8.242  &         0.000        &       -6.905    &       -4.252     \\
\textbf{prog}      &       0.1233  &        0.163     &     0.755  &         0.450        &       -0.197    &        0.443     \\
\textbf{math}      &       0.0861  &        0.010     &     8.984  &         0.000        &        0.067    &        0.105     \\
\bottomrule
\end{tabular}
%\caption{Poisson Regression Results}
\end{center}
$$

<table class="simpletable">
<caption>Poisson Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>      <td>num_awards</td>    <th>  No. Observations:  </th>  <td>   200</td>  
</tr>
<tr>
  <th>Model:</th>                <td>Poisson</td>     <th>  Df Residuals:      </th>  <td>   197</td>  
</tr>
<tr>
  <th>Method:</th>                 <td>MLE</td>       <th>  Df Model:          </th>  <td>     2</td>  
</tr>
<tr>
  <th>Date:</th>            <td>Thu, 16 May 2024</td> <th>  Pseudo R-squ.:     </th>  <td>0.1816</td>  
</tr>
<tr>
  <th>Time:</th>                <td>09:19:35</td>     <th>  Log-Likelihood:    </th> <td> -189.75</td> 
</tr>
<tr>
  <th>converged:</th>             <td>True</td>       <th>  LL-Null:           </th> <td> -231.86</td> 
</tr>
<tr>
  <th>Covariance Type:</th>     <td>nonrobust</td>    <th>  LLR p-value:       </th> <td>5.148e-19</td>
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
 <th>Intercept</th> <td>   -5.5781</td> <td>    0.677</td> <td>   -8.242</td> <td> 0.000</td> <td>   -6.905</td> <td>   -4.252</td>
</tr>
<tr>
  <th>prog</th>      <td>    0.1233</td> <td>    0.163</td> <td>    0.755</td> <td> 0.450</td> <td>   -0.197</td> <td>    0.443</td>
</tr>
<tr>
  <th>math</th>      <td>    0.0861</td> <td>    0.010</td> <td>    8.984</td> <td> 0.000</td> <td>    0.067</td> <td>    0.105</td>
</tr>
</table>
