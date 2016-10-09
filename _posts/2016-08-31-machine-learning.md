---
title: "Machine Learning Code Cheat Sheet"
description: "Machine Learning Code Cheat Sheet"
category: Programming
tags: [programming]
sidebar:
  nav: "new_side"
---
[source](https://www.analyticsvidhya.com/blog/2015/05/data-visualization-python/)

# Table of contents

* TOC
{:toc}


# Preparation

## split training data

### R
```R
library(caret)
# 
Train <- createDataPartition(df$target, p=0.8, list=FALSE)
training <- df[ Train, ]
testing <- df[ -Train, ]

```

### Python
```python
from sklearn.cross_validation import train_test_split
x_train,x_test, y_train, y_test = train_test_split(X, y, random_state = 1 )
```

(<a href="#top">Back to top</a>)
<hr>


## Linear models

### GLM
predictors may be continuous, categorical (need to be dummyfied, n-1 new variables for n-categories) or mixture.

R

```R
mod_fit <- train(target ~ AccountHierarchyComplexity + ContactInvolvementCount_Last12Months + ContactRolesWithPastActivities + CurrentStageActivitiesCount + 
                   IsOverDue,  data=training, method="glm", family="binomial")
# with all columns
df<-data.frame(y=rnorm(10),x1=rnorm(10),x2=rnorm(10))
lm(y~.,df)
```

Python

```python
from sklearn import datasets
-
from sklearn.linear_model import LinearRegression
-
lr.fit(boston.data, boston.target)
-
predictions = lr.predict(boston.data)
-
lr.coef_
zip(boston.feature_names, lr.coef_)
```


```python
import statsmodels.api as sm  # statistical models (including regression)
import statsmodels.formula.api as smf  # statistical models (including regression)
# read in data
df = pd.read_csv('studenmunds_restaurants.csv')
# specify regression model
my_model = str('sales ~ competition + population + income')
# fit the model to the data
my_model_fit = smf.ols(my_model, data = df).fit()

```

## Linear Regression (ridge)

## Logistic Regression

## Gradient Boosting


# Clustering

## Principal component analysis
R

```R
spine_pca = prcomp(spine[,1:12],scale. = T)
plot(spine_pca$x[,1], spine_pca$x[,2],col=spine$predicted_class,
     xlab="PC1",ylab="PC2",main="PCA of spine dataset")
```

## KMeans

### Prepare Data
```python
from sklearn.datasets import make_blobs
```

### Plot initial clusters
```python
import matplotlib.pyplot as plt
f, ax = plt.subplots(figsize=(7.5, 7.5))
```

### Run KMeans
```python
from sklearn.cluster import KMeans
kmean.cluster_centers_ # print centers
```

## KKmeans (minibatch for large data)


## Probabilistic clustering with Gaussian Mixture Models


# Classification

## Decision tree

```python
from sklearn import datasets

from sklearn.tree import DecisionTreeClassifier
dt = DecisionTreeClassifier()

preds = dt.predict(X)
```

## Random Forest (Regression)
R

```R
fit <- randomForest(as.factor(Survived) ~ Pclass + Sex + Age + SibSp + Parch + Fare +
                                            Embarked + Title + FamilySize + FamilyID2,
                      data=train, 
                      importance=TRUE, 
                      ntree=2000)
                      
# classification || regression: make sure that 'target' is of correct class via "class(target)" 
```

Python

```python
from sklearn import datasets

X, y = datasets.make_classification(1000)    #create the dataset with 1,000 samples

from sklearn.ensemble import RandomForestClassifier

print "Accuracy:\t", (y == rf.predict(X)).mean()
print "Total Correct:\t", (y == rf.predict(X)).sum()
```

### Feature importance
R

```R
(VI_F=importance(fit))
varImp(fit)
varImpPlot(fit,type=2)
```

Python

```python
rf = RandomForestClassifier(compute_importances=True)
```

(<a href="#top">Back to top</a>)
<hr>

### Tuning Random Forest



#### Confusing matrix
R

```R
resultframe <- data.frame(OutCome=testing$target, pred=testing$predictions_result)
rtab <- table(resultframe)
rtab

overall_accuracy <- sum(diag(rtab))/sum(rtab)
model_precision <- sum(rtab[1,1])/sum(rtab[,1])
model_recall <- sum(rtab[1,1])/sum(rtab[1,])
false_positive_rate <- sum(rtab[2,1])/sum(rtab[2,])

paste("overall_accuracy: ",overall_accuracy)
paste("model_precision: ",model_precision)
paste("model_recall: ",model_recall)
paste("false_positive_rate: ",false_positive_rate)
```

Python

```python
import numpy as np

from sklearn.metrics import confusion_matrix
       rf.fit(X[training], y[training])
```

Look at confusion matrix

```python
import pandas as pd
```

(<a href="#top">Back to top</a>)
<hr>


#### Number of Estimators: Iterating through 1 - 20

```python
n_estimator_params = range(1, 20)
                                         
accuracy = lambda x: np.trace(x) / np.sum(x, dtype=float)
```

(<a href="#top">Back to top</a>)
<hr>

## Support Vector Machine

```python
from sklearn import datasets
from sklearn.svm import SVC
```

(<a href="#top">Back to top</a>)
<hr>

## Decision Tree Classifier

```python
from sklearn import datasets
		n_informative=3)
```

(<a href="#top">Back to top</a>)
<hr>



```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.cross_validation import train_test_split
# Training a RF to get some metrics
X_train, X_val, y_train, y_val = train_test_split(train, train_outcome, test_size=0.1)
forest = RandomForestClassifier(n_estimators=250, n_jobs=2)
forest.fit(X_train, y_train)
y_pred_val = forest.predict(X_val)
from sklearn.metrics import classification_report, accuracy_score
print(classification_report(y_val, y_pred_val))
print(accuracy_score(y_val, y_pred_val))
# Training a RF with the complete training set
forest = RandomForestClassifier(n_estimators=500, n_jobs=2)
forest.fit(train, train_outcome)
y_pred = forest.predict_proba(test)
```

(<a href="#top">Back to top</a>)
<hr>



# Support Vector machine
```python
from sklearn.svm import SVC
model = SVC(kernel='linear')
model = model.fit(train_data[0:,2:], train_data[0:,0])
```

(<a href="#top">Back to top</a>)
<hr>