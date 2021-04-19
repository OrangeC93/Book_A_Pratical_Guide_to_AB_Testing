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
- Attribution: This type of attribution is challenging because we need to consider both endogenous reasons such as user-learned effects, and exogenous reasons such as competitive landscape changes.
- Institutional learning
  - What is the difference between short term and long term? If the difference is sizable, what is causing it? If there is a strong novelty effect, this may indicate a suboptimal user experience. 
  - For example, if it takes a user too long to discover a new feature they like, you may expedite uptake by using in-product education. On the other hand, if many users are attracted to the new feature but only try it once, it may indicate low quality or click-bait. 
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
(1)Cohort Analysis:
- There are two important considerations to keep in mind: 
  - You need to evaluate how stable the cohort is For example, if the ID is based on cookies and when cookie churn rate is high, this method does not work well for correcting bias 
  - If the cohort is not representative of the overall population, there may be external validity concerns because the analysis results may not be generalizable to the full population. You can use additional methods to improve the generalizability, such as a weighting adjustment based on stratification 

(2)Post-Period Analysis
![image](/img/post_period_analysis.png)
(3)Time Staggered Treatment
- Measuring long-term effect after we observe the two time-staggered Treatments have converged 
- Note that it is important to determine the practically significant delta and ensure that the comparison has enough statistical power to detect it. At this point, we can apply the post-period method after time t to measure the long-term effect. And this method assumes that the difference between the two Treatments grows smaller over time. 
- In practice, you also need to ensure that there is enough time gap between the two staggered Treatments. If the learned effect takes some time to manifest, and the two Treatments start right after one another, there may not be enough time for the two Treatments to have a difference at the start of T1.
![image](/img/time_staggered_treatment.png).

(4)Holdback and reverse experiment
- keeping 10% of users in Control for several weeks (or months) after launching the Treatment to 90% users 
- we ramp 10% of users back into the Control several weeks (or months) after launching the Treatment to 100% of users. 
