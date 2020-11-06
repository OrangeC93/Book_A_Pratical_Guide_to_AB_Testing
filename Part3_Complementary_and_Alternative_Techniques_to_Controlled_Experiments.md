## The Space of Complementary Techniques
To have successful A/B experiments, we not only need the care and rigor in analysis and in creating the experimentation platform and tools, but we also need: 
- Ideas for experiments, that is, an ideas funnel. 
- Validated metrics to measure the effects we care about. 
- Evidence supporting or refuting hypotheses, when running a controlled experiment is either not possible or insufficient. 
- Optionally, metrics that are complementary to the metrics computed from controlled experiments. 
![image](/img/Complementary_Method.png)

#### Logs-based Analysis
- Building intuition: helps you understand your product and system baseline, what the variance is, what is happening organically independent of experimentation, what size change might be pratically significant and more.
- Characterizing potential metrics: helps you understand the variance and distributions, how new metrics correlate with existing metrics. 
- Generating ideas for A/B experiments based on exploring the underlying data.
- Natural experiments.
- Observational causal studies.

One limitation is that these analyses can only infer what will happen in the future based on what happened in the past. 

#### Human Evaluation
Human evaluation is where a company pays a human judge, also called a rater , to complete some task. The results are then used in subsequent analysis. This is a common evaluation method in search and recommendation systems. 
- Human evaluation results are also useful for debugging.

One limitation of human evaluation is that raters are generally not your end users. 

#### User Experience Research(UER)
While user experience research (UER) uses a variety of methods, we focus here on a subset of field and lab studies that typically go deep with a few users, often by observing them doing tasks of interest and answering questions in either a lab setting or in situ 
 
These techniques can be useful for generating metric ideas based on correlating “ true ” user intent with what we observe via instrumentation. You must validate these ideas using methods that scale to more users, such as observational analyses and controlled experiments. 

#### Focus Groups
Focus groups are guided group discussions with recruited users or potential users. Focus groups can be useful for getting feedback on ill-formed hypotheses in the early stages of designing changes that become future experiments, or for trying to understand underlying emotional reactions, often for branding or marketing changes. Again, the goal is to gather information that cannot be measured via instrumentation and to get feedback on not-yet-fully-formed changes to help further the design process. 

#### Surveys
Some potential issues are needed to consider:
- Questions must carefully worded
- Answers are self reported
- The population can easily be biased 

#### External Data

## Observational Causal Study

