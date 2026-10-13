**English** | [Русский](README.ru.md) | [Українська](README.uk.md)

# Winning versus judging

*We study how strongly winning a game and the judge's evaluation of the game are associated with a player's final place in the tournament table — and whether this association works the same way in different federations of the Open League.*

- **Tournaments:** 2 012
- **Player×tournament rows:** 36 132
- **Author:** Anatolii Dudko
- **Period:** tournaments from January 2024 to September 2026, 5 federations
- **Data:** tournaments and games from the Open Mafia World project database, exported in September 2026
- **Methods:** conditional logit · GEE · Spearman rank correlation (Fisher‑z, random‑effects)

## 01. Key findings

Both predictors — the player's share of wins in the tournament (win_rate) and the judge's evaluation of their play (judge_rank) — are statistically associated with the final place in all five federations. But which of them is more strongly associated with place — winning or judging — depends on the federation and changes noticeably when the predictors are compared on a comparable scale and when we account for the same players taking part in many tournaments.

- **The main result of the report: repeated participation of players substantially changes the interpretation of the iMafia finding (section 09).** The same player_id takes part in many tournaments of a federation, and not by chance — 68% of rows in iMafia come from players with more than 10 tournaments. After a check that is robust to this (clustering the uncertainty by player), the difference between judge_rank and win_rate remains statistically confirmed in the full iMafia sample for both outcomes — but splitting by player activity shows that it mostly applies to about 200 regular participants of the federation; among less active players no statistically convincing difference between judge_rank and win_rate was found. This is currently the best-supported result of the whole report — it is independently confirmed by five different checks (section 09).
- **Both predictors are significant everywhere.** In conditional logit — the main model of the report (section 04) — win_rate and judge_rank are significantly associated with both top‑3 and top‑half in all 5 federations without exception.
- **After a fair comparison of scales, the balance depends on the federation.** In most other federations (FIIM, Mafgame, Emotion Games, and at the point estimate also CLM, although for CLM this direction is not confirmed under clustering by player — section 09) win_rate, rescaled to 1 standard deviation, is more strongly associated with place than judge_rank. **In the full iMafia sample it is the other way round, and in both outcomes**: the standardised association of judge_rank with the outcome is larger than that of win_rate — 30.8 versus 12.6 for top‑3 and 30.2 versus 20.3 for top‑half (OR per 1 within-tournament SD, conditional logit, section 06; but see the first point above about the concentration among regular participants).
- **Direct correlation of place with the predictors confirms the same.** In iMafia place correlates with judge_rank more strongly (0.885) than with win_rate (0.789) — and this holds in all three years of the sample, 2024–2026 (sections 07–08). In most other federations the relation is reversed or the values are close.
- **An optional illustration, not an empirical finding (section 10).** If we additionally assume (without testing it on these data) that a player's win_rate does not change much between federations, the findings above imply that for regular participants the iMafia tournament system, more than the others, sorts players with similar win_rate by judge scores. Section 10 discusses this in detail; the other conclusions of the report do not depend on this assumption.
- **A caveat that applies to all conclusions above (section 11):** both predictors enter the very formula of the place mathematically, so the correlations and odds ratios in this report show how the place-scoring system of each federation is built, not an independently measured causal effect of "skill" or "judging generosity" on the result.

Below are the context of the game, the methodology, the formulas and the full tables behind these numbers.

## 02. About the game and what we study

Mafia is a team game; one round (a "game") is played by 10 people at a table. Roles are dealt at random, but their composition is always the same: 2 mafia, 1 don, 1 sheriff and 6 civilians. The mafia team (the don and the ordinary mafia) know each other and secretly eliminate players of the civilian team at night; the civilian team (including the sheriff) tries to identify and eliminate the mafia through discussion and voting during the day. The game ends with a win for one of the two sides. For every game played a player receives points for their side's win; separately, the judge evaluates the player's skill in that particular game (argumentation, accuracy of accusations, etc.) on their own scale. All these points for all games of the tournament are summed — this gives the final table of one tournament.

A tournament is not one game but a set of many games (often in parallel at several tables and over several rounds), so one tournament may have from 10 to several hundred participants, and each plays several games in the tournament, collecting points for all of them.

The main question of this report: in different federations, how much does the judge's evaluation of play matter compared with winning/losing — and how strongly, relative to each other — when it comes to reaching the top‑3 or the top half of the final tournament table. To answer it we statistically relate two separate quantities of a player in a tournament — the share of games they won (win_rate) and the judge's evaluation of their play (judge_rank) — to their final place, and compare the strength of this association across five federations, each with its own tournament system and its own judges. The report includes only tournaments that the federations themselves marked as rating tournaments (counted in the federation's official rating), without preliminary and qualifying stages.

### Place in the tournament

```text
total_points = round(Σ(value + win_points + ci), 2)      — over all games of the tournament
value        = comp.positive + comp.negative + comp.best_move

rank = total_points ↓, then round(Σvalue,2) ↓, then player_id ↑
top‑3    = rank ≤ 3
top‑half = rank ≤ ⌈n_players / 2⌉
```

### Two predictors

**win_rate** — the player's share of won games in the tournament, a value from 0 to 1. **judge_rank** — the percentile rank of the player's sum Σ(comp.positive + comp.negative) among all players of this tournament, (fractional_rank−1)/(n−1), from 0 to 1, with average ranks for ties. The components `ci` and `best_move` are not part of this predictor: the first is compensation for being shot, not a judge's evaluation; the second is awarded by formula for a correctly guessed move, not by a judge's decision. Both still enter the final place mechanically through `value` — this is taken into account in the section on caveats.

> ⚠️ The two predictors have scales of a different nature
>
> win_rate is a natural share of wins. judge_rank is a constructed percentile rank, statistically close to a uniform distribution within a tournament. "The same step of +0.1" on these two scales is not an equally meaningful step in reality: the pooled standard deviation of win_rate over the dataset is ≈0.155–0.162 depending on the federation, that of judge_rank is ≈0.30–0.31, i.e. almost twice as large. An OR "per +0.1" therefore cannot be read as a comparison of "importance" without caveats — see section 06, where the same comparison is made per one standard deviation of each predictor.

### Two outcomes

**top‑3** — an indicator of "place ≤ 3". **top‑half** — an indicator of "place ≤ ⌈n_players/2⌉". Both are computed separately at all stages: a separate model and a separate correlation for each outcome. For both outcomes the number of "successes" in each tournament is fixed by its size (exactly 3, and exactly ⌈n/2⌉) — this is not an ordinary independent binary outcome, and this determines the choice of model below.

## 03. Formulas

#### Conditional logit — the main model

Stratum = tournament i, n_i players, exactly k_i of them are a "success" (k_i=3 for top‑3, k_i=⌈n_i/2⌉ for top‑half). Since the number of successes in each stratum is fixed by the very design of the outcome, the conditional likelihood (Chamberlain, 1980) is a model that uses this structure explicitly rather than approximating it:

```text
L_i(β) = exp( Σ_{j∈S} x_jᵗβ )
  / Σ_{S′⊆{1..n_i}, |S′|=k_i} exp( Σ_{j∈S′} x_jᵗβ )

  S — the actual set of "successful" players of tournament i
  the denominator is a sum over all C(n_i, k_i) subsets of the same size
  x_j = (wr_j, jr_j, wr_j·jr_j) — predictors of player j (centred or raw)
```

Conditioning on the exact number of successes in a stratum removes the tournament intercept — the comparison is made strictly between players of the same tournament, not between tournaments; tournaments of different size and different average level of participants do not distort the estimate.

#### GEE — the secondary model (population-average)

```text
logit(P(Y_ij = 1)) = β0 + β1·wr_ij + β2·jr_ij + β3·wr_ij·jr_ij

  i — tournament, j — player within tournament i
  Y_ij ∈ {0,1}  — top‑3 or top‑half (two separate models)
```

Binomial distribution, logit link, estimation via Generalized Estimating Equations (Liang & Zeger, 1986), exchangeable working correlation, robust (sandwich) SE:

```text
Σᵢ Dᵢᵗ Vᵢ⁻¹ (Yᵢ − μᵢ(β)) = 0,        Dᵢ = ∂μᵢ/∂β

Cov(β̂) = M⁻¹ · ( Σᵢ Dᵢᵗ Vᵢ⁻¹ Ĉov(Yᵢ) Vᵢ⁻¹ Dᵢ ) · M⁻¹
  where M = Σᵢ Dᵢᵗ Vᵢ⁻¹ Dᵢ
```

Unlike conditional logit, GEE does not explicitly use the fact that the number of successes in a stratum is fixed — it estimates a "population-average" effect, averaged over all tournaments, rather than the effect within a specific tournament. Both models are given below: GEE as an additional check of the direction and order of magnitude of the effect, not as the main result.

#### Centring, odds ratios and the full joint effect

Both models above use the "raw" parameterisation (0 means no wins / zero judge score). For interpretation the predictors are centred: wr → (wr−0.5), jr → (jr−0.5). With this substitution the coefficient of the product does not change, and the coefficients of the linear terms shift:

```text
β3 (of wr·jr)          → unchanged
β1 (of wr)             → β1 + β3·0.5
β2 (of jr)             → β2 + β3·0.5

OR(+0.1 to win_rate, at average judge_rank)   = exp[(β1 + β3·0.5) · 0.1]
OR(+0.1 to judge_rank, at average win_rate)   = exp[(β2 + β3·0.5) · 0.1]
OR(interaction — ONLY the addition on top of the two
   main effects, jointly +0.1 and +0.1)
  = exp(β3 · 0.1 · 0.1) = exp(β3 · 0.01)

OR(full joint effect of +0.1 to both predictors at once,
   for an average player) = OR(win_rate) × OR(judge_rank) × OR(interaction)

OR per 1 standard deviation (SD) of predictor X (for a fair comparison
between win_rate and judge_rank, which vary on different scales):
  OR_per_SD(X) = OR_per_0.1(X) ^ (SD(X) / 0.1)
```

The second-to-last line follows directly from the fact that logits add up: the full effect of both predictors rising by 0.1 at once is the product of all three odds ratios. The OR_per_SD formula converts from a step of 0.1 to a step of "one standard deviation" without requiring direct access to the raw coefficients.

#### Spearman rank correlation and pooling it over tournaments

```text
Within tournament i (n_i players): rho_i = Pearson( rank(place), rank(X) )
  where rank is the rank with average values for ties (average ranks)
  (without ties this is identical to 1 − 6·Σd²/(n_i·(n_i²−1)))
```

win_rate and judge_rank regularly produce ties, so the correct formula is the Pearson correlation of average ranks, not the shortcut formula through Σd², which is in general not equivalent with ties. This is how the standard Spearman rank correlation with average ranks for ties is computed by default — and this is how it was computed (confirmed and independently rechecked, section 12, layer 3).

```text
Fisher transformation:  z_i = atanh(rho_i) = 0.5·ln((1+rho_i)/(1−rho_i))
```

Variance of z_i: the basic formula SE(z_i)≈1/√(n_i−3) is derived exactly for the Pearson coefficient under bivariate normality and underestimates the variance for the rank rho. The main calculation (section 07) therefore uses a more accurate version — Bonett–Wright (2000):

```text
SE_BW(z_i) = √( (1 + ρ̄² / 2) / (n_i − 3) )

  ρ̄ — the overall pooled estimate of rho for the federation and predictor (not rho_i of
  a specific tournament — otherwise a tournament's weight would depend on its own estimate
  and would systematically bias the result, section 12), obtained in two passes:
  1) classic pooling with SE=1/√(n_i−3) gives an initial estimate of ρ̄
  2) SE_BW(z_i) is recomputed with this ρ̄ and the pooling is repeated
```

```text
Random-effects (DerSimonian–Laird, 1986), w_i = 1/SE(z_i)² with either formula above:
  Q  = Σ w_i·(z_i − z_FE)²,      C = Σw_i − Σw_i²/Σw_i
  τ² = max(0, (Q − (k−1)) / C)
  w_i* = 1/(1/w_i + τ²),         z_RE = Σ(w_i*·z_i) / Σw_i*,   SE_RE = 1/√(Σw_i*)

I² = max(0, (Q − (k−1)) / Q) × 100%

Confidence interval of the mean:  z_RE ± 1.96 · SE_RE
Prediction interval (for a NEW tournament of the same federation, wider than the CI):
  z_RE ± t_{0.975, k−2} · √(τ² + SE_RE²)

Back-transformation for the report: rho = tanh(z)
```

Section 07 gives the result with the Bonett–Wright variance as the main one; section 11 shows the same pooling with the basic formula SE=1/√(n_i−3) as a comparison (sensitivity) — the point estimates of rho agree almost everywhere, the difference is mainly in the estimate of heterogeneity (I²).

*z_i is undefined at |rho_i|=1 (atanh(±1)=±∞) — the only such case in the dataset (one tournament, section 07) is excluded from the pool rather than replaced by an approximation.*

#### Clustering by player and nested models (section 09)

Player i within a stratum (tournament) contributes u_i=(y_i−π_i)·x_i to the gradient of the conditional likelihood, where y_i∈{0,1} is success, π_i is the conditional probability of success from the formula of section 03, and x_i are their predictors. The ordinary (model-based) covariance of the coefficients is H⁻¹, where H is the information matrix at the maximum. The sandwich covariance clustered by player (player_id within the federation):

```text
s_p = Σ_{i∈p} u_i   (sum of the contributions of all rows of player p, including different tournaments)

V_player = H⁻¹ · ( Σ_p s_p s_pᵀ ) · H⁻¹ · G/(G−1)

  G — number of players (clusters) in the federation; G/(G−1) — finite-sample correction (CR1)
```

Two-way covariance (by player and by tournament at once) — Cameron–Gelbach–Miller (2011):

```text
V_twoway = V_player + V_tournament − V_row

  V_row — the ordinary (one-level) sandwich covariance by rows
  V_tournament — the same, clustered by tournament_id instead of player_id
  The matrix need not be positive semi-definite; if it is not,
  the two-way estimate for that slice is not given (section 09)
```

Comparing single-predictor models with the full one (M_full = wr+jr+wr·jr) — nested models on the same strata:

```text
LR = 2·( logLik(M_full) − logLik(M_restricted) ),   df = number of dropped coefficients
  LR ~ χ²(df) for independent strata

AIC = −2·logLik + 2k   (k — number of model coefficients)

Wald (clustered by player) for the same restriction R·β=0:
  W = (R·β̂)ᵀ · [R·V_player·Rᵀ]⁻¹ · (R·β̂)   ~ χ²(df)

  LR assumes independent strata; if one player appears in different
  tournaments, the strata are not fully independent — the clustered Wald accounts for this
  and is given alongside as a more robust version of the same test
```

Out-of-sample check — K-fold by whole tournaments (not by rows, so as not to split a stratum between train and test):

```text
NLL_oos = − (1/T_test) · Σ_{i∈test} logLik_i(β̂_train)   (averaged per held-out tournament)

pseudo-R²_oos = 1 − NLL_oos(model) / NLL_oos(null model, β=0)
```

## 04. Main result — conditional logit, odds ratio per +10 p.p.

The model compares players strictly within one tournament — it accounts for the fixed number of places in the top‑3/top‑half of each tournament. The OR reads as "by how many times the odds change for a player with an average value of the other predictor when their predictor increases by 10 percentage points (for judge_rank — by 10 percentiles), relative to other players of the same tournament".

| Federation | Outcome | OR win_rate | 95% CI | OR judge_rank | 95% CI | OR interact. | 95% CI | p (interact.) |
|---|---|---|---|---|---|---|---|---|
| **iMafia** | Top‑3 | 5.02 | [4.40, 5.73] | 2.98 | [2.77, 3.21] | 1.019 | [0.987, 1.053] | 0.2490 |
|  | Top‑half | 6.82 | [6.20, 7.50] | 2.97 | [2.83, 3.12] | 1.095 | [1.064, 1.126] | 7.0e-10 |
| **FIIM** | Top‑3 | 8.57 | [7.25, 10.15] | 2.38 | [2.20, 2.57] | 1.187 | [1.145, 1.230] | 8.2e-21 |
|  | Top‑half | 9.04 | [7.99, 10.22] | 2.60 | [2.47, 2.74] | 1.217 | [1.173, 1.262] | 3.9e-26 |
| **Mafgame** | Top‑3 | 5.66 | [4.94, 6.49] | 1.84 | [1.74, 1.95] | 1.118 | [1.083, 1.153] | 4.8e-12 |
|  | Top‑half | 7.02 | [6.26, 7.87] | 2.09 | [2.00, 2.19] | 1.098 | [1.062, 1.136] | 5.3e-08 |
| **Emotion Games** | Top‑3 | 15.45 | [10.90, 21.91] | 2.90 | [2.52, 3.35] | 1.178 | [1.101, 1.260] | 1.7e-06 |
|  | Top‑half | 14.77 | [11.78, 18.52] | 2.94 | [2.69, 3.22] | 1.342 | [1.268, 1.422] | 7.7e-24 |
| **CLM** | Top‑3 | 10.81 | [6.04, 19.33] | 2.42 | [1.92, 3.04] | 1.354 | [1.174, 1.562] | 3.2e-05 |
|  | Top‑half | 12.30 | [7.41, 20.44] | 2.93 | [2.35, 3.66] | 1.314 | [1.139, 1.516] | 0.0002 |

> ✅ Both predictors are significant in all 5 federations and in both outcomes. The interaction is mostly positive and significant (except **iMafia, top‑3**, where OR≈1, p=0.25) — that is, a high judge score more often amplifies the effect of an already high win_rate than compensates for its absence. But directly comparing the numbers in this table between the two predictors is misleading before standardisation — see section 06.

#### Full joint effect (not only the interaction OR)

The interaction OR on its own is only a correction on top of the two main effects (section 03). Below is the product of all three components: by how many times the odds change for a player with average values if both predictors rise by 0.1 at once.

| Federation | Outcome | GEE: factors | GEE, total | clogit: factors | clogit, total |
|---|---|---|---|---|---|
| **iMafia** | Top‑3 | 2.42 × 2.02 × 0.978 | 4.78 | 5.02 × 2.98 × 1.019 | 15.26 |
|  | Top‑half | 4.19 × 2.78 × 1.059 | 12.33 | 6.82 × 2.97 × 1.095 | 22.17 |
| **FIIM** | Top‑3 | 3.20 × 1.77 × 1.067 | 6.04 | 8.57 × 2.38 × 1.187 | 24.21 |
|  | Top‑half | 5.24 × 2.46 × 1.148 | 14.79 | 9.04 × 2.60 × 1.217 | 28.61 |
| **Mafgame** | Top‑3 | 2.75 × 1.54 × 1.055 | 4.47 | 5.66 × 1.84 × 1.118 | 11.63 |
|  | Top‑half | 4.63 × 2.05 × 1.084 | 10.29 | 7.02 × 2.09 × 1.098 | 16.14 |
| **Emotion Games** | Top‑3 | 2.96 × 1.75 × 1.061 | 5.49 | 15.45 × 2.90 × 1.178 | 52.83 |
|  | Top‑half | 6.01 × 2.52 × 1.200 | 18.15 | 14.77 × 2.94 × 1.342 | 58.37 |
| **CLM** | Top‑3 | 3.79 × 1.78 × 1.158 | 7.83 | 10.81 × 2.42 × 1.354 | 35.35 |
|  | Top‑half | 6.08 × 2.52 × 1.171 | 17.96 | 12.30 × 2.93 × 1.314 | 47.39 |

## 05. GEE — population-average, as a check of direction

The same model, but without accounting for the fixed number of successes in a tournament — an effect averaged over the whole population rather than a within-tournament comparison. Given as a secondary check of the direction of the effect.

| Federation | Outcome | OR win_rate | 95% CI | OR judge_rank | 95% CI | OR interact. | 95% CI | p (interact.) |
|---|---|---|---|---|---|---|---|---|
| **iMafia** | Top‑3 | 2.42 | [2.23, 2.62] | 2.02 | [1.95, 2.10] | 0.978 | [0.957, 1.000] | 0.0520 |
|  | Top‑half | 4.19 | [3.74, 4.68] | 2.78 | [2.65, 2.92] | 1.059 | [1.023, 1.096] | 0.0012 |
| **FIIM** | Top‑3 | 3.20 | [2.96, 3.47] | 1.77 | [1.69, 1.85] | 1.067 | [1.042, 1.092] | 4.3e-08 |
|  | Top‑half | 5.24 | [4.69, 5.85] | 2.46 | [2.33, 2.60] | 1.148 | [1.095, 1.203] | 1.0e-08 |
| **Mafgame** | Top‑3 | 2.75 | [2.49, 3.03] | 1.54 | [1.48, 1.60] | 1.055 | [1.028, 1.081] | 3.3e-05 |
|  | Top‑half | 4.63 | [3.90, 5.50] | 2.05 | [1.95, 2.15] | 1.084 | [1.034, 1.136] | 0.0008 |
| **Emotion Games** | Top‑3 | 2.96 | [2.63, 3.32] | 1.75 | [1.64, 1.86] | 1.061 | [1.031, 1.092] | 5.3e-05 |
|  | Top‑half | 6.01 | [4.89, 7.38] | 2.52 | [2.31, 2.74] | 1.200 | [1.118, 1.288] | 4.6e-07 |
| **CLM** | Top‑3 | 3.79 | [2.76, 5.20] | 1.78 | [1.55, 2.06] | 1.158 | [1.047, 1.280] | 0.0042 |
|  | Top‑half | 6.08 | [3.68, 10.07] | 2.52 | [2.10, 3.03] | 1.171 | [0.984, 1.394] | 0.0760 |

> The direction and significance of both predictors agree with conditional logit in all 5 federations and both outcomes without exception. The ORs here are systematically smaller than in section 04 (the population-average effect is "diluted" compared with the within-tournament one) — consistent with the tendency known from the literature for population-averaged versus cluster-specific models (Neuhaus, Kalbfleisch & Hauck, 1991). This tendency is not a strict mathematical guarantee for the specific pair "GEE vs fixed-effects conditional logit" (unlike the pair "marginal vs random-effects mixed model", for which the inequality is formally proven) — so the agreement in direction should be read as consistent evidence, not as a formal proof that there is no error.

| Federation | Outcome | OR win_rate (GEE) | OR win_rate (clogit) | OR judge_rank (GEE) | OR judge_rank (clogit) |
|---|---|---|---|---|---|
| **iMafia** | Top‑3 | 2.42 | 5.02 | 2.02 | 2.98 |
|  | Top‑half | 4.19 | 6.82 | 2.78 | 2.97 |
| **FIIM** | Top‑3 | 3.20 | 8.57 | 1.77 | 2.38 |
|  | Top‑half | 5.24 | 9.04 | 2.46 | 2.60 |
| **Mafgame** | Top‑3 | 2.75 | 5.66 | 1.54 | 1.84 |
|  | Top‑half | 4.63 | 7.02 | 2.05 | 2.09 |
| **Emotion Games** | Top‑3 | 2.96 | 15.45 | 1.75 | 2.90 |
|  | Top‑half | 6.01 | 14.77 | 2.52 | 2.94 |
| **CLM** | Top‑3 | 3.79 | 10.81 | 1.78 | 2.42 |
|  | Top‑half | 6.08 | 12.30 | 2.52 | 2.93 |

## 06. Standardised comparison — OR per 1 SD

win_rate and judge_rank are quantities of a different nature (section 02), so their scales cannot be compared directly through "OR per +0.1". Here the same OR is rescaled to a step of one standard deviation of each predictor — but the SD is chosen differently for the two models, and it is important to say this explicitly. **For GEE** (population-average, not tied to a tournament) the pooled SD over the federation's whole dataset is used: SD(win_rate)≈0.155–0.162, SD(judge_rank)≈0.304–0.310. **For conditional logit** (which compares players strictly within a tournament) the pooled within-tournament SD is used — the standard deviation computed from deviations from each tournament's own mean (not from the overall dataset mean), since this is exactly the scale of variability that the model itself "sees": SD(win_rate)≈0.147–0.157, SD(judge_rank)≈0.313–0.322. In both cases the step for judge_rank is roughly twice as wide as the step for win_rate. The confidence intervals in the clogit columns are player-clustered (section 09).

| Federation | Outcome | GEE: win_rate /SD | GEE: judge_rank /SD | clogit: win_rate /SD | clogit: judge_rank /SD |
|---|---|---|---|---|---|
| **iMafia** | Top‑3 | 4.16 | 8.55 | 12.6 [6.02, 26.3] | 30.8 [11.8, 80.2] |
|  | Top‑half | 10.10 | 22.59 | 20.3 [15.1, 27.3] | 30.2 [23.8, 38.4] |
| **FIIM** | Top‑3 | 5.73 | 5.63 | 23.9 [11.2, 50.9] | 15.1 [6.65, 34.4] |
|  | Top‑half | 11.98 | 15.41 | 25.5 [18.7, 34.7] | 19.7 [15.3, 25.4] |
| **Mafgame** | Top‑3 | 5.01 | 3.73 | 14.9 [9.04, 24.5] | 6.74 [4.53, 10.0] |
|  | Top‑half | 11.51 | 8.89 | 20.8 [14.5, 29.9] | 10.1 [8.41, 12.2] |
| **Emotion Games** | Top‑3 | 5.35 | 5.55 | 62.7 [7.30, 538] | 29.2 [4.90, 174] |
|  | Top‑half | 16.06 | 16.91 | 58.2 [31.0, 109] | 30.4 [18.9, 48.9] |
| **CLM** | Top‑3 | 7.73 | 6.01 | 34.3 [5.11, 231] | 17.1 [3.55, 82.3] |
|  | Top‑half | 15.98 | 17.61 | 41.6 [9.88, 175] | 31.9 [8.65, 118] |

> ✅ After standardisation the picture is noticeably less clear-cut than in sections 04–05. In **iMafia** the judge score per 1 SD is more strongly associated with place than win_rate, in both models and both outcomes (for example, GEE/top‑half: 22.6 versus 10.1; conditional logit/top‑3: 30.8 versus 12.6, the confidence intervals do not overlap). In **Emotion Games** the predictors are almost comparable under GEE (top‑3: 5.55 versus 5.35), although under conditional logit win_rate is ahead again. In Mafgame and CLM win_rate is ahead in both models. The relative strength of the two factors depends on the federation and on whether the scale is standardised: in iMafia the judge score, at least statistically, does not fall behind winning but exceeds it. But all of this is at the average value of the other predictor: because of the wr×jr interaction, the effect of each predictor itself changes with the level of the other, see section 09.

## 07. Correlation coefficient by federation

Spearman rank correlation of place (1 = best) with the predictor, computed within each tournament and pooled over all tournaments of the federation via random-effects (DerSimonian–Laird) with the Bonett–Wright variance (section 03) — the main calculation. Comparison with the cruder basic variance (underestimated for the rank rho) — section 11. The sign is flipped (|rho| is shown).

| Federation | Tournaments | \|rho\| win_rate | 95% CI / Prediction Interval | I² | \|rho\| judge_rank | 95% CI / Prediction Interval | I² |
|---|---|---|---|---|---|---|---|
| **iMafia** | 733 | 0.789 | CI [0.780, 0.798]   PI [0.714, 0.846] | 8.08% | 0.885 | CI [0.880, 0.889]   PI [0.880, 0.889] | 0.00% |
| **FIIM** | 511 | 0.845 | CI [0.835, 0.854]   PI [0.692, 0.925] | 31.38% | 0.846 | CI [0.838, 0.853]   PI [0.773, 0.897] | 12.27% |
| **Mafgame** | 435 | 0.856 | CI [0.845, 0.867]   PI [0.646, 0.946] | 41.66% | 0.793 | CI [0.782, 0.804]   PI [0.719, 0.849] | 7.71% |
| **Emotion Games** | 277/276 * | 0.869 | CI [0.857, 0.880]   PI [0.749, 0.933] | 23.72% | 0.857 | CI [0.844, 0.868]   PI [0.734, 0.926] | 22.78% |
| **CLM** | 56 | 0.852 | CI [0.817, 0.880]   PI [0.666, 0.938] | 27.68% | 0.769 | CI [0.729, 0.804]   PI [0.728, 0.805] | 0.00% |

> |rho| is 0.77–0.89 for both predictors in all federations. In **iMafia** the judge score correlates with place even more strongly than win‑rate (0.885 versus 0.789) — consistent with section 06. Heterogeneity between tournaments with this variance is low to moderate (I² 0–42%; section 11 explains why the cruder formula gives estimates up to 57%) — the prediction interval is therefore narrower than with that cruder variance, but still noticeably wider than the confidence interval of the mean (for example, iMafia/win_rate: CI [0.780,0.798], PI [0.714,0.846]) — this is the expected range of rho for one new tournament of the same federation, not for the average over many tournaments. For **iMafia/judge_rank** and **CLM/judge_rank** τ²=0 (I²=0%) — no between-tournament heterogeneity was detected, which is not the same as proving its absence at this sample size.

*** Emotion Games:** one tournament (2024, 10 players) gave rho(place, judge_rank)=−1 exactly — the judge rank matched the place perfectly, which makes z=atanh(−1) undefined. This tournament is excluded from the pool for judge_rank (276 tournaments instead of 277); for win_rate there is no such tournament, and the pool there is complete (277).

## 08. Stability over years

The same rank correlation (|rho|, fixed-effect — for the breakdown by year, where samples are smaller and RE is less stable), by year.

| Federation | 2024 (wr / jr) | 2025 (wr / jr) | 2026 (wr / jr) |
|---|---|---|---|
| **iMafia** | 0.81 / 0.89 | 0.77 / 0.88 | 0.78 / 0.88 |
| **FIIM** | 0.86 / 0.84 | 0.82 / 0.85 | 0.84 / 0.86 |
| **Mafgame** | 0.84 / 0.78 | 0.85 / 0.80 | 0.87 / 0.80 |
| **Emotion Games** | 0.88 / 0.84 | 0.88 / 0.87 | 0.82 / 0.88 |
| **CLM** | 0.84 / 0.80 | 0.84 / 0.76 | — |

In all federations and in all years |rho| stays within 0.76–0.89 for both predictors — the picture barely changes from year to year. CLM has no data for 2024 (there are no rating tournaments of this federation in the sample for that year). 2026 is incomplete: the sample includes tournaments up to September 2026.

## 09. Repeated participation of players

The same player_id may be present in several tournaments of one federation, so observations across tournaments are not fully independent at the player level. The main conditional logit coefficients (section 04) are estimated strictly within tournaments and do not depend on this, but for an honest estimate of uncertainty and to check whether any of the conclusions is driven by a small group of active players, below is a separate block of calculations on top of the main model. Player identifiers are not comparable between federations (different namespaces, the same person may have different IDs in different federations) — matching the same physical person across federations remains an unobservable limitation, and all calculations of this section are done strictly within each federation separately.

#### How "regular-heavy" the sample is (by federation)

| Federation | Tournaments | Rows | Players | Median t./player | P95 | Max | Share of rows: 1 / 2–5 / 6–10 / >10 tournaments |
|---|---|---|---|---|---|---|---|
| **iMafia** | 733 | 13 298 | 1 725 | 3 | 30.0 | 102 | 3.8% / 13.6% / 14.7% / 68.0% |
| **FIIM** | 511 | 9 865 | 1 972 | 2 | 20.0 | 55 | 7.9% / 21.2% / 16.8% / 54.1% |
| **Mafgame** | 435 | 7 674 | 1 591 | 2 | 18.0 | 65 | 8.0% / 22.6% / 19.0% / 50.4% |
| **Emotion Games** | 277 | 4 528 | 1 024 | 2 | 17.0 | 44 | 8.4% / 26.7% / 23.3% / 41.5% |
| **CLM** | 56 | 767 | 225 | 2 | 13.0 | 27 | 12.8% / 35.3% / 16.6% / 35.3% |

> ⚠️ The median player takes part in only 2–3 tournaments, but the distribution is strongly skewed: 100+ tournaments for the most active iMafia player, and players with more than 10 tournaments contribute **68% of all rows in iMafia** (and 41–54% in the other federations). Clustering the uncertainty by player is not a formality here.

#### Own implementation of conditional logit

The standard library used for section 04 does not return the per-row contributions to the likelihood gradient that are needed for the sandwich estimate by player. Therefore the clustered calculations of this section were done with a separate implementation of the exact conditional likelihood (section 03), validated in three ways: (1) on a small stratum — the likelihood and gradient match a full enumeration of all C(n,k) combinations to 1e-10; (2) on all 10 slices — the ordinary (model-based) standard errors match those given in section 04 to 3–4 significant digits; (3) in all fits — the gradient at the found maximum is below 1e-7. The maximum point in the new implementation converges slightly more precisely — the point estimates of OR differ from those given in section 04 by no more than ~1% (the largest — FIIM/top‑3, win_rate: 8.65 versus 8.57), which nowhere changes the sign, significance or qualitative conclusions of sections 04–07. This is an additional, independent check of the accuracy of the main calculations.

#### Player-clustered uncertainty: does the main iMafia conclusion hold?

A direct test on the scale of 1 within-tournament SD (section 06): the difference log-OR_perSD(judge_rank) − log-OR_perSD(win_rate), with a 95% confidence interval and p-value clustered by player (section 03). A positive value means judge_rank is stronger; a negative one means win_rate is stronger. The last column is the same test on a cruder scale (pooled-by-federation SD, without a split by tournament), as a sensitivity check to the choice of standardisation scale.

| Federation | Outcome | Difference (log-OR/within-SD, jr−wr) | 95% CI (player-clustered) | p (within-SD) | p (pooled-SD, for comparison) |
|---|---|---|---|---|---|
| **iMafia** | Top‑3 | +0.893 | [+0.565, +1.221] | 9.1e-08 | 1.8e-06 |
|  | Top‑half | +0.398 | [+0.221, +0.576] | 1.1e-05 | 0.021 |
| **FIIM** | Top‑3 | -0.457 | [-0.727, -0.186] | 0.00095 | 1.1e-05 |
|  | Top‑half | -0.256 | [-0.497, -0.015] | 0.038 | 0.0014 |
| **Mafgame** | Top‑3 | -0.792 | [-1.078, -0.505] | 6.3e-08 | 1.5e-09 |
|  | Top‑half | -0.720 | [-0.994, -0.446] | 2.5e-07 | 2.6e-09 |
| **Emotion Games** | Top‑3 | -0.764 | [-1.328, -0.201] | 0.0079 | 0.0029 |
|  | Top‑half | -0.649 | [-1.055, -0.244] | 0.0017 | 5.5e-05 |
| **CLM** | Top‑3 | -0.698 | [-1.562, +0.166] | 0.11 | 0.049 |
|  | Top‑half | -0.265 | [-1.123, +0.593] | 0.55 | 0.25 |

> ✅ **iMafia is the only federation where the difference is positive (judge_rank stronger) in both outcomes, and on the scale appropriate for conditional logit (within-tournament SD) this is especially clear** — for top‑half p drops from 0.021 (crude scale) to 1.1e‑05 (appropriate scale). In all other federations the sign (win_rate stronger) is preserved, but the significance is weaker than on the crude scale, and for **CLM** the advantage of win_rate loses significance on the appropriate scale in both outcomes (p=0.11 and p=0.55) — one more reason to be careful with conclusions about CLM (56 tournaments, section 11).

Two-way clustering (by player and tournament at once, section 03) is not given as the main one: in this model it turns out *narrower*, not wider, than player-clustered — the contributions of rows within a stratum sum to zero by construction (Σ(y_i−π_i)=0), so within-tournament dependence is already partly accounted for by conditioning on the stratum, and the two-way correction subtracts more than it adds. It is not a conservative estimate for this model, so the main one is player-clustered. For **CLM/top‑3** (56 tournaments) the two-way matrix is moreover not positive semi-definite — it was not computed rather than imitated.

#### Is the conclusion driven by a small group of active players? "One tournament per player" subsamples

200 replicates per slice; in each, every player of the federation has exactly one randomly chosen row (tournament) — so by construction no player can drive the result disproportionately. Shown are the share of replicates where the judge score is stronger than winning on the SD scale, and the median difference [5th–95th percentile across replicates].

| Federation | Outcome | Share of replicates, jr>wr | Median difference [5–95%] |
|---|---|---|---|
| **iMafia** | Top‑3 | 50.5% | +0.020 [-1.210, +0.775] |
|  | Top‑half | 18.5% | -0.242 [-0.873, +0.247] |
| **FIIM** | Top‑3 | 0.0% | -1.119 [-1.695, -0.649] |
|  | Top‑half | 9.0% | -0.199 [-0.456, +0.051] |
| **Mafgame** | Top‑3 | 0.0% | -1.967 [-3.308, -1.156] |
|  | Top‑half | 0.0% | -0.986 [-1.356, -0.693] |
| **Emotion Games** | Top‑3 | 2.5% | -0.928 [-1.678, -0.212] |
|  | Top‑half | 4.5% | -0.373 [-0.757, -0.006] |
| **CLM** | Top‑3 | 1.5% | -1.456 [-2.889, -0.236] |
|  | Top‑half | 4.5% | -1.197 [-2.467, -0.052] |

> ⚠️ An important caveat to this test
>
> The subsamples are small (2–5 rows per stratum, 25–289 informative strata instead of the full 56–733), and conditional logit on sparse strata is known to bias coefficients upwards (sparse-data bias, close to quasi-separation) — it hits the larger and noisier β_win_rate hardest: the median OR_win_rate in the subsamples is everywhere above the full-data estimate (for example, Mafgame/top‑3: 5.7 → 11.7). Therefore this test is *systematically biased in favour of win_rate* and is not a "clean" check of the influence of regulars — for iMafia it does not confidently confirm "jr>wr" (50.5% and 18.5% of replicates), but because of its own bias it does not clearly refute it either. A more precise answer to the question about regulars is below, in the split into frequent and infrequent players without thinning the strata.

#### How much information each predictor carries: nested models

For each slice — conditional logit with both predictors (M_full), with win_rate only (M_wr) and with judge_rank only (M_jr), on the same tournament strata (section 03). ΔAIC — how much worse the single-predictor model is than the full one (smaller = closer to the full one, i.e. less is lost by dropping the other predictor).

| Federation | Outcome | ΔAIC without judge_rank (wr only) | ΔAIC without win_rate (jr only) | Which single-predictor model is closer to the full one |
|---|---|---|---|---|
| **iMafia** | Top‑3 | 3 215 | 1 881 | judge_rank |
|  | Top‑half | 5 974 | 3 720 | judge_rank |
| **FIIM** | Top‑3 | 1 987 | 2 110 | win_rate |
|  | Top‑half | 3 588 | 3 173 | judge_rank |
| **Mafgame** | Top‑3 | 1 313 | 1 715 | win_rate |
|  | Top‑half | 2 341 | 2 734 | win_rate |
| **Emotion Games** | Top‑3 | 1 074 | 1 147 | win_rate |
|  | Top‑half | 1 645 | 1 699 | win_rate |
| **CLM** | Top‑3 | 208 | 258 | win_rate |
|  | Top‑half | 309 | 312 | win_rate (almost a tie) |

In both single-predictor models the fit is sharply worse than the full one in all 10 slices (ΔAIC from 200 to almost 6000), and the player-clustered Wald test (the robust version of the LR test, section 03 — LR assumes independent strata, while players repeat) rejects both restricted models everywhere, p≤0.0007. Both predictors carry irreplaceable information in every federation.

#### The same picture out of sample: cross-validation by whole tournaments

5-fold CV, the folds are whole tournaments (not rows), one run with a fixed seed. pseudo-R²_oos = 1 − NLL_oos/NLL_oos(null model) — the share of conditional uncertainty explained by this particular model on tournaments not seen during training.

| Federation | Outcome | pseudo-R²_oos, full model | win_rate only | judge_rank only |
|---|---|---|---|---|
| **iMafia** | Top‑3 | 0.740 | 0.376 | 0.529 |
|  | Top‑half | 0.750 | 0.381 | 0.521 |
| **FIIM** | Top‑3 | 0.756 | 0.452 | 0.433 |
|  | Top‑half | 0.730 | 0.431 | 0.466 |
| **Mafgame** | Top‑3 | 0.684 | 0.435 | 0.358 |
|  | Top‑half | 0.676 | 0.424 | 0.382 |
| **Emotion Games** | Top‑3 | 0.822 | 0.490 | 0.468 |
|  | Top‑half | 0.781 | 0.476 | 0.467 |
| **CLM** | Top‑3 | 0.776 | 0.452 | 0.367 |
|  | Top‑half | 0.746 | 0.410 | 0.414 |

> The in-sample picture (ΔAIC above) is confirmed out of sample: the full model explains 68–82% of the conditional uncertainty of place compared with the model "all permutations are equally likely". In **iMafia** the judge_rank-only model predicts noticeably better than the win_rate-only model (0.529 versus 0.376 for top‑3, 0.521 versus 0.381 for top‑half) — the largest gap among all federations. In Mafgame it is the opposite, win_rate is ahead; in FIIM the difference depends on the outcome; in Emotion Games and CLM it is practically a tie. The same caveat about endogeneity (section 11) applies here in full: judge_rank enters total_points directly, so the high R² largely reproduces the scoring formula rather than "predicting the game" in a substantive sense.

#### Where exactly the effect arises in iMafia: frequent and infrequent players

A grouping feature "frequent participant" is added to the model (freq=1 if the player's number of tournaments in this federation is ≥ the row-weighted median — each federation has its own threshold) with interactions freq×win_rate and freq×judge_rank; the set of tournament strata does not change, and the group varies within a tournament. Shown is the same difference log-OR_perSD(jr−wr) separately for infrequent and frequent players, and the p-value for whether the groups differ significantly from each other.

| Federation (threshold, frequent/infrequent players) | Outcome | Difference jr−wr, infrequent players | Difference jr−wr, frequent players | p (frequent ≠ infrequent) |
|---|---|---|---|---|
| **iMafia**   ≥18 tournaments, 214/1511 | Top‑3 | +0.141 [-0.190,+0.472]   p=0.41 | +1.111 [+0.720,+1.501]   p=2.5e-08 | 0.00032 |
|  | Top‑half | +0.019 [-0.205,+0.243]   p=0.87 | +0.344 [+0.083,+0.605]   p=0.0099 | 0.047 |
| **FIIM**   ≥11 tournaments, 272/1700 | Top‑3 | -0.741 [-1.134,-0.348]   p=0.00022 | -0.549 [-0.912,-0.187]   p=0.003 | 0.49 |
|  | Top‑half | -0.289 [-0.598,+0.019]   p=0.066 | -0.578 [-0.936,-0.219]   p=0.0016 | 0.22 |
| **Mafgame**   ≥11 tournaments, 196/1395 | Top‑3 | -1.131 [-1.533,-0.730]   p=3.4e-08 | -0.814 [-1.207,-0.422]   p=4.8e-05 | 0.21 |
|  | Top‑half | -0.967 [-1.297,-0.637]   p=9.5e-09 | -0.827 [-1.287,-0.368]   p=0.00042 | 0.61 |
| **Emotion Games**   ≥8 tournaments, 168/856 | Top‑3 | -1.060 [-1.894,-0.225]   p=0.013 | -0.971 [-1.727,-0.215]   p=0.012 | 0.83 |
|  | Top‑half | -0.767 [-1.247,-0.288]   p=0.0017 | -1.149 [-1.811,-0.486]   p=0.00067 | 0.33 |
| **CLM**   ≥6 tournaments, 33/192 | Top‑3 | -1.165 [-2.650,+0.320]   p=0.12 | -0.696 [-1.695,+0.303]   p=0.17 | 0.60 |
|  | Top‑half | -0.516 [-1.435,+0.403]   p=0.27 | -0.513 [-2.147,+1.122]   p=0.54 | 1.00 |

> ✅ The key finding of this block
>
> In iMafia the advantage of judge_rank over win_rate is not a property of the whole sample but is concentrated among about 200 of the most active players (≥18 tournaments, 214 of 1725, but they contribute the majority of rows — see the diagnostics above). Among them the difference in favour of judge_rank is large and confidently significant: +1.11 [0.72,1.50] for top‑3 (p=2.5e‑08), +0.34 [0.08,0.61] for top‑half (p=0.0099). Among less active iMafia participants judge_rank and win_rate are statistically indistinguishable: +0.14 (p=0.41) and +0.02 (p=0.87) — neither difference differs from zero. The difference between the groups is itself significant (p=0.00032 and p=0.047). In the other four federations there is no such stratification — the difference between frequent and infrequent players is nowhere significant (p≥0.21), and the advantage of win_rate is equally pronounced in both groups.

**Refined statement:** in iMafia the judge score is, on average, no weaker than winning in its association with place (sections 04, 06, 07) and noticeably stronger among the federation's regular participants — but this advantage is concentrated in a relatively small group of the most active players and is not a uniform property of the whole iMafia sample.

#### Gradient or threshold? Continuous dependence on player experience

Splitting into frequent and infrequent players at the median is a dichotomy that could artificially create a boundary where there is actually a smooth gradient. The check: the same model, but instead of a binary feature — continuous activity a=log(1+n_tournaments of the player in the federation) with interactions a×win_rate and a×judge_rank (plus, separately, a restricted cubic spline in a with 4 knots to test non-linearity). dslope — how much the advantage of judge_rank over win_rate (on the within-SD scale) grows when log(1+n) increases by one; the non-linearity test — whether the spline differs from a straight line.

| Federation | Outcome | dslope (jr−wr per log(1+n)) | p (dslope) | p (non-linearity, df=4) |
|---|---|---|---|---|
| **iMafia** | Top‑3 | +0.501 | 0.070 | 0.51 |
|  | Top‑half | +0.253 | 0.0039 | 0.17 |
| **FIIM** | Top‑3 | +0.227 | 0.16 | 0.38 |
|  | Top‑half | -0.175 | 0.20 | 0.80 |
| **Mafgame** | Top‑3 | +0.232 | 0.085 | 0.71 |
|  | Top‑half | +0.045 | 0.76 | 0.22 |
| **Emotion Games** | Top‑3 | +0.275 | 0.29 | 0.49 |
|  | Top‑half | -0.231 | 0.29 | 0.91 |
| **CLM** | Top‑3 | +0.078 | 0.88 | 0.93 |
|  | Top‑half | +0.365 | 0.62 | 0.87 |

Prediction for iMafia — the difference jr−wr (on the within-SD scale) at the 10th/50th/90th percentile of the player's number of tournaments (row-weighted: 3/18/50), from the linear model:

| Outcome | n tournaments (percentile) | Difference jr−wr | 95% CI (player-clustered) | p |
|---|---|---|---|---|
| **Top‑3** | 3 (p10) | +0.052 | [-0.643, +0.747] | 0.88 |
|  | 18 (p50) | +0.832 | [+0.536, +1.127] | 3.6e-08 |
|  | 50 (p90) | +1.326 | [+0.570, +2.082] | 0.00059 |
| **Top‑half** | 3 (p10) | -0.000 | [-0.259, +0.258] | 1.00 |
|  | 18 (p50) | +0.394 | [+0.176, +0.611] | 0.00038 |
|  | 50 (p90) | +0.643 | [+0.314, +0.973] | 0.00013 |

> ✅ **This is a gradient, not an artefact of the ≥18 tournaments threshold.** In iMafia the advantage of judge_rank over win_rate grows smoothly with player experience — for low-activity players (n≈3) the difference is practically zero (0.05 and 0.00), for the median regular participant (n≈18) it is already large and significant, and for the most active (n≈50) it is even larger. The spline predicts almost the same as the linear model, and the non-linearity test is not significant (p=0.51 and 0.17) — there is no step at the threshold, a linear gradient in log(1+n) is sufficient. In none of the other four federations was a stable gradient found (p(dslope)≥0.085 everywhere, with signs in different directions) — the dependence of the effect on player experience is in this sense specific to iMafia.

Caveat: a player's experience (number of tournaments played) may itself be related to their level of play or to which judges they more often play under — the model shows that the effect is modified by experience, not why.

#### The effect of one predictor depends on the level of the other (marginal effects)

The OR per 1 SD in section 06 is the effect of a predictor exactly at the average value of the other one (this is how a model with the wr×jr interaction term works). Below — by how many times the OR of each predictor changes if its value at a low (10th percentile) and high (90th percentile) level of the other predictor are compared — that is, how strong the interaction is "by eye".

| Federation | Outcome | Growth of OR(judge_rank) from low to high win_rate | Growth of OR(win_rate) from low to high judge_rank |
|---|---|---|---|
| **iMafia** | Top‑3 | ×1.3 | ×1.3 |
|  | Top‑half | ×3.2 | ×3.2 |
| **FIIM** | Top‑3 | ×7.2 | ×7.9 |
|  | Top‑half | ×9.9 | ×10.9 |
| **Mafgame** | Top‑3 | ×4.2 | ×4.2 |
|  | Top‑half | ×3.4 | ×3.3 |
| **Emotion Games** | Top‑3 | ×7.9 | ×7.8 |
|  | Top‑half | ×43.6 | ×42.0 |
| **CLM** | Top‑3 | ×38.5 | ×47.3 |
|  | Top‑half | ×27.1 | ×32.5 |

For illustration — the levels of OR(judge_rank per +1 SD) in iMafia at low/medium/high win_rate (p10=0.25 / p50=0.47 / p90=0.67): top‑3 — 26.4 / 30.1 / 34.0; top‑half — 15.1 / 27.5 / 48.0.

> Almost everywhere the effect of each predictor is noticeably stronger for players who are already strong on the other predictor — the judge score "works" more strongly for those who already win a lot, and vice versa (growth from ×3 to ×47). **iMafia/top‑3 is a notable exception:** the effect of judge_rank barely changes with the level of win_rate (×1.3, and the interaction is not significant there — section 04). This is an argument against the reading "a high judge score compensates for losses": in most federations at a low win_rate the effect of judge_rank is several times weaker than at a high one — the predictors reinforce each other rather than substitute for each other.

#### Limitations of this block

- **player_id is not comparable between federations.** The same person may have different IDs in different federations — matching the same physical person across federations remains an unobservable limitation, and all calculations of this section are done strictly within one federation.
- **Two-way (player×tournament) clustering for CLM/top‑3 is not available** — the matrix is not positive semi-definite with 56 tournaments, and it was not imitated.
- **"One tournament per player" subsamples are subject to sparse-data bias**, which systematically shifts the comparison in favour of win_rate — they are used only as an additional, not decisive, signal.
- **Cross-validation is a single 5-fold run without repetitions with different seeds.** The direction of the result (the full model is better than both single-predictor ones; in iMafia jr-only is better than wr-only) is consistently reproduced in the independent in-sample test (ΔAIC) of the same table, but the absolute values of pseudo-R²_oos could fluctuate slightly with other seeds.

## 10. Hypothetical cross-federation interpretation (assuming win_rate is transferable)

> ⚠️ This section is not a standalone empirical result but an illustration of what the findings of sections 04–09 lead to if an additional assumption, not separately tested on the data of this report, is accepted. The main conclusions of the report (sections 04–09) do not need it and do not depend on it.

The starting assumption of this section: the win_rate of the same player does not, on average, depend much on which of the five federations they play in. It rests on practice and on the uniformity of the game rules — the basic set of roles and their random dealing are the same everywhere (section 02), and win_rate is essentially the share of won games with a randomly dealt role. The report does not test this assumption statistically on its data — a direct test would require matching the same player across federations, and the data are organised in separate federation namespaces and do not provide such matching. But if it is accepted, the difference between federations in what is more strongly associated with place — win_rate or judge_rank — is no longer explained by a different level of players and starts to reflect how the table of each federation turns judge points into place.

Under this assumption iMafia is not like the other four. In the standardised comparison (section 06, conditional logit — the main model) only in iMafia is the standardised association of judge_rank with the outcome larger than that of win_rate, and in both outcomes at once: top‑3 — 30.8 versus 12.6, top‑half — 30.2 versus 20.3 (OR per 1 within-tournament SD). In most other federations (FIIM, Mafgame, Emotion Games) in the same model, on the contrary, the association of win_rate is larger; in CLM the same direction is seen in the point estimate, but once repeated participation of players is accounted for it can no longer be called statistically reliable for either outcome (section 09). This is also consistent with the direct correlation (section 07): in iMafia place correlates with judge_rank at 0.885 and with win_rate at 0.789, and this is stable over the years (section 08) — 0.88–0.89 versus 0.77–0.81 in every year 2024–2026.

Section 09 tests this finding for robustness to the same players taking part in many iMafia tournaments. The check (player-clustered uncertainty, nested models, out-of-sample prediction) on the whole confirms the conclusion for the full sample — but with an important refinement: the advantage of judge_rank over win_rate is concentrated among about 200 of the most active iMafia players (section 09, the last block). Among less active iMafia participants judge_rank and win_rate are statistically indistinguishable — no such structural difference from the other federations was found there.

> ✅ Practical conclusion — under this assumption, with the refinement from section 09
>
> If a player's win_rate is indeed transferable between federations, then of the two factors determining their place, for a **regular participant of iMafia tournaments** the main source of differences from players with a similar win_rate is the judging component: in this group it noticeably exceeds win_rate. For an occasional or infrequent iMafia participant no such skew is observed — there the judge score and win_rate are statistically equally associated with place, as in most other federations. Conversely: for a player with a high win_rate but average judge points, wins convert into place more noticeably in FIIM, Emotion Games or CLM than in iMafia — this remains true regardless of how often they participate. This is a description of how sorting in the table works, not a causal recommendation to "play so as to get more judge points" — judge_rank is not necessarily a quantity that can be controlled by strategy in the same sense as the choice of game tactics.

This does not mean that the absolute probability of reaching the top‑3 for the same player is higher in iMafia than in other federations. The report only shows that *within* iMafia (and above all among regular participants) the judge score sorts players relative to each other more strongly at a comparable win_rate — this is a comparison of players' relative positions within one tournament table, not of one player's absolute chances across federations (which would require additional data — for example, on the strength of the field of each tournament).

This is the same result as in sections 06–09, only formulated from the player's point of view rather than the model's — and with the same caveat as everywhere in the report (section 11, endogeneity): both predictors enter the very formula of the place mathematically, so this is about how the place-scoring system of each federation is built, not about an independently measured effect of "skill" on the result.

## 11. Caveats

> ⚠️ Endogeneity
>
> Both predictors enter the place formula mechanically: a win gives `win_points`, the judge score is part of `value`, and both are terms of `total_points`. The regression here largely decomposes the scoring formula into its components rather than measuring the effect of "good play" on something external to the place. Correlations of about 0.8 in section 07 are not proof of the "strength of influence" of judging or wins in a substantive sense: a significant part of the dependence is built mathematically into the definition of the place itself.

> ⚠️ GEE and repeated participation of players
>
> GEE (section 05) is clustered by tournament but not by player: the same player_id appears in different tournament clusters, and this second dependence is not accounted for in its standard errors at all. Section 09 checks this for the main model (conditional logit) via player-clustered uncertainty; there is no similar check for GEE. Since GEE is used in this report only as a secondary check of the direction of the effect (section 05), not as a source of final confidence intervals, this does not affect the conclusions — but its CIs and p-values should be treated as additional, not final, results.

> ⚠️ Collinearity of predictors
>
> r(win_rate, judge_rank) = 0.34–0.51 in all federations — judges more often give extra points to winners. Both models account for this by including both predictors in one model simultaneously, but with r in this range (VIF ≈ 1.1–1.4) the coefficients are not at risk of numerical instability — the caveat concerns the interpretation of the "pure" contribution of each predictor, not the stability of the estimate itself.

> ⚠️ Fisher z — comparison with the cruder (basic) variance
>
> Section 07 uses the more accurate Bonett–Wright variance as the main one: SE_BW(z)=√((1+ρ̄²/2)/(n−3)), where ρ̄ is the overall pooled estimate of rho for the federation and predictor, not the rho of a specific tournament (section 03 explains why: plugging in the tournament's own rho would make its weight depend on its own estimate and would bias the result — this is not a generally accepted strict rule but a reasonable plug-in technique that removes exactly this dependence). Here is the same pooling with the cruder formula SE(z)≈1/√(n−3), derived exactly for the Pearson coefficient under bivariate normality and underestimating the variance for the rank rho, as a comparison (sensitivity):
>
> | Federation | Predictor | \|rho\| Bonett–Wright (section 07) | I² Bonett–Wright | \|rho\| basic formula | I² basic formula |
> |---|---|---|---|---|---|
> | **iMafia** | win_rate | 0.789 | 8.08% | 0.793 | 30.06% |
> |  | judge_rank | 0.885 | 0.00% | 0.882 | 27.53% |
> | **FIIM** | win_rate | 0.845 | 31.38% | 0.847 | 49.50% |
> |  | judge_rank | 0.846 | 12.27% | 0.844 | 35.31% |
> | **Mafgame** | win_rate | 0.856 | 41.66% | 0.859 | 57.38% |
> |  | judge_rank | 0.793 | 7.71% | 0.787 | 29.54% |
> | **Emotion Games** | win_rate | 0.869 | 23.72% | 0.871 | 44.69% |
> |  | judge_rank | 0.857 | 22.78% | 0.854 | 43.42% |
> | **CLM** | win_rate | 0.852 | 27.68% | 0.856 | 47.06% |
> |  | judge_rank | 0.769 | 0.00% | 0.769 | 0.00% |
>
> The point estimates of rho barely change between the two formulas (within 0.01), but I² with the basic formula is systematically higher — part of the "heterogeneity between tournaments" that it shows is actually explained by the underestimated within-tournament variance of the rho estimate itself, not by genuine differences between the federation's tournaments. The choice of formula does not affect the sign or the order of magnitude of rho, or any of the conclusions of sections 01, 06, 07 and 09 — the difference is only in how "heterogeneous" the picture between tournaments looks.

> ⚠️ Small samples and degeneracy
>
> **CLM** (56 tournaments) is noticeably smaller than the other federations (277–733), and its confidence intervals are systematically wider. One slice (5 CLM tournaments in one year, 81 rows) did not converge for the top‑3 model because of quasi-separation (with a small number of tournaments and continuous predictors a linear combination can almost perfectly separate 0 and 1, which sends the logistic regression coefficient to infinity) — this row is excluded from the analysis as invalid rather than presented with a distorted confidence interval.

## 12. Self-check

The check was done in four layers: (1) internal mathematical consistency of all numbers in the report; (2) algebraic correspondence between the two parameterisations of the model; (3) correctness of the methodology at the boundaries of applicability — ties in the rank correlation and degenerate cases; (4) robustness of the main result to repeated participation of players.

### Layer 1 — numerical consistency

- **Additivity.** T, N, Σtop‑3, Σtop‑half by year add up exactly to the combined figure for each of the 5 federations — without a single discrepancy.
- **The identity Σjudge_rank = N/2.** Checked on all rows without exception.
- **exp(coef) reproduces the OR in the uncentred model** on all checked rows — the recalculation matches the stated OR up to rounding.
- **The full joint OR and the OR per 1 SD (sections 04, 06) are recalculated from already checked numbers** without new raw data — both operations (the product of three ORs; raising the OR to the power SD/0.1) follow directly from the formulas of section 03.

### Layer 2 — algebraic cross-check of the two GEE parameterisations

The uncentred and the centred models are the same model represented differently, so there is an exact algebraic correspondence between their coefficients (section 03).

| Federation | Outcome | β₃ (uncentred) | β₃, recovered from OR | OR win_rate, predicted | OR win_rate, obtained |  |
|---|---|---|---|---|---|---|
| **iMafia** | Top‑3 | -2.186 | -2.184 | 2.417 | 2.417 | matches |
|  | Top‑half | 5.702 | 5.733 | 4.187 | 4.186 | matches |
| **FIIM** | Top‑3 | 6.452 | 6.485 | 3.203 | 3.203 | matches |
|  | Top‑half | 13.800 | 13.802 | 5.236 | 5.236 | matches |
| **Mafgame** | Top‑3 | 5.308 | 5.354 | 2.747 | 2.747 | matches |
|  | Top‑half | 8.052 | 8.066 | 4.630 | 4.632 | matches |
| **Emotion Games** | Top‑3 | 5.899 | 5.921 | 2.956 | 2.956 | matches |
|  | Top‑half | 18.200 | 18.232 | 6.010 | 6.011 | matches |
| **CLM** | Top‑3 | 14.670 | 14.669 | 3.791 | 3.791 | matches |
|  | Top‑half | 15.780 | 15.786 | 6.080 | 6.083 | matches |

> ✅ Agreement on all 10 slices (5 federations × 2 outcomes) to the third decimal place.

### Layer 3 — correctness of the methodology at the boundaries of applicability

- **Correctness of Spearman with ties.** The calculation used the standard Spearman rank correlation with average ranks for ties (correct for this case). In addition, it was independently recomputed by hand (average ranks + Pearson correlation) for all 4024 "tournament×predictor" pairs — the maximum discrepancy between the two independent implementations is 2.2×10⁻¹⁶, i.e. at the level of floating-point error. Ties in the predictor were actually present in 3734 of the 4024 pairs — the check is substantive, not a formality.
- **Handling of the degenerate case rho=−1.** In one tournament (Emotion Games, 2024, 10 players) the judge rank matched the place perfectly — rho=−1 exactly, for which the Fisher transformation is undefined (atanh(±1)=±∞). This tournament is excluded from the pool for judge_rank (section 07) rather than replaced by an approximation through clipping |rho| — within the method chosen here (Fisher z-transform + DerSimonian–Laird) this is a correct way to handle the boundary, since z at this point is undefined, not merely large; alternative methods of meta-analysis of the rank rho (not based on Fisher z) could handle this case differently.
- **The Bonett–Wright recalculation (section 07) is checked against the basic formula (section 11).** The same pooling algorithm with the variance 1/(n−3) instead of Bonett–Wright reproduces another independent calculation to the third decimal place — the difference is not in the pooling itself but only in the variance formula.

### Layer 4 — robustness of the main result to repeated participation of players (section 09)

- **The independent implementation of conditional logit is checked against the main one in three ways** — on a small stratum with full enumeration (agreement to 1e-10), model-based SE against those given in section 04 (agreement to 3–4 significant digits), gradient at the maximum <1e-7 in all fits. The point estimates differ from those given in section 04 by no more than ~1% of OR — this is not a discrepancy of the method but slightly more precise convergence of the optimiser; it does not affect any sign or any significance.
- **The finding "in iMafia the judge score is no weaker than winning" is checked in five independent ways** — player-clustered SE, nested models (AIC), out-of-sample cross-validation, one-tournament-per-player subsamples, and the split into frequent and infrequent participants — and it is this check that revealed a more precise picture: the effect is not uniform across the whole iMafia sample but is concentrated among about 200 active players. This is not a refutation of the finding of section 06 but its refinement — exactly what the check was undertaken for.
- **Two-way (player×tournament) clustering was not computed where it is mathematically unavailable** (CLM, top‑3, the matrix is not positive semi-definite), instead of being substituted by an approximation — the same discipline as in the handling of rho=−1 above.
- **The "gradient vs threshold" check (section 09) confirmed that the finding about regular iMafia participants is not an artefact of the specific ≥18 tournaments threshold.** The continuous model of player activity and the restricted cubic spline give the same picture (the non-linearity test is not significant, p=0.51 and 0.17): the advantage of judge_rank grows smoothly with experience. No such gradient was found in any of the other federations.

### What remains an approximation

> **Fisher z variance** — the main estimate in section 07 uses the Bonett–Wright approximation for the rank rho. The classic formula 1/(n−3), exact for the Pearson coefficient under bivariate normality, is given in section 11 as a sensitivity analysis. The choice of formula barely affects the pooled rho but noticeably affects the estimate of between-tournament heterogeneity I². **Cross-validation in section 09** is a single 5-fold run with a fixed seed, not an average over several splits. **The "one tournament per player" subsamples (section 09)** are subject to the known upward bias on sparse strata and are used only as an additional, not decisive, signal.

## Result files

The final model results on which the report's tables are based are in [`data/`](data/):

| File | Contents |
|---|---|
| [`clogit_all.csv`](data/clogit_all.csv) | Conditional logit (section 04): coefficients, SE, OR per +0.1, 95% CI, p by federation, outcome and term |
| [`gee_raw.csv`](data/gee_raw.csv) | GEE in the raw parameterisation, by federation and period (all / 2024 / 2025 / 2026) |
| [`gee_centered_all.csv`](data/gee_centered_all.csv) | GEE with centred predictors, OR per +0.1, all years (section 05) |
| [`spearman.csv`](data/spearman.csv) | Rank correlation of place with the predictor by federation and period: FE and RE pool, I², median rho (section 08). The pool is computed with the basic variance 1/(n−3) over all tournaments, including the Emotion Games tournament with rho=−1, so for Emotion Games/judge_rank RE and I² differ from section 11 |
| [`self_check.csv`](data/self_check.csv) | Checksums for the self-check: T, N, Σwin_rate, Σjudge_rank, Σtop‑3, point-biserial r, exchangeable ρ (section 12) |

Federations are denoted by codes in the files: `uimafia` — iMafia, `ufiim` — FIIM, `umafgame` — Mafgame, `ueg` — Emotion Games, `uclm` — CLM.

---

*Data: tournaments and games from the Open Mafia World project database, exported in September 2026, 5 federations, January 2024 – September 2026 · conditional logit / GEE / Spearman*
