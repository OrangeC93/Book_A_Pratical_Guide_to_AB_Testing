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
- Page-level: Each new web page viewed on a site is considered a unit. 
- Session-level: This unit is the group of webpages viewed on a single visit. A session, or visit, is typically defined to end after 30 minutes of inactivity. 
- User-level: All events from a single user is the unit. Note that a user is typically an approximation of a real user, with web cookies or login IDs typically used. Cookies can be erased, or in-private/incognito browser sessions used, leading to overcounting of users. For login IDs, shared accounts can lead to undercounting, whereas multiple accounts (e.g., users may have multiple e-mail accounts) can lead to overcounting. 

When trying to decide on granularity, there're 2 main questions to consider:
1. How important is the consistency of the user experience? (wether the user will notice the changes, As an extreme example, imagine that the experiment is on font color. If we use a fine granularity, such as page-level, then the font color could change with every page.)
2. Which metrics matter? 


