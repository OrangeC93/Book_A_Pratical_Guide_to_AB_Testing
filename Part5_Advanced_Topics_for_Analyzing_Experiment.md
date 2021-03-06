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
Triggering provides experimenters with a way to improve sensitivity (statistical power) by filtering out noise created by users who could not have been impacted by the experiment. 

#### Example 1: Intentional Partial Exposure 
Suppose you are making a change and running the experiment on a segment of the population: only users from the US. You should only analyze users from the US. Note that you must include “ mixed ” users, those from both the United States and other countries, in the analysis if they could have seen the change. 

#### Example 2: Conditional Exposure
Suppose the change is to users who reach a portion of your website, such as checkout, or users who use a feature, like plotting a graph in Excel, then only analyze those users. In these examples, as soon as the user was exposed to a change, they triggered into the experiment because there was some difference. Conditional exposure is a very common triggering scenario; here are some additional examples: 
1. A change to checkout: only trigger users who started checkout. 
2. A change to collaboration, such as co-editing a document in Microsoft Word or Google Docs: only trigger users participating in collaboration. 
3. A change to the unsubscribe screen(s): only trigger users that see these changes. 
4. A change to the way the weather answer displays on a search engine results page: only trigger users who issue a query resulting in a weather answer. 

#### Example 3: Coverage Increase
Suppose that your site is offering free shipping to users with more than $35 in their shopping cart and you are testing a lower threshold of $25. A key observation is that the change only impacts users who at some point started checkout with a shopping cart between $25 and $35. Users with shopping carts over $35 and those with shopping carts under $25 have the same behavior in Treatment as Control. Only trigger users who see the free shipping offer when they have $25 to $35 in their shopping cart. For this example, we assume that no “ advertisement ” of the free shipping promotion is on the site; if at some point free shipping displays for the user and it is different between Control and Treatment, that immediately becomes a trigger point. 

#### Example 4: Coverage Change
Things become a bit more complicated when the coverage isn't increased For example, suppose Control offers free shipping to shoppers with at least $35 in their cart but Treatment offers free shipping to users with at least $25 in their cart except if they returned an item within 60 days before the experiment started. 

#### Example 5: Counterfactual Triggering for Machine Learning Models 
Suppose you have a machine learning classifier that classifies users into one of three promotions or a recommender model that recommends related products to the one shown on the page. You trained the new classifier or recommender model, and V2 did well in your offline tests. Now you expose to see if it improves your OEC (see Chapter 7 ). 

The key observation is that if the new model overlaps the old model for most users, as when making the same classifications or recommendations for the same inputs, then the Treatment effect is zero for those users. How would you know? You must generate the counterfactual. The Control would run both the model for Control and Treatment and expose users to the Control while logging the Control and Treatment (counterfactual) output; the Treatment would run both the model for Control and Treatment and expose users to the Treatment while logging the output of both models. Users are triggered if the actual and counterfactual differ. 



#### Optimal and Conservative Triggering 
If there are multiple Treatments, ideally information representing all variants is logged, the actual plus all counterfactuals. This then allows for optimal triggering of users who were impacted. However, multiple Treatments can present significant cost as multiple models must be executed to generate the counterfactuals. 

In practice, it is sometimes easier to do a non-optimal but conservative triggering, such as including more users than is optimal. This does not invalidate the analysis, but rather loses statistical power. If the conservative trigger does not identify many more users than the ideal trigger, the simplicity tradeoff may be worthwhile. Here are some examples: 
- Multiple Treatments. Any difference between the variants triggers the user into the analysis. Instead of logging the output of each variant, just log a Boolean to indicate that they differed. It is possible that the behavior for Control and Treatment1 was identical for some users but differed for Treatment2. So, when comparing just Control and Treatment1, include users with a known zero Treatment effect. 
- Post-hoc analysis. Suppose the experiment was run and there was something wrong with counterfactual logging, perhaps the recommendation model used during checkout did not properly log counterfactuals. You can use a trigger condition such as “ user-initiated checkout. ” While it identifies more users than those for which the recommendation model at checkout differed, it may still remove the 90% of users who never initiated checkout, thus had zero Treatment effect. 

#### Overall Treatment Effect 

When computing the Treatment effect on the triggered population, you must dilute the effect to the overall user base, sometimes called diluted impact or side-wide impact.
- If the change was made to the checkout process, the triggered users were those who initiated checkout. If the only way to generate revenue is to initiate checkout, then you improved both triggered and overall revenue by 3% and there is no need to dilute that percentage. 
- If the change was made to very low spenders who spend 10% of the average user, then you improved revenue by 3% for 10% of users who spend 10%, so you improved revenue by 3% of 10% of 10% = 0.03%, a negligible improvement. 
- To dilute ratio metrics, more refined formulas need to be used (Deng and Hu 2015 ). Note that ratio metrics can cause Simpson ’ s paradox (see Chapter 3 ), where the ratio in the triggered population improves, but the diluted global impact regresses. 

#### Trustworthy Triggering
There are two checks you should do to ensure a trustworthy use of triggering. We have found these to be highly valuable and they regularly point to issues. - Sample Ratio Mismatch (SRM; see Chapter 3 ). If the overall experiment has no SRM, but the triggered analysis shows an SRM, then there is some bias being introduced. Usually, the counterfactual triggering is not done properly. 
- Complement analysis. Generate a scorecard for never triggered users, and you should get an A/A scorecard (see Chapter 19 ). If more than the expected metrics are statistically significant, then there is a good chance your trigger condition is incorrect; you influenced users not included in the trigger condition. 

#### Common Pitfalls
Pitfall 1: Experimenting on Tiny Segments That Are Hard to Generalize 
- There is one important exception to this rule, which is generalizations of a small idea. For example, in Aug 2008, MSN UK ran an experiment whereby the link to Hotmail opened in a new tab, as measured by clicks/user on the homepage, by 8.9% for the triggered users who clicked the Hotmail link 

Pitfall2: A Triggered User Is Not Properly Triggered for the Remaining Experiment Duration 
- For example, assume that the Treatment provides such a terrible experience that users significantly reduce visits. If you analyze users by day or by session, you will underestimate the Treatment effect. If visits-per-user has not significantly changed statistically, you can get statistical power by looking at triggered visits. 

Pitfall 3: Performance Impact of Counterfactual Logging 
To log the counterfactual, both Control and Treatment will execute each other ’ s code (e.g., model). If the model for one variant is significantly slower than the other, this will not be visible in the controlled experiment. These two things can help: 
1. Awareness of this issue. The code can log the timing for each model so that they can be directly compared. 
2. Run an A/A ꞌ /B experiment, where A is the original system (Control), A ꞌ is the original system with counterfactual logging, and B is the new Treatment with counterfactual logging. If A and A ꞌ are significantly different, you can raise an alert that counterfactual logging is making an impact. 

It is worth noting that counterfactual logging makes it very hard to use shared controls (see Chapter 12 and Chapter 18 ), as those shared controls are typically running without code changes. In 

#### Open questions
Question 1: Triggering Unit 


Question 2: Plotting Metrics over Time 
- Plotting a metric over time with increasing numbers of users usually leads to false trends (Kohavi et al. 2012 , Chen, Liu and Xu 2019 ). It ’ s best to look at graphs over time where each day shows the user who visited that day. 
- The key issue is that the overall Treatment effect must include all days, so the single-day and overall (or cross-day) numbers do not match. 

## Sample Ratio Mismatch and other Trust Related Guardrail Metrics
Guardrail metrics are critical metrics designed to alert experimenters about violated assumptions. There are two types of guardrail metrics: **organizational and trust-related**. Chapter 7 discusses organizational guardrails that are used to protect the business, and this chapter describes the Sample Ratio Mismatch (SRM) in detail, which is a trust-related guardrail. 

#### Sample ratio mismatch
The Sample Ratio Mismatch (SRM) metric looks at the ratio of users (or other units) between two variants, usually a Treatment and the Control. If the experiment design calls for exposing a certain ratio of users (say 1:1) to the two variants, then the results should closely match the design. 

When the p-value for the Sample Ratio metric is low, that is, the probability of observing such a ratio or more extreme conditioned on the design ratio, there is a sample ratio mismatch (SRM), and all other metrics are probably invalid. You can use a standard t-test or chi-squared test to compute the p-value. 

#### Scenario 1
- Control: 821,588 users 
- Treatment: 815,482 users 
- The ratio between the two is 0.993 whereas, per design, the ratio should be 1.0. The p-value of the above .993 Sample Ratio is 1.8E-6, so You just observed an extremely unlikely event. It is therefore more likely that there is a bug in the implementation of the experiment


#### Scenario 2
![image](/img/bing_scoreboard.png)
Scorecard from Bing. The left column shows the meta-data, or metric names. The center column shows the statistics for each metric for the overall experiment. The right column shows the statistics for each metric for a segment of the population.
- The middle columns shows small pvalue but the right column represents slightly over 96% of users; the excluded users were those that used an old version of the Chrome browser, which was the cause of the SRM. Also, a bot was not properly classified due to some changes in the Treatment, causing an SRM. Without the segment, the remaining 96% of users are properly balanced, and the metrics show no statistically significant movement in the five success metrics. 

#### SRM Causes
- Buggy randomization of users. While simple Bernoulli randomization of users based on the percentages that get assigned to Control and Treatment is easy to imagine, things get more complex in practice because of ramp-up procedures(e.g., starting an experiment at 1%, and ramping up to 50%), exclusions (users in experiment X should not be in experiment Y), and attempts to balance covariates by looking at historical data.
- Data pipeline issues, such as the bot filtering mentioned in Scenario 2 above. 
- Residual effects. Experiments are sometimes restarted after fixing a bug. When the experiment is visible to users, there is a desire not to re-randomize the users, so the analysis start date is set to the point after introducing the bug fix. If the bug was serious enough for users to abandon, then there will be an SRM.
- Bad trigger condition. The trigger condition should include any user that could have been impacted. A common example is a redirect: website A redirects a percentage of users to website A ꞌ , their new website being built and tested. Because the redirect generates some loss, there will be typically be an SRM if only users that make it to website A ꞌ ; are assumed to be in the Treatment. 
- Triggering based on attributes impacted by the experiment. For example, suppose you are running a campaign on dormant users based on a dormant attribute stored in the user profile database. If the Treatment is effective enough to make some dormant users more active, then identifying users based on this attribute at the end of the experiment will give an SRM: The users that were dormant early and are now active will be excluded by the trigger condition. The analysis should be triggered to the state of the dormant attribute before the experiment started (or before each user was assigned). 

## Debugging SRMs
- Validate that there is no difference upstream of the randomization point or trigger point.
  - If you ’ re evaluating 50% off vs. two-for-one at the checkout point, you cannot mention any of these options on the homepage; if you do, you must analyze users starting from the homepage. 
- Validate that variant assignment is correct. Are users properly randomized at the top of the data pipeline? 
  - For example, suppose one experiment changes the font color from black to dark blue and a concurrent experiment starts that changes the background color, but that experiment filters to users who have their font set to black. Due to the way the code is run, the second experiment “ steals ” users from the first, but only from the font-set-to-black variant. Of course, this causes an SRM. 
Kohavi, Ron,Tang, Diane,Xu, Ya. Trustworthy Online Controlled Experiments (p. 223). Cambridge University Press. Kindle Edition. 
- Follow the stages of the data processing pipeline to see whether any cause an SRM. For example, a very common source of SRMs is bot filtering. 
- Exclude the initial period. Is it possible that both variants did not start together? 
  - Exclude the initial period. Is it possible that both variants did not start together? In 
- Look at the Sample Ratio for segments. 
- Look at the intersection with other experiments. Treatment and Control should have similar percentages of variants from other experiments. 

#### Ohter Trust related guardrial metrics
- Telemetry fidelity
- Cache hit rate
- Cookie write rate
- Quick queires are two or more search queries that arrive from the same user to a search engine within one second of each other

## Leakage and Interference between variants
We define interference as a violation of SUTVA, sometimes called spillover or leakage between the variants. 

There're two ways interference may arise: direct (social network)or indirect connection(treatment and control shared same ad campaign budget).
- Direct connections
  - Facebook/Linkedin
  - Skype call
- Indirect connections
  - Airbnb: If the AirBnB marketplace site of homes for rent improved conversion flow for Treatment users, resulting in more booking, it would naturally lead to less inventory for Control users. 
  - Uber: surge price lead the price goes up for both treatment adn control group.
  - eBay: bidding encouragement will lead to the overestimate of delta of the total number of transaction between Treatment and Control
  - Ad Campaign: Consider an experiment that shows users different rankings for the same ads. If the Treatment encourages more clicks on the ads, it uses up campaign budget faster. 
 - Relevance Model Training:Imagine a search engine that uses a simple click-based relevance model for ranking. If we use data collected from all users to train both Treatment and Control models, the longer the experiment runs, the more the “ good ” clicks from Treatment benefit Control. 
- CPU: We have experienced examples where a bug in Treatment unexpectedly held up CPUs and memory on the machines, and as a result, requests from both Treatment and Control took longer to process. 
- Sub-user experiment unit: Experiment units smaller than a user, such as a pageview, can cause potential leakage among units from the same user if there is a strong learning effect from the Treatment. 

#### Rule of Thumb： Ecosystem value of an Action
Identify actions that can potentially spill over, and only have concern about interference if these actions are materially impacted in an experiment. This usually means not only the first-order action, but also the potential responses to actions. For example: 
- Total number of messages sent, and messages responded to 

Once you identify metrics indicative of a downstream impact, you can establish general guidance for how each action. For example, how much does a message from user A translate to visit sessions from both A and their neighbors? 
- One approach for establishing this rule-of-thumb is using historical experiments shown to have downstream impact and extrapolating this impact to the downstream impact of actions X/Y/Z using the Instrumental Variable approach 

The approach does have limitations, rule-of-thumb is only an approximation and may not work for all scenarios. 
- For example, additional messages resulting from a certain Treatment may have an ecosystem impact larger than average. 

#### Isolation
- Splitting shared resources: There are two things to watch out for when applying this approach: 
  -  Can your interfering resources be split exactly according to the traffic allocation for your variants
  -  Does your traffic allocation(the resouce split size) introduce bias
- Geo-based randomization: Randomize at the region level to isolate the interference between Treatment and Control 
  - Caveat: randomizing at the geo level limits the sample size by the number of available geo locations. This leads to a bigger variance and less power for A/B tests. See Chapter 18 for discussions on reducing variance and achieving better power
- Time-basedd randomization:
At any time t, you could flip a coin and decide whether to give all users Treatment or all users Control. above). The time unit can be short (seconds) or long (weeks), depending on what is practical and how many sample points you need. 
  - One thing to keep in mind is that there is usually strong temporal variation, like the day-of-week or hour-of-day effects. This usually helps reduce variance by utilizing this information in paired **t-tests** or **covariate adjustments**.(Chapter 18) for more details. A similar technique called **interrupted time series (ITS)**(Chapter 11). 
- Network cluster randomization: Similar to geo-based randomization, on social networks, we construct “ clusters ” of nodes that are close to each other based on their likelihood to interfere. We use each cluster as “ mega ” units and randomize them independently into Treatment or Control groups 
  - It's rare to have perfect isolation in practice: For example, when attempting to create 10,000 isolated and balanced clusters from the entire LinkedIn graph, there were still more than 80% of the connections between clusters.
  - Like other mega-unit randomization approaches, the effective sample size (number of clusters) is usually small, which leads to a variance-bias tradeoff when we built the clusters. The larger number of clusters leads to a smaller variance, but also gave us a larger bias with less isolation. 
- Network ego centric randomziation


Whenever applicable, always combine isolation methods to get a larger sample size. For 


#### Edge level analysis
Use Bernoulli randomization on users and then label the edges based on the experiment assignment of the users (nodes) as one of these four types: Treatment-to-Treatment, Treatment-to-Control, Control-to-Control, and Control-to-Treatment. Contrasting interactions (e.g., messages, likes) that happen on different edges, allows you to understand important network effects. 
- For example, use the contrast between the Treatment-to-Treatment and Control-to-Control edges to estimate unbiased delta, or identify whether units in Treatment prefer to message other Treatment units over Control units (Treatment affinity), and whether new actions created by Treatment get a higher response rate. 



## Measuring Long-Term Treatment
#### Reasons the treatment effect may differ between short term and long term
- User learned effects
- Network effects
- Delayed experience and measurement
- Ecosystem change
  - launching other new features
  - seasonality
  - competitive landscape
  - Goverment policies
  - Concept drift: The performance of machine learning models trained on data that is not refreshed may degrade over time as distributions change. 
  - Software rot
#### Why measure long-term effects?
- Attribution
- Institutional learning
  - What is the difference between short term and long term? If the difference is sizable, what is causing it? If there is a strong novelty effect, this may indicate a suboptimal user experience. 
- Generalization

#### Long Running Experiments
You can measure the Treatment effect at the beginning of the experiment (in the first week) and at the end of the experiment (in the last week). Note that this analysis approach differs from a typical experiment analysis that would measure the average effect over the entire Treatment period. 
![image](/img/long_term_effect.png)
There're challenges:
- For attribution: The measurement from the last week of the long-running experiment ( p Δ T ) may not represent the true long-term Treatment effect for the following reasons: 
  - Treatment effect dilution
  - The user may use multiple devices or entry point
  - If you randomize the experiment units based on cookies, cookies can churn due to user behavior or get clobbered due to browser issues 
  - If network effects are present, unless you have perfect isolation between the variants, the Treatment effect can “ leak ” from Treatment to Control
  - Survivorship bias. Not all users at the beginning of the experiment will survive to the end of the experiment, if the survivial rate is different between Treatment and Control, which should also trigger an SRM alert.
  - Interaction with other new features
  - For measuring a time-extrapolated effect

#### Alternative Methods for long-running experiments
Cohort Analysis:
- There are two important considerations to keep in mind: 
  - You need to evaluate how stable the cohort is For example, if the ID is based on cookies and when cookie churn rate is high, this method does not work well for correcting bias 
  - If the cohort is not representative of the overall population, there may be external validity concerns because the analysis results may not be generalizable to the full population. You can use additional methods to improve the generalizability, such as a weighting adjustment based on stratification 

Post-Period Analysis
![image](/imag/post_period_analysis.png)
Time Staggered Treatment
![image](/imag/time_staggered_treatment.png)
Holdback and reverse experiment
- keeping 10% of users in Control for several weeks (or months) after launching the Treatment to 90% users 
- we ramp 10% of users back into the Control several weeks (or months) after launching the Treatment to 100% of users. 










 


 
