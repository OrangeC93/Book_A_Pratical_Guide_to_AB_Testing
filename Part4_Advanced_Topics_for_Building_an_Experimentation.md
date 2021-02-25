## Client-Side Experiments
#### Differences betweeen Server and Client Side
- Release process: The release process involves three parties: the app onwer, the app store and the end user. Also Apple and Google play support stagged rollout which allow owners to make the new app available to only a percentage of users and pause it if something problematic is discovered. However it cannot be analyzed as random experiments because the app owners do not know which users are eligible to receive the new app. App owners only know who has adopted the new app.
- Data Communication between Client and Server: The data connection between client and server may be limited or delayed by (1) Internet connectivity (2) Cellular data bandwidthv (3) Battery (4) CPU, latency and performance (5) Memory and storage
#### Implication for Experiments
- Anticipate Changes Early and Parameterize: (1) A new app may be releated before features are completed which can be gated by feature flags, that turn the feature off by default and turn it on when they are ready to be launch (2) More features are built so they are configurable from the server side (3) More fine-grained parameterization can be used extensively to add flexibility in creating new variants without needing a client release.
- Expect a Delayed Logging and Effective Starting Time: The experiment is not fully active because (1) Get the new experiment configuration (2) New configuration need to take effect (3) There;re can be many devices with old versions without the new experiment code
- Create a Failsafe to Handle Offline or Startup Cases
- Triggered Analysis May Need Client-Side Experiment Assignment Tracking 
- Track Important Guardrails on Device and App Level Health 
- Monitor Overall App Release through Quasi-experimental Methods 
- Watch Out for Multiple Devices/Platforms and Interactions between Them 

## Instrumentation

## Choosing a Randomization Unit
Some types of Randomization Unit:
- Page-level: Each new web page viewed on a site is considered a unit. 
- Session-level: This unit is the group of webpages viewed on a single visit. A session, or visit, is typically defined to end after 30 minutes of inactivity. 
- User-level: All events from a single user is the unit. Note that a user is typically an approximation of a real user, with web cookies or login IDs typically used. 
  - Cookies can be erased, or in-private/incognito browser sessions used, leading to overcounting of users. 
  - For login IDs, shared accounts can lead to undercounting, whereas multiple accounts (e.g., users may have multiple e-mail accounts) can lead to overcounting. 

When trying to decide on granularity, there're 2 main questions to consider:
1. How important is the consistency of the user experience? (wether the user will notice the changes, As an extreme example, imagine that the experiment is on font color. If we use a fine granularity, such as page-level, then the font color could change with every page.)
2. Which metrics matter? 

Your choice of metrics and your choice of randomization unit also interact. Finer levels of granularity for randomization creates more units, so the variance of the mean of a metric is smaller and the experiment will have more statistical power to detect smaller changes. 

Other concerns:
- If features act across that level of granularity, you cannot use that level of granularity for randomization.
- Similarly, if metrics are computed across that level of granularity, then they cannot be used to measure the results. 
- Exposing users to different variants may violate the stable unit treatment value assumption (SUTVA)

Randomization Unit and Analysis Unit 

Recommend: If the **randomization unit** be the same as (or coarser than) the **analysis unit** in the metrics you care about, it is easier to correctly compute the variance of the metrics.  
- For example, randomizing by page means that clicks on each pageview are independent, so computation for the variance of the mean, click-through rate (clicks/pageviews), is standard. 

If **randomization unit** be coarser than the **analysis unit**. Unit, such as randomizing by user and analyzing the click-through rate (by page), will work, but the experiment results can be skewed by bots that use a single user ID, e.g., a bot that has 10,000 pageviews all done using the same user ID. If this type of scenario is a concern, consider bounding what any individual user can contribute to the finer-grained metric or switching to a user-based metric such as the average click-through rate-per-user.

If **metrics** are computed at the user-level (e.g., sessions-per-user or revenue-per-user) and the **randomization** is at a finer granularity (i.e., page-level), the user's experience likely contains a mix of variants. As a result, computing metrics at the user-level is not meaningful; 

User-level Radomization
- Signed-in user id: Signed-in IDs are typically stable not just across platforms, but also longitudinally across time. 
- Pseudonymous user id, such as cookie
- Device id

The main difference between these different IDs is their scope. 
- If you need that level of consistency and it is available, a signed-in user ID is really your best choice. 
- If you are testing a process that cuts across the boundary of a user signing in, such as a new user on-boarding process that includes a user signing in for the first time, then using a cookie or device ID is more effective. 

The other question about scope is the longitudinal stability of the ID. In some experiments, the goal may be to measure whether there is a long-term effect. Examples may include latency or speed changes or or the users' learned response to ads. For these cases use a randomization unit with longevity, such as a signed-in user ID, long-lived cookie, or device ID. 

One final option that we do not recommend unless it is the only option is IP address. IP-based variant assignment may be the only option for infrastructure changes, such as for comparing latency using one hosting service (or one hosting location) versus another, as this can often only be controlled at the IP level. 
