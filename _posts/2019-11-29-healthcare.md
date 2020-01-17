---
title: Quality of Patient Health Outcomes in America
subtitle: R, Python
date: 2019-11-29T06:00:00.000+00:00
thumb_img_path: "/images/Healthcare.jpg"
excerpt: 'A project that illustrates how healthcare analytics can be improved by incorporating
  patient socioeconomic data. '
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

Our project encompasses a broader range of data and metrics than is typically used within the medical community to analyze the quality of care. The parameters we have opted to include preserve the ones currently used in healthcare analytics and add extra dimension by incorporating socioeconomic features. Additionally, our project expands upon the scope of the Donabedian model by aggregating the available facility data and parsing them for the approximately 3000 counties across the United States. This allowed us to drive out additional significant factors that influence the quality of healthcare that have not previously been identified. Specifically, our project accomplished the following: 

1. Expanded upon the Donabedian framework used in healthcare analytics by incorporating patient socioeconomic features.
2. Focused on aggregated county metrics and resources as opposed to facility specific measures.
3. Presented significant findings via an engaging user interface that maximizes the data to ink ratio instead of a simple statistical report with a static interface.


### Data 

Our project uses readily available public datasets sourced from government databases. Process and outcome measure data from 2017 and socioeconomic data from 2016 was obtained from datasets available from the Department of Health and Human Services. We gathered data for over 4,800 public hospitals.

A limiting factor of the process and outcome measures is that a hospital is not present in every county to evaluate. Additionally, not every hospital has a sufficient volume of adverse cases and events for meaningful, condition specific evaluation. This was observed in rural counties with lower population densities. These measures required the most processing due to Missing at Random (MAR) data. I created a Random Forest regression model to impute the process and outcome parameters with MAR data. I chose this model because it was better suited to capture the dimensionality of the observed values and generally yields better imputation accuracy for MAR data. 

### Experiment

These five questions form the motivation behind the models I created to study drivers behind mortality rates, readmission rates, and facility ratings:
- Can our three dependent variables of interest be adequately explained using only process measures?
- What statistical significance do process measures have as indicators for mortality, readmission rate, and facility rating?
- How does this significance change when socioeconomic features are included in the model?
- Are our proposed models that use both process and socioeconomic data statistically better than the models that use process alone?
- And finally, out of all the variables we sourced, what are the five most important ones for each variable of interest?

#### Implemented models

- Random Forest Regression Model with recursive feature selection: Random forests perform well by ranking the features that best balance the bias-variance tradeoff. However, when correlated features are present (as with our Age, Gender, and Ethnicity data), equivalent importance can be assigned to the group, which can over- or underestimate the actual feature importancexxi. This can be corrected by selecting features recursively. We used the feature selection tool from the Scikit-lean module in Python to identify the features whose importance is greater than the mean importance of all features.
- Multivariate Regression models with mixed stepwise variable selection: Regression analysis is a simple way to examine the nature and degree of relationships between predictors and the variable of interest. We performed mixed stepwise variable selection in R to iteratively add and remove variables until the subset of features that produces the lowest AIC is found.

Test Measures The null hypothesis (H0) of our experiments is that the models fit to the conventional framework are statistically equivalent to the models fit using our proposed framework. The measures we use to establish which subset is ‘best’ are:
- The Akaike Information Criterion (AIC). Lower values indicate better quality models.
- Adjusted Coefficient of determination (R2). Higher values indicate a better proportion of explained variance. We used the adjusted statistic because it adds a penalty for added variables in multiple regression.
- Analysis of Variance (ANOVA). Used to test if the models are equivalent. A low p-value (below 0.05) indicates that our model is statistically superior.

##### Testing framework

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


### Poster Presentation
I created our team poster that illustrated and explained the experimental work that I completed. My three minute presentation that summarized my work can be viewed below.   

<iframe width="560" height="315" src="https://www.youtube.com/embed/7-og9-yht2w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Conclusion 

I tested whether the current standard of analyzing healthcare could be improved by including publicly available data for socioeconomic factors. The results proved that our proposed method was statistically superior. I identified statistical measures that show that race, education level and poverty rates are socioeconomic factors that should be considered when evaluating ways to improve patient outcomes. This kind of information has enhanced policy implications as more emphasis is placed on overhauling our healthcare system.