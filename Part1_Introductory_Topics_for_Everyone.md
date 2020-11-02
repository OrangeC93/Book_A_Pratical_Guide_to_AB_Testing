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



## 3 Twyman's Law and Experimentation Trustworthiness 

