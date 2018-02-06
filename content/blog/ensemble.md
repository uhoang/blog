---
title: "Ensemble methods"
date: 2017-02-25T15:16:17-05:00
categories = ["machine learning"]
---

# Bagging & Boosting

Bagging and boosting are two ensenble methods that a set of weak learner are combined to create a strong learner, which has a better performance than a single learner. 

In learning, the main cause of error are due to noise, bias and variance. The goal of these ensemble methods is to reduce the error from bias and variance. Thus, it produces a more reliable classifier / regressor.

### How they work and differ

Both bagging and boosting get N learners by training on new training dataset produced by sampling with replacement from the original dataset.

1. At sampling stage
  * Bagging uses equal weights to sample a new dataset
  * Boosting applies an unequal weights for each observation
2. At training stage
  * Bagging builds each model independently
  * Boosting builds each learner in sequential way. Weights are redistributed after each training step. That is, putting more weights on difficult cases.
3. At classification / prediction stage
  * Bagging averages over the response of N learners
  * Boosting takes the weighted average of the estimates, which are assigned with different weights

### How to select the best suited method

This decision depends on your problem at hand. First, I will list out the main features of each method. 

1. Bagging: 
  * reduce variance
  * handle overfitting
  * independent classifers 

2. Boosting: 
  * reduce variance and bias
  * can overfit
  * sequential classifiers          

Both methods try to decrease a variance of a single estimate to create a higher stability model. However, bagging suffers a low performance if a single model is underfitted. Because bagging rarely get a better bias.

When the problem is overfitting a single model, boosting will perform worse by increasing the model complexity.

### Gradient Boosting Method
Setup: Denote $\\{(x_i, y_i)\\}\_{i=1}^n$ as a training set, a differentiable loss function $L(y, F(x))$ and a number of iteration $M$

1. Initialize model with a constant value: $F\_0(x) = argmin\_\gamma \sum\_i^n L(y\_i; \gamma)$
2. For $m = 1$ to $M$ 
    1. Compute pseudo residuals: 
    $$r\_{im} = -\left[\frac{\partial L(y\_i; F(x\_i))}{\partial F(x\_i)}\right]$$
    2. Fit a base learner (tree) $h\_m(x)$ to pseudo residuals, $\{(r\_{im}, x\_i)\}\_i^n$
    3. Compute multiplier $\gamma_m$ by solving the one-dim optimization problem 
    $$\gamma\_m = argmin\_\gamma \sum\_{i = 1}^n L(y\_i, F\_{m-1}(x\_i) + \gamma h\_m(x\_i))$$
    4. Update the model $F\_m(x) = F\_{m-1}(x) + \gamma\_m h\_m(x)$
    
3. Output $F_M(x)$


