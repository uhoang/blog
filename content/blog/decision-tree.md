---
title: "Decision trees"
date: 2017-02-17T14:53:17-05:00
tags: ["machine learning"]
---

# Decision trees

The main idea of decision trees is to learn a set of rules that help to classify/predict a new example given a training dataset. In this process, we split the data according to one of the features, so that we end up with the purest possible distribution of the labels

### How are these rules learnt?

#### Explaining decision trees:


We start at the root node, which takes an input as an entire training dataset. At each node, we will ask T/F question about one of the features. In response to this question, we split/partition data into 2 subsets.
Then these subsets becomes an input to two child nodes. The goal of question is to unmix labels as we proceed down. In order to build an effective tree, one needs to understand which questions to ask and when to ask. We use a metric called 
Gini impurity in CART (or entropy in ID3) to measure the uncertainty at a single node. These metric s quantify how much the question helps to unmix the labels. Finally, we use a concept called information gain to measure how much uncertainty the question helps to reduce at each node. We select the question and threshold that give the highest information gain.


#### When to stop:
All child nodes are pure, or the information gain is 0


#### Metrics:
1. Gini impurity: measure of how often a randomly chosen element from the set would be
incorrectly labeled if is was randomly labeled according to the distribution of labels 
in the subset
$$I\_g(p) = 1 - \sum\_{i = 1}^J p\_i^2$$

  where $i = \{1, ..., J\}$ and $p\_j$ be fraction of items labeled with class i in the set
  
  

2) Entropy: 
$$H(S) = \sum\_{x \in X} -p(x) log\_2 p(x)$$
  where <br/>
  * $S$ - The current dataset for which entropy is being calculated  <br/>
  * $X$ - Set of classes in $S$ <br/>
  * $p(x)$ - The proportion of the number of elements in class $x$ to  the number of elements set $S$ <br/>

3) Information gain 

$$IG(T|l, r) = H(T) - p\_{l} H(T|l) - p\_{r}H(T|r)$$

