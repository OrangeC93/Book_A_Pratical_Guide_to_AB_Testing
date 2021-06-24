## The Space of Complementary Techniques
To have successful A/B experiments, we not only need the care and rigor in analysis and in creating the experimentation platform and tools, but we also need: 
- Ideas for experiments, that is, an ideas funnel. 
- Validated metrics to measure the effects we care about. 
- Evidence supporting or refuting hypotheses, when running a controlled experiment is either not possible or insufficient. 
- Optionally, metrics that are complementary to the metrics computed from controlled experiments. 
![image](/img/Complementary_Method.png)

#### Logs-based Analysis
- Building intuition: helps you understand your product and system baseline, what the variance is, what is happening organically independent of experimentation, what size change might be pratically significant and more.
- Characterizing potential metrics: helps you understand the variance and distributions, how new metrics correlate with existing metrics. 
- Generating ideas for A/B experiments based on exploring the underlying data.
- Natural experiments.
- Observational causal studies.

One limitation is that these analyses can only infer what will happen in the future based on what happened in the past. 

#### Human Evaluation
Human evaluation is where a company pays a human judge, also called a rater , to complete some task. The results are then used in subsequent analysis. This is a common evaluation method in search and recommendation systems. 
- Human evaluation results are also useful for debugging.

One limitation of human evaluation is that raters are generally not your end users. 

#### User Experience Research(UER)
While user experience research (UER) uses a variety of methods, we focus here on a subset of field and lab studies that typically go deep with a few users, often by observing them doing tasks of interest and answering questions in either a lab setting or in situ 
 
These techniques can be useful for generating metric ideas based on correlating “ true ” user intent with what we observe via instrumentation. You must validate these ideas using methods that scale to more users, such as observational analyses and controlled experiments. 

#### Focus Groups
Focus groups are guided group discussions with recruited users or potential users. Focus groups can be useful for getting feedback on ill-formed hypotheses in the early stages of designing changes that become future experiments, or for trying to understand underlying emotional reactions, often for branding or marketing changes. Again, the goal is to gather information that cannot be measured via instrumentation and to get feedback on not-yet-fully-formed changes to help further the design process. 

#### Surveys
Some potential issues are needed to consider:
- Questions must carefully worded
- Answers are self reported
- The population can easily be biased 

#### External Data
External data is data relevant to you and what you are looking at that a party external to your company has collected data and analyzed. 


## Observational Causal Study
The “ basic identity of causal inference ” (Varian 2016 ) is:

```Outcome for treated - Outcome for untreated = Impact of Treatment on treated + Selection bias```

Controlled experiments are the gold standard for assessing causality because, with random assignment of units to variants, the first term is the observed difference between Treatment and Control and the second term has an expected value of zero. 

However...
#### When Controlled Experiments Are Not Possible
- Not under the control of organization, user behaviour when they change their phone
- Two few units, M&A senario
- May incur too large an opportunity cost, running ads during Superbowl
- Change is expensive relative to the perceived value
- Cannot be properly randomized, when assessing the  value of TV ads
- Unethical or illegal, withholding medical treatment that are believed to be beneficial

Under the above situation, we could user other methods, including small scale user experience studies, surveys, and observational studies which we call observational causal studies, the goal is to get as close to a causal result as possible, while the general data analysis have different goals, ranging from summarizing, prediction, analyzing metrics etc.

## Designs for Observational Causal Studies
In observational causal studies, the challenges are: 
- How to construct Control and Treatment groups for comparison. 
- How to model the impact given those Control and Treatment groups. 

#### 1.Interrupted Time Series
Interrupted Time Series (ITS) is a quasi-experimental design, where you use the same population for Control and Treatment, and you vary what the population experiences over time. For example, the effect of police helicopter surveillance on home burglaries was estimated using multiple Treatment interventions In an online setting, a similar example is to understand the impact of online advertising on search-related site visits. 
![image](/img/its.png)

Note that sophisticated modeling may be necessary to infer the impact, with an online example of ITS being Bayesian Structural Time Series analysis 

Issues: (1) Time-based effects as the comparisons are made across different points of time,like seasonality, other underlying system changes, Changing back and forth multiple times will help reduce the likelihood of that (2) User experience

Reference: 
- https://towardsdatascience.com/what-is-the-strongest-quasi-experimental-method-interrupted-time-series-period-f59fe5b00b31
- https://netflixtechblog.com/quasi-experimentation-at-netflix-566b57d2e362

#### 2.Interleaved Experiments
Interleaved experiment design is a common design used to evaluate ranking algorithm changes, such as in search engines or search at a website . In an interleaved experiment, you have two ranking algorithms, X and Y. Algorithm X would show results x 1 , x 2 , … x n in that order, and algorithm Y would show y 1 , y 2 , … y n . An interleaved experiment would intersperse results mixed together, e.g. x 1 , y 1 , x 2 , y 2 , … x n , y n with duplicate results removed. 

One way to evaluate the algorithms would be to compare the click-through rate on results from the two algorithms. While this design is a powerful experiment design, it is limited in its applicability because the results must be homogenous. 

#### 3. Regression Discountinuity Design
Regression Discontinuity Design (RDD) is a methodology that can be used whenever there is a clear threshold that identifies the Treatment population. Based on that threshold, we can reduce selection bias by identifying the population that is just below the threshold as Control and compared to the population that is just above the threshold as Treatment. 
![image](/img/rdd.png)

[Implementing RD with regresssion](https://youtu.be/tWRsYWSP3fM?t=244)

Things to worry about: 
- In RDD, the threshold discontinuity may be contaminated by other factors that share the same threshold. 
 - For example, a study of the impact of alcohol that chooses the legal age of 21 as the threshold may be contaminated by the fact that this is also the threshold for legal gambling. 
- The underlying relationship is jumpy
- Individuals are able to manipulate x to push themselves over the threshold

Fuzzy discountinuities:
- Sometimes being above a threshold means it's more likely that you'll be part of the treatment
- This is the special case of an instrumental vairable
- Use 2SLS with the instrument being a dummy for being above the threshold



#### 4. [Instrumented Variables and Natual Experiments](https://www.zhihu.com/question/29067965/answer/247659376)
Instrumental Variables (IV) is a technique that tries to approximate random assignment. Specifically, the goal is to identify an Instrument that allows us to approximate random assignment (this happens organically in a natural experiment) 

#### 5. Propensity Score Matching
Another class of approaches here is to construct comparable Control and Treatment populations, often by segmenting the users by common confounds, in something akin to stratified sampling. The idea is to ensure that the comparison between Control and Treatment population is not due to population mix changes. For example, if we are examining an exogenous change of the impact of users changing from Windows to iOS, we want to ensure that we are not measuring a demographic difference in the population. 

The key concern about PSM is that only observed covariates are accounted for; unaccounted factors may result in hidden biases. 



#### 6. [Difference in Differences](https://www.eddjberry.com/post/adjustment-in-a-b-testing/)
Randomization purpose vs Baseline difference:
- Suppose we’ve found that the customers in our treatment group historically engaged with emails at a higher rate than our control group. A common thing we might be tempted to do is test whether this baseline difference is significant (Boer et al., 2015). These tests are often used to determine “whether randomization was successful” (Boer et al., 2015), but this represents a misunderstanding of why we randomise (Harvey, 2018). **We do not randomise to ensure that there are no baseline differences between our groups, we randomise to remove any relationship between baseline values and treatment assignment.** The test of baseline differences is then testing a hypothesis we already know to be true: that there is no association between baseline engagement and treatment assignment. We know that the baseline differences are the result of chance because we did the randomisation! 2 As Bland and Altman (2011) put it (as cited by Harvey, 2018):

If testing for baseline differences in irrelevant, then what should we do? In a simple experiment where we have no covariates, we might consider doing a difference-of-differences analysis. For example, consider the table below
![image](img/did.png)

Limitation
- While a diff-of-diffs analysis is certainly a useful starting point, it has some limitations. Most commonly people point out that it assumes the coefficient predicting post-experiment outcomes from pre-experiment values is 1

When to include baseline performance
- It might be tempting to think that we only need to include baseline performance in our model when there are differences between treatment and control at baseline. However, adjusting for baseline values by including them in our model will increase the precision of our estimated treatment effect, even when there aren’t baseline differences 

Handling other covariates
- For the model without the covariates, a significant effect of treatment is found in 58.9% of samples versus 89.8% for the covariate model. In other words, including the covariates increases our ability to detect the true effect. The simple model is therefore underpowered compared to the covariate model to detect an effect of the size used here.


Geographically based experiments commonly use this technique. 

This method can also be applied even when you do not make the change, and the change happens exogenously. 
- For example, when a change was made to the minimum wage in New Jersey, researchers who wanted to study its impact on employment levels in fast-food restaurants, compared it to eastern Pennsylvania, which matched on many characteristics.

[Regression Model](https://www.publichealth.columbia.edu/research/population-health-methods/difference-difference-estimation)

DID is usually implemented as an interaction term between time and treatment group dummy variables in a regression model.
Y= β0 + β1*[Time] + β2*[Intervention] + β3*[Time*Intervention] + β4*[Covariates]+ε
#### Pitfalls
(1) Confounders (2)Spurious correlations

