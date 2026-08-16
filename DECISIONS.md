# Decision Log

## Assignment 2: Dataset (2026-XX-XX)
- Dataset: Airbnb listings across ten European cities — 51,707 rows, 12 fields covering price, room characteristics, host status, guest satisfaction, and location-level indices. [ADD: where the dataset came from, e.g. Kaggle link]
- Main variable of interest: total listing price, because it's the natural business outcome to explain and predict, and it turned out to be far from well-behaved (severely right-skewed: skewness ~25.7, kurtosis 1,500+).
- Key decisions: confirmed there were no missing values. Flagged two variables — `Safety_Index` and `Meal_at_Inexpensive_Restaurant` — as city-level lookup values (one value repeated across every listing in a city) rather than true listing-level variables, meaning they'd overstate their contribution if treated as independent continuous predictors later. Also noted ~4,500 listings with `num_bedrooms = 0`, mostly "entire home/apt" listings — flagged as plausible studios rather than data errors, but something to confirm before treating bedroom count as a clean ordinal variable.

## Assignment 3: Descriptive Stats (2026-XX-XX)
- Cleaning done: identified a small number of extreme high-price outliers (fewer than 1% of listings above $2,000/night, including a $43,956/night London listing — over 160x the median) that distort mean-based city comparisons. Decided that before doing comparative work, the outliers should be removed, log-transformed, or split out as a "luxury" segment rather than left to distort city-level averages. Also noted uneven city representation (London and Rome together are over a third of the data) and a lopsided room-type split (Entire home/apt 63%, Private room 36%, Shared room 0.7%) that makes city-by-room-type breakdowns unreliable for the smallest category.
- Most surprising pattern: superhosts average a *lower* satisfaction score (91.2) than non-superhosts (96.9) — the opposite of what the "superhost" label implies. Also surprising: business-listing share varies enormously by city, from ~10% in Amsterdam to ~59% in Lisbon.

## Assignment 4: Probability (2026-XX-XX)
- Normal vs. empirical, and why: price is nowhere close to normal (skewness ~25.7, kurtosis >1,500), driven by a handful of extreme luxury listings, so relied on the empirical distribution rather than a normal-distribution assumption. The empirical histogram and the probability scenarios agreed — both strongly right-skewed. Practically, ~64% of listings price above $200/night, giving a benchmark for budget vs. mid-market vs. premium positioning, while the thin share of listings under $75 suggests the deep-budget segment isn't worth chasing.

## Assignment 5: Inference (2026-XX-XX)
- What we tested, alpha, conclusion:
  1. One-sample z-test on mean price vs. $370: sample mean was $368.62 (below $370), but z = -0.615, p = 0.269 at α = 0.05 → fail to reject H0; not enough evidence the true average price is below $370.
  2. One-sample test on mean distance to city center vs. 3.2 miles: sample mean 3.19 miles, p = 0.425 at α = 0.10 → fail to reject H0; consistent with a true average distance of 3.2 miles.
  3. 95% CI for proportion of private-room listings: 35.738%–36.566%, a tight band given the huge sample size.
  - With n > 51,000, the CLT holds easily (np and n(1-p) far above 10 for the proportion test) even though the raw price variable itself is heavily skewed — the sampling distribution of the mean/proportion is effectively normal regardless.

## Assignment 6: Regression (2026-XX-XX)
- First predictor removed and why: an insignificant weekday dummy variable was dropped first.
- Multicollinearity handling: checked pairwise correlations among the five continuous/near-continuous predictors; the highest was 0.43 (distance to city center vs. nearby meal cost) and the next was -0.30 (safety index vs. meal cost) — both well under the usual 0.7 concern threshold. Decided not to remove any variables on multicollinearity grounds.
- Final model: 9 predictors, R² = 0.1453 (adjusted 0.1452). Bedroom count is the strongest driver (~$131/night per bedroom). Most surprising result: relative to a private room, an entire home/apartment prices about $143 *lower* and a shared room about $180 lower — the reverse of typical Airbnb pricing patterns, likely reflecting omitted factors like unit size or city tier rather than a real preference for smaller spaces. Framed the R² of 0.145 as a signal the model should guide direction, not precision, and noted the data is observational, so none of the relationships should be read as causal.
