---
title: "Machine Learning Culinary School: Chopping Up Your Data :knife:"
show_date: true 
---

Say you are building a linear regression model to make predictions. You've got the data set formatted and cleaned. Run the code on your whole data set, find your parameters, and calculate the error of the model. After a couple of minutes, you start to wonder..."How do I know if this model is any good with new data?" 

This is where the knife-chopping skills come in, or more, your *data-splitting* skills. We split data commonly into two different subsets, the *training set* and the *test set*. First, let's break the entire process step-by-step, and after we will discuss the advantages and variations.
1. Split your data into two, a *training set* and a *test set*.  
2. Build your model on the training data, and measure the error rate.
3. Use this model to build predictions with your new test data, and measure the error rate. 

There are some things to note here. Usually, you perform an 80-20 split, where 80% of your data is for training and the remaining 20% is for testing. However, there are variations of this such as a 70-30 split. When measuring your error rate on the test cases, your *generalization error*, you are now getting a measurement of how your model performs on new data. If your training error is low but the generalization error is high, you have a model that is overfitting.

Pretty simple right?

### What if my Model has a Hyperparameter?

Hyperparameters add a level of complexity to the scenario. Why? Let us recall what the difference between a model parameter and a model hyperparameter is. Model parameters help us determine what our prediction of the next instance will be; the values of these parameters are what we are trying to optimize. A hyperparameter is a parameter of the learning algorithm during training, one that is not determined by data but by us as model builders. We often refer to this choosing of a value for a hyperparameter as *model tuning*. 

Intuitively, to find the best hyperparameter value, we could train a lot of different models with a lot of different hyperparameter values. Test all these different models on the test set, find the hyperparameter with the lowest generalization error, and pick that model. What is the problem here? You have only tuned this model for **this specific set** of test data. Any new data you use in the future with this model is not likely to perform well. 

We can alleviate this by splitting our data into an additional piece, the *validation set* (or the *development/dev set*). Let's update our step-by-step guide with this additional process:
1. Split your data into three a *training set*, a *validation set*, and a *test set*.  
2. Train multiple models on the training data, and measure the error rate of each against the validation set.
3. The model with the lowest error on the validation set gets retrained with the training set and validation set combined as one new data set. 
4. Use this final model to build predictions with your new test data, and measure the generalization error rate. 

While this process will perform quite well, there is something to be cautious of: the size of your validation set. If your validation set is too small, you risk picking a suboptimal model, whereas if the validation set is too large, you may not have enough data to build an accurate model in training. You could alleviate this with *cross-validation*, which allows you to pick a model using many different validation sets and taking the average of your model performance. However, this computational complexity increases when doing this, which is yet another tradeoff. 

Now you should have a solid grasp of the advantage of splitting your data for model building. Questions/Feedback? Feel free to reach out! 
