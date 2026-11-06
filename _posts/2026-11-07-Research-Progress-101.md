

# Research Progress 101

 **Can We Detect Market Regimes Without Looking Into the Future?**

I've been working on a question around market regimes, particularly how we identify changes in market behaviour without having access to future information.

The starting point was fairly simple: financial markets don't behave the same way all the time. There are periods of relatively low volatility, periods of uncertainty, and periods where the market experiences unusually large movements. These are often described as market regimes.

But identifying a regime after looking at years of historical data is very different from identifying one while the market is actually moving.

That distinction became the starting point of this research.

### Research Question

A Hidden Markov Model (HMM) can be used to identify different market regimes from historical observations. For this project, I'm using three states: calm, neutral and volatile.

However, there's an important limitation. A conventional full-history HMM can use information from observations that come after the day it's trying to label. In other words, it can look backwards and forwards through the dataset to determine what regime a particular day most likely belonged to.

That's useful for historical analysis, but it doesn't represent what a live detection system can do.

A live system only has access to information available up to the current day.

This led me to a more interesting question:

**How much labelled historical data does a relatively simple classifier need to identify market regimes in real time, and how late does it detect an actual regime change?**

I'm also investigating whether incorporating news sentiment can improve detection speed.

**Why detection delay matters more than accuracy**

One thing I wanted to avoid was building another model that looks good primarily because of its accuracy score.

Imagine two systems. The first correctly identifies 90% of market regimes but takes two weeks to recognise that volatility has increased sharply. The second is slightly less accurate but recognises the transition within a few trading days.

Depending on the application, the second system could be considerably more useful.

This is why I'm treating detection delay as one of the main evaluation criteria, alongside false alarms and missed regime changes.

The focus isn't on predicting stock prices or generating trading returns. It's on understanding how quickly a system can recognise that the market has started behaving differently.

Pakistan's market makes this particularly interesting. Political developments, regulatory decisions and sudden interventions can produce sharp changes in market behaviour. These transitions may not leave enough historical examples for a model to learn from.

That brings me to the few-shot aspect of the research.

### What I'm trying to test

The current experimental design uses a full-history HMM as a retrospective reference. It provides the regime labels against which the causal detectors will be evaluated.

I'll then train relatively simple classifiers, initially a Random Forest and a small MLP, using only a limited number of labelled examples per state.

The training sizes I'm considering are 5, 10, 25 and 50 examples per state.

Once trained, these classifiers will process the market chronologically. They won't have access to future observations when making predictions.

I'm also comparing them against simpler alternatives, including rolling-volatility rules and causal HMM implementations.

The experiments will use multiple random seeds to account for variation in which labelled examples are selected.

Another important part of the design is keeping the final two years of data untouched until the experimental settings are finalised. I want to avoid repeatedly adjusting the methodology based on results from the period I'm eventually claiming to evaluate.

The primary comparison will be detection delay at a fixed false-alarm rate.

I'm also separating the features into three groups:

-   **A:** Market return and volatility features.
-   **B:** Market features with news sentiment.
-   **C:** Market features with sentiment and macroeconomic information.

This should help establish whether additional information actually improves detection rather than simply making the model more complicated.

### Data Exploration

Before getting to the models, I spent considerable time investigating the historical KSE-100 data.

The current dataset contains 6,127 daily closing observations, covering 2002 to 13 October 2026.

I checked duplicate dates, missing observations, weekend entries and return calculations. I also cross-checked recent closing values against independent news reports.

One thing that became apparent was that historical market data isn't always as straightforward as it looks.

There are 33 gaps longer than five calendar days. Rather than filling these with interpolated values, I've flagged them and kept the original observations intact.

I also found several historical market conditions that could quietly distort the results.

The most interesting example was the 2008 price floor. There were 29 days with near-zero returns that weren't representative of normal market behaviour. Including them could distort the volatility estimates and, consequently, the regime labels. These observations are now excluded.

Circuit breaker changes during 2008 present another problem. Market restrictions can artificially limit observed price movements, making a volatile period appear less volatile than it actually was.

For that reason, I'm planning to report results both with and without the affected window.

Daily price limits, holidays and trading gaps also need to be handled carefully. The analysis is indexed by trading days rather than calendar days, which matters when calculating detection delays.

This part of the work has changed how I'm approaching the project. Before evaluating a detector, I need to be reasonably confident that the data isn't introducing patterns that the model will simply learn.

### Sentiment

Initially, I considered sentiment analysis a central part of the project. The idea was that market movements alone might not provide enough information to recognise a developing regime change.

For example, a political announcement or regulatory intervention might affect market behaviour before its impact becomes fully visible in price and volatility features.

But I don't want to assume that sentiment will improve detection simply because it sounds useful.

I've therefore moved it into a separate experimental comparison.

Dawn is one of the potential sources because its historical coverage extends back to 2002. Open Data Pakistan also provides approximately 27,000 headlines, although its coverage is concentrated in 2020–2021.

I'm currently investigating how to build a reliable historical headline dataset and how to check whether a financial sentiment model such as FinBERT actually interprets Pakistani news correctly.

A model trained primarily on other markets and news sources might not necessarily understand the context of Pakistani political and financial reporting.

If sentiment doesn't improve detection, that's also a useful result.

### Changes till now

The initial idea has already gone through a few changes.

I originally considered using synthetic market simulations, but decided to drop them in favour of concentrating on actual KSE-100 behaviour.

Sentiment was moved from being a core dependency to an experimental feature group.

I've also fixed the number of regimes at three before testing. I don't want to select the number of states simply because it produces a better statistical fit.

Another important consideration is that the HMM labels aren't being treated as absolute ground truth. They are retrospective estimates, not confirmed descriptions of what the market was actually experiencing.

I'll be investigating the sensitivity of the results to this assumption using a jump-model-based robustness check.

These decisions are important because the objective isn't to make a particular model look successful. It's to understand whether the underlying approach holds up under realistic constraints.

**What's next?**

The next stage is primarily about establishing the experimental foundation.

I'm working towards:

1.  Implementing the baselines and causal feature pipeline.
2.  Testing whether the retrospective HMM labels are sufficiently consistent to serve as useful reference labels.
3.  Finalising the walk-forward evaluation procedure.
4.  Building a manually labelled sample of Pakistani financial and political headlines.
5.  Evaluating whether FinBERT produces useful sentiment signals on that sample.

I haven't reached the model evaluation stage yet, so there are no claims about detection performance at this point.

For now, the most useful progress has been understanding the data, identifying potential sources of bias and narrowing down what exactly needs to be tested.

The central question remains:

**Can a relatively simple model learn to recognise changing market regimes with very little labelled history, without relying on information from the future?**

That's what I'm trying to find out.
