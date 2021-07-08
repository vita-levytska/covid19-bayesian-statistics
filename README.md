# A Bayesian Interpretation of COVID-19 Project (R)
### By Vita Levytska and Russow Beck


# Introduction
COVID-19 had a big influence on our lives for the last 10 months and continues affecting our daily lives. We often hear about elevated unemployment rates and differences in party policies regarding COVID-19 preventative measures. The focus of this project is to explore such relationships and determine their significance.    

For our data analysis the following datasets and variables were used:   

1. The **COVID-19 Cases Dataset** from which the following variables were used Confirmed, Deaths, People Tested, Mortality Rate. The data is cumulative for 50 states from Jan. 1st 2020 up to Oct. 31st 2020. 
2. **Stay-at-Home Dataset** that contains one variable – number of days under Stay-at-Home Order (the variable was manually created from the news report information)
3. **Governors Dataset** – only variable ‘party’ was used and transformed to ‘republican’ binary variable, where 1 stands for republican governor and 0 stands for democrat governor. 
4. **Employment Dataset** – Unemployed and Employed for October 2020 and June 2020 were used as variables for analysis.
5. **Population Dataset** – population per each state was used to create percentages.


All datasets were combined, and the following new variables were created:
- Confirmed percentage = Confirmed Cases / Population * 100
- Employed percentage = Employed / Population * 100
- Unemployed percentage = Unemployed / Population * 100
- Stay at home order days percentage (Oct) = Stay-at-home days / 300 * 100  
- Stay at home order days percentage (June) = Stay-at-home days / 150 * 100  

Also, Confirmed, Deaths, Employed, Unemployed, Mortality Rate were log transformed to normalize them and have a better linear fit. 

Data before transformation

![Before](https://github.com/vita-levytska/covid19-bayesian-statistics/blob/main/Graphs/1.png)

Data after transformation

![After](https://github.com/vita-levytska/covid19-bayesian-statistics/blob/main/Graphs/2.png)

In our analysis we attempt to answer the following questions:
1. What predictors explain best the percent of confirmed cases?
2. What predictors are correlated with Confirmed COVID-19 cases?
3. Do governor’s political affiliation and length of Stay-at-home order have any influence on Employment?
4. What variables are correlated to the length of stay-at-home order? 

In the analysis we use multiple linear regression, MAP and MCMC methods to answer the forementioned questions.

# Methods
**a. Priors**. For all our priors for slopes we used flat priors with mean 0 and standard deviation 1-10. The reason for this was to allow both positive and negative slopes and enough variation to get a reasonable estimate. For intercept we determined the priors by plotting the response variable and taking a reasonable mean value and big standard deviation to allow for variation, since intercept tends to vary a lot.      
**b. Likelihood**. In our analysis we used normal distribution for likelihood, for some models the responses were normally distributed thus the choice of likelihood was justified and MAP was used. For other models we used normal distribution for likelihoods as well because it was used throughout the class and gave good estimates using MCMC for approximation instead of MAP.     
**c. Posterior Estimates**.     
Here are the results for different models that we fit to the data.       

## For the following models we used MAP (maximum a posteriori estimate).   

### Model 1
percent.confirmed ~ α - β<sub>1</sub> * Days_Of_Stay_Home_Order    

*y<sub>(%confirm)</sub> ~ Normal(μ,σ)   
α ~ Normal(2.5,5)   
β<sub>1</sub> ~ Normal(0,10)   
σ ~ Uniform(0,10)*   

**The results were significant with an 89% probability that the true Days of Stay Home Order coefficient lies between -.04 and -.02.**    

### Model 2
percent.confirmed ~ α - β<sub>m</sub> * log.mortality   

*y<sub>(%confirm)</sub>~ Normal(μ,σ)   
α ~ Normal(2,5)   
β<sub>m</sub> ~ Normal(0,10)   
σ ~ Uniform(0,10)*   

**The results were significant with an 89% probability that the true coefficient for log mortality lies between -.97 and -.20.**  

### Model 3
percent.confirmed ~ α - β<sub>r</sub> * republican   

*y<sub>(%confirm)</sub> ~ Normal(μ,σ)   
α ~ Normal(2.5,5)   
β<sub>r</sub> ~ Normal(0,10)   
σ ~ Uniform(0,10)*   

**The results were significant with an 89% probability that the true coefficient for republican lies between -.35 and -.1.26.**     

### Model 4
percent.confirmed ~ α - β<sub>m</sub> * log⁡(mortality) + β<sub>d</sub> * Days_At_Home + β<sub>p</sub> * log⁡(People_Tested) + β<sub>mp</sub> * log⁡(mortality) * log⁡(People_Tested) + β<sub>dp</sub> * Days_At_Home * log⁡(People_Tested)

*y<sub>(%confirm)</sub> ~ Normal(μ,σ)
α ~ Normal(2.5,5)
β<sub>m</sub> ~ Normal(0,10)
β<sub>d</sub> ~ Normal(0,10)
β<sub>p</sub> ~ Normal(0,10)
β<sub>mp</sub> ~ Normal(0,10)
β<sub>dp</sub> ~ Normal(0,10)
σ ~ Uniform(0,20)*

**The results were significant with an 89% probability that the true coefficient for the interaction of log(Mortality) and log(People_Tested) lies between -.64 and -.03. As were the results for the interaction of Days_Of_Stay_At_Home and log(People_Tested) having an 89% probability that the true coefficient for the lies between .01 and .03.**

## MCMC with Normal Likelihood was used to fit the following models

### Model 5
(m1.6) logConfirmed ~ α + β<sub>1</sub> * logDeaths + β<sub>2</sub> * logPeople_Tested + β<sub>3</sub> * Days_Of_Stay_Home_Order + β<sub>4</sub> * logEmployed_2020_Oct + β<sub>5</sub> * logUnemployed_2020_Oct + β<sub>6</sub> * republican
with priors:    
    *α ~ Normal(2,5),      
    β<sub>1</sub> ~ Normal(0,10),   
    β<sub>2</sub> ~ Normal(0,10),   
    β<sub>3</sub> ~ Normal(0,10),   
    β<sub>4</sub> ~ Normal(0,10),   
    β<sub>5</sub> ~ Normal(0,10),   
    β<sub>6</sub> ~ Normal(0,10),   
    σ ~ Uniform(0,100)*   
    
**From precis output we see that logDeath is positively correlated with response with CI (0.23,0.55), Days of Stay at Home Order is negatively correlated with response with CI (-0.02,-0.01); both logEmployed_2020_Oct and republican are positively correlated with response with CI (0.14, 0.91) and (0.01, 0.40) respectively.**

### Model 6
After removing the non-significant variables we get the following model:   

(m1.18)  logConfirmed ~ α + β<sub>1</sub> * logDeaths + β<sub>3</sub> * Days_Of_Stay_Home_Order + β<sub>4</sub> * logEmployed_2020_Oct    
with priors:      
    *α ~ Normal(2,5),      
    β<sub>1</sub> ~ Normal(0,10),      
    β<sub>3</sub> ~ Normal(0,10),     
    β<sub>4</sub> ~ Normal(0,10),     
    σ ~ Uniform(0,100)*    

**The confidence intervals are similar to those in previous model for these coefficients are given in appendix section.
After comparing WAIC, we see that reduced model is better and has a higher weight than full model.**    

The following models are testing employment / unemployment percent for October 2020 
 
### Model 7
(m1.12)  Employed_Perc ~ α + β<sub>1</sub> * percent_confirmed + β<sub>3</sub> * stay_home + β<sub>6</sub> * republican    
    *α~Normal (10,20),   
    β<sub>1</sub> ~ Normal(2,10),   
    β<sub>3</sub> ~ Normal(2,10),   
    β<sub>6</sub> ~ Normal(2,10),   
    σ ~ Uniform(0,100)*  
    
**For this model none of the coefficients are significant, thus the percentage employed is not influenced by the predictors.**    

### Model 8
(m1.13)  Unemployed_Perc ~ α + β<sub>1</sub> * percent_confirmed + β<sub>3</sub> * stay_home + β<sub>6</sub> * republican    
    *α ~ Normal (10,20),   
    β<sub>1</sub> ~ Normal(2,10),   
    β<sub>3</sub> ~ Normal(2,10),   
    β<sub>6</sub> ~ Normal(2,10),    
    σ ~ Uniform(0,100)*   
    
**For this model only percent_confirmed is significant coefficient with CI (-1.23,-0.33).**

Next, the model is fit for unemployment percent for June 2020.

### Model 9
(m1.22) June_2020_perc ~ α + β<sub>1</sub> * percent_confirmed + β<sub>3</sub> * stay_home + β<sub>6</sub> * republican 
    *α ~ Normal (10,20),    
    β<sub>1</sub> ~ Normal(2,10),    
    β<sub>3</sub> ~ Normal(2,10),    
    β<sub>6</sub> ~ Normal(2,10),   
    σ ~ Uniform(0,100)*   
    
**For this model percent_confirmed is positively correlated with percent unemployed for June 2020 and republican is negatively correlated with response, the confidence intervals are (1.42,4.16) and (-3.32,-0.70) respectively.**    

The last two models are analyzing stay at home percentage vs republican and percent confirmed.    

### Model 10
(m1.14) stay_home ~ α + β<sub>6</sub> * republican    
    *α ~ Normal (10,20),    
    β<sub>6</sub> ~ Normal (2,10),   
    σ ~ Cauchy (0,100)*    
    
**Republican is negatively correlated with percentage of days at Stay at Home Order with confidence interval (-7.03,-0.80).**

### Model 11
(m1.15) stay_home ~ α + β<sub>1</sub> * percent_confirmed    
    *α ~ Normal(10,20),    
    β<sub>1</sub> ~ Normal (2,10),   
    σ ~ Cauchy(0,100)*
    
**Percent_confirmed is negatively correlated with percentage of days at Stay at Home Order with confidence interval (-5.22,-2.88).**    


# d. Conclusion.

Using the Bayesian posterior estimates we were able to investigate our predictors and find significance among our political and economical hypotheses. We learned that as the **number of days a state remains in lockdown increases, then the mean percent of COVID-19 cases reduce as expected**. When **log of the mortality rate increased, we saw a similar trend decreasing the mean COVID-19 percent**. This finding was counter intuitive. One would think the higher log of the mortality rate would lead to a higher percent of COVID cases for a state. An investigation on the population of 65+ for each state would be suggested to address this phenomenon. Another interesting find was the conclusion that the **governor’s party affiliation had a significant effect on the percent of confirmed cases for the population**. There was a divide among party lines to protect more lives with higher pandemic restrictions or aid the economy by reducing restrictions sooner. We see through our data that **republican ran states had higher confirmed rates than those with democratic governors**. We also found that the log of people tested interacted with both the number of days in lockdown as well as the log mortality rate to produce a strong model for the mean percent of confirmed cases for a state.    
After analyzing the models we see that **Deaths and Employed have positive correlation with Confirmed Cases**, which makes sense, because the more COVID cases the more deaths we have and the more people go to work, the higher is the spread of the virus. The interesting finding was that **percent of unemployed is negatively correlated to republican governor, thus the unemployment is lower in states with republican governor and higher in states with democrat governor**. Also, if we look at unemployment percentage in June, we see the same trend with governor, but also the **percent of confirmed cases is also positively correlated with percent of unemployed**, which is consistent with reports that claim that the virus caused the increase in unemployment. Percentage of days in Stay-at-home order is negatively correlated to both political affiliation of governor and percent of confirmed cases. Therefore, **republican governors imposed stay-at-home orders for less time than democrat governors and the more people stayed at home, the lower was the percent of confirmed cases**.   
