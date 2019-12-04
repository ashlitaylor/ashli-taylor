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
1. A util.py code file that implements the functions to compute entropy, information gain, and perform splitting.
2. A decision_tree.py code file that implements the classify() method to predict the label of a test record and the learn() method to build the decision tree using the utility functions above.
3. A random_forest.py code file that implements the functions _bootstrapping(), fitting(), voting().

<header>
<h3> <u><br>The Code</u> </h3>
</header>

##### Util.py

The util.py code file contains utility functions to build the decision tree. It implements the functions to compute entropy, information gain, and perform splitting. I imported three packages for this code file:
* Scipy stats - Statistical functions from Scipy that ___
* Numpy - Multi-dimensional container from [Numpy](http://www.numpy.org)
* Math - for math

```
from scipy import stats
import numpy as np 
import math
```
The first function computes entropy for information gain. Entropy is a measure of the probability and disorder of a system. For example, a bag that contains four red balls would have an entropy value of zero (0) bits whereas a bag that contains two red balls and two blue balls would have one (1) bit of entropy. 

For example the entropy of the following set is
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

The second function partitions the data(X) and labels(y) based on the split value - BINARY SPLIT. It takes the following inputs:
* X: data containing all attributes
* y: labels
* split_attribute : column index of the attribute to split on
* split_val: either a numerical or categorical value to divide the split_attribute

First the code checks if the split attribute is numerical or categorical. If the split attribute is categorical, split_val should be one of the categories.   

<strong>Numeric Split Attribute: </strong>If the split attribute is numeric, split_val should be a numerical value. I split the X data into two lists(X_left and X_right) where the first list has all the rows where the split attribute is less than or equal to the split value, and the second list has all the rows where the split attribute is greater than the split value. I also create two lists(y_left and y_right) with the corresponding y labels.
<br/>
<strong>Categorical Split Attribute: </strong>I split the data X into two lists(X_left and X_right) where the first list has all the rows where the split attribute is equal to the split value, and the second list has all the rows where the split attribute is not equal to the split value. I also create two lists(y_left and y_right) with the corresponding y labels.

Example: Here, columns 0 and 2 represent numeric attributes, while column 1 is a categorical attribute.
```
    X = [[3, 'aa', 10],                 y = [1,
         [1, 'bb', 22],                      1,
         [2, 'cc', 28],                      0,
         [5, 'bb', 32],                      0,
         [4, 'cc', 32]]                      1]
``` 

Consider the case where the function is called with split_attribute = 0 and split_val = 3 (mean of column 0). Then the code divides X into two lists - X_left, where column 0 is &lt; 3  and X_right, where column 0 is &gt; 3.

``` 
    X_left = [[3, 'aa', 10],                 y_left = [1,
              [1, 'bb', 22],                           1,
              [2, 'cc', 28]]                           0]
              
    X_right = [[5, 'bb', 32],                y_right = [0,
               [4, 'cc', 32]]                           1]
```
Consider another case where the function is called with split_attribute = 1 and split_val = 'bb'. Then the code divides X into two lists, one where column 1 is 'bb', and the other where it is not 'bb'.
```        
    X_left = [[1, 'bb', 22],                 y_left = [1,
              [5, 'bb', 32]]                           0]
              
    X_right = [[3, 'aa', 10],                y_right = [1,
               [2, 'cc', 28],                           0,
               [4, 'cc', 32]]                           1]
```
  
The code implementation is below:
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
This next section of code calculates the information gain using the entropy function. It computes and returns the information gain from partitioning the previous_y labels into the current_y labels.
[Reference](http://www.cs.cmu.edu/afs/cs.cmu.edu/academic/class/15381-s06/www/DTs.pdf). It takes the following inputs:
* previous_y: the distribution of original labels (0's and 1's)
* current_y: the distribution of labels after splitting based on a particular split attribute and split value
    
Example:
```
    previous_y = [0,0,0,1,1,1]
    current_y = [[0,0], [1,1,1,0]]
    
    info_gain = 0.45915 
```
The code is implemented below.   
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
##### Decision_tree.py 
This code file contains a decision tree class to build the tree. It implements the classify() method to predict the label of a test record and the learn() method to build the decision tree using the utility functions above. I imported three functions for this code:
* entropy - The entropy function from the util.py code file. 
* information_gain - The information gain function from the util.py code file.
* partition_classes - The partition class function from the util.py code file.
* numpy
* ast
```
from util import entropy, information_gain, partition_classes
import numpy as np 
import ast
```
First, I initialize the tree as an empty dictionary.

```
class DecisionTree(object):
    def __init__(self):
        self.tree = {}
        pass
```
The next function trains the decision tree (self.tree) using the sample X and y labels using the functions from utils.py. Each node in self.tree is in the form of a [dictionary](https://docs.python.org/2/library/stdtypes.html#mapping-types-dict). For example, a non-leaf node with two children can have a 'left' key and  a 'right' key.  
```
    def learn(self, X, y):    
. 
        leny = len(set(y))
        #print(leny)
        if leny == 1:  
            self.tree['label'] = y[0]
            return
        if leny == 0:
            self.tree['label'] = 0
            return        
        
        x_left = []
        x_right = []
        y_left = []
        y_right = []

        maxInfoGain = 0
        maxIndex = 0
        best_split_attr = 0
        split_val = 0
        
        terminate_case = 0, 0.0, 0

        for i in range(len(X)-4):
        
                for j in range(len(X[0])):
                    split_val_update = X[i][j]
                    x_l, x_r, y_l, y_r = partition_classes(X,y,j,split_val_update)
                    y_update = [y_l, y_r]
                    infoGain = information_gain(y, y_update)
                    if infoGain > maxInfoGain: 
             
                            maxInfoGain = infoGain
                            split_val = split_val_update
                            best_split_attr = j
                            testing = i, maxInfoGain, best_split_attr

                            x_left = x_l
                            x_right = x_r
                            y_left = y_l
                            y_right = y_r

        
        self.tree['left'] = DecisionTree()
        self.tree['right'] = DecisionTree()
        self.tree['left'].learn(x_left,y_left)
        self.tree['right'].learn(x_right, y_right)
        self.tree['attribute'] = best_split_attr
        self.tree['value'] = split_val

        pass
```
The next function classifies the record using self.tree and returns the predicted label. 
```
    def classify(self, record):
        root = self.tree
        
        while 'value' in root:
            split_attribute = root['attribute']
            split_val = root['value']

            if isinstance(record[split_attribute], int):
                if record[split_attribute] <= split_val:
                    root = root['left'].tree
                else:
                    root = root['right'].tree
            else:
                if record[split_attribute] == split_val:
                    root = root['left'].tree
                else:
                    root = root['right'].tree

        return root['label']
        
        pass
```

##### Random_forest.py
This code file contains a random forest class and a main method to test the random forest. It implements the functions _bootstrapping(), fitting(), voting(). 

I imported the following functions:
* DecisionTree from the decision_tree.py file
* csv 
* numpy
* ast 
* time
```
from decision_tree import DecisionTree
import csv
import numpy as np  
import ast
#import time
```
Here:
1. X is assumed to be a matrix with n rows and d columns where n is the number of total records and d is the number of features of each record. 
2. y is assumed to be a vector of labels of length n.
3. XX is similar to X, except that XX also contains the data label for each record.
4. The bootstraps_datasets is a list of lists, where each list in bootstraps_datasets is a bootstrapped dataset.
5. The bootstraps_labels is the true class labels, corresponding to records in the bootstrapping datasets. It is a list of lists, where the 'i'th list contains the labels corresponding to records in the 'i'th bootstrapped dataset.

```
class RandomForest(object):
    num_trees = 0
    decision_trees = []

    bootstraps_datasets = []
 
    bootstraps_labels = []
```
The functions implemented are as follows: 
* __init__(self, num_trees): Initialization done here
* \_bootstrapping(self, XX, n): Creates a sample dataset of size n by sampling with replacement from the original dataset XX.
* bootstrapping(self, XX): This function initializes the bootstap datasets for each tree
* fitting(self): Trains `num_trees` decision trees using the bootstraps datasets and labels by calling the learn function from your DecisionTree class.
* voting(self, X): Performs the following steps-
		1. Find the set of trees that consider the record as an out-of-bag sample.
		2. Predict the label using each of the above found trees.
		3. Use majority vote to find the final label for this recod.
        
I initialized the implementation according to my implementation with a minimum forest size forest_size = 30. This value can be changed to any integer value greater than or equal to 10. 
```
    def __init__(self, num_trees):
        self.num_trees = num_trees
        self.decision_trees = [DecisionTree() for i in range(num_trees)]

    def _bootstrapping(self, XX, n):
        #* Reference: https://en.wikipedia.org/wiki/Bootstrapping_(statistics)

        numbers = np.random.randint(1, n)
        s = set()

        for i in range(numbers):
            cur = np.random.randint(0, n-1)
            s.add(cur)

        samples = [] # sampled dataset
        labels = []  # class labels for the sampled records

   for i in s:
            samples.append(XX[i][:-1])
            labels.append(XX[i][-1])
        return (samples, labels)

    def bootstrapping(self, XX):
        #* Initializing the bootstap datasets for each tree
        for i in range(self.num_trees):
            data_sample, data_label = self._bootstrapping(XX, len(XX))
            self.bootstraps_datasets.append(data_sample)
            self.bootstraps_labels.append(data_label)


    def fitting(self):        
        for index, i in enumerate(self.decision_trees):
            #print("building tree no: ", index + 1)
            i.learn(self.bootstraps_datasets[index], self.bootstraps_labels[index])

        pass      


    def voting(self, X):
        y = []

        for record in X:
            votes = []
            for i in range(len(self.bootstraps_datasets)):
                dataset = self.bootstraps_datasets[i]
                if record not in dataset:
                    OOB_tree = self.decision_trees[i]
                    effective_vote = OOB_tree.classify(record)
                    votes.append(effective_vote)


            counts = np.bincount(votes)
            
            if len(counts) == 0:
                #***Special case 
                #*  Handle the case where the record is not an out-of-bag sample
                #*  for any of the trees. 

                num_votes = []

                for i in self.decision_trees:
                    num_votes.append(i.classify(record))

                y = np.append(y, np.argmax(np.bincount(num_votes)))
                pass
            else:
                y = np.append(y, np.argmax(counts))

        return y

#* DO NOT change the main function apart from the forest_size parameter!
def main():
    X = list()
    y = list()
    XX = list()  #* Contains data features and data labels
    numerical_cols = numerical_cols=set([i for i in range(0,43)]) # indices of numeric attributes (columns)

    #* Loading data set
    print("reading QSAR Data")
    with open("qsardata.csv") as f:
        for line in csv.reader(f, delimiter=","):
            xline = []
            for i in range(len(line)):
                if i in numerical_cols:
                    xline.append(ast.literal_eval(line[i]))
                else:
                    xline.append(line[i])

            X.append(xline[:-1])
            y.append(xline[-1])
            XX.append(xline[:])

    #* TODO: Initialize according to my implementation
    #* Minimum forest_size should be 10
    forest_size = 30
    
    #* Initializing a random forest.
    randomForest = RandomForest(forest_size)

    #* Creating the bootstrapping datasets
    print("creating the bootstrap datasets")
    randomForest.bootstrapping(XX)

    #* Building trees in the forest
    print("fitting the forest")
    randomForest.fitting()

    #* Calculating an unbiased error estimation of the random forest
    #* based on out-of-bag (OOB) error estimate.
    y_predicted = randomForest.voting(X)

    #* Comparing predicted and true labels
    results = [prediction == truth for prediction, truth in zip(y_predicted, y)]

    #* Accuracy
    accuracy = float(results.count(True)) / float(len(results))

    print("accuracy: %.4f" % accuracy)
    print("OOB estimate: %.4f" % (1-accuracy))
    #print("end time: ", time.clock())

if __name__ == "__main__":
    main()
```

<header>
<h3> <u><br>Concluding Remarks</u> </h3>
</header>
The Random Forest I implemented uses entropy gain to select the splitting attribute and split point for the selected attribute and achieves 80% accuracy on the QSAR dataset.