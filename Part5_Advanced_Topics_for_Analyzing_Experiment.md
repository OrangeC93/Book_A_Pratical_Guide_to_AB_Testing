## The Statistics behind Online Controlled Experiments
#### Two sample t test
#### p value and confidence interval
P value, the probabiity that t would be at least this extreme if there really is no difference between Treatment and Control.

Common mistake for p value interpretation:
- p-value captures the probability that the Null hypothesis is true given the data observed
  - We could use Byes rule to break it down: As indicated in the equation, to know whether the Null hypothesis is true based on data collected (posterior probability), you not only need a p-value but also the likelihood that the Null hypothesis is true. 
![image](/img/pvalue_bayes.png)

Another way to examine whether the delta is statistically significant is to check whether the **confidence interval** overlaps with zero. 
- The delta is statistically significant at 0.05 significance level if the 95% confidence interval does not contain zero or if the p-value is less than 0.05. 

#### Normality Assumption
Many people consider **the sample distribution of the metric Y** is a poor assumption because almost none of the metrics used in practice follow a normal distribution. However, **the average Y** usually does because of the Central Limit Theorem. As the sample size increases, the distribution of the mean Y becomes more normally distributed. 

One rule-of-thumb for the minimum number of samples needed for the average Y to have normal distribution is 355 s^2 for each variant: s is the skewness coefficient of the sample distribution of the metric Y defined as below

![image](/img/skewness.png)

- Some metrics, especially revenue metrics , tend to have a high skewness coefficient . One effective way to reduce skewness is to transform the metric or cap the values. For example, after Bing capped Revenue/User to $10 per user per week, they saw skewness drop from 18 to 5, and the minimum sample needed drop tenfold from 114k to 10k. This rule-of-thumb provides good guidance for when | s | > 1 but does not offer a useful lower bound when the distribution is symmetric or has small skewness. On the other hand, it is generally true that fewer samples are needed when skewness is smaller.
- For two-sample t-tests, because you are looking at the difference of the two variables with similar distributions, the number of samples needed for the normality assumption to be plausible tends to be fewer. This is especially the case if Treatment and Control have equal traffic allocations 
- If you ever wonder whether your sample size is large enough to assume normality, test it at least once with offline simulation . You can randomly shuffle samples across Treatment and Control to generate the null distribution and compare that distribution with the normal curve using statistical tests such as Kolmogorov – Smirnov and Anderson-Darling 

#### Type I/II Erros and Power
A Type I error is concluding that there is a significant difference between Treatment and Control when there is no real difference. A Type II error is when we conclude that there is no significant difference when there really is one. Power is the probability of detecting a difference between the variants , that is, rejecting the null, when there really is a difference.

Clearly, there is a tradeoff between these two errors. Using a higher p-value threshold means a higher Type I error rate but a smaller chance of missing a real difference, therefore a lower Type II error rate. 

Challenge:
- For online experiments, sample size estimation is more complex because online users visit over time, so the duration of the experiment also plays a role in the actual sample size of an experiment. Depending on the randomization unit, the sample variance σ 2 can also change over time. 
- Another challenge is that with triggered analysis (see Chapter 20 ), the values σ 2 and δ change as the trigger conditions change across experiments. For these reasons, we present a more practical approach in Chapter 15 for deciding traffic allocation and the duration for most online experiments. 

Sample size & Power
![image](/img/sample_size_mean.png)
![image](/img/sample_size_proportion.png)

Misinterpretation of Power
Many people consider power an absolute property of a test and forget that it is relative to the size of the effect you want to detect. An experiment that has enough power to detect a 10% difference does not necessarily have enough power to detect a 1% difference. 

#### Bias (chapter3)

#### Multiple Testing
- https://zhuanlan.zhihu.com/p/45715632
- https://www.invespcro.com/blog/multiple-testing-problem-how-adding-more-variations-to-your-ab-test-will-impact-your-results/
- http://home.uchicago.edu/amshaikh/webfiles/palgrave.pdf

Multiple testing refers to any instance that involves the simultaneous testing of several hypotheses. This scenario is quite common in much of empirical research in economics. Some examples include: 
- one fits a multiple regression model and wishes to decide which coefficients are different from zero; 
- one compares several forecasting strategies to a benchmark and wishes to decide which strategies are outperforming the benchmark;
- one evaluatesa program with respect to multiple outcomes and wishes to decide for which outcomes the program yields significant effects

If one does not take the multiplicity of tests into account, then the probability that some of the true null hypotheses are rejected by chance alone may be unduly large. Take the case of S = 100 hypotheses being tested at the same time, all of them being true, with the size and level of each test exactly equal to α. For α = 0.05, one expects five true hypotheses to be rejected. Further, if all tests are mutually independent, then the probability that at least one
true null hypothesis will be rejected is given by 1 − 1-0.05^100 = 0.994. The graph below illustrates how the overall type I error increases as the number of tests increases:
![image](/img/test_error_number_of_test.png)

**Bonferroni and Hochberg and BHY adjustment**

The simplest solution that works for a limited number of comparisons is using statistical methods correction for multiple testing, which rely on some statistical adjustments made to p-values with the goal of reducing the chances of obtaining false-positive results. 
- Advantage: Simple. For example, the significance level for a single test would be 0.05 divided by 3 (number of variations)
- Disadvantage: Too conservative. In a sense, using the Bonferroni correction, we are more likely not to state any difference between the control and any other variation.

code: https://towardsdatascience.com/an-overview-of-the-multiple-comparison-problem-166aa5fcaac5 
#### Fisher's meta analysis
Fisher ’ s method (or any other meta-analysis technique) is great for increasing power and reducing false-positives. You may have an experiment that is underpowered even after applying all power-increasing techniques, such as maximum power traffic allocation (see Chapter 15 ) and variance reduction (see Chapter 22 ). In this case, you can consider two or more (orthogonal) replications of the same experiment (one after another) and achieve higher power by combining the results using Fisher ’ s method. 

## Variance Estimation and Improved Sensitivity: Pitfalls and Solutions
#### Delta vs Delta %
It is very common to use the relative difference instead of the absolute difference when reporting results from an experiment. 
![image](/img/pert_delta.png)

#### Ratio Metrics When analysis unit is different from experiment unit
Many important metrics come from the ratio of two metrics. For example, click-through rate (CTR) is usually defined as the ratio of total clicks to total pageviews; revenue-per-click is defined as the ratio of total revenue to total clicks. Unlike metrics such as clicks-per-user or revenue-per-user。

The variance formula assumption is satisfied if the analysis unit is the same as the experimental (randomization) unit. It is usually violated otherwise. 
- For user-level metrics , each Yi represents the measurement for a user. The analysis unit matches the experiment unit and hence the assumption is valid. 
- However, for page-level metrics, each Yi represents a measurement for a page while the experiment is randomized by user, so Y1 , Y2  and Y3 could all be from the same user and are “ correlated. ” Because of such “ within user correlation, ” variance computed using the simple formula would be biased. 


Note that there are metrics that cannot be written in the form of the ratio of two user-level metrics, for example, 90th percentile of page load time. For these metrics, we may need to resort to bootstrap method (Efron and Tibshriani 1994 ) where you simulate randomization by sampling with replacement and estimate the variance from many repeated simulations. 

#### Outliers

It is critical to remove outliers when estimating variance. A practical and effective method is to simply cap observations at a reasonable threshold. For example, human users are unlikely to perform a search over 500 times or have over 1,000 pageviews in one day. There are many other outlier removal techniques as well 

#### Improving sensitivity
The ability of detecting the Treatment effect when it exists is generally referred to as power or sensitivity. One way to improve sensitivity is reducing variance. Here are some of the many ways to achieve a smaller variance: 
- Create an evaluation metric with a smaller variance while capturing similar information. 
  - Purchase amount (real valued) has higher variance than purchase (Boolean). Kohavi et al. ( 2009 ) gives a concrete example where using conversion rate instead of purchasing spend reduced the sample size needed by a factor of 3.3. 
- Transform a metric through capping , binarization , or log transformation . 
  - For example, instead of using average streaming hour, Netflix uses binary metrics to indicate whether the user streamed more than x hours in a specified time period. 
  - For heavy long-tailed metrics, consider log transformation, especially if interpretability is not a concern, like revenue metric.
- Use triggered analysis. This is a great way to remove noise introduced by people not affected by the Treatment. 
- Use stratification , Control-variates or CUPED. 
  - In stratification, you divide the sampling region into strata, sample within each stratum separately, and then combine results from individual strata for the overall estimate, which usually has smaller variance than estimating without stratification. The common strata include platforms (desktop and mobile), browser types (Chrome, Firefox and Edge) and day of week and so on. 
  - While stratification is most commonly conducted during the sampling phase (at runtime), it is usually expensive to implement at large scale. Therefore, most applications use post-stratification, which applies stratification retrospectively during the analysis phase. When the sample size is large, this performs like stratified sampling, though it may not reduce variance as well if the sample size is small and variability among samples is big. Control-variates is based on a similar idea, but it uses covariates as regression variables instead of using them to construct the strata. 
- Randomize at a more granular unit. 
  - For example, if you care about the page load time metric, you can substantially increase sample size by randomizing per page. Note that there are disadvantages with a randomization unit smaller than a user:  If the experiment is about making a noticeable change to the UI, giving the same user inconsistent UIs makes it a bad user experience. 
- Design a paired experiment. 
  - **Pool Control groups**. If you have several experiments splitting traffic and each has their own Control, consider pooling the separate controls to form a larger, shared Control group. Comparing each Treatment with this shared Control group increases the power for all experiments involved. If you know the sizes of all Treatments you ’ re comparing the Control group with, you can mathematically derive the optimal size for the shared Control. Here are considerations for implementing this in practice: 
    - If each experiment has its own trigger condition, it may be hard to instrument them all on the same Control. 
    - You may want to compare Treatments against each other directly. How much does statistical power matter in such comparisons relative to testing against the Control? 
    - There are benefits of having the same sized Treatment and Control in the comparison, even though the pooled Control is more than likely bigger than the Treatment groups. Balanced variants lead to a faster normality convergence (see Chapter 17 ) and less potential concern about cache sizes (depending on how you cache implementation). 

#### Variance of other statistics
When it comes to time-based metrics, such as page-load-time (PLT), it is common to use quantiles, not the mean, to measure site-speed performance. For instance, the 90th or 95th percentiles usually measure user engagement-related load times, while the 99th percentile is more often server-side latency measurements. While you can always resort to bootstrap for conducting the statistical test by finding the tail probabilities, it gets expensive computationally as data size grows. On the other hand, if the statistic follows a normal distribution asymptotically, you can estimate variance cheaply. For example, the asymptotic variance for quantile metrics is a function of the density 

There is another layer of complication. Most time-based metrics are at the event/page level, while the experiment is randomized at user level. In this case, apply a combination of density estimation and the delta method 

## A/A Test
#### Why A/A Tests?
- Ensure that Type I errors are controlled (e.g., at 5%) as expected. 
- Assessing metrics ’ variability. 
- Ensure that no bias exists between Treatment and Control users, especially if reusing populations from prior experiments. 
- Compare data to the system of record. 
- Estimate variances for statistical power calculations. A/A tests provide variances of metrics that can help determine how long to run your A/B tests for a given minimal detectable effect. 

#### Example1: Analysis Unit Differs from Randomization Unit 

- The first is to count the clicks and divide by the number of page views; 
- The second is to average each user ’ s CTR and then average all the CTRs. 

There is no right or wrong in these definitions, both are useful definitions for CTR, but using different user averages yields different results. In practice, it is common to expose both metrics in scorecards, although we generally recommend definition 2 as we find it more robust to outliers, such as bots having many pageviews or clicking often. 

#### Example 2: Optimizely Encouraged Stopping When Results Were Statistically Significant 

#### Example 3: Browser Redirects 
Our experience is that redirects usually fail A/A tests. Either build things so that there are no redirects (e.g., server-side returns one of two home pages) or execute a redirect for both Control and Treatment (which degrades the Control group). 

#### Example 4: Unequal Percentages 
 
- Uneven splits (e.g., 10%/90%) may suffer from shared resources providing a clear benefit to the larger variant (Kohavi and Longbotham 2010 , section 4). Specifically, least recently used (LRU) caches shared between Control and Treatment have more cache entries for the larger variant 
- Another problem with unequal percentages is that the rate of convergence to a Normal Distribution is different. 

##### Example 5: Hardware Difference
Facebook had a service running on a fleet of machines. They built a new V2 of the service and wanted to A/B test it. They ran an A/A test between the new and old fleet, and even though they thought the hardware was identical, it failed the A/A test. Small hardware differences can lead to unexpected differences 

#### How to Run A/A Tests
Running a thousand A/A tests may be expensive, but here's a little trick you can use: replay the last week. This of course, assumes that you stored the relevant raw data. This is an example of why we say to store your data for running future tests and applying newly developed metrics. There are limits to this approach, of course: you will not catch performance issues or shared resources such as the LRU cache mentioned above, but it is a highly valuable exercise that leads to identifying many issues. 

Because you are not really making a change to your product and the two variants being tested are identical, you can just simulate the A/A test. For each iteration, pick a new randomization hash seed for user assignment and replay the last week of data, splitting users into the two groups. Then generate the p-value for each metric of interest (usually tens to hundreds of metrics) and accumulate them into histograms, one for each metric. 

#### When the A/A Test fails
1. The distribution is skewed and clearly not close to uniform. 
  - Is the independence assumption violated (as in the CTR example) because the randomization unit differs from the analysis unit? If so, deploy the delta method or bootstrapping. 
  - Does the metric have a highly skewed distribution? Normal approximation may fail for a small number of users. In some cases, the minimum sample size may need to be over 100,000 users. Capped metrics or setting minimum sample sizes may be necessary.
2. There is a large mass around p-value of 0.32, indicating a problem with outliers. 
3. The distribution has a few point masses with large gaps. This happens when the data is single-valued (e.g., 0) with a few rare instances of non-zero values. The delta of the means can only take a few discrete values in such scenarios, and hence the p-value can only take a few values. Here again, the t-test is not accurate, but this is not as serious as the prior scenario, because if a new Treatment causes the rare event to happen often, the Treatment effect will be large and statistically

## Triggering for Improved Sensitivity 
