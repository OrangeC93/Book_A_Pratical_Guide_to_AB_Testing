## Metrics for Experimentation and the Overall Evaluation Criterion 
#### Expeirmentation Metrics Taxonomy
- Data Quality metircs: are reuslts trustworthy?SRM
- OEC: was the treatment successful
- Guardrial metrics: did the treatment cause an unacceptable harm to key metircs
- Local feature and diagnostic metrics: why OEC and guardrail metrics moved or did not move

#### From Business Metrics to Metrics Appropriate for Experimentation
Experimentation metrics must be measurable, attributasble, sensitive and timely. Then you may need to further augment that metric set with: 
- (1) surrogate metrics 
- (2) more granular metrics
- (3) trustworthiness guardrails and data quality metircs 
- (4) diagnostic and debug metircs 

Some examples about sensitivity: 
- (1) Ads revenue, it's common for a few outliers to have a disproportionally high influence on revenue, which make it harder to detect treatment effects, for this reason, youc could consider a truncated version of revenue for experiments as an additional more sensitive metric 
- (2) Consider a yearly renewal subscription business, for this case, instead of using renewal rate in experiments, it's common to find surrogate metrics, such as usage, which are early indicators of satisfaction that will lead to renewals.

OEC vs KPIs (Key Performance Indicators)
- KPIs are lagging metrics reported monthly/quarterly/yearly at the overall product level (DAU, MAU, Revenue, etc.)
- OEC is a leading metric measured during the experiment (e.g. 2 weeks) at user level, which is indicative of long term increase in KPIs

#### Combining Key Metrics into an OEC

Oftentimes, there is a mental model of the tradeoffs, and devising a single metric – an OEC – that is a weighted combination of such objectives may be the more desired solution. If you have multiple metrics , one possibility proposed by Roy ( 2001 ) is to normalize each metric to a predefined range, say 0 – 1, and assign each a weight. Your OEC is the weighted sum of the normalized metrics. 

If you are unable to combine your key metrics into a single OEC, try to minimize the number of key metrics.
```
When you have  k (independent) metrics, the probability of having at least one p-value < 0.05 is 1 − (1 − 0.05) k .  For  k  = 5 , you have a 23% probability of seeing something statistically significant.  For  k  = 10 , that probability rises to 40%. The more metrics you have, the higher the chance that one would be significant, causing potential conflicts or questions. 
```

#### Example1: OEC for Email at Amazon
- Previous: "fitness fucntion", give credit to a program based on the revenue it generated from users clicking through the email. 
- Problem: the click-through revenue OEC is optimizing for short-term revenue instead of user lifetime value. 
- After: (Revenue - s* unsubscribe LTV Loss)/n

#### Example2: OEC for Bing search Engine
The two key search engine (Bing, Google) KPIs are Query Share (distinct queries) and Revenue, Should OEC be Queries/User and Revenue/User?
- A ranking bug in an experiment resulted in very poor search results. Degraded (algorithmic) search results cause users to search more to complete their task, and ads appear more relevant. Therefore, Distinct queries went up over 10%, and revenue went up over 30%

Queries/Month = Users/Month * Sessions/Users * Distinct queries/Session
- Users per month: determined by experiment design
- Distinct queries per task (or per session as a surrogate): it's hard to measure since we want users to find answers and complete tasks quickly, so queries/session should be smaller, but it may also means abandoment. 
- Session per user: key metric to optimize, satisfied users viit more often 

#### Other examples:
Netflix: 
- Subscription business
- KPI: Retention (i.e. the fraction of users who return month over month)
- OEC: Viewing Hours. Strong correlation between viewing hours and retention

In fact, the Netflix team found a very strong correlation between viewing hours and retention. So, for instance, if a user watched only one hour of Netflix per month, then they were not as likely to renew their monthly subscription as if they watched 15 hours of Netflix per month. As a result, the Netflix team used viewing hours (or content consumption) as their strongest proxy metric for retention, and many tests at Netflix had the goal of increasing the number of hours users streamed.


Coursera: 
- Care about course completion, make money by users pay for certifications
- KPIs: Course completions, # Certificates sold, Revenue
- OEC: Test completion and Course engagement. Predictive of course completion and certificates sold



## Organizational Metrics
#### Metrics Taxonomy 
(1) Goal metrics (2) Driver metrtics (3) Guadrail metrics (4) Others: Asset vs engagement metrics, business vs operational metrics, data quality metrics, diagnosis or dedug metrics

Depending on organization size and objectives, you may have multiple teams, each with their own goal, driver, and guardrail metrics, and all of which must align with your overall goal, driver, guardrail metrics. 

#### Formulating Metrics: Principles and Techniques
1. Ensure that your goal metrics are (1) simple (2) stable
2. Ensure that driver metrics are (1) aligned with goal (2) actionable and relevant (3) sensitive (4) resistant to gaming

Somes considerations for developing metrics: 
- Use ideas fromless-scalable method (survey), and then validate them in scalable data analyses to determine a precise definition.
- Consider quality when defining goal or driver metrics
  - LinkedIn profile is a “ good ” profile if it contains sufficient information to represent the user.
- Keep the model interpretable and validated over time. Otherwise, it may be hard to get buy-in from stakeholders, even harder if a sudden drop on the metric needs to be investigated. 
- Sometimes it may be easier to precisely measure what you **do not** want, such as user dissatisfaction or unhappiness
- Always remember that metrics are themselves proxies; each has its own set of failure cases. 
  - For example, a search engine may want to use CTR to measure user engagement but driving just CTR may lead to increased clickbait. In such cases, you must create additional metrics to measure the edge cases. In this example, one possibility is to use human evaluation as a metric to measure relevance and counterbalance a tendency towards rewarding clickbait. 

#### Evaluating Metrics
One of the most common and challenging evaluations is establishing the causal relationship of driver metrics to organizational goal metrics, that is, **whether this driver metric really drives the goal metrics**, Here are a few high-level approaches to tackle causal validation (1) Utilize other data sources such as surveys, focus groups, and UER studies (2) Analyze observational data (3) Check whether similar validation is done at other companies (4) Conduct an experiment with a primary goal of evaluating metrics (5) User a corpus of historical experiments as golden samples for evaluating new metrics

#### Evolving Metrics
Metrics may envolve along with the business, environment and your understanding. Certain metrics may evolve more quickly than others. 

#### Sidebar: Guardrail Metrics
There are two types of guardrail metrics: trustworthiness-related guardrail metrics and organizational guardrail metrics. 
- Trustworthiness-related guardrail metrics are necessary to ensure that experimental results are trustworthy. 
- Organizational guardrail metrics: an increase in latency of even a few milliseconds can result in revenue loss and a reduction in user satisfaction. Thus, latency is often used as a guardrail metric because it is so sensitive, especially relative to revenue and user satisfaction metrics. 
  - HTML response size per page
  - JavaScript error per page
  - Revenue-per-user
  - Pageviews-per-user
  - Client crashes
 
#### Sidebar: Gameability
Your goal and driver metrics need to be hard to game: One common scenario is to use short-term revenue as a key metric. However, you could increase short-term revenues by raising prices or plastering a website with ads, and either of those would likely lead to users abandoning the site and customer LTV declining. 

Generally, **we recommend using metrics that measure user value and actions**. You should avoid vanity metrics that indicate a count of your actions, which users often ignore. 

## Speed Matters
To conduct a controlled experiment, you want to isolate latency as the only factor changed. It is very hard to improve performance or else developers would have already made those changes, so we resort to a simple technique: slowing down the web site or product. 

#### Key Assumption: Local Linear Approximation
The key assumption for slowdown experiments is that the metric (e.g., revenue) graph vs. performance is well approximated by a linear line around the point matching today ’ s performance. This is a first-order Taylor-series approximation, or linear approximation . 

When we slow down the Treatment, we move from the point where the vertical line intersects the graph to the right, and we can measure the change to the metric. The assumption we make is that if we were to move left of the vertical line (improving performance), the vertical delta on the left would be approximately the *same* as the one we measured on the right. 

#### How to Measure Website Performance 
Understand how to measure page load time.

#### The Slowdown Experiment Design 
Where to slowdown: the right place to delay is when the server finishes computing Chunk2, which is the URL-dependent HTML. Instead of the server sending the HTML, we delay the response, as if the server took longer to generate the query-dependent part of the HTML. 

How long to slow down: slowdowns of 100 msec and 250 msec were determined to be reasonable choices by Bing. 

#### Impact of Different Page Elements Differs 
Multiple proposals have been developed to estimate perceived performance, including: (1) Time to first result (2) Above the Fold Time (3) Sppeed Index (4) Page Phase Time and User Ready Time

#### Extreme Results
(1) Speed performance matters might be overstated (2) Too fast may reduce users trust
