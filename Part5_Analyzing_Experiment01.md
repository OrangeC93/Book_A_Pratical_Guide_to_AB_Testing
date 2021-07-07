## 17. The Statistics behind Online Controlled Experiments
#### Two sample t test
#### P value and Confidence Interval
(1)One way: **P value**, p-value is the probability of obtaining test results at least as extreme as the results actually observed, under the assumption that the null hypothesis is correct.

(2)Another way: check whether the difference of [**confidence interval**](https://www.socscistatistics.com/confidenceinterval/default3.aspx) overlaps with zero or whether the two **confidence intervals** overlap
- [difference between the sample mean(proportion) + - z*(standard error for difference)](https://online.stat.psu.edu/stat100/lesson/9/9.3)

**SD vs SE**

SD is a measure of the amount of variation or dispersion of a set of values
- SDM measures the amount of variability from the individual data values to the mean, how spread out the data is. In any distribution, [about 95% of values will be within 2 standard deviations of mean](https://s4be.cochrane.org/blog/2018/09/26/a-beginners-guide-to-standard-deviation-and-standard-error/)
  - sqrt(sum([x - mean(x)]^2)/n)
- SD of proportion: sqrt(p(1-p)/n)

SE is the standard deviation of its sampling distribution
- SEM measures how far the sample mean (average) of the data is likely to be from the true population mean. The SEM is always smaller than the SD. Due to CLT: the sampling distribution of the sample mean approaches a normal distribution as the sample size gets larger. In addition, the normal distribution has the mean equals to the population mean, and the standard deviation equals to population standard deviation divided by the square root of sample size n. Thus, the standard error of the mean is the sample standard deviation divided by the square root of sample size n, if the population standard deviation is unknown. 
  - for a mean: SDM/sqrt(n)
  - [for the difference beween two means: sqrt(SDM1^2 + SDM2^2)](https://online.stat.psu.edu/stat100/lesson/9/9.3)
- [SE of median](https://towardsdatascience.com/how-to-estimate-the-standard-error-of-the-median-the-bootstrap-strategy-ed09cccb838a): Unfortunately, the Central Limit Theory does not apply to the median, we need bootstrap method.
  - (1)take n items from the given sample as a new sample, from this sample, we can easily calculate the sample median, (2)repeat the previous procedure for B rounds, and we will get B new samples, with B sample medians, (3)now we have got an empirical distribution of medians, thus we can estimate the standard error of medians from them: calculate the mean of the previous sample of medians, calculate the variation, calculate the standard error, which is the standard deviation of the empirical sample
- SE of proportion: [sqrt(p(1-p)/n)](https://online.stat.psu.edu/stat100/lesson/9/9.1)
  - [for the difference between two proportions: sqrt(SDP1^2 + SDP2^2)](https://online.stat.psu.edu/stat100/lesson/9/9.3)

**[Z Statistic vs T Statistic](https://www.stratascratch.com/blog/a-comprehensive-statistics-cheat-sheet-for-data-science-interviews/)**
- A Z-test is a hypothesis test with a normal distribution that uses a z-statistic. A z-test is used when know the population variance or if don’t know the population variance but have a large sample size.
- A T-test is a hypothesis test with a t-distribution that uses a t-statistic. You would use a t-test when don’t know the population variance and have a small sample size. You also need the degrees of freedom to convert a t-statistic to a p-value.
![image](/img/t-statistic.png)
When to use:
![image](/img/t_test_z_test.png)

**Factors influence p value**
- (1) Effect size: A 7kg or 10 mmHg difference will have a lower P value (and more likely to be significant) than a 2-kg or 4 mmHg difference.
- (2) Size of sample. The larger the sample the more likely a difference to be detected. Further, a 7 kg difference in a study with 500 participants will give a lower P value than 7 kg difference observed in a study involving 250 participants in each group.
- (3) Spread of the data. The spread of observations in a data set is measured commonly with standard deviation. The bigger the standard deviation, the more the spread of observations and the lower the P value.
#### [ANOVA](https://www.statisticshowto.com/probability-and-statistics/hypothesis-testing/anova/)

**One-way or Two-way** refers to the number of independent variables (IVs) in your Analysis of Variance test.
- One-way has one independent variable (with 2 levels). For example: brand of cereal,
- Two-way has two independent variables (it can have multiple levels). For example: brand of cereal, calories.

**Groups or levels** are different groups within the same independent variable. 
- In the above example, your levels for “brand of cereal” might be Lucky Charms, Raisin Bran, Cornflakes — a total of three levels. 

**Replication**: It’s whether you are replicating (i.e. duplicating) your test(s) with multiple groups. With a two way ANOVA with replication , you have two groups and individuals within that group are doing more than one thing

**Types of Test**
There are two main types: one-way and two-way. Two-way tests can be with or without replication.
- One-way ANOVA between groups: used when you want to test two groups to see if there’s a difference between them.
- Two way ANOVA without replication: used when you have one group and you’re double-testing that same group. For example, you’re testing one set of individuals before and after they take a medication to see if it works or not.
- Two way ANOVA with replication: Two groups, and the members of those groups are doing more than one thing. For example, two groups of patients from different hospitals trying two different therapies.

**One-way ANOVA:**
- A one way ANOVA is used to compare two means from two independent (unrelated) groups using the F-distribution. The null hypothesis for the test is that the two means are equal. Therefore, a significant result means that the two means are unequal.

**Limitation of one-way:**
- A one way ANOVA will tell you that at least two groups were different from each other. But it won’t tell you which groups were different. If your test returns a significant f-statistic, you may need to run an ad hoc test (like the Least Significant Difference test) to tell you exactly which groups had a difference in means.

**Two-way ANOVA**
The results from a Two Way ANOVA will calculate a main effect and an interaction effect. The main effect is similar to a One Way ANOVA: each factor’s effect is considered separately. With the interaction effect, all factors are considered at the same time. Two null hypotheses are tested if you are placing one observation in each cell. For this example, those hypotheses would be:
- H01: All the income groups have equal mean stress.
- H02: All the gender groups have equal mean stress.
- H03: The factors are independent or the interaction effect does not exist.

An F-statistic is computed for each hypothesis you are testing.
**Assumption**
- The population must be close to a normal distribution.
- Samples must be independent.
- Population variances must be equal (i.e. homoscedastic).
- Groups must have equal sample sizes.


#### Normality Assumption
CLT: Many people consider **the sample distribution of the metric Y** is a poor assumption because almost none of the metrics used in practice follow a normal distribution. However, **the average Y** usually does because of the **Central Limit Theorem**. As the sample size increases, the distribution of the mean Y becomes more normally distributed. 

Minimum Sample size:
- One rule-of-thumb for the minimum number of samples needed for the average Y to have normal distribution is 355 s^2 for each variant: s is the [skewness coefficient](https://www.itl.nist.gov/div898/handbook/eda/section3/eda35b.htm) of the sample distribution of the metric Y defined as below
- This rule-of-thumb provides good guidance for when | s | > 1 but does not offer a useful lower bound when the distribution is symmetric or has small skewness. On the other hand, it is generally true that fewer samples are needed when skewness is smaller.

![image](/img/skewness.png)

Metrics: 
- Some metrics, especially revenue metrics, tend to have a high skewness coefficient. Some effective ways to reduce skewness is to (1)**transform the metric**, (2)**cap the values**, (3)**new metrics** (4)**percentile metrics or trimmed means**
  - For example, after Bing capped Revenue/User to $10 per user per week, they saw skewness drop from 18 to 5, and the minimum sample needed drop tenfold from 114k to 10k. 
- For two-sample t-tests, because you are looking at the difference of the two variables with similar distributions, the number of samples needed for the normality assumption to be plausible tends to be fewer. 
  - This is especially the case if Treatment and Control have equal traffic allocations 

Test Normality:
- If you ever wonder whether your sample size is large enough to assume normality, test it at least once with offline simulation . 
  - You can randomly shuffle samples across Treatment and Control to generate the null distribution and compare that distribution with the normal curve using statistical tests such as Kolmogorov – Smirnov and Anderson-Darling 

#### Type I/II Erros and Power
Type I and Type II: 
- A Type I error: reject true h0 (假的说成真的，因此很多真的混杂着假的).
- A Type II error: failed to reject false h0 (真的说成假的，因此很多错过很多真的).
- Trade off between these two errors: using a higher p-value threshold means a higher Type I error rate （可能会很多假的说成真的）but a smaller chance of missing a real difference（但是不会错过一个真的）, therefore a lower Type II error rate. 

**Power** is (1) the probability of detecting a difference between the variants: rejecting the null, when there really is a difference, is (2) the probability of not committing a type 2 error. It is not a single value. It varies according to the underlying truth.

[General conversion: 80% but could be more, which means a true difference will be missed 20% of the time](https://www.youtube.com/watch?v=KIRwsYTR62A) 
  - For most researchers: Type 1 error are 4 times more serious than Type 2 errors so 0.05*

[Factors affect Power](https://www.jospt.org/doi/pdfplus/10.2519/jospt.2001.31.6.307)
- (1) effect size: a larger difference between the means should be easier to detect and distinguish from the null hypothesis distribution, as the distance between the null hypothesis and research hypothesis distributions increases, the alpha area does not change, but the beta area decreases, with a corresponding increase in the 1 - P area.
- (2) sample size: sample size affects power by influencing the variability of the sampling distribution of mean differences
- (3) significance level: The lower the significance level, the lower the power of the test. If you reduce the significance level (e.g., from 0.05 to 0.01), the region of acceptance gets bigger. As a result, you are less likely to reject the null hypothesis. This means you are less likely to reject the null hypothesis when it is false, so you are more likely to make a Type II error. In short, the power of the test is reduced when you reduce the significance level; and vice versa.

Challenges:
- For online experiments, sample size estimation is more complex because online users visit over time, so the duration of the experiment also plays a role in the actual sample size of an experiment. Depending on the randomization unit, the sample variance σ^2 can also change over time. 
- Another challenge is that with triggered analysis (see Chapter 20), the values σ^2 and δ change as the trigger conditions change across experiments. For these reasons, we present a more practical approach in Chapter 15 for deciding traffic allocation and the duration for most online experiments. 

#### [Sample size & Power Analysis](https://cloud.tencent.com/developer/article/1544632)
[Main output of a power analysis: estimation of an appropriate sample size](https://www.youtube.com/watch?v=KIRwsYTR62A)
- Too big: waster of resources
- Too small: may miss the effect
- Grants: justification of sample size
- Publications: reviewers ask for power calculation evidence

Power Analysis depends on relationship between 6 variables:
- The difference of biological interest
- The variability in the data(standard deviation)
- Power
- Significance level
- Sample size
- One or Two sided test: two times easier to reach significance with a one-tailed than a two tailed

In order to obtain meaningful results, we want our test to have sufficient statistical power. And, sample size influence statistical power. For example, when comparing two means, the follow formula can be used to calculate statistical power. 

![image](/img/sample_size_mean.png)

![image](/img/sample_size_proportion.png)


#### Bias (chapter3)
Bias arises when the estimate and the true value of the mean are systematically different. It can be caused by a platform bug, a flawed experiment design, or an unrepresentative sample such as company employee or test accounts. We discuss several examples and recommendations for prevention and detection in Chapter 3


#### Multiple Testing
- https://zhuanlan.zhihu.com/p/45715632
- http://m.biodiscover.com/group/topic/7720.html （这个好）
- https://www.jianshu.com/p/7de733382274
- https://www.jianshu.com/p/9e97e9b351fd
- https://www.invespcro.com/blog/multiple-testing-problem-how-adding-more-variations-to-your-ab-test-will-impact-your-results/
- http://home.uchicago.edu/amshaikh/webfiles/palgrave.pdf
- https://towardsdatascience.com/an-overview-of-the-multiple-comparison-problem-166aa5fcaac5

Multiple testing refers to any instance that involves the simultaneous testing of several hypotheses. This scenario is quite common in much of empirical research in economics. 

If one does not take the multiplicity of tests into account, then the probability that some of the true null hypotheses are rejected by chance alone may be unduly large. Take the case of S = 100 hypotheses being tested at the same time, all of them being true, with the size and level of each test exactly equal to α. For α = 0.05. Further, if all tests are mutually independent, then the probability that at least one true null hypothesis will be rejected is given by 1 − 0.95^100 = 0.994.

Application senarios include: 
- One fits a multiple regression model and wishes to decide which coefficients are different from zero
- One compares several forecasting strategies to a benchmark and wishes to decide which strategies are outperforming the benchmark
- One evaluates a program with respect to multiple outcomes and wishes to decide for which outcomes the program yields significant effects

 
**FWER(family wise error rate) Methods**
- FWER = 1 - (1 - α)^m. The error rate indicates the probability of making one or more false discoveries when performing multiple hypotheses test.
- If we run a test (α = 0.05) to assess whether there is a statistically significant difference between two groups, the FWER is: 1 - (1-0.05) = 0.05. However, if we run the same test six times, the FWER would not be 5% anymore, but it would increase to ~26%: 1 - (1 - 0.05)^6 = 0.2649
- In simpler terms, we are adjusting the α somehow to make sure the FWER ≤ α. This is to ensure that the Type I error always controlled at a significant level α. For example, when we have 20 features as independent variables for our prediction model, we want to do a significance test for all 20 features. It means all the 20 hypothesis tests are in one family.

**1. Bonferroni**
- The Bonferroni correction, which rely on some statistical adjustments made to p-values with the goal of reducing the chances of obtaining false-positive results, sets the significance cut-off at α/n.
- Advantage: Simple. 
- Disadvantage: Too conservative, leading to a **high rate of false negative**(fails to indicate the presence of a condition when it is present). In a sense, using the Bonferroni correction, we are more likely not to state any difference between the control and any other variation.
- code: https://towardsdatascience.com/an-overview-of-the-multiple-comparison-problem-166aa5fcaac5 

**2. Bonferroni-Holm correction**
- This other method improves the method above by sorting the obtained p-values from lowest to highest and comparing them to nominal alpha levels of α/m to α
- Disadvantage: Bonferroni Correction is proven too strict at correcting the α level where Type II error/ False Negative rate is higher than what it should be. 
- Pk < α/(m+1-k): lowest p_value <α/m, 2nd_lowest p_value <α/(m-1), …, highest p_value <α
- When we Fail to Reject the Null Hypothesis. When this happens, we stop at this point, and every ranking is higher than that would be Failing to Reject the Null Hypothesis.

**3. Šidák correction**
- This method also defines a new α’ to reach. This threshold is defined using the FWER and the number of tests.
- α’ = 1 - (1 - FWER)^(1/m)

**FDR Method**
- **FWER** is way of adjusting α, resulting in too few hypotheses are passed the test. **FDR** = E(false positives rejections) = E(#false positive/#rejections)
- While FWER methods control the probability for at least one Type I error, FDR methods control the expected Type I error proportion. In this way, FDR is considered to have greater power with the trade-off of the increased number Type I error rate.
- [The FDR approach adjusts the p-value for a series of tests. A p-value gives you the probability of a false positive on a single test; If you’re running a large number of tests from small samples (which are common in fields like genomics and protoemics), you should use **q-values** instead](https://www.statisticshowto.com/false-discovery-rate/)
  - A p-value of 5% means that 5% of all tests will result in false positives.
  - A q-value of 5% means that 5% of significant results will be false positives.

**1. Benjamini–Hochberg (BH) correction**
- BH method ranks the P-value from the lowest to the highest, then Pk < k/m * α.
- [We keep repeating the equation until we stumbled into a rank where the P-value is Fail to Reject the Null Hypothesis.](https://www.gs.washington.edu/academics/courses/akey/56008/lecture/lecture10.pdf) 
![image](/img/fdr_eg.png)

**2. The positive False Discovery Rate**
We try to control the probability that the null hypothesis is true, given that the test rejected the null. This method works by first fixing the rejection region, then estimating α, which is quitethe opposite of how the FDR is handled.

**Regression Method**
#### Fisher's meta analysis
Fisher ’ s method (or any other meta-analysis technique) is great for increasing power and reducing false-positives. You may have an experiment that is underpowered even after applying all power-increasing techniques, such as maximum power traffic allocation (see Chapter 15 ) and variance reduction (see Chapter 22 ). In this case, you can consider two or more (orthogonal) replications of the same experiment (one after another) and achieve higher power by combining the results using Fisher ’ s method. 


#### [Novelty and Primacy Effect](https://productds.com/wp-content/uploads/Novelty_Effect.html)
(1)Rule out the possibility: run tests on first time users

(2)If test is already running: compare first time users to older users in the treatment group

#### Interfrerence between Varians
- Typical design
  - Split users randomly
  - Users are independent
- Cases when assumption fails:
  - Social network: facebook, linkedIn, Twitter
    - Actual effect > treatment effect
  - Two sided markets: uber, lyft, airbnb
    - Resources are shared among control and treatment groups
    - Actual effect < treatment effect

Network effect:
- Users behaviours are impacted by others
- The effect can spillover the control group
- The difference underestimates the treatment effect

Two-sided markets:
- Geo-based randomization
  - Split by geolocations
  - Eg, New York vs San Francisco
  - Big variance since market are unique

- Time-based randomization
  - Split by day of week
  - Assign all users to either treatment or control
  - Only when treatment effect is in short time
  - Works when treatment effect takes a short time, like surge pricing
  - Does not when when treatment effect takes a long time, like referral program


Social network:
- Create network clusters:
  - People in teract mostly whithin a cluster
  - Assign cluster randomly
- Ego-network randomization:
  - Originated from LinedIn
  - A cluster is composed of an ego and her alters
  - One-out network effect: user either has the feature or not
  - Simpler and more scalable
