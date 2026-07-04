# Analyzing the Impact of Dementia Risk Factors on Regions Worldwide 

Franchesca Garcia and Isha Nayak

December 11, 2024

## Introduction
Dementia is a neurological condition characterized by a decline in cognitive abilities, such as the ability to execute motor activities, recognize objects, generate speech, and think abstractly. The World Health Organization (WHO) estimates that dementia currently affects 55 million people globally. It is also known to be the seventh leading cause of death among older adults. Dementia results from damage to brain structures and is characterized by an abundance of abnormal structures in the brain, such as amyloid plaques and neurofibrillary tangles. Dementia leads to a loss of cognitive abilities. 

Given how severe dementia can be, understanding how to reduce the risk factors associated with it is critical. Several factors have been identified as increasing the likelihood of developing dementia, including hypertension (high blood pressure), obesity, exposure to fine particulate matter (air pollution), physical inactivity, excessive alcohol consumption, and high cholesterol levels. However, it is unclear how these risk factors vary in their effects across different populations and regions. For example, the prevalence of these risk factors differs widely between countries, influenced by socioeconomic status, healthcare access, environmental factors, and lifestyle habits. As a result, the relationship between these risk factors and dementia outcomes may not be uniform across global regions.

This research aims to investigate the impact of various risk factors on the burden of dementia. Specifically, we will analyze how the prevalences of risk factors such as alcohol and obesity are associated with the burden of dementia regionally. We hypothesize that certain risk factors, particularly lifestyle-related factors, will have differing impacts across regions. By examining these regional patterns, we hope to provide insight into how interventions could be modified and tailored to fit specific risk profiles of different populations to reduce the global burden of dementia.

In summary, this study will contribute to the growing body of research on dementia by exploring how regional and lifestyle factors interact with and influence the disease burden of dementia. We aim to analyze how different risk factors contribute to the DALY burden of dementia worldwide to provide more information on how future public health strategies can be modified for dementia prevention and care.

## Data Sources & Methods
### 2.1 Data Sources
Our research is based on data from several datasets by WHO’s Global Health Observatory (GHO). From each dataset, we selected country-level estimates of our variable of interest aggregated on both sexes.
#### 2.1.1 Burden of Dementia
For measurements of the burden of dementia, we extracted data from “Global Health Estimates 2021: Disease burden by Cause, Age, Sex, by Country and by Region, 2000-2021”. In this report, the burden due to dementia was estimated in terms of disability-adjusted life years (DALYs) for 185 countries in each of the years 2000, 2010, 2015, 2019, 2020, and 2021 based on population surveys assessing the severity of disability by dementia. WHO defines DALYs as “A composite measure of disease burden calculated as the sum of Years Lived with Disability (YLD) and Years of Life Lost (YLL), thereby summarizing the effects of dementia on both the quantity (premature mortality) and quality of life (disability).”
#### 2.1.2 Risk Factors of Dementia
For measurements of the prevalence of dementia risk factors, we used data from the GHO’s Indicators database. The indicator datasets included in the final model of our research were “Alcohol, heavy episodic drinking (population) past 30 days” and “Prevalence of obesity among adults, BMI ≥ 30”. The first dataset includes age-standardized measurements of the “proportion of adults (15+ years) who have had at least 60 grams or more of pure alcohol on at least one occasion in the past 30 days” based on surveys on nationally representative samples and population-based surveys. Measurements were available for 194 countries for each of the years spanning 2000 to 2019. The second dataset includes age standardized measurements of the “percentage of adults aged 18+ years with a body mass index (BMI) of 30 kg/m2 or higher” based on population-based surveys. Measurements were available from the years spanning 1990 to 2021 for 199 countries. 

Other indicators included in our research but not in our final model were “Prevalence of insufficient physical activity among adults ages 18+ years”, “Prevalence of overweight among adults, BMI>= 25”, and “Prevalence of hypertension among adults aged 30-79 years”.
### 2.2 Data Cleaning
All datasets were imported into and cleaned using R. From each dataset, we selected columns for the main value, country, and year. From one dataset, we additionally included the corresponding WHO region of each country in the final data. From the DALYs dataset, we additionally kept the population of each country to standardize the DALY measurements. After recognizing curved trends involving them, we also performed a log transformation on DALYs. All quantitative variables (DALYs, year, alcohol, obesity, insufficient physical activity, overweight, and hypertension) were also centered.

Each dataset was then inner joined on country and year. The resulting data frame had 706 rows, where each observation was an occasion within a country, including 178 countries in the years 2000, 2010, 2015, and 2019.



Figure 1. Data has a multilevel structure with occasions as Level 1 units and countries as Level 2 units.
### 2.3 Methods
All statistical analysis was also performed using R. Packages used include the tidyverse, lme4, and emmeans packages. To handle the nested structure of our data, we built a linear multilevel model using the lmer function from the lme4 package. In order to test whether the effect of risk factors on the burden of dementia differs across regions, we used DALYs due to dementia as the response variable and included cross-level interactions between each risk factor and region. Note that while countries are also nested within regions, we decided to treat region as a country-level variable and use interactions rather than treat region as a third level and use random slopes out of interest in the coefficients at each specific region. To test the significance of each set of terms added to the model, we used restricted maximum likelihood (REML) tests. To assess the final models’ estimated marginal effects of each risk factor across regions, we looked at confidence intervals produced by the emtrends function from the emmeans package.

## Results
### 3.1 Exploratory Analysis
Based on a matrix plot including all initial quantitative variables, we decided to narrow down our risk factor variables of interest to alcohol and obesity. Both showed relatively strong positive associations with DALYs, suggesting they would be appropriate predictors to include in our model. Hypertension and insufficient physical activity showed no apparent association, so we did not consider including either during our model building. Overweight as a risk factor also showed a positive association with DALYs as well as a clear association with obesity, so it was excluded to avoid multicollinearity. We preferred obesity over overweight as it is a more direct risk factor for dementia (Alzheimers.org, 2024). DALYs also showed a positive trend across years. Although obesity also increases over time, we decided to consider year as a predictor to account for aging populations, which would contribute to increased cases of dementia as dementia most commonly appears among the elderly and ultimately contribute to higher DALY measurements. Still, it is important to note the consequential risk of multicollinearity and proceed with caution.

Figure 2. Matrix plot for the variables DALYs due to dementia, year, and prevalences of heavy drinking, hypertension, lack of physical activity, obesity, and overweight.

Scatterplots separated by region suggested that the associations between each risk factor and DALYs do differ across regions. Notably, while Africa, Europe, and the Western Pacific have similar wide spreads for alcohol, they do not share as extreme DALYs; the slope appears to be greater for Europe and flat for Africa, with the Western Pacific in between. In the association between obesity and DALYs, the slopes for Africa and the Eastern Mediterranean are noticeably more flat. These differences support the inclusion of interactions between our risk factors and region in our model.


Figure 3. Scatter plots of DALYs due to dementia against (a) prevalence of heavy drinking and (b) prevalence of obesity, faceted by region.
### 3.2 Model Building
We began with a null model including only random intercepts for country. We learned from the model that the between-country variation in DALYs is much greater than the within-country variation (τ2 = 0.559, σ2 = 0.063). Accordingly, the null model had a high ICC value of 0.899, meaning 89.9% of variation in DALYs can be attributed to differences between countries. We proceeded to add the Level 1 predictors year, alcohol, and obesity. This model was a significantly better fit than the null model (X2 = 338.39, df = 3, p < 2.2e-16), and each coefficient had a t-value greater than 2. We then proceeded to add the Level 2 predictor region, which was also statistically significant (X2 = 105.99, df = 5, p < 2.2e-16). Finally, we added the interaction between obesity and region, then the interaction between alcohol and region. Each significantly improved the model before it (X2 = 124.85, df = 5, p < 2.2e-16 and X2 = 9.7683.85, df = 5, p = 0.082 < 0.1). This final model explained 64.3% of the unexplained Level 1 variation and 50.5% of the unexplained Level 2 variation from the null model.
### 3.3 Final Model
The final model estimated the country-to-country variance in DALYs due to dementia to be 0.277 log(years), or 1.32 years. The occasion-to-occasion variance in DALYs was estimated to be 0.022 log(years) or 1.02 years. Using Africa as the reference region, the model estimated an intercept of -0.87. This means the predicted DALYs due to dementia in an African country with average rates of heavy drinking and obesity in the average year 2010 is e-0.87 = 0.42 years per capita, or approximately 42,000 years per 100,000 population. For year, our model estimated a slope coefficient of 0.02. This means each additional year is associated with a 1.02-fold change in DALYs due to dementia in an African country with average rates of heavy drinking and obesity. For alcohol, our model estimated a slope coefficient of 0.005. This means a percentage point increase in the prevalence of heavy drinking is associated with a e0.005 = 1.005-fold change in DALYs due to dementia in an African country with average obesity in 2010. For obesity, our model estimated a slope coefficient of -0.03. This means a percentage point increase in the prevalence of heavy drinking is associated with a e-0.03 = 0.95-fold change in DALYs due to dementia in an African country with an average rate of heavy drinking in 2010.

Each coefficient for a region alone describes the typical DALYs of countries in the region compared to the typical DALYs in Africa. For example, the coefficient 0.75 for the Americas means the predicted DALYs due to dementia in a country in the Americas is e0.75- 1 = 112% greater than the predicted DALYs in an African country. Each coefficient for the interaction of alcohol in a region describes how the effect of alcohol on DALYs in that region compares to the effect in Africa. For example, the coefficient 0.007 for the Americas means the predicted change in DALYs associated with a percentage point increase in the prevalence of alcohol in a country in the Americas is e0.007- 1 = 6.8% greater than the predicted change in an African country. Similarly, each coefficient for the interaction of obesity in a region describes how the effect of obesity on DALYs in that region compares to the effect in Africa. For example, the coefficient 0.04 for the Americas means the predicted change in DALYs associated with a percentage point increase in the prevalence of alcohol in a country in the Americas is e0.04- 1 = 4.5% greater than the predicted change in an African country.
		
#### Fixed effects:
| | Estimate | t value |
| --- | --- | --- |
|(Intercept)|-0.8740|-9.395|
|cenAlcohol:regionAmericas|0.007|0.671|
|cenYear|0.022|13.753|
|cenAlcohol:regionEastern Mediterranean|-0.041|-0.950|
|cenAlcohol|0.005|1.030|
|cenAlcohol:regionEurope|0.015|2.170|
|cenObesity|-0.035|-5.977|
|cenAlcohol:regionSouth-East Asia|-0.007|-0.595|
|regionAmericas|0.755|5.420|
|cenAlcohol:regionWestern Pacific|0.016|1.975|
|regionEastern Mediterranean|-0.095|-0.121|
|regionAmericas:cenObesity|0.044|8.157|
|regionEurope|1.484|11.398|
|regionEastern Mediterranean:cenObesity|0.017|2.862|
|regionSouth-East Asia|1.258|5.099|
|regionEurope:cenObesity|0.057|9.740|
|regionWestern Pacific|0.798|5.253|
|regionSouth-East Asia:cenObesity|0.075|6.753|
|regionWestern Pacific:cenObesity|0.046|7.294|

Table 1. Fixed effect estimates from a linear mixed-effects model predicting DALYs due to dementia by year, prevalence of heavy drinking, and prevalence of obesity, with cross-level interactions between prevalences of risk factors and region, grouped by country.

The emtrends function from the emmeans package combines the main effects of alcohol and obesity with the interaction coefficients to produce the following marginal effects of each region, back transformed:

#### Marginal Effects:
| Region | Alcohol: | SE | Obesity | SE | 
| --- | --- | --- | --- | --- |
| Africa | 1.005 | 0.005 | 0.966 | 0.006 |
| Americas | 1.012 | 0.009 | 1.009 | 0.004 |
| Eastern Mediterranean | 0.965 | 0.043 | 0.983 | 0.005 |
| Europe | 1.020 | 0.005 | 1.022 | 0.005 |
| South-East Asia | 0.998 | 0.011 | 1.041 | 0.011 |
| Western Pacific | 1.021 | 0.006 | 1.011 | 0.005 |

Table 2. Marginal effect estimates of the prevalence of alcohol and obesity by region.

95% confidence intervals for these marginal effects show that the effect of alcohol on DALYs is only significant in Europe and the Western Pacific, as 1 is not included only in the intervals for these two regions. This exclusion of 1 means it is not plausible that DALYs remain constant as the prevalence of heavy drinking increases by a percentage point. The intervals also show that the effect of obesity on DALYs is significantly less in Africa and the Eastern Mediterranean, as the intervals for these regions do not overlap with the intervals of the other regions. This separation means it is not plausible that the effect of obesity on DALYs is the same between these two sets of regions. 

 
Figure 4. 95% confidence intervals of the marginal effect of (a) the prevalence of heavy drinking and (b) the prevalence of obesity on DALYs due to dementia by region.

### 3.4 Diagnostic Analysis
The residuals versus fits of the final model show a fairly random scatter with no clear fanning, allowing us to assume linearity and equal variance of the residuals. The points of the Q-Q plot indicate slight deviation; however, the majority of points fall roughly along the reference line, allowing us to assume the normality of the residuals. This is further supported by the histogram of residuals showing an approximately normal distribution. The satisfaction of these assumptions provides proof of the validity of the significance tests and confidence intervals from our model.

Figure 5. Diagnostic plots verify the linearity, normality, and equal variance of residual assumptions.

## Discussion

Upon completing our analysis regarding dementia and its risk factors across regions worldwide, several interesting findings emerged. A notable result was that while alcohol consumption was generally a significant predictor of the burden of dementia, after examining its interaction with various regions we found that alcohol’s impact was only significant in Europe and the Western Pacific. Another important finding was that the effect of obesity on the burden of dementia was significantly less in Africa and the Eastern Mediterranean. These differences in the influence of risk factors such as alcohol and obesity may indicate that dementia research needs to be more region-specific, as risk factors and their effects can vary significantly by geographic and cultural context.

From our research, we discovered that a lot of dementia research is currently conducted in developed countries, such as the U.S. and the U.K., which have many research centers dedicated to studying dementia. However, there appears to be a gap in research being conducted in less developed regions. This lack of research may indicate a lack of knowledge of dementia risk factors within those regions. As a result, it can make it difficult to extend conclusions on dementia risk factors to developing countries confidently. For example, regions that lack access to proper healthcare access and have different lifestyle patterns may result in a difference in the prevalence and progression of dementia in ways that aren’t adequately captured by current studies that focus on wealthier countries. 

Our study does have a few limitations. First, the regional categories used in our project are fairly broad, which may mask important differences within individual countries. For example, the risk factors prevalent in one part of a region may not reflect those in another, leading to oversimplified conclusions. Additionally, there may be potential confounding variables at play, such as variations in healthcare quality across regions as well as multicollinearity between obesity and year due to the estimated effect of obesity in Africa being unexpectedly negative. In developing countries, shorter lifespans due to limited healthcare access can affect the number of dementia cases, particularly since the disease tends to occur in older populations. This may help explain why dementia appears more prevalent in developed countries, where people live longer. Furthermore, lifestyle factors (such as the car-centric culture in the U.S. contributing to higher obesity rates) may also influence results. In contrast, countries with more integrated public transportation or active lifestyles, such as Japan, may see different outcomes. 

One benefit of our report includes the emphasis placed on the impact of certain risk factors across regions. These findings highlight regional variations in dementia risk factors by adding specificity to our understanding of dementia, suggesting the need for localized research. This report also points out the gap in research on dementia between developed and developing regions. 

While our report successfully highlights regional differences in DALYs, there are some areas for improvement. One main weakness within our report and model is that we did not fully account for the time lag between risk factors and dementia outcomes, which could provide a more meaningful analysis. Additionally, the model lacks contextual factors that could help explain some of the confounding variables influencing our results. 

In the future, increasing the number of regions included in the study would reduce the risk of masking results. Additionally, adjusting for more potential confounding variables could help mitigate multicollinearity and improve the accuracy of the analysis. One way to address this would be to directly use the proportion of the population who are elderly as a predictor. It would also be important to account for any time lag between exposure to risk factors and the onset of dementia outcomes, such as with a time analysis.

## Appendix

### Term Definitions 
Disability-Adjusted Life Years (DALYs) account for mortality and morbidity. Mortality includes the life expectancy of an individual at the time they become sick - the age at their death. Morbidity can be calculated by finding the product of disability weight (which is defined as 0 for a fully healthy individual, 0.3, which is common for an individual with a long-term chronic illness, or 1 for being fully disabled) and duration of the disability. 


### Data Sources
For more information on the data sources:

DALYs due to Dementia:

[https://www.who.int/data/gho/data/themes/mortality-and-global-health-estimates/global-health-estimates-leading-causes-of-dalys]

Prevalence of Obesity: 

[https://www.who.int/data/gho/data/indicators/indicator-details/GHO/prevalence-of-obesity-among-adults-bmi--30-(age-standardized-estimate)-(-)]

Alcohol, Heavy Episodic Drinking: 

[https://www.who.int/data/gho/data/indicators/indicator-details/GHO/alcohol-heavy-episodic-drinking-(15-)-past-30-days-(-)-age-standardized-with-95-ci]




### Variables
| Name | Variable Role | Type | Value (Units) |
| --- | --- | --- | --- |
|DALY s of dementia|Response|Quantitative|Years per capita|
|Year|Level 1 Predictor|Quantitative|Year (2000, 2010, 2015, 2019)|
|Prevalence of heavy drinking (alcohol)|Level 1 Predictor|Quantitative|Percentage|
|Prevalence of obesity|Level 1 Predictor|Quantitative|Percentage|
|WHO Region|Level 2 Predictor|Categorical|Africa, the Americas, South-East Asia, Europe, Eastern Mediterranean, Western Pacific|



### Marginal Effects of Obesity and Alcohol on Estimated DALYs
The graphs displayed below visualize the marginal effect of obesity and alcohol on estimated DALYs due to dementia, respectively. 

The two risk factors addressed in this study are taken from common risk factors of dementia as noted by the resources utilized as reference for this paper and from health classes, such as Nutrition in Aging,  taught at the California Polytechnic University at San Luis Obispo. 

Based on the graph depicting the marginal effect of obesity on estimated DALYs, the prevalence of obesity in the Eastern Mediterranean and Africa doesn’t follow an upward trend compared to the other regions. Instead, the curve lies more flat than curved. This indicates that the prevalence of obesity may not play as significant of a role in DALYs for Africa and the Eastern Mediterranean as it does in other regions. This may be due to other factors, such as a lack of healthcare services available to patients in Africa or the Eastern Mediterranean, which can lead to more deaths as a direct effect of obesity rather than dementia, as the average life expectancy is typically lower in such countries. We do want to note that the downward trend of obesity in Africa is not expected, but this shape may be due to confounding variables that were not accounted for within the model. 

Based on the graph depicting the marginal effect of alcohol on estimated DALYs, the prevalence of heavy drinking of alcohol in Europe and the Western Pacific appears to be more substantial compared to other regions. The insignificant effects of alcohol on DALYs in those other regions could indicate that other factors may have more of an impact on DALYs due to dementia than alcohol does.


### Citations
Alzheimers.gov. (2024, July 9). Research on alzheimer’s disease and related dementias | alzheimers.gov. Research on Alzheimer’s Disease and Related Dementias . https://www.alzheimers.gov/taking-action/research-activities

Alzheimer’s Disease International, & Mana Communications. (2021, September 21). Adi - over 41 million cases of dementia go undiagnosed across the globe – world alzheimer report reveals. Alzheimer’s Disease International (ADI). https://www.alzint.org/news-events/news/over-41-million-cases-of-dementia-go-undiagnosed-across-the-globe-world-alzheimer-report-reveals/

Alzheimer’s Research UK. (2024, December 4). Obesity and dementia risk. https://www.alzheimersresearchuk.org/dementia-information/dementia-risk/obesity-and-dementia-risk/

Alzheimer’s Society. (2024, August). Obesity and dementia risk. Alzheimer’s Society. https://www.alzheimers.org.uk/about-dementia/managing-the-risk-of-dementia/reduce-your-risk-of-dementia/obesity#:~:text=Does%20obesity%20increase%20the%20risk,which%20contribute%20to%20dementia%20risk.

Douglas Bates, Martin Maechler, Ben Bolker, Steve Walker (2015). Fitting Linear
  Mixed-Effects Models Using lme4. Journal of Statistical Software, 67(1), 1-48.
  doi:10.18637/jss.v067.i01.

Fox J, Weisberg S (2019). _An R Companion to Applied Regression_, Third edition.
  Sage, Thousand Oaks CA. <https://www.john-fox.ca/Companion/>.

Global Health Estimates 2021: Disease burden by Cause, Age, Sex, by Country and by Region, 2000-2021. Geneva, World Health Organization; 2024.

Kahn, J. G. (2014, May 16). The DALY Show, Disability-Adjusted Life Year (DALY). YouTube. https://www.youtube.com/watch?v=Exce4gy7aOk&t=379s

Lenth R (2024). _emmeans: Estimated Marginal Means, aka Least-Squares Means_. R
  package version 1.10.5, <https://CRAN.R-project.org/package=emmeans>.

 Lüdecke et al., (2021). performance: An R Package for Assessment, Comparison and
  Testing of Statistical Models. Journal of Open Source Software, 6(60), 3139.
  https://doi.org/10.21105/joss.03139

Michigan Medicine. (2024, August 19). Study links dementia diagnosis rates to geographic location. News. https://www.news-medical.net/news/20240816/Study-links-dementia-diagnosis-rates-to-geographic-location.aspx

NIH. (2023, March 1). Alzheimer’s disease genetics fact sheet | National Institute on Aging. Alzheimer’s Disease Genetics Fact Sheet. https://www.nia.nih.gov/health/alzheimers-causes-and-risk-factors/alzheimers-disease-genetics-fact-sheet

NIH. (n.d.). Find an alzheimer’s disease research center | National Institute on Aging. Find an Alzheimer’s Disease Research Center . https://www.nia.nih.gov/health/clinical-trials-and-studies/find-alzheimers-disease-research-center

R Core Team (2024). _R: A Language and Environment for Statistical Computing_. R
  Foundation for Statistical Computing, Vienna, Austria.
  <https://www.R-project.org/>.

Rizzi, L., Rosset, I., & Roriz-Cruz, M. (2014, June 25). Global Epidemiology of Dementia: Alzheimer’s and vascular types. BioMed research international. https://pmc.ncbi.nlm.nih.gov/articles/PMC4095986/#:~:text=In%20a%20review%20about%20the,diabetes%20%5B4%2C%2013%5D.

Whitmer, R. A., Gustafson, D. R., Barrett-Connor, E., Haan, M. N., Gunderson, E. P., & Yaffe, K. (2008, September 30). Central obesity and increased risk of dementia more than ... Central Obesity and Increased Risk of Dementia More Than Three Decades Later . https://www.neurology.org/doi/10.1212/01.wnl.0000306313.89165.ef
  Wickham H, Averick M, Bryan J, Chang W, McGowan LD, François R, Grolemund G,
  Hayes A, Henry L, Hester J, Kuhn M, Pedersen TL, Miller E, Bache SM, Müller K,
  Ooms J, Robinson D, Seidel DP, Spinu V, Takahashi K, Vaughan D, Wilke C, Woo K,
  Yutani H (2019). “Welcome to the tidyverse.” _Journal of Open Source Software_,
  *4*(43), 1686. doi:10.21105/joss.01686 <https://doi.org/10.21105/joss.01686>.

Williams, N. (2023, October 13). Ethnic minorities experience greater effect of dementia risk factors, study suggests. Alzheimer’s Research UK. https://www.alzheimersresearchuk.org/news/ethnic-minorities-experience-greater-effect-of-dementia-risk-factors-study-suggests/#:~:text=The%20research%20team%20found%2012.6,from%20other%20minority%20ethnic%20groups.




  



  


