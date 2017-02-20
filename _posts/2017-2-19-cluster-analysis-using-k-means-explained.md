---
layout: post
title: Cluster Analysis Using K-means Explained
comments: True
excerpt_separator: <!--more-->
---

Clustering or [cluster analysis](https://en.wikipedia.org/wiki/Cluster_analysis) *divides data into groups (clusters) in such a way that objects in the same cluster are more similar to each other than those in other clusters*. It is used in data mining, machine learning, pattern recognition, data compression and in many other fields. In machine learning, it is often a **starting point**. In a machine learning application I built couple of years ago, we used clustering to divide six million prepaid subscribers into five clusters and then built a model for each cluster using [linear regression](https://en.wikipedia.org/wiki/Linear_regression). The goal of the application was to predict future recharges by subscribers so operators can make intelligent decisions like whether to grant or deny emergency credit. Another (trivial) application of clustering is for dividing customers into groups based on spending habits or brand loyalty for further analysis or to determine the best promotional strategy.

<!--more-->

There are various models and techniques for cluster analysis. When we were building the machine learning application, I was *mistakenly searching for 'the best clustering model or technique.'* I wasn't aware that there is no universal best algorithm and the choice depends on your requirements and the dataset. There are [density-based](https://en.wikipedia.org/wiki/Cluster_analysis#Density-based_clustering), [graph based](https://en.wikipedia.org/wiki/HCS_clustering_algorithm) or [centroid based](https://en.wikipedia.org/wiki/Cluster_analysis#Centroid-based_clustering) clustering models. We finally settled on a clustering technique called k-means. This blog post is a brain-dump of everything I've learned about clustering and k-means so far.

# K-means

K-means is a very simple and widely used clustering technique. It divides a dataset into '*k*' clusters. The '*k*' must be supplied by the users, hence the name k-means. It is general purpose and the [algorithm is straigh-forward](http://johnloeber.com/docs/kmeans.html):

> We call the process k-means clustering because we assume that there are **k** clusters, and each cluster is defined by its center point — its mean. To find these clusters, we use **Lloyd’s Algorithm**: we start out with **k** random centroids. A centroid is simply a datapoint around which we form a cluster. For each centroid, we find the datapoints that are closer to that centroid than to any other centroid. We call that set of datapoints its cluster. Then we take the mean of the cluster, and let that be the new centroid. We repeat this process (using the new centroids to form clusters, etc.) until the algorithm stops moving the centroids.[0] We do this in order to minimize the total sum of distances from every centroid to the points in its cluster — *that is our metric for how well the clusters split up the data*.

Here's an [animation](http://simplystatistics.org/2014/02/18/k-means-clustering-in-a-gif/) showing how it works.

![kmeans-animated]({{ site.url }}/img/kmeans/kmeans-animated.gif)

As far as its performance is concerned, k-means and its [variants can usually process large datasets very quickly](https://upcommons.upc.edu/bitstream/handle/2117/23414/R13-8.pdf), as long as the number of clusters isn't very high.

## Finding 'k': number of clusters using the elbow method

If you know the number of clusters before hand, you have everything you need to apply k-means. However, in practice, it's rare that the number of clusters in the dataset is known. When I was modeling, I wasn't sure if there are 3 clusters or 13 in the dataset. Luckily, there's a technique called the '[elbow method](https://en.wikipedia.org/wiki/Determining_the_number_of_clusters_in_a_data_set)' that you can use to determine the number of clusters:

> One should choose a number of clusters so that adding another cluster doesn't give much better modeling of the data. More precisely, if one plots the percentage of variance explained by the clusters against the number of clusters, the first clusters will add much information (explain a lot of variance), but at some point the marginal gain will drop, giving an angle in the graph. The number of clusters is chosen at this point, hence the "elbow criterion".

![kmeans-elbow]({{ site.url }}/img/kmeans/kmeans-elbow.JPG)

Silhouette analysis is another popular technique for [finding the optimum number of clusters visually](http://scikit-learn.org/stable/auto_examples/cluster/plot_kmeans_silhouette_analysis.html), and the one we actually used in our application.

## K-means limitations and weaknesses

Unfortunately, k-means has limitations and doesn't work well with different types of clusters. It doesn't do well when:

1. the clusters are of unequal size or density.
2. the clusters are non-spherical.
3. there are outliers in the data.

I have read that the dataset must have well-separated clusters in order for k-means to properly work. However, in practice, I have experienced k-means doing a pretty good job of diving data up into clusters, even when the clusters are not well separated or obvious. I will keep it off the list.

### 1. The clusters are of unequal size or density

K-means won't identify clusters properly if clusters have an uneven size or density. To illustrate the point, I generated a 2d dataset.

![uneven-unlabelled]({{ site.url }}/img/kmeans/uneven-unlabelled.png)

I see 3 clusters of uneven densities. Do you?

![uneven-labelled-manual]({{ site.url }}/img/kmeans/uneven-labelled-manual.png)

Let's see how k-means does. I wrote a simple python script with scikit-learn and ran k-means with *k=3*.

![uneven-applied]({{ site.url }}/img/kmeans/uneven-applied.png)

As it was expected, k-means messed up and couldn't find naturally occurring clusters that we recognized in the dataset and expected k-means to find. It identified the larger cluster correctly but mixed the smaller ones. If you have datasets where naturally occurring clusters have unequal densities, try look into [density based models](https://en.wikipedia.org/wiki/Cluster_analysis#Density-based_clustering).

### 2. The clusters are non-spherical

Let's generate a 2d dataset with non-spherical clusters.

![kmeans-spherical-unlabelled]({{ site.url }}/img/kmeans/kmeans-spherical-unlabelled.png)

It's how you look at it, but I see two clusters in the dataset. Let's run k-means and see how it performs.

![kmeans-spherical-applied]({{ site.url }}/img/kmeans/kmeans-spherical-applied.png)

It's obvious: k-means couldn't correctly identify the clusters. If you have a similar dataset, try a hierarchical or density based algorithm like [spectral clustering](https://en.wikipedia.org/wiki/Spectral_clustering) or [DBSCAN](https://en.wikipedia.org/wiki/DBSCAN) that are better suited.

### 3. There are outliers in the data.

If outliers are present in the dataset, they can influence clusterings results and change the outcome. The dataset should be pre-processed before applying k-means to detect and remove any outliers. There are many techniques for [outlier detection](http://www.pmg.it.usyd.edu.au/outliers.pdf) [and](https://pdfs.semanticscholar.org/49f3/d110f87ae245127d2e30049628785e95d23e.pdf) [removal](http://stackoverflow.com/questions/13989419/removing-outliers-from-a-k-mean-cluster). Unfortunately, discussing these techniques is outside the scope of this post and my knowledge.

## Evaluating k-means results

Let's assume you have just applied k-means to a dataset. **How do you tell if k-means did its job right and identified all clusters correctly?** *If* your dataset is 2-dimensional and isn't very large, you could probably plot the results and visually inspect to assess k-means, just like how I've been showing results so far. But what if your dataset is high-dimensional, as is often the case? We are trapped and can't visualize data beyond 3-dimensions. So we need some way to evaluate result of k-means and there are a couple that I know of:

1. Supervised evaluation
2. Unsupervised evaluation

### 1. Supervised evaluation

Supervised evaluation can be used to check the results of k-means when you have a pre-classified dataset available that can act as a benchmark. Typically, an expert would carefully process a small dataset assigning datapoints to clusters manually. This is the **gold standard** for evaluation and tells us how close the results are to the benchmark. Keep in mind that in order to use supervised evaluation, you'd need to be able to train k-means and then supply the pre-classified benchmarks to check expectations against actual results (something not possible if you are using a tool like Weka or Tableau). This page describes how to use supervised evaluation in [scikit-learn](http://scikit-learn.org/stable/modules/clustering.html#homogeneity-completeness-and-v-measure), a popular python machine learning library.

### 2. Unsupervised evaluation

Unsupervised evaluation doesn't rely on external information. In our app, we used inter-cluster (*within* cluster sum of squares) and intra-cluster (*between* cluster sum of squares) variances to decide if the results were good enough. These are just fancy terms to describe **cohesion** of points that are within a cluster and **separation** between clusters. **We want good cohesive clusters that are well separated from others**.

![cohesion-separartion]({{ site.url }}/img/kmeans/cohesion-separartion.jpg)

For more information on, this [wikipedia article](https://en.wikipedia.org/wiki/Cluster_analysis#Evaluation_and_assessment) has more information and describes several algorithms.

That's everything I have learned about k-means and clustering in general. If you have any comments or want to suggest improvements to this post, please leave them in the comments section below.
