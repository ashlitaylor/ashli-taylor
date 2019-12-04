---
title: Random Forest Classifier on the QSAR Biodegradation Data Set
subtitle: Python
date: 2019-03-24
thumb_img_path: images/biodegradation.jpg
content_img_path: ''
excerpt: Implementing a random forest classifier from scratch in Python. The performance
  of the classifier is evaluated via an out-of-bag (OOB) error estimate
layout: post
github: <a href="https://github.com/ashlitaylor/Biodegradation"  target="_blank" class="btn
  btn-primary">GitHub Repository</a>
canonical_url: ''

---
<style>
  .indented{
    padding-left:75pt;
    padding-right:75pt;
  }
</style>

<header> <h4 align="center"> Random Forest Classification on the QSAR Biodegradation Data Set </h4> <h6 align="center"> Bootstrapping,OOB Error estimates, Entropy Gain </h6> </header>

<header>
<h3> <u><br>Background</u> </h3>
</header>

The Quantitative Structure Activity Relationships (QSAR) Bidegradation [dataset](https://snap.stanford.edu/data/soc-sign-bitcoin-otc.html) was collected to facilitate the study of the relationships between chemical structure and biodegradation of molecules. Models based on QSAR provide promise a relatively inexpensive means of determining the biodegradability of chemicals. The desire for this kind of testing stems from a desire around the world to reduce the amount of non-biodegradable matrials as one method of improving sustainability.

The dataset has 42 columns: the first 41 columns represent the attributes of the molecule, and the last column is the ground truth label which tells us if the molecule is biodegradable or not (1 means biodegradable, 0 means not biodegradable). The dataset has been cleaned to remove missing attributes, and is comprised of 1055 instances.

<header>
<h5> <u><br>OOB Error Estimate</u> </h5>
</header>

This python code builds a random forest from scratch and performs classification on the QSAR dataset. Instead of cross validation, I used  the Out-of-bag (OOB) error estimate to evaluate the model performance. Each tree in the forest is constructed using a different bootstrap sample from the original data. Each bootstrap sample is constructed by randomly sampling from the original dataset with replacement. The proportion of times that a predicted class is not equal to the true class of a record averaged over all records is the OOB error estimate.

Bootstrapping allows confidence intervals to be placed on the model predictions based on uncertainties in the input data. Being able to obtain the uncertainty level of individual model predictions for chemical biodegradability is desireable as public and private entities pursue regulatory and chemical design goals.

This code is comprised of three files: 
1. A util.py code file that contains utility functions to build the decision tree. Implements the functions to compute entropy, information gain, and perform splitting.
2. A decision_tree.py code file that contains a decision tree class to build the tree. Implements the classify() method to predict the label of a test record and the learn() method to build the decision tree using the utility functions above.
3. A random_forest.py code file that contains a random forest class and a main method to test the random forest. Implements the functions _bootstrapping(), fitting(), voting().

<header>
<h3> <u><br>The Code</u> </h3>
</header>
The code is comprised of the following three files:
1. A util.py code file
2. A decision_tree.py code file
3. A random_forest.py code file

##### Util.py

The util.py code file contains utility functions to build the decision tree. It implements the functions to compute entropy, information gain, and perform splitting. I imported three packages for this code file:

1. Scipy stats - Statistical functions from Scipy that ___
2. Numpy - Multi-dimensional container
3. Math - for math

```
from scipy import stats
import numpy as np
import math
```
The first method computes entropy for information gain. Entropy is defined as __ and is calculated by

For example the ex=ntropy of the following set is:::::::
entropy([0,0,0,1,1,1,1,1,1]) = 0.92

```
def entropy(class_y):
    #* Input:            
    #*   class_y         : list of class labels (0's and 1's)
    
    #Compute the entropy for a list of classes
    #*        
    zeroCount = 0
    oneCount = 0
    #length_y = len(class_y)
    
    if len(class_y) == 0:
        return 0
        
    zeroCount = class_y.count(0)
    oneCount = class_y.count(1)
   
    prob_zero = (1.0*zeroCount)/len(class_y)
    prob_one = (1.0*oneCount)/len(class_y)
    #Entropy: H = -sum(-prob(i)*log_2(prob(i)))
    if prob_zero == 0:
        entropy = (-1.0*prob_one*math.log(prob_one,2))
    elif prob_one == 0:
        entropy = (-1.0*prob_zero*math.log(prob_zero,2))
    else:
        entropy = (-1.0*prob_zero*math.log(prob_zero,2)) + (-1*prob_one*math.log(prob_one,2)) 
    return entropy
```

The second function partitions the classes and takes three values:
* Data containing all attributes
* Labels
* Split attribute
* Split val

Inputs:
    #*   X               : data containing all attributes
    #*   y               : labels
    #*   split_attribute : column index of the attribute to split on
    #*   split_val       : either a numerical or categorical value to divide the split_attribute
    
TODO: Partition the data(X) and labels(y) based on the split value - BINARY SPLIT.

First: check if the split attribute is numerical or categorical    
    #* If the split attribute is numeric, split_val should be a numerical value
    #* If the split attribute is categorical, split_val should be one of the categories.   
    #*
    #* Numeric Split Attribute:
    #*   Split the data X into two lists(X_left and X_right) where the first list has all
    #*   the rows where the split attribute is less than or equal to the split value, and the 
    #*   second list has all the rows where the split attribute is greater than the split 
    #*   value. Also create two lists(y_left and y_right) with the corresponding y labels.
    #*
    #* Categorical Split Attribute:
    #*   Split the data X into two lists(X_left and X_right) where the first list has all 
    #*   the rows where the split attribute is equal to the split value, and the second list
    #*   has all the rows where the split attribute is not equal to the split value.
    #*   Also create two lists(y_left and y_right) with the corresponding y labels.
    Example:
  <div><p class = "indented"> 
    X = [[3, 'aa', 10],                 y = [1,
         [1, 'bb', 22],                      1,
         [2, 'cc', 28],                      0,
         [5, 'bb', 32],                      0,
         [4, 'cc', 32]]                      1]
    
    Here, columns 0 and 2 represent numeric attributes, while column 1 is a categorical attribute.
    
    Consider the case where we call the function with split_attribute = 0 and split_val = 3 (mean of column 0)
    Then we divide X into two lists - X_left, where column 0 is &lt; 3  and X_right, where column 0 is &gt; 3.
    
    X_left = [[3, 'aa', 10],                 y_left = [1,
              [1, 'bb', 22],                           1,
              [2, 'cc', 28]]                           0]
              
    X_right = [[5, 'bb', 32],                y_right = [0,
               [4, 'cc', 32]]                           1]
    Consider another case where we call the function with split_attribute = 1 and split_val = 'bb'
    Then we divide X into two lists, one where column 1 is 'bb', and the other where it is not 'bb'.
        
    X_left = [[1, 'bb', 22],                 y_left = [1,
              [5, 'bb', 32]]                           0]
              
    X_right = [[3, 'aa', 10],                y_right = [1,
               [2, 'cc', 28],                           0,
               [4, 'cc', 32]]                           1]
               
  
  </p></div>
  
The code is below:
```
def partition_classes(X, y, split_attribute, split_val):
    
    X_left = []
    X_right = []
    
    y_left = []
    y_right = []

    if isinstance(X[0][split_attribute], int):
        for index, i in enumerate(X):
            if i[split_attribute] <= split_val:
                X_left.append(i)
                y_left.append(y[index])
            else:
                X_right.append(i)
                y_right.append(y[index])  
    else:
        for index, i in enumerate(X):
            if i[split_attribute] == split_val:
                X_left.append(i)
                y_left.append(y[index])
            else:
                X_right.append(i)
                y_right.append(y[index])            
    #print(X_left)
    #print(y_left)
    #print(X_right)
    #print(y_right)
    return (X_left, X_right, y_left, y_right)
```

#* Inputs:
    #*   previous_y: the distribution of original labels (0's and 1's)
    #*   current_y:  the distribution of labels after splitting based on a particular
    #*               split attribute and split value
    #* Compute and return the information gain from partitioning the previous_y labels
    #* into the current_y labels.
    #* Information gain calculated using entropy function.
    #* Reference: http://www.cs.cmu.edu/afs/cs.cmu.edu/academic/class/15381-s06/www/DTs.pdf
    
Example:
<div><p class = "indented">
    previous_y = [0,0,0,1,1,1]
    current_y = [[0,0], [1,1,1,0]]
    
    info_gain = 0.45915 
</p></div>
    
``` 
def information_gain(previous_y, current_y):
    #Information Gain: IG = H - ((H_L*P_L) +(H_R*P_R))
    info_gain = 0
    current_H = 0.0
    previous_H = 1.0*entropy(previous_y)
    leny = len(previous_y)
    for m in current_y:
        lenm = len(m)
        if lenm > 0:
            current_H += (entropy(m) * ((1.0* lenm)/leny)) 
    info_gain += round((previous_H - current_H),5)
    return info_gain
```

2. A decision_tree.py code file that contains a decision tree class to build the tree. Implements the classify() method to predict the label of a test record and the learn() method to build the decision tree using the utility functions above.
3. A random_forest.py code file that contains a random forest class and a main method to test the random forest. Implements the functions _bootstrapping(), fitting(), voting().

The Random Forest uses entropy gain to select the splitting attribute and split point for the selected attribute and achieves 80% accuracy on the QSAR dataset.

