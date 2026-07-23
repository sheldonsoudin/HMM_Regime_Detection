# Regime Detection in U.S. Equity and Credit Markets Using a Gaussian Hidden Markov Model

## Introcudtion 


For the midterm report I expanded on the proposal, and I am planning on doing the same with the final report based on feedback or further additions to the implementation of midterm report and midterm implementation, more specifically the additional goals I gave myself in proposal (part of the library implementation). My intent is for the final report to be a readible descriptoin of the project implementation. , furthermore I want to have a more readilbe code and more comments. 


This project is a 3-state Gaussian Hidden Markov Model (HMM) implementation with the objective of identifying market regimes and producing probabilistic forecasts of the regime state (a predictive distribution over the three regimes rather than a single predicted regime). The three latent states represent distinct market regimes: Calm, Transition, and Crisis.


# Dataset Description
Market data will be sourced from Yahoo Finance, specifically S&P 500 price and volume. Credit market data will be sourced from FRED (Federal Reserve Economic Data), specifically the high-yield OAS and investment-grade OAS series.
Before fitting the model, the raw data will be transformed into three preprocessed input features: log returns of the S&P 500, rolling realized volatility computed over a moving window, and the differential between the high-yield and investment-grade OAS series. Together, these features capture the return, volatility, and credit-stress dimensions.

Methods and Tools:
A 3-state Gaussian HMM will be fit to the preprocessed inputs using maximum likelihood estimation via the EM (Expectation-Maximization) algorithm, which jointly estimates the emission parameters of each state and the transition probabilities between states. The resulting states will then be interpreted and labeled as the Calm, Transition, and Crisis regimes based on their estimated volatility and return characteristics. The project will be implemented in Python, using Pandas for data handling, yfinance and fredapi to retrieve the market and credit data, hmmlearn (or statsmodels' MarkovRegression / MarkovAutoregression) to fit the model, and matplotlib for visualization.

Evaluation:
Walk-forward one-step-ahead regime-probability forecasts will be evaluated using scoring rules for categorical forecasts (multi-class log score and/or Brier score), and benchmarked against a naive volatility-threshold baseline. Because the true regime is latent and not directly observed, forecasts will be scored against a label derived from in-sample Viterbi decoding. In-sample Viterbi decoding will also be used separately for descriptive regime labeling.


Experiment Setup and Tools Used



Maximum likelihood estimation via Expectation-Maximization 

Results and Visualisations
Analysis of results

Challenges

Sources


AI Resources

These resources were used in review and debugging of code, as well as for searching for pertinent information and sources. OpenAI’s ChatGPT Anthropic’s Claude
