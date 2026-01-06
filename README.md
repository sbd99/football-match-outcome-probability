# football-match-outcome-probability
Probabilistic football match outcome model using Poisson regression

# Overview

This project uses a probabilistic football match outcome model that estimates:

- expected goals for each team

- the probability of a home win, draw or away win

The main goal of the project is to build a clean prediction pipeline using only pre-match information.

The model is just a base layer that could be improved with more football data.


# Data

- Competition: Serie A

- Seasons used: 2020/21 – 2023/24 (four full seasons)

- Data type: historical match results (final scores only)

The 2024/25 season was excluded because the available dataset did not have the complete set of fixtures. Incomplete seasons were deliberately removed to avoid biased evaluation.

Each match includes:

- date

- home team

- away team

- home goals

- away goals

No in-match, post-match or market data is used.


# Feature Engineering

For each match, team features were calculated using recent match history, based only on games played before the fixture.

- average goals scored in the last 5 matches

- average goals conceded in the last 5 matches

- computed separately for home and away teams

- a home advantage indicator

Key methodological choices:

- Rolling features use shift(1) so the current match is never included

- Matches without sufficient prior history are dropped

- Promoted teams are handled naturally by this rule

- No missing values are imputed

This approach avoids data leakage and ensures all features reflect information that would have been available before kick-off.


# Modelling Approach

Two Poisson Generalised Linear Models (GLMs) are fitted:

1. Home goals model
2. Away goals model

Poisson regression is a standard baseline in football analytics because:

- goals are count data

- outputs are interpretable

- models produce full probability distributions rather than point predictions

The models estimate expected goals (λ) for each team in a match.

# From Expected Goals to Match Probabilities

Expected goals are converted into outcome probabilities by:

- assuming goals follow independent Poisson distributions

- enumerating possible scorelines

- aggregating probabilities into:

  - home win

  - draw

  - away win

All predicted probabilities are verified to sum to 1.

# Evaluation

A time-based split is used:

- Training: 2020/21 – 2022/23

- Test: 2023/24 season

This avoids look-ahead bias and reflects real-world deployment.

# Metric

The primary evaluation metric is log loss, which measures the quality of probabilistic predictions.

# Result

Log loss ≈ 1.10

For a three-outcome football prediction task:

- random guessing ≈ 1.10–1.12

- simple, well-implemented Poisson baselines typically fall in the 1.08–1.15 range

This result indicates that the model produces reasonable, well-calibrated probabilities, given the deliberately limited feature set.

# Project Structure
football-match-outcome-probability/
│
├── data/
│   ├── raw/            # original, untouched match data
│   └── processed/      # engineered features and predictions
│
├── notebooks/
│   ├── 01_data_checks.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_poisson_model.ipynb
│   └── 04_evaluation.ipynb
│
└── README.md



# Limitations

This project is intentionally scoped as a baseline model. Limitations include:

- No team strength priors (e.g. Elo or long-term averages)

- No shot-based metrics such as expected goals (xG)

- No player availability or scheduling effects

- No market odds

As a result, early-season and cold-start predictions are conservative by design.

# Future Work

Possible extensions include:

- incorporating team strength priors to improve early-season predictions

- replacing raw goals with xG and xGA

- modelling team effects using hierarchical or Bayesian approaches

- comparing model probabilities against market odds

# Purpose

The goal of this project is to demonstrate:

- correct handling of time-dependent football data

- probabilistic modelling using appropriate statistical tools

- honest evaluation and clear communication of uncertainty

It is intended as a clean, extensible foundation, rather than a finished or optimised betting system.
