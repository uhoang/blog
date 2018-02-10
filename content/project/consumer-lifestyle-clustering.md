---
title: "Consumer Lifestyle Clustering"
date: 2017-10-10T16:07:03-05:00
tags: ['clustering', 'R']
---

Recently, I’ve built a consumer lifestyle segmentation tool for the analytics and insight team at Mountain Equipment Co-op. The main purpose of this project is to replicate a typing tool, that has previously been developed for US consumers by McKinsey's data team. This tool is leveraged to design a scalable, customer-centric marketing solution. The analysis for this tool is written in R. For further details, you can visit [my Github repository] (https://github.com/uhoang/mec/).

### Objectives: 
- Build health lifestyle segmentation for Canadian consumers using an online survey of 2000 responses arranged by a panel provider.
- Identify clusters and interpret its meaning via descriptive statistics
- Perform pairwise comparisons among group means on profiling questions
- Future work -- prototyping a lifestyle scoring tool to predict a class / segment for a new consumer

### Methods
In this section, I will describe the clustering approach, its rationales and main findings.

###### 1. Data
  - The data contains a series of multi-select questions on health conditions, that respondents are currently suffer from or are concerned about preventing in the future. The answers are captured by a set of binary variables. Each represents one category in the question, in which the value 1 means the category is selected, and the value 0 means otherwise. 
  - It also includes additional profiling questions such as gender, age, frequency of participation in some activities, time recently spending on certain products, rate personal lifestyle goals and statements.

###### 2. Analysis:

* __Filter data__: I remove any low-quality observation, in which a respondent speeds through the survey by giving low-effort responses, and engages in variety of other behaviors that negatively impact response quality.

* __Transform inputs__: I regroup some health/prevention conditions into category to reduce the complexity of the inputs. This step helps to produce a cleaner interpretation of clusters in a later step. Age is dichotomized into two groups, below 58 and 58+. This threshold is based on the previous study. But other option could be explored. This coding makes all variables comparable. 
   
* __Select a similarity measure__: In order to group observations together, I first need to define some notion of similarity between observations. Since the data contains asymmetric binary variables, I have to use a distance metric that can handle this data type. In this case, I use a metric called Gower distance. The distance is always a number between 0 (identical) and 1 (maximally dissimilar). For each binary variable, Gower distance uses one minus Jaccard's Similarity Coefficient to calculate the distance. Then, the final distance is obtained as a weighted sum of distances for each variable. 
  
* __Select a clustering algorithm__: <br/>
  + I run both Partitioning around Medoids (PAM) and k-means algorithms for clustering. Both algorithms have quadratic run time and memory (i.e: $O(n^2)$). However, PAM is more robust to noise and outliers when compared to k-means, and it has the added benefit of having an observation serve as the exemplar for each cluster. 
  + Note that algorithms have similar procedure except PAM uses an observation as a cluster center (medoid), while k-means has a cluster center (centroid) defined by the mean of all observations in that cluster. Thus, I will only present the iterative steps in PAM:
      * Choose k random entities to be the medoids
      * Assign every entity to its closest medoid (using the custom distance matrix)
      * For each cluster, identify the observation that would yield the lowest average distance to other members in the cluster. Then make this observation the new medoid.
      * If at least one medoid has changed, return to the second step. Otherwise, end the algorithm.
      
* __Select the number of clusters__: A variety of metrics exist to help choose the number of clusters. In this analysis, I use Silhouette width as a validation metric. It is an aggregated measure of how similar an observation is to its own cluster compared its closest neighboring cluster. The metric can range from -1 to 1, where higher values are better. I then plot silhouette width for clusters ranging from 2 to 10 for both algorithms. From the Figure 1, we see that the PAM algorithm with 5 clusters yields the highest Silhouette value. On the other hand, K-Means with 2 clusters gives the highest Silhouette score. <br/> 

{{<figure src="/images/pam_silhouette_width_vs_num_clusters.png" title="Fig.1: Silhouette width by number of cluster in PAM">}}
<br/> 
{{<figure src="/images/kmean_silhouette_width_vs_num_clusters.png" title="Fig.2: Silhouette width by number of cluster in k-means">}}
<br/>

* __Visualize clusters in lower dimensions__: In order to see how well-separated clusters that PAM or k-means is able to detect, I employ t-distributed stochastic neighbor embedding (t-SNE) to visualize clusters in 2D. The technique tries to reduce the dimensionality of inputs, but still preserve its local structure. For further details, see the [t-SNE software] (https://lvdmaaten.github.io/tsne/) developed by Laurens van der Maaten. <br/>

{{<figure src="/images/pam_tsne_5clusters.png" title="Fig.3: t-SNE">}}
The plot shows that some clusters are unstable or not very well-separated from other clusters. To probe further, I employ several resampling schemes(bootstrap, subsetting, jittering, replacement of points by noise) to assess the stability of these clusters. These methods are implemented in [`clusterboot`](https://www.rdocumentation.org/packages/fpc/versions/2.1-11/topics/clusterboot) [in package fpc] by Christian Hennig.

```R
* Cluster stability assessment *
Cluster method:  clara/pam 
Full clustering results are given as parameter result
of the clusterboot object, which also provides further statistics
of the resampling results.
Number of resampling runs:  100 

Number of clusters found in data:  5 

 Clusterwise Jaccard bootstrap (omitting multiple points) mean:
[1] 0.5212346 0.4841230 0.7535759 0.6628636 0.6756281
dissolved:
[1] 53 65  4 27 23
recovered:
[1] 21 25 55 42 38
 Clusterwise Jaccard subsetting mean:
[1] 0.4776582 0.4270802 0.7189634 0.5886231 0.6485604
dissolved:
[1] 57 74  6 41 23
recovered:
[1] 13 15 40 25 31
 Clusterwise Jaccard replacement by noise mean:
[1] 0.8392858 0.9193778 0.9520704 0.9101964 0.9417290
dissolved:
[1] 8 6 1 7 1
recovered:
[1] 68 90 96 92 98
 Clusterwise Jaccard jittering mean:
[1] 1 1 1 1 1
dissolved:
[1] 0 0 0 0 0
recovered:
[1] 100 100 100 100 100

```
The mean value of Jaccard coefficient over all the bootstrap iterations suggests that two clusters are considered unstable and could be dissolved into others. Some clusters captures a certain pattern in the data. <br/>

* __Cluster Interpretation__: From the results of the above stability assessment, I decide to run the PAM algorithm with three clusters. After that I run summary on each cluster to interpret the clusters. For example, it seems that Cluster 1 is mainly the people who is suffering chronic and serious conditions like high cholesterol and diabetes they want treat or prevent. They tend to be older, and often feel fatigue / lack of energy. Similar fashion can be applied to other profiling questions to infer the cluster description. 

```
[[1]]
   age_break      COND_chronic_disease  PREV_chronic_disease
 Min.   :0.0000   Min.   :0.0000         Min.   :0.0000        
 1st Qu.:0.0000   1st Qu.:0.0000         1st Qu.:1.0000        
 Median :0.0000   Median :1.0000         Median :1.0000        
 Mean   :0.4742   Mean   :0.6761         Mean   :0.7629        
 3rd Qu.:1.0000   3rd Qu.:1.0000         3rd Qu.:1.0000        
 Max.   :1.0000   Max.   :1.0000         Max.   :1.0000        

omit...
```
###### 3. Future work
* Applying divisive hierarchical clustering
* Employing two-step clustering when dealing with larger samples
* Prototyping a lifestyle scoring tool to predict a class / segment for a new consumer



<!--    2.6. Assess the cluster stability <br/>
   &nbsp;&nbsp;&nbsp; I employed clusterboot algorithm, which uses the Jaccard coefficient to measure the similarity between sets generated over different bootstrap samples. The cluster stability of each cluster in the original clustering is the mean value of its Jaccard coefficient over all the bootstrap iterations. As a rule of thumb, clusters with a stability value less than 0.6 should be considered unstable. Values between 0.6 and 0.75 indicate that the cluster is measuring a pattern in the data, but there isn’t high certainty about which points should be clustered together. Clusters with stability values above about 0.85 can be considered highly stable (they’re likely to be real clusters). --> 