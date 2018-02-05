---
title: "Note on Factorization machines"
date: 2017-01-24T15:28:54-05:00

---

Factorization machines (FM) are a generic model class that can mimic most factorization models just by feature engineering.

FMs combine feature engineering with factorization models(matrix factorization or tensor factorization) in estimating interactions between categorical variables.

<!-- Factorization machines (FMs) uses linear regression and matrix factorization to model sparse feature interactions but in linear time.
 -->
## Factorization Machines Math 

$$ \hat y := w\_0 + \sum\_{i= 1}^n w\_i x\_i + \sum\_{i = 1}^n \sum\_{j = i+1}^n \hat w\_{i,j} x\_i x\_j $$

where $\hat w\_{i,j}$ are the factorized interaction parameters between pairs

$$ w\_{i, j} = \langle \mathbf{v\_i}, \mathbf{v\_j}\rangle = \sum\_{f = 1}^k v\_{i,f} \cdot v\_{j,f} $$

and the model parameters $\Theta$ that have to be estimated are:
$$w\_{0}, \mathbf{w} \in \mathbb{R}^n, \mathbf{V} \in \mathbb{R}^{n \times k}$$

Intepretation: $w\_0$ is the global bias, $w\_i$ models the interaction of the i-th variable to the target and $\hat w\_{i, j}$ models the factorized interaction of a pair of variables with the target.

When we consider quadractic feature interactions, the complexity increases to $O(n^2)$ in the above formula. Now consider a very sparse set of features, the runtime blows up. In most cases, we instead model a very limited set of feature interactions to manage the complexity.

## How do FMs solve the problem?
In the recommendataion problem space, the sparsity problem has been dealt with a well-documented technique called (non-negative) matrix factorization


![Example image](/images/factorization.png)

We factorize sparse user item matrix $( r )$ $\in \mathbb{R}^{U \times I}$ into a user matrix $(u)$ $\in \mathbb{R}^{U  \times K}$ and an item matrix $(i)$ $\in \mathbb{R}^{I \times K}$, where $K << U$ and $K << I$.

User $(u\_i)$'s preference for item $i\_j$ can be approximated by $u\_i \cdot i\_j$.

Factorization Machines takes inspiration from matrix factorization, and models the feature iteractions like using latent vectors of size $K$. As a result, every sparse feature $f_i$ has a corresponding latent vector $v\_i$. And two feature's interactions are modelled as $v\_i \cdot v\_j$.
