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
