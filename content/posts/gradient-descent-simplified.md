---
title: "Gradient Descent Simplified"
date: 2023-04-21T10:38:50+02:00
draft: false
description: "Gradient descent optimizes machine learning models by iteratively minimizing the loss function using various techniques."
tags: ["Gradient Descent", "Algorithm", "Machine Learning"]
ShowToc: true
ShowBreadCrumbs: false
math: true
---

# Gradient Descent Algorithm in Machine Learning

Gradient descent is an optimization algorithm used in machine learning to minimize a loss function, which quantifies the difference between predicted and actual outcomes. It iteratively adjusts model parameters to minimize the error between predictions and actual data. In this post, we'll provide a simple, technical explanation of gradient descent, and discuss how it works in a machine learning context.

## Overview

Consider a loss function, `L(w)`, which measures the error between the predicted and actual outcomes. The goal is to find the optimal values of the weights `w` that minimize the loss function. Gradient descent achieves this by iteratively adjusting the weights in the direction of the steepest decrease in the loss function, which is given by the negative of the gradient.

The gradient of the loss function, denoted by `∇L(w)`, is a vector of partial derivatives with respect to each weight. The gradient points in the direction of the steepest increase in the function, so we update the weights by subtracting the gradient times a learning rate `α`. The learning rate determines the step size of the update and is a hyperparameter that needs to be chosen carefully.

The update rule for gradient descent is:

$$ \(w_{\text{new}} = w_{\text{old}} - \alpha \nabla L(w_{\text{old}})\) $$

This process is repeated until convergence, which can be determined by a variety of stopping criteria, such as reaching a maximum number of iterations, or when the change in the loss function between iterations falls below a predefined threshold.

## Types of Gradient Descent

There are three main types of gradient descent, which differ in how they calculate the gradient:

1. **Batch Gradient Descent**: Computes the gradient using the entire dataset. This can be computationally expensive for large datasets, but provides an accurate estimate of the gradient.

   $$ \(\nabla L(w) = \frac{1}{N} \sum_{i=1}^N [\nabla l_i(w)]\) $$

2. **Stochastic Gradient Descent (SGD)**: Computes the gradient using a single randomly selected data point per iteration. This makes it computationally efficient, but introduces noise in the gradient estimation.

   $$ \(\nabla L(w) = \nabla l_i(w) \quad \text{for a randomly chosen } i\) $$

3. **Mini-batch Gradient Descent**: Computes the gradient using a small subset (mini-batch) of the dataset. This balances the computational efficiency of SGD with the accuracy of batch gradient descent.

   $$ \(\nabla L(w) = \frac{1}{|B|} \sum_{i \in B} [\nabla l_i(w)]\) $$

## Example: Linear Regression

Let's consider a simple example of linear regression, where we want to fit a line to a set of data points. The linear model is given by:

$$ \(y_{\text{pred}} = w_0 + w_1 \cdot x\) $$

We use the mean squared error (MSE) as our loss function:

$$ \(L(w) = \frac{1}{N} \sum_{i=1}^N [(y_i - y_{\text{pred}_i})^2]\) $$

To perform gradient descent, we need to compute the gradient of the MSE with respect to the weights `w_0` and `w_1`:

$$ \(\nabla L(w) = \left[ \frac{\partial L}{\partial w_0}, \frac{\partial L}{\partial w_1} \right]\) $$

After calculating the partial derivatives, we get:

$$ \(\frac{\partial L}{\partial w_0} = \frac{-2}{N} \sum_{i=1}^N [(y_i - y_{\text{pred}_i})]\) $$

$$ \(\frac{\partial L}{\partial w_1} = \frac{-2}{N} \sum_{i=1}^N [(y_i - y_{\text{pred}_i}) \cdot x_i]\) $$

Now, we can apply the gradient descent update rule for each weight:

$$ \(w_{0_{\text{new}}} = w_{0_{\text{old}}} - \alpha \frac{\partial L}{\partial w_0}\) $$

$$ \(w_{1_{\text{new}}} = w_{1_{\text{old}}} - \alpha \frac{\partial L}{\partial w_1}\) $$

We repeat these updates until convergence or until we reach the maximum number of iterations.

## Pseudocode

Here's a simple pseudocode for implementing gradient descent on a linear regression problem:

```
1. Initialize the weights w_0 and w_1 with random values
2. Set the learning rate α and the maximum number of iterations
3. Repeat until convergence or maximum iterations:
   a. Calculate the gradient of the loss function with respect to the weights:
      ∂L/∂w_0 and ∂L/∂w_1
   b. Update the weights using the gradient descent rule:
      w_0_new = w_0_old - α * ∂L/∂w_0
      w_1_new = w_1_old - α * ∂L/∂w_1
   c. Check convergence criteria
```

## Summary

Gradient descent is a powerful optimization algorithm used in machine learning to minimize loss functions. It works by iteratively updating model parameters in the direction of the steepest decrease in the loss function. There are three main types of gradient descent: batch, stochastic, and mini-batch. In this post, we discussed the algorithm in the context of linear regression, but gradient descent can be applied to a wide variety of machine learning problems.
