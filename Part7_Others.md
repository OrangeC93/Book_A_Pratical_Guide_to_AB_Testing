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
