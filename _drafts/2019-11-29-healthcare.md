---
title: Quality of Patient Health Outcomes in America
subtitle: R, Python
date: 2019-11-29T06:00:00.000+00:00
thumb_img_path: "/images/Healthcare.jpg"
excerpt: 'An assortment of classification models that evaluates the future potential
  of predicting whether or not a person''s eyes are open or     closed using EEG sensor
  data. '
layout: post
content_img_path: ''
canonical_url: ''
github: <a href="https://github.com/ashlitaylor/EyeState"  target="_blank" class="btn
  btn-primary">GitHub Repository</a>

---
<a href="https://github.com/ashlitaylor/HealthCare" target="_blank" class="btn btn-primary">GitHub Repository</a>


### Questions to be answered:
These questions form the motivation behind the Random Forest model I created to identify the drivers behind mortality rates, readmission rates, and facility ratings:
* Can the above dependent variables of interest be adequately explained using only process measures?
* Are our proposed models that use both process and socioeconomic data statistically better than the models that use process alone?
* Out of all of the features we sourced, what are the five most important ones for each variable of interest?

<table>
    <tr>
        <th>Dependent Variable</th><th>Conventional Framework</th><th>Our Proposed Framework</th>
    </tr>
    <tr>
        <td>Mortality Rate</td><td>Process + Readmissions</td><td>Process + Readmissions + Socioeconomic</td>
    </tr>
    <tr>
        <td>Readmission Rate</td><td>Process</td><td>Process + Socioeconomic</td>
    </tr>
    <tr>
        <td>Facility Rating</td><td>Process</td><td>Process + Socioeconomic</td>
    </tr>
</table>

#### Recursive Feature Selection
Random forests perform well by ranking the features that best balance the bias-variance tradeoff. However, when correlated features are present (as with our Age, Gender, and Ethnicity data), equivalent importance can be assigned to the group, which can over- or underestimate the actual feature importance. This can be corrected by selecting features recursively. I used the [feature ranking tool](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFECV.html#sklearn.feature_selection.RFECV) with recursive feature elimination and cross-validated selection of the best number of features from the Scikit-lean module in Python to identify the features whose importance is greater than the mean importance of all features.

##### Load the libraries
The sklearn library is built on numpy so I import that library, as well as the necessary sklearn libraries to build the Random Forest and perform recursive feature selection. I also import pandas to load and store the data in a dataframe. 


### Poster Presentation
<iframe width="560" height="315" src="https://www.youtube.com/embed/7-og9-yht2w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>