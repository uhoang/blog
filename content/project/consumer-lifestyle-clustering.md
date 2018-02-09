---
title: "Consumer Lifestyle Clustering"
date: 2017-10-10T16:07:03-05:00
tags: ['clustering', 'R']
---

Recently, I've been offered to build a consumer lifestyle segmentation tool for the analytics and insight team at Moutain Equipment Co-op. The main purpose of this project is to replicate a typing tool, that has previously been developed for US consumers by McKinsey's data team. This tool is leveraged to design a scalable, customer-centric marketing solutions. The analysis for this tool is written in R. For futher details, you can visit [my Github repository] (https://github.com/uhoang/mec_project/).

### Objectives: 
- Build health lifestyle segmentation for Canadian consumers using an online survey of 2000 responses arranged by a panel provider.
- Identify clusters and intepret its meaning via descriptive statistics
- Perform pairwise comparisions among group means on profiling questions
- Future work -- prototyping a lifestyle scoring tool to predict a class / segment for a new consumer

### Methods
In this section, I will describe the clustering approach, its rationales and main findings.

###### 1. Data
  - The data contains a series of multi-select questions on health conditions, that respondents are currently suffer from or are concerned about preventing in the future. The answers are captured by a set of binary variables. Each represents one category in the question, in which the value 1 means the category is selected, and the value 0 means otherwise. 
  - It aslo includes additional profiling questions such as gender, age, frequency of participation in some activities, time recently spending on certain products, rate personal lifestyle goals and statements.

###### 2. Analysis:

* __Filter data__: I remove any low-quality observation, in which a respondent speeds through the survey by giving low-effort responses, and engages in variety of other behaviors that negatively impact response quality.

* __Transform inputs__: I regroup some health/prevention conditions into category to reduce the complexity of the inputs. This step helps to produce a cleaner interpretation of clusters in a later step. Age is dichotomized into two groups, below 58 and 58+. This threshold is based on the previous study. But other option could be explored. This coding makes all variables comparable. 
   
* __Select a similarity measure__: In order to group observations together, I first need to define some notion of similarity between observations. Since the data contains asymmetric binary variables, I have to use a distance metric that can handle this data type. In this case, I use a metric called Gower distance. The distance is always a number between 0 (identical) and 1 (maximally dissimilar). For each binary variable, Gower distance uses one minus Jaccard's Similarity Coefficient to calculate the distance. Then, the final distance is wobtained as a weighted sum of distances for each variable. 
  
* __Select a clustering algorithm__: <br/>
  + I run both Partitioning around Medoids (PAM) and K-means algorithms for clustering. Both algorithms have quadratic run time and memory (i.e: $O(n^2)$). However, it is known that PAM is more robust to noise and outliers when compared to k-means, and it has the added benefit of having an observation serve as the exemplar for each cluster. 
  + Explain how Partitioning around medoids (PAM) works
      * Choose k random entities to become the medoids
      * Assign every entity to its closest medoid (usign our custom distance matrix in this case)
      * For each cluster, identify the observation that woud yield the lowest average distance if it were to be re-assigned as the medoid. if so, make this observation the new medoid
      * If at least one medoid has changed, return to the second step. Otherwise, end the algorithm
      
* __Select the number of clusters__: A variety of metrics exist to help choose the number of clusters. In this analysis, I use Silhouette width as a validation metric. It is an aggregated measure of how similar an observation is to its own cluster compared its closest neighboring cluster. The metric can range from -1 to 1, where higher values are better. I then plot silhouette width for clusters ranging from 2 to 10 for both algorithms. From the Figure 1, we see that the PAM algorithm with 5 clusters yields the highest Silhouette value. On the other hand, K-Means with 2 clusters gives the highest Silhouette score. <br/> 

{{<figure src="/images/pam_silhouette_width_vs_num_clusters.png" title="Fig.1: Silhouette width by number of cluster in PAM">}}
<br/> 
{{<figure src="/images/kmean_silhouette_width_vs_num_clusters.png" title="Fig.2: Silhouette width by number of cluster in K-Means">}}
<br/>

* __Visualize clusters in lower dimensional space__ <br/>
   &nbsp;&nbsp;&nbsp; Use t-distributed stochastic neighborhood embedding method to preserve local structure of the data such as to make clusters visible in a 2D or 3D visualization. 

   2.6. Assess the cluster stability <br/>
   &nbsp;&nbsp;&nbsp; I employed clusterboot algorithm, which uses the Jaccard coefficient to measure the similarity between sets generated over different bootstrap samples. The cluster stability of each cluster in the original clustering is the mean value of its Jaccard coefficient over all the bootstrap iterations. As a rule of thumb, clusters with a stability value less than 0.6 should be considered unstable. Values between 0.6 and 0.75 indicate that the cluster is measuring a pattern in the data, but there isn’t high certainty about which points should be clustered together. Clusters with stability values above about 0.85 can be considered highly stable (they’re likely to be real clusters).