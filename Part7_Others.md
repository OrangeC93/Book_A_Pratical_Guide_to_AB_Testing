## Non-parameter testing
https://www.zhihu.com/column/p/49472487

## MS ExP
[Ron Kohavi KDD 2015 Talk](https://www.youtube.com/watch?v=ZfhQ-fIg4EU&feature=youtu.be&t=2m59s)

- Bing ads
- Bing search

[Exp-platform](https://exp-platform.com/2018StrataABtutorial/)

## [Optimizely KDD 2017 Paper Talk](https://www.youtube.com/watch?v=AJX4W3MwKzU)
- Sequential Testing

## [Multi-arm/Contextual bandit](https://multithreaded.stitchfix.com/blog/2018/11/08/bandits/)

## Cases 
#### Uber
- https://eng.uber.com/xp/
- https://eng.uber.com/analyzing-experiment-outcomes

#### Airbnb
Dynamic P, cluster-based, variance reduction
- https://medium.com/airbnb-engineering/experiments-at-airbnb-e2db3abf39e7 
- https://medium.com/airbnb-engineering/experimentation-measurement-for-search-engine-optimization-b64136629760
- https://youtu.be/rxQ6D-QQMWc?t=728

#### Patreon (small company)

https://patreonhq.com/please-please-dont-a-b-test-that-980a9630e4fb (公开了内部的Experiment Template)

https://patreonhq.com/thats-not-a-hypothesis-25666b01d5b4


#### Netflix
https://medium.com/netflix-techblog/its-all-a-bout-testing-the-netflix-experimentation-platform-4e1ca458c15 

## When should we A/B testing:
- Established products: UI, Function
- Fledgling products

## Analyze
- [HTE](http://www.unofficialgoogledatascience.com/2019/04/misadventures-in-experiments-for-growth.html)
- [Percent increase](http://jwegan.com/growth-hacking/wrong-way-to-analyze-experiments/)
- [Udacity Case](https://github.com/shubhamlal11/Udacity-AB-Testing-Final-Project)

## Consideration
- Long term goal: https://research.google/pubs/pub43887/


## Network Effect
- [Linkedin graph clustering method](https://engineering.linkedin.com/blog/2019/06/detecting-interference--an-a-b-test-of-a-b-tests)
  - Cluster the LinkedIn graph into 10,000 clusters. The graph comprises all active LinkedIn members as nodes and their “connections” as edges.  
  - Split these clusters into two parallel experiments: individual vs cluster based

## [Pitfalls of AB testing (HBR with LinkedIn and Netflix)](https://hbr.org/2020/03/avoid-the-pitfalls-of-a-b-testing)
- (1) Not looking beyond averagr: firms should create different versions tailored to the preferences of important segments of users.
  - Use metrics and approaches that reflect the value of different customer segments.
    - Netfilx recommendation to heavy and light users: they alternates each users experience between treatment and control based on days. Also they developed a metric that balance the efforts on lightly and heavily engaged members to ensure that product changes don't benefit on e segment at the expense of another
  - Measure the impact across different levels of digital access
    - Netflix and LinkedIn track the upper, middle, and low percentiles of technical metrics(app loading time, delays before playback commences, and crash rates), Has the treatment slowed app loading relative to the control for both users in 5th of loading times and users in 95th, or has the treatment benefited users in the 5th while harming those in 95the, They user this approach to test innovations aimed at improving the quality of streaming video playback for different devices and network connectivity conditions
  - Always account for group specific behavior
    - LinkedIn’s A/B testing platform automatically computes the effects of experiments by group. for instance, LinkedIn found that instantly notifying job seekers of new job listings disproportionately increased the likelihood that sparsely connected individuals would apply for positions, because they’re less likely to hear of openings through other means than well-connected people are.
    - Finally, LinkedIn tracks the impact of changes on inequality itself by checking whether an innovation is increasing or decreasing the share of revenue, page views, and other top-line metrics contributed by the top 1% of users. This ensures that LinkedIn is not overoptimizing for the most active members at the expense of the less engaged.
  - Segment key markets
    - LinkedIn developed the LinkedIn Lite version of its main application, which has a lower image quality and a modified user interface, reducing the amount of data the app has to process. 
    - At Netflix, market-specific research on device usage has led the firm to experiment with and ultimately release a mobile-only membership plan for India.
- (2) Forgetting customers are connected
  - Use **network AB**: testing: LinkedIn content-recommendation algorithm that shows more long-form text content, such as news articles, and fewer images. Typically, images generate a lot of likes and a few comments, and news articles generate fewer likes but more comments. hile a standard A/B test will show that the new algorithm is generating fewer likes, network A/B testing will capture both the likes and the positive downstream impact initiated by more comments from the exposed users. 
  - Use **time series experiments**:
    - For example, imagine that LinkedIn develops a new algorithm for matching job seekers with job openings. To measure its effectiveness, LinkedIn would simultaneously expose all job postings and seekers in a given market to the new algorithm for 30 minutes. In the next 30-minute period, it would randomly decide to either switch to the old algorithm or stay with the new one. It would continue this process for at least two weeks to ensure that it sees all types of job search pattern. 
    - [Doordash switchback test](https://medium.com/@DoorDash/switchback-tests-and-randomized-experimentation-under-network-effects-at-doordash-f1d938ab7c2a)
- (3) Focusing predominantly on short term
  - Get the length of experiments right: week 
  - Run holdout experiment: Imagine you’re testing a feature that highlights professional milestones (such as getting a new job) achieved by network connections in a social media feed. This feature would likely be triggered intermittently, perhaps only once or twice a week, depending on who is in a member’s network. In such cases an experimental period of several weeks or months may be needed to ensure that members of the treatment group are exposed to enough updates to test the feature’s effect on feed quality, or how relevant users perceive the content to be.
