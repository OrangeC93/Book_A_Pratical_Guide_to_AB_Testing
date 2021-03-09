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

#### Interrupted Time Series
Interrupted Time Series (ITS) is a quasi-experimental design, where you use the same population for Control and Treatment, and you vary what the population experiences over time. For example, the effect of police helicopter surveillance on home burglaries was estimated using multiple Treatment interventions In an online setting, a similar example is to understand the impact of online advertising on search-related site visits. 
![image](/img/its.png)

Note that sophisticated modeling may be necessary to infer the impact, with an online example of ITS being Bayesian Structural Time Series analysis 

Issues: (1) Time-based effects as the comparisons are made across different points of time,like seasonality, other underlying system changes, Changing back and forth multiple times will help reduce the likelihood of that (2) User experience

#### Interleaved Experiments
Interleaved experiment design is a common design used to evaluate ranking algorithm changes, such as in search engines or search at a website . In an interleaved experiment, you have two ranking algorithms, X and Y. Algorithm X would show results x 1 , x 2 , … x n in that order, and algorithm Y would show y 1 , y 2 , … y n . An interleaved experiment would intersperse results mixed together, e.g. x 1 , y 1 , x 2 , y 2 , … x n , y n with duplicate results removed. 

One way to evaluate the algorithms would be to compare the click-through rate on results from the two algorithms. While this design is a powerful experiment design, it is limited in its applicability because the results must be homogenous. 

#### Regression Discountinuity Design
Regression Discontinuity Design (RDD) is a methodology that can be used whenever there is a clear threshold that identifies the Treatment population. Based on that threshold, we can reduce selection bias by identifying the population that is just below the threshold as Control and compared to the population that is just above the threshold as Treatment. 
![image](/img/rdd.png)
One key issue is again confounding factors. In RDD, the threshold discontinuity may be contaminated by other factors that share the same threshold. For example, a study of the impact of alcohol that chooses the legal age of 21 as the threshold may be contaminated by the fact that this is also the threshold for legal gambling. 

#### [Instrumented Variables and Natual Experiments](https://www.zhihu.com/question/29067965/answer/247659376)
Instrumental Variables (IV) is a technique that tries to approximate random assignment. Specifically, the goal is to identify an Instrument that allows us to approximate random assignment (this happens organically in a natural experiment) 

#### Propensity Score Matching
Another class of approaches here is to construct comparable Control and Treatment populations, often by segmenting the users by common confounds, in something akin to stratified sampling. The idea is to ensure that the comparison between Control and Treatment population is not due to population mix changes. For example, if we are examining an exogenous change of the impact of users changing from Windows to iOS, we want to ensure that we are not measuring a demographic difference in the population. 

The key concern about PSM is that only observed covariates are accounted for; unaccounted factors may result in hidden biases. 



#### [Difference in Differences](https://www.eddjberry.com/post/adjustment-in-a-b-testing/)
Geographically based experiments commonly use this technique. 

Note that this method can also be applied even when you do not make the change, and the change happens exogenously. For example, when a change was made to the minimum wage in New Jersey, researchers who wanted to study its impact on employment levels in fast-food restaurants, compared it to eastern Pennsylvania, which matched on many characteristics 

#### Pitfalls
(1) Confounders (2)Spurious correlations

