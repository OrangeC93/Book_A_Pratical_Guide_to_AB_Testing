## 17. The Statistics behind Online Controlled Experiments
#### Two sample t test
#### p value and confidence interval
(1)One way: **P value**, if there really is no difference between Treatment and Control, the probabiity that t would be at least this extreme.

- Common mistake for p value interpretation:
  - p-value captures the probability that the Null hypothesis is true given the data observed
    - We could use Byes rule to break it down: As indicated in the equation, to know whether the Null hypothesis is true based on data collected (posterior probability), you not only need a p-value but also the likelihood that the Null hypothesis is true. 
  ![image](/img/pvalue_bayes.png)

(2)Another way: check whether the **confidence interval** overlaps with zero. 
- The delta is statistically significant at 0.05 significance level If the 95% confidence interval does not contain zero 

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

Power is (1) the probability of detecting a difference between the variants: rejecting the null, when there really is a difference, is (2) the probability of not committing a type 2 error. It is not a single value. It varies according to the underlying truth.

[Factors affect Power](https://www.jospt.org/doi/pdfplus/10.2519/jospt.2001.31.6.307)
- (1) effect size: a larger difference between the means should be easier to detect and distinguish from the null hypothesis distribution, as the distance between the null hypothesis and research hypothesis distributions increases, the alpha area does not change, but the beta area decreases, with a corresponding increase in the 1 - P area.
- (2) sample size: sample size affects power by influencing the variability of the sampling distribution of mean differences
- (3) significance level: The lower the significance level, the lower the power of the test. If you reduce the significance level (e.g., from 0.05 to 0.01), the region of acceptance gets bigger. As a result, you are less likely to reject the null hypothesis. This means you are less likely to reject the null hypothesis when it is false, so you are more likely to make a Type II error. In short, the power of the test is reduced when you reduce the significance level; and vice versa.

Challenges:
- For online experiments, sample size estimation is more complex because online users visit over time, so the duration of the experiment also plays a role in the actual sample size of an experiment. Depending on the randomization unit, the sample variance σ^2 can also change over time. 
- Another challenge is that with triggered analysis (see Chapter 20), the values σ^2 and δ change as the trigger conditions change across experiments. For these reasons, we present a more practical approach in Chapter 15 for deciding traffic allocation and the duration for most online experiments. 

Sample size & Power
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

Multiple testing refers to any instance that involves the simultaneous testing of several hypotheses. This scenario is quite common in much of empirical research in economics. Some examples include: 
- One fits a multiple regression model and wishes to decide which coefficients are different from zero
- One compares several forecasting strategies to a benchmark and wishes to decide which strategies are outperforming the benchmark
- One evaluates a program with respect to multiple outcomes and wishes to decide for which outcomes the program yields significant effects

 
**FWER(family wise error rate) Methods**
- The error rate indicates the probability of making one or more false discoveries when performing multiple hypotheses test.
- FWER = 1 - (1 - α)^m
- If one does not take the multiplicity of tests into account, then the probability that some of the true null hypotheses are rejected by chance alone may be large. For example k=100 hypotheses being testd at the same time, α = 0.05, one expects five true hypotheses to be rejected, if all tests mutually independent, then the probability that at least one true null hypothesis will be rejected is 1-0.95^100 = 0.994. The overall type I error increases as the number of tests increases.
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

**4. Many others**

**FDR Method**
- FWER is way of adjusting α, resulting in too few hypotheses are passed the test.
- While FWER methods control the probability for at least one Type I error, FDR methods control the expected Type I error proportion. In this way, FDR is considered to have greater power with the trade-off of the increased number Type I error rate.

**1. Benjamini–Hochberg (BH) correction**
- BH method ranks the P-value from the lowest to the highest, then Pk < k/m * α.
- We keep repeating the equation until we stumbled into a rank where the P-value is Fail to Reject the Null Hypothesis. 

**2. The positive False Discovery Rate**
We try to control the probability that the null hypothesis is true, given that the test rejected the null. This method works by first fixing the rejection region, then estimating α, which is quitethe opposite of how the FDR is handled.

**Regression Method**
#### Fisher's meta analysis
Fisher ’ s method (or any other meta-analysis technique) is great for increasing power and reducing false-positives. You may have an experiment that is underpowered even after applying all power-increasing techniques, such as maximum power traffic allocation (see Chapter 15 ) and variance reduction (see Chapter 22 ). In this case, you can consider two or more (orthogonal) replications of the same experiment (one after another) and achieve higher power by combining the results using Fisher ’ s method. 


#### [Novelty Effect](https://productds.com/wp-content/uploads/Novelty_Effect.html)
