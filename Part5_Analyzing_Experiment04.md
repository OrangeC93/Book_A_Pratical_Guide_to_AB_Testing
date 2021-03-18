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
![image](/img/post_period_analysis.png)
Time Staggered Treatment
![image](/img/time_staggered_treatment.png)
Holdback and reverse experiment
- keeping 10% of users in Control for several weeks (or months) after launching the Treatment to 90% users 
- we ramp 10% of users back into the Control several weeks (or months) after launching the Treatment to 100% of users. 
