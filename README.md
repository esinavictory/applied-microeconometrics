# applied-microeconometrics
# Incumbency Advantage in U.S. House Elections: A Regression Discontinuity Replication of Lee (2008)

Project assignment in Applied Microeconometrics, HSE University (St. Petersburg), April 2026.
polynomial_fits.png

## Overview

This project replicates the main analysis of Lee (2008), "Randomized experiments from non-random selection in U.S. House elections" (*Journal of Econometrics*), which estimates the causal effect of incumbency on electoral outcomes using a regression discontinuity design (RDD).

**Research question.** Does winning an election cause a party to do better in the next one? Comparing winners and losers directly is misleading, because stronger candidates both win and perform better later. RDD solves this by comparing districts where the Democratic party barely won with districts where it barely lost: near the cutoff, the outcome of the election is close to random.

**Result.** Barely winning an election raises the Democratic vote share in the next election by about **9.1 percentage points** (s.e. 0.010, p < 0.001).

## Design

| Element | Definition |
|---|---|
| Running variable | `difdemshare`: Democratic vote share margin of victory in election *t* |
| Cutoff | 0 |
| Treatment | `victory = 1` if `difdemshare >= 0` (Democrats win and become the incumbent party) |
| Outcome | `demsharenext`: Democratic vote share in election *t+1* |
| Design type | Sharp RDD |
| Identifying assumption | Near the cutoff, winning is determined by chance, so treatment is locally as good as randomized |

## Data

Replication data from Lee (2008): U.S. House elections, with one observation per district-election.

| Characteristic | Value |
|---|---|
| Full sample | 11,795 elections (6,700 Democratic wins, 5,095 losses) |
| Estimation sample (non-missing outcome) | 9,177 |
| Clustering variable | `statedisdec` (state-district-decade) |

Baseline covariates used in the robustness check: `demshareprev`, `demwinprev`, `demofficeexp`, `demelectexp`, `othofficeexp`, `othelectexp`.


## Methodology

| Task | What was done |
|---|---|
| 1. Treatment indicator | Built `victory` from the running variable and described the assignment mechanism |
| 2. Local averages | Averages of `demsharenext` in bins of width 0.01 on [-0.25, 0.25], plotted around the cutoff |
| 3. Polynomial approximation | Fourth-order polynomials fitted separately on each side of the cutoff; discontinuity gap read at zero |
| 4. RDD regression | OLS with `victory`, a fourth-order polynomial in the margin, and their interactions; standard errors clustered by `statedisdec` |
| 5. Covariate sensitivity | Same specification with pre-determined covariates added, as a specification test |

The regression in Task 4 is

demsharenext = β₀ + β₁ · victory + f(mov) + victory · f(mov) + ε,  where f(mov) = γ₁mov + γ₂mov² + γ₃mov³ + γ₄mov⁴

and β₁ is the estimated incumbency effect.

## Main results

| Specification | Effect of victory | Std. error | N |
|---|---|---|---|
| Fourth-order polynomial, fitted values at cutoff (Task 3) | 0.0906 | n/a | 9,177 |
| RDD regression (Task 4) | 0.0906 | 0.0102 | 9,177 |
| Same restricted sample, no covariates (Task 5) | 0.0755 | 0.0114 | 6,559 |
| With baseline covariates (Task 5) | 0.0766 | 0.0109 | 6,559 |

**Key takeaways**

- The local averages show a clear jump at the cutoff: Democrats who barely won receive about 53% of the vote in the next election, while those who barely lost receive about 44%.
- The regression estimate of the incumbency effect equals the polynomial gap at zero (9.1 percentage points) and is highly significant.
- Adding pre-determined covariates barely changes the estimate (0.0755 vs. 0.0766 on the same sample) and slightly lowers the standard error. This is what we would expect if treatment is locally randomized, so it supports the validity of the design.
- Among the covariates, only the previous Democratic vote share (`demshareprev`) is significant: it helps predict the outcome but does not confound the treatment effect.

## Reference

Lee, D. S. (2008). Randomized experiments from non-random selection in U.S. House elections. *Journal of Econometrics*, 142(2), 675-697.

## Stack 
- Python
- R
