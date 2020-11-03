## 1 Introduction and Motivation
```
Introduction Example: 2012, an Bing employee suggest changing the ad headline display, which turns out the best revenue-generation idea. 
Bing uses an OEC that weighs revenue against user-experience metrics, including Sessions per user (are users abandoning or increasing engagement) and several other components. The key point is that user-experience metrics did not significantly degrade even though revenue increased dramatically. 
```
#### Online controlled experiments terminology
OEC: A quantitative measure of the experiment's objective which must be measurable in the short term yet believed to casusally drive long term strategic objectives. In the search case, OEC can be a combination of usage(session per user), relevance(successful sessions, time to success) and ad revenue

Parameter: A controllable experimental variable that is thought to influence the OEC or other metrics of interest. 

Variant: A user experience being tested, typically by assigning values to parameters. In a simple A/B test, A and B are the two variants, usually called Control and Treatment. 

Randomization Unit: A pseudo-randomization (e.g.hashing) process is applied to units (e.g., users or pages) to map them to variants. 

#### Why experiment? Correlations, Causality and Trustworthiness
```
Example1： X% users churn every month, you introduce a new feature and observe that churn rate for users using that feature is X%/2, you might be tempted to claim causality, the feature is reducing churn by half.

Example2:
Office 365 users that see error messages and experience crashes have lower churn rates, but that does not mean that Office 365 should show more error messages or that Microsoft should lower code quality, causing more crashes. It turns out that all three events are caused by a single factor: usage. Heavy users of the product see more error messages, experience more crashes, and have lower churn rates. Correlation does not imply causality and overly relying on these observations leads to faulty decisions. 
```

We believe online controleed experiments are (1) best scientific way to establish causality with high probability (2) able to detect small changes that are harder to detect with other techniques, such as changes overtime (sensitivity) (3) able to detect unexpected changes, often underappreciated, but many experiments uncover suprising impacts on other metrics, be it performance degradation, increased crashes/errors, or cannibalizing clicks from other features

#### Necessary ingredients for running useful controlled experiments
1. There're experimental units taht can be assigned to different variants with no interference or little interference. 
2. There're enough experimental units.
3. OEC are agreed upon and can be pratically evaluated.
4. Changes are easy to make

When controlled experiments are not possible, modeling could be done, and other experimental techniques might be used (see Chapter 10 ). The key is that if controlled experiments can be run, they provide the most reliable and sensitive mechanism to evaluate changes. 

#### Tenets
1. The organization wants to make data-driven decisions and has formalized an OEC. 
2. The organization is willing to invest in the infrastructure and tests to run controlled experiments and ensure that the results are trustworthy. 
3. The organization recognizes that it is poor at assessing the value of ideas. 

#### Improvements over Time
Changes are improved over time and step by step. 

#### Strategy, Tactics, and Their Relationship to Experiments



## 2 Running and Analyzing Experiments
#### An example: 
Add a coupon code field to the checkout page, and test two different UIs, and would like to evaluate the impact on revenue, the hypothesis is adding a coupon code field to the checkout page will degrade revenue. Treatment1: coupon or gift code field below credit card information, Treatment2: coupon or gift code as a popup.
- OEC： they do not recommend the overall revenue since it depends on the number of users in each varaint, so a normalized metric by the actual sample size, like **making revenue per users** is a good OEC, but how to determin the denominator
  - All users who visited the site: we need to exclude the people who does not initiated checkout since they'll not be impacted by the changes. 
  - Only users who complete the purchase process: it's incorrect, as it assumes that the change will impact the amount purchased, not the percentage of users who complete the purchase, if more users purchase, revenue per user may drop even though total revenue increases.
  - Only users who start the purchase process: this is the best choice, given where the change is in the funnel, we include all potentially affected users, but no unaffected who dilute the result.
 
#### Designing the experiment:
What's the randomization unit: 
- Users.

What's the population of radomization units do we want to target: 
- If we want to detect a smail change or a lower p value thereshold, we need to increase the sample size.
- If it's for large changes where you're uncertain about how users might react, you may want to start with a smaller proportion.
- Does this experiment need to share traffic with other experiments?

How long to run the experiment:
- More users
- Day of week effect
- Seasonality
- Primary and novelty effect


The experiment design is now as follows: 
1. The randomization unit is a user. 
2. We will target all users and analyze those who visit the checkout page. 
3. To have 80% power to detect at least a 1% change in revenue-per-user, we will conduct a power analysis to determine size. 
4. This translates into running the experiment for a minimum of four days with a 34/33/33% split among Control/Treatment one/Treatment two. We will run the experiment for a full week to ensure that 

#### Running the Experiment and Getting Data 
To run an experiment, we need indtrumentation to get logs data on how users are interacting with the site asn infrasture to be able to run an experiment, ranging from experiment configureation to variant assignment.

#### Interpreting the Results
Sanity check: look at the guardrail metrics or invariants. These metrics should not change between the Control and Treatment. There are two types of invariant metrics: 
1. Trust-related guardrail metrics, such as expecting the Control and Treatment samples to be sized according to the configuration or that they have the same cache-hit rates. 
2. Organizational guardrail metrics, such as latency, which are important to the organization and expected to be an invariant for many experiments. In the checkout experiment, it would be very surprising if latency changed.  

#### From Results to Decision
We need to consider more under a broader context:
- Tradeoff between different metrics: user engagement goes up but revenue goes down 
- The cost of launching: (1) build (2)maintenanace
- Downside of making wrong decision

We may need to update the thresholds pior to the start of the experiment to reflect the broader context: 
![image](/img/figure2.4.png)
```
1. The result is not statistically significant and also no pratical significance
2. The result is statistically and practically significant. Again, an easy decision: launch! 
3. The result is statistically significant but not practically significant. In this case, you are confident about the magnitude of change, but that magnitude may not be sufficient to outweigh other factors such as cost. This change may not be worth launching. 
4. Consider this example neutral, like our first example; however, the confidence intervals are outside of what is practically significant. If you run an experiment and find out it could either increase or decrease revenue by 10%, would you really accept that experiment and say that change is neutral? It ’ s better to say you do not have enough power to draw a strong conclusion, and it is also such that we do not have enough data to make any launch decision. For this result, we recommend running a follow-up test with more units, providing greater statistical power. 
5. The result is likely practically significant but not statistically significant. So even though your best guess is that this change has an impact you care about, there is also a good chance that there is no impact at all. From a measurement perspective, the best recommendation would be to repeat this test but with greater power to gain more precision in the result. 
6. The result is statistically significant, and likely practically significant. Like 5, it is possible that the change is not practically significant. Thus here, like the prior example, we suggest repeating the test with more power. From a launch/no-launch decision, however, choosing to launch is a reasonable decision. 
```

## 3 Twyman's Law and Experimentation Trustworthiness 
#### Misinterpretation of the Statistical Results
- Lack of Statistical Power: a common mistake is to assume that just because a metric is not statistically significant, there is no Treatment effect. It could very well be that the experiment is underpowered to detect the effect size we are seeing, that is, there are not enough users in the test. For example, an evaluation of 115 A/B tests at GoodUI.org suggests that most were underpowered.
- Misinterpreting p-values: the p-value is the probability of obtaining a result equal to or more extreme than what was observed, assuming that the Null hypothesis is true.
  - Here's one incorrect statement: p-value = .05 means that if you reject the Null hypothesis, the probability of a false positive is only 5%. 
- Peeking at pvalues: when running an online controlled experiment, you could continuously monitor the p-values. Here are two alternatives: Optimizely implemented a solution based on the first method, whereas the experimentation platforms being used at Google, LinkedIn, and Microsoft use the second. 
  1. Use sequential tests with always valid p-values.
  2. Use a predetermined experiment duration, such as a week, for the determining statistical significance. 
- Multiple Hypothesis Test: when there are multiple tests, and we choose the lowest p-value, our estimates of the p-value and the effect size are likely to be biased.
- Confidence Intervals: there is a duality between p-values and confidence intervals. For the Null hypothesis of no-difference commonly used in controlled experiments, a 95% confidence interval of the Treatment effect that does not cross zero implies that the p-value is < 0.05. 
  - A common mistake is to look at the confidence intervals separately for the Control and Treatment, and assume that if they overlap, the Treatment effect is not statistically different. The opposite, however, is true: if the 95% confidence intervals do not overlap, then the Treatment effect is statistically significant with p-value < 0.05. 
  - Another common misunderstanding about confidence intervals is the belief that the presented 95% confidence interval has a 95% chance of containing the true Treatment effect. 

#### Threats to Internal Validity 
- Violation of SUTVA: experiment units (e.g., users) do not interfere with one another. The assumption could clearly be violated in settings, including the following: (1)Social Networks (2)Skype(communication tool) (3)Document authoring tool, such as Microsoft Office and Google Docs (4)Two-sided marketingplaces, such as auctions, Airbnb, eBay, Lift or Uber (5)Shared resources, such as CPU, storage and caches

- Survivorship Bias: analyzing users who have been active for some time (e.g., two months) introduces survivorship bias. 

- [Intention-to-Treat](https://zhuanlan.zhihu.com/p/93174068):
In some experiments, there is non-random attrition from the variants. For example, in medical settings, patients in a Treatment may stop taking a medication if it has side effects. In the online world, you may offer all advertisers the opportunity to optimize their ad campaign, but only some advertisers choose to do the suggested optimization. Analyzing only those who participate, results in selection bias and commonly overstates the Treatment effect. Intention-to-treat uses the initial assignment, whether it was executed or not. The Treatment effect we are measuring is therefore based on the offer, or intention to treat, not whether it was actually applied. 

- [Sample Ratio Mismatch(SRM)](http://www.woshipm.com/pmd/3759717.html): if the ratio of users (or any randomization unit) between the variants is not close to the designed ratio, the experiment suffers from a Sample Ratio Mismatch (SRM). For example, if the experiment design is for a ratio of one-to-one (equally sized Control and Treatment), then deviations in the actual ratio of users in an experiment likely indicate a problem that requires debugging. Here're some examples:
  - Browser redirects: redirect the treatment to another page, there're several reasons: (1) a performance differences: users in the treatment group suffer an extra redirect (2) bots: robots handle redirects differently (3) redirects are asymmetirc: treated users may bookmark it or pass the link to their friends.
  - Lossy instrumentation: ctr is known to be lossy, which is not normally an issue as the loss is similar for all variants but sometimes the Treatment can impact the loss rate, making low-activity users appear at a different rate and cause an SRM
  - Residual or carryover effects: new experiment may involve bugs, even though after a quick fix and restart the experiment, some users were already impacted, so it's important to run pre-experiment A/Atest. The opposite could also be true, sometimes when a experiment was stopped and restarted, there was a significant carryover effect from the prior experiment, which is enough to create an SRM and invalidate the results

  - Bad has function for randomization: zhao et al. ( 2016 ) describe how Treatment assignment was done at Yahoo! using the Fowler-Noll-Vo hash function, which sufficed for single-layer randomization, but which failed to properly distribute users in multiple concurrent experiments when the system was generalized to overlapping experiments. Cryptographic hash functions like MD5 are good (Kohavi et al. 2009 ) but slow; a non-cryptographic function used at Microsoft is Jenkins SpookyHash 

  - Triggering impacted by Treatment: If triggering is done based on attributes that are changing over time, then you must ensure that no attributes used for triggering could be impacted by the Treatment. 

  - Time of Day Effects
  - Data pipeline impacted by Treatment: Bot filtering is a serious problem, especially for search engines. For Bing, over 50% of US traffic is from bots, and that number is higher than 90% in China and Russia. 

#### Threats to External Validity
An important check for primacy and novelty effect s is to plot usage over time and see whether it ’ s increasing or decreasing. 

#### Segment Differences
Analyzing a metric by different segments can provide interesting insights and lead to discoveries, What are good segments? Here are several: （1）market or country (2) device or platform (3) time of day and day of week (4) user type (5) user account characteristic

Segmented views are commonly used two ways: 
1. Segmented view of a metric, independent of any experiment. 
2. Segmented view of the Treatment effect for a metric, in the context of an experiment, referred to in Statistics as heterogeneous Treatment effects , indicating that the Treatment effect is not homogenous or uniform across different segments. 


#### [Simpson's Paradox](https://towardsdatascience.com/solving-simpsons-paradox-e85433c68d03)
#### Encourage Healthy Skepticism 
