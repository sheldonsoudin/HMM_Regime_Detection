Comp490 Midterm Report
Regime Detection in U.S. Equity and Credit Markets Using a Gaussian Hidden Markov Model




For the midterm report I expanded on the proposal. 
I am planning on doing the same with for final report based on this report and feedback and further additions.

STILL TODO:
Make final report more readable overall, and expansion: specifically of “results and visualisation” , and “analysis of results” section
possible further implementation of library functions
walk forward evaluation with log and Brier scores against a volatility-threshold baseline (resolve this forward evaluation in a way that is not circular), important to know if it actually works to some extent. 
restoring the full sample (if possible, FRED distributes as three-year window only it seems) a larger sample would mean better testing for 2008, 2020, 2022
further verification and understanding of fundamentals upon which this model is based.
Introduction
This project is a 3-state Gaussian Hidden Markov Model (HMM) implementation with the objective of identifying market regimes and producing probabilistic forecasts of the regime state (a predictive distribution over the three regimes rather than a single predicted regime). The three latent states represent distinct market regimes: Calm, Transition, and Crisis.
Dataset Description
Market data is sourced from Yahoo Finance, specifically S&P 500 price and volume. Credit market data is sourced from FRED (Federal Reserve Economic Data), specifically the high-yield OAS (BAMLH0A0HYM2) and investment-grade OAS (BAMLC0A0CM) series. Volume is retrieved but is not used as a model input.

Before fitting, the raw data is transformed into three preprocessed input features: log returns of the S&P 500, rolling realized volatility over a 21-day window, and the differential between the high-yield and investment-grade OAS series. Together these capture the return, volatility, and credit-stress dimensions.

The two sources use different calendars and different index conventions (yfinance returns a timezone-aware index, fredapi a naive one), so both are normalized before joining. The OAS series are forward-filled and prices are not: an OAS is a level that still had a value on an unpublished day, whereas a return is a change that does not exist on a non-trading day, and filling it would fabricate a 0% return.
Methods and Tools:
A 3-state Gaussian HMM is fit to the preprocessed inputs using maximum likelihood estimation via the EM (Baum–Welch) algorithm, which jointly estimates the emission parameters of each state and the transition probabilities between states. EM converges only to a local optimum, so the fit is repeated from 15 random initializations and the highest-likelihood model kept.

Features are z-score standardized before fitting, since the three differ in scale by about two orders of magnitude and the Gaussian likelihood is scale-sensitive. State indices are arbitrary, so states are labeled after fitting by ranking their mean realized volatility: lowest is Calm, highest is Crisis.

Forecasts are produced by propagating the filtered state through the transition matrix, which requires no future observations. This is implemented as an explicit forward recursion rather than predict_proba, which returns the smoothed posterior conditioned on the whole sample.

Implemented in Python using Pandas, yfinance and fredapi for retrieval, hmmlearn for fitting, scikit-learn for standardization, and matplotlib for visualization
Results and Visualisations
Diagnostics checked each state for low self-persistence or near-zero inflow; no flags triggered.

Table 1 , Table 2, 
Regime occupancy (Viterbi): Calm 66.2%, Transition 30.4%, Crisis 3.4%.
Figure 1 — S&P 500 price with regime probabilities beneath as a stacked area chart. This describes history rather than forecasting.
Figure 2 — Forward forecast from the final date, 2026-07-22, where the filtered state was Calm 1.000. Calm falls from 0.991 at h=1 to 0.927 at 
h=10; Crisis reaches 0.001. Plotted against the stationary distribution.
Evaluation:
!! From proposal and still to be resolved: Not sure if it makes sense ... 
Walk-forward one-step-ahead regime-probability forecasts will be evaluated using scoring rules for categorical forecasts (multi-class log score and/or Brier score), and benchmarked against a naive volatility-threshold baseline. Because the true regime is latent and not directly observed, forecasts will be scored against a label derived from in-sample Viterbi decoding. In-sample Viterbi decoding will also be used separately for descriptive regime labeling.
!! 
Analysis of results
The transition matrix cannot jump between Calm and Crisis in either direction; both entries are estimated at exactly zero, so the chain must pass through the middle state. This was not directly imposed

Expected durations: Calm persists longest at about 120 days, Crisis shortest at 25.

At 3.4% occupancy the Crisis state rests on roughly 25 observations, the three-year window contains no 2008, 2020, or 2022 # todo entire sample. 
Challenges
Timezone mismatch: yfinance returns a timezone-aware index and fredapi a naive one; joining directly yields a near-empty result with no error. Both are now normalized first.

API key handling:  I   initially wrote the key into a notebook cell and so it would have been saved and submitted with it.Since I want to share on github, I moved it to Colab's secret manager. Because getpass hides input, a mispaste then went unnoticed until it appeared as an unrelated-looking URL error, so I added key validation. 

EM local optima:  Different seeds gave materially different fits; resolved with 15 restarts keeping the best likelihood.

Sources

Yahoo Finance — S&P 500 (^GSPC).
FRED — ICE BofA US High Yield Index OAS (BAMLH0A0HYM2), Ice Data Indices, LLC.
FRED — ICE BofA US Corporate Index OAS (BAMLC0A0CM), Ice Data Indices, LLC.
Baum, L. E., Petrie, T., Soules, G., & Weiss, N. (1970). A maximization technique occurring in the statistical analysis of probabilistic functions of Markov chains. Annals of Mathematical Statistics, 41(1), 164–171.
Software: hmmlearn, yfinance, fredapi, Pandas, NumPy, scikit-learn, SciPy, matplotlib.
AI Resources

These resources were used in review and debugging of code, as well as for searching for pertinent information and sources. OpenAI's ChatGPT, Anthropic's Claude.
