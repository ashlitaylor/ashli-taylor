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


### Abstract
Quality of care and patient outcomes are significant determinants of the effectiveness of a healthcare system. My team's project expands upon the best practices in healthcare analytics for evaluating the current status of patient care, and analyzes relationships between various factors that drive cost and influence high quality outcomes. Our project summarizes the findings in a comprehensive, interactive representation of these factors to provide enhanced insight into the main drivers of quality of healthcare, and ultimately facilitates improved patient control and targeted improvement efforts.

### Background

The importance of the integration of information technology and healthcare has been established since 2001 when the Institute of Medicine made a recommendation for IT to be used to improve access to information and support evidence-based decision making. Unfortunately, identifying the information that is available, coalescing it into a cohesive whole, and pulling out relevant details is still a challenge, particularly in the integration of the disparate data sources. As a result of these limits, decisions and policies are made with an incomplete picture of contributing factors. Contrary to other industries that have leveraged technology to produce improvements in expenditure and efficiency, America’s healthcare spending and effectiveness have not improved proportionately with increased technology integration and spending.

I conducted a literature survery of over 20 papers and books that explored the current standards of statistical inference in healthcare analytics, and how they can improve by incorporating socioeconomic data. While there have been advancements and improvements, the implementation of best practices has not been uniform, and in fact has resulted in deteriorating health outcomes, reduced life expectancy and maternal mortality rates. America has dramatically increased spending on healthcare innovation and technology, and information has never been more ubiquitous, however patients are less informed and less empowered about their healthcare decisions than ever. 

The lack of cohesive understanding behind the factors that influence our healthcare system is well-known and has not been without effort to address it. Entities that research healthcare quality are often limited in what data they use, opting for a myopic approach with data they have readily available. A simple web search can reveal information about various statistics such as average expenditures, patient outcomes and various hospital ratings, however analysis that explains how these numbers are related is scant. My team believed that an additional layer of research and analysis is needed to gain a deeper understanding of the problem. This is where our project comes in. 

### Evaluation

Healthcare analytics conventionally use the Donabedian model when evaluating the quality of healthcare provided at a medical facility. This model utilizes three evidence-based process measures; structural, process and outcome measures.

- Structural measures provide a general sense of the capacity and capability of a hospital facility to provide high-quality care. These measures typically receive the lowest priority for targeted improvement. Examples of these measures include room size, availability of certain diagnostic technologies and the number or proportion of board-certified physicians.
- Process measures refer to specific steps that are undertaken during patient care that produce positive or negative patient outcomes. They form the majority of health care quality measures that are used for public reporting.
- Outcome measures reflect the impact of the healthcare service or intervention on the health status of patients. A prime example of an outcome measure is patient mortality rate. These measures are often reported to government and commercial payers and form the high-level clinical or financial metrics that hospital facilities typically prioritize for improvement.

Our project encompasses a broader range of data and metrics than is typically used within the medical community to analyze the quality of care. The parameters we have opted to include preserve the ones currently used in healthcare analytics and add extra dimension by incorporating socioeconomic features. Additionally, our project expands upon the scope of the Donabedian model by aggregating the available facility data and parsing them for the approximately 3000 counties across the United States. This allowed us to drive out additional significant factors that influence the quality of healthcare that have not previously been identified.



A limiting factor of the process and outcome measures is that a hospital is not present in every county to evaluate. Additionally, not every hospital has a sufficient volume of adverse cases and events for meaningful, condition specific evaluation. This was observed in rural counties with lower population densities. These measures required the most processing due to Missing at Random (MAR) data. See the Appendix for the list of all the collected measures and the completeness of the records.
![Alt text](https://raw.githubusercontent.com/ashlitaylor/ashlitaylor.github.io/master/images/Healthcare.jpg)

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
I created our team poster that illustrated and explained the work that we had completed. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/7-og9-yht2w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Conclusion 

We tested whether the current standard of analyzing healthcare could be improved by including publicly available data for socioeconomic factors. The results proved that our proposed method was statistically superior. We identified statistical measures that show that race, education level and poverty rates are socioeconomic factors that should be considered when evaluating ways to improve patient outcomes. This kind of information has enhanced policy implications as more emphasis is placed on overhauling our healthcare system.