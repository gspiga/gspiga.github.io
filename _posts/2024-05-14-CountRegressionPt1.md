---
title: "Counting on the Right Regression (Pt. 1): Poisson vs. Linear for Count Data"
show_date: true 
---

Count data, which represents the number of times an event occurs, is common in many fields, from the number of customer visits to a store to the number of accidents occurring at a specific location. Often, the gut reaction is to apply linear regression given the numerical outcomes, but it usually falls short with count data. In part one of this three-part series, I'll explore why Poisson regression is often more suitable for modeling counts and demonstrate its advantages over linear regression.

### The Structure of Count Data

Count data measures the frequency of an event. Because of this, count data is always going to be discrete as well as non-negative. 

