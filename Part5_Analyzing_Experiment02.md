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
