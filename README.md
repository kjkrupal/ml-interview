# Machine learning interview

This repository covers how to prepare for machine learning interviews, mainly
in the format of questions & answers. Asides from machine learning knowledge,
other crucial aspects include:

* [Explain your resume](#explain-your-resume)
* [SQL](#sql)

Go directly to [machine learning](#machine-learning)


## Explain your resume

Your resume should specify interesting ML projects you got involved in the past,
and **quantitatively** show your contribution. Consider the following comparison:

> Trained a machine learning system

vs.

> Trained a deep vision system (SqueezeNet) that has 1/30 model size, 1/3 training
> time, 1/5 inference time, and 2x faster convergence compared with traditional
> ConvNet (e.g., ResNet)

We all can tell which one is gonna catch interviewer's eyeballs and better show
case your ability.

In the interview, be sure to explain what you've done well. Spend some time going
over your resume before the interview.


## SQL

Although you don't have to be a SQL expert for most machine learning positions,
the interviews might ask you some SQL related questions so it helps to refresh
your memory beforehand. Some good SQL resources are:

* [W3schools SQL](https://www.w3schools.com/sql/)
* [SQLZOO](http://sqlzoo.net/)


## Machine learning

First, it's always a good idea to review [Chapter 5](http://www.deeplearningbook.org/contents/ml.html) 
of the deep learning book, which covers machine learning basics.


* [Linear regression](#linear-regression)
* [Logistic regression](#logistic-regression)
* [KNN](#knn)
* [SVM]
* [Naive Bayes]
* [Decision tree](#decision-tree)
* [Bagging](#bagging)
* [Random forest](#random-forest)
* [Boosting](#boosting)
* [Generative vs discriminative](#generative-vs-discriminative)
* [Clustering]
* [MLP]
* [CNN]
* [RNN]
* [word2vec]



### Linear regression

* how to learn the parameter: minimize the cost function
* how to minimize cost function: gradient descent
* regularization: 
    - L1 (lasso): can shrink certain coef to zero, thus performing feature selection
    - L2 (ridge): shrink all coef with the same proportion; almost always outperforms L1
    - combined (Elastic Net): 
* assumes linear relationship between features and the label
* can add polynomial and interaction features to add non-linearity

![lr](http://scikit-learn.org/stable/_images/sphx_glr_plot_cv_predict_thumb.png)

[back to top](#machine-learning)


### Logistic regression

* Generalized linear model (GLM) for classification problems
* Apply the sigmoid function to the output of linear models, squeezing the target
to range [0, 1] 
* Threshold to make prediction: if the output > .5, prediction 1; otherwise prediction 0
* a special case of softmax function, which deals with multi-class problem

[back to top](#machine-learning)

### KNN

Given a data point, we compute the K nearest data points (neighbors) using certain
distance metric (e.g., Euclidean metric). For classification, we take the majority label
of neighbors; for regression, we take the mean of the label values.

Note for KNN technically we don't need to train a model, we simply compute during
inference time. This can be computationally expensive since each of the test example
need to be compared with every training example to see how close they are.

There are approximation methods can have faster inference time by
partitioning the training data into regions.

Note when K equals 1 or other small number the model is prone to overfitting (high variance), while
when K equals number of data points or other large number the model is prone to underfitting (high bias)

![KNN](https://cambridgecoding.files.wordpress.com/2016/03/training_data_only_99_1.png?w=610)

[back to top](#machine-learning)


### Decision tree

* Non-parametric, supervised learning algorithms
* Given the training data, a decision tree algorithm divides the feature space into
regions. For inference, we first see which
region does the test data point fall in, and take the mean label values (regression)
or the majority label value (classification).
* **Construction**: top-down, chooses a variable to split the data such that the 
target variables within each region are as homogeneous as possible. Two common
metrics: gini impurity or information gain, won't matter much in practice.
* Advantage: simply to understand & interpret, mirrors human decision making
* Disadvantage: 
    - can overfit easily (and generalize poorly)if we don't limit the depth of the tree
    - can be non-robust: A small change in the training data can lead to a totally different tree

![decision tree](http://www.fizyka.umk.pl/~wduch/ref/kdd-tut/d-tree-iris.gif)

[back to top](#machine-learning)


### Bagging

To address overfitting, we can use an ensemble method called bagging (bootstrap aggregating),
which reduces the variance of the meta learning algorithm. Bagging can be applied
to decision tree or other algorithms.

Here is a [great illustration](http://scikit-learn.org/stable/auto_examples/ensemble/plot_bias_variance.html#sphx-glr-auto-examples-ensemble-plot-bias-variance-py) of a single estimator vs. bagging

![bagging](http://scikit-learn.org/stable/_images/sphx_glr_plot_bias_variance_001.png)

[back to top](#machine-learning)


### Random forest

Random forest improves bagging further by adding some randomness. In random forest,
only a subset of features are selected at random to construct a tree. The benefit is
that random forest **decorrelates** the trees. 

For example, suppose we have a dataset. There is one very predicative feature, and a couple
of moderately predicative features. In bagging trees, most of the trees
will use this very predicative feature in the top split, and therefore making most of the trees
look similar, **and highly correlated**. Averaging many highly correlated results won't lead
to a large reduction in variance compared with uncorrelated results. 
In random forest for each split we only consider a subset of the features and therefore
reduce the variance even further by introducing more uncorrelated trees.

I wrote a [notebook](notebooks/bag-rf-var.ipynb) to illustrate this point.

In practice, tuning random forest entails having a large number of trees (the more the better, but
always consider computation constraint). Also, `min_samples_leaf` (The minimum number of
samples at the leaf node)to control the tree size and overfitting. Always CV the parameters. 


[back to top](#machine-learning)


### Boosting

**How it works**

Boosting builds on weak learners, and in an iterative fashion. In each iteration,
a new learner is added, while all existing learners are kept unchanged. All learners
are weighted based on their performance (e.g., accuracy), and after a weak learner
is added, the data are re-weighted: examples that are misclassified gain more weights,
while examples that are correctly classified lose weights. Thus, future weak learners
focus more on examples that previous weak learners misclassified.


**Difference from random forest (RF)**

* RF grows trees **in parallel**, while Boosting is sequential
* RF reduces variance, while Boosting reduces errors by reducing bias


**XGBoost (Extreme Gradient Boosting)**


> XGBoost uses a more regularized model formalization to control overfitting, which gives it better performance


[back to top](#machine-learning)


### Generative vs discriminative

* Discriminative algorithms model *p(y|x; w)*, that is, given the dataset and learned
parameter, what is the probability of y belonging to a specific class. A discriminative algorithm
doesn't care about how the data was generated, it simply categorizes a given example
* Generative algorithms try to model *p(x|y)*, that is, the distribution of features given
that it belongs to a certain class. A generative algorithm models how the data was
generated.

> Given a training set, an algorithm like logistic regression or
> the perceptron algorithm (basically) tries to find a straight line—that is, a
> decision boundary—that separates the elephants and dogs. Then, to classify
> a new animal as either an elephant or a dog, it checks on which side of the
> decision boundary it falls, and makes its prediction accordingly.

> Here’s a different approach. First, looking at elephants, we can build a
> model of what elephants look like. Then, looking at dogs, we can build a
> separate model of what dogs look like. Finally, to classify a new animal, we
> can match the new animal against the elephant model, and match it against
> the dog model, to see whether the new animal looks more like the elephants
> or more like the dogs we had seen in the training set.

[back to top](#machine-learning)
