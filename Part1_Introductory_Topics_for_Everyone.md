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

