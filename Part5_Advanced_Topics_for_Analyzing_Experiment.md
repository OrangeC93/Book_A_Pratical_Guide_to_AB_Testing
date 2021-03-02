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


#### Fisher's meta analysis
Fisher ’ s method (or any other meta-analysis technique) is great for increasing power and reducing false-positives. You may have an experiment that is underpowered even after applying all power-increasing techniques, such as maximum power traffic allocation (see Chapter 15 ) and variance reduction (see Chapter 22 ). In this case, you can consider two or more (orthogonal) replications of the same experiment (one after another) and achieve higher power by combining the results using Fisher ’ s method. 

