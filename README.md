# Does Height Affect Overall Rating in NBA 2K25?

An econometric analysis in R of how player height relates to overall rating in *NBA 2K25* (N = 457 players), built for ECON 425 at the University of Hawai'i at Manoa (Fall 2025).

**Full write-up:** `index.html` (rendered report) | **Code:** `scripts/` | **Data:** `data/current_nba_players.csv`

## Question

Taller players tend to be better at some things in basketball, so I expected height to push a player's overall rating up. I tested whether that holds once a player's skill ratings are accounted for.

## Data

- `current_nba_players.csv`: ratings and measurements for every player in NBA 2K25 (2024-25 season), 457 players.
- Outcome: `overall` (range 67-98, mean 77.8).
- Main variable: `height_cm` (173-226 cm, mean 200).
- Controls: `weight_kg`, plus 12 skill attributes (three-point shot, driving dunk, standing dunk, speed, ball handle, pass accuracy, interior defense, perimeter defense, steal, block, offensive rebound, defensive rebound).
- 14 players are missing skill attributes, so Model 3 uses 443 observations.
- Data source: [add source and date retrieved].

## Method

Three nested OLS models, each answering a different question:

1. `overall ~ height_cm` (raw relationship)
2. `overall ~ height_cm + weight_kg` (add body size)
3. `overall ~ height_cm + weight_kg + 12 skill attributes` (hold skill constant)

Comparing the height coefficient across models shows how much of the raw correlation is really omitted variable bias.

I also checked the six Classical Linear Model assumptions (MLR.1 to MLR.6) for the full model.

## Results

| | Model 1 | Model 2 | Model 3 |
|---|---|---|---|
| `height_cm` | 0.055 (p = 0.111) | -0.020 (p = 0.676) | **-0.024 (p = 0.643)** |
| `weight_kg` | | 0.091 (p = 0.015) | 0.064 (p = 0.041) |
| Observations | 457 | 456 | 443 |
| R-squared | 0.006 | 0.020 | 0.535 |
| Adjusted R-squared | 0.003 | 0.016 | 0.520 |

Strongest skill predictors in Model 3 (all p < 0.001): pass accuracy (0.198), defensive rebound (0.136), three-point shot (0.105), interior defense (0.097), block (0.065), and steal (-0.060, negative).

**Takeaways**

- Height is not a statistically significant predictor of overall rating in any model, and it is never close once skills are controlled (p = 0.64).
- Adding skill attributes raises R-squared from 0.02 to 0.54, roughly 51 percentage points of explained variation, so ratings are driven by skills rather than size.
- The result fits the omitted-variable story: height is correlated with skills like rebounding and interior defense, which are what the rating rewards.

## Diagnostics

- **MLR.1 (linearity in parameters):** holds; no transformations or interactions.
- **MLR.2 (random sampling):** formally violated, since the data is the full population of players in the game. Results apply to NBA 2K25 specifically rather than to other seasons.
- **MLR.3 (no perfect collinearity):** no perfect collinearity, but collinearity among height, weight, dunking, rebounding and defense is high, which widens standard errors and makes height hard to estimate precisely. [Add VIF results if you run `car::vif(m3)`.]
- **MLR.4 (exogeneity):** Models 1 and 2 likely suffer from omitted variable bias. Model 3 reduces it but cannot rule it out.
- **MLR.5 (homoskedasticity):** Breusch-Pagan test, BP = 17.56, df = 14, p = 0.228. Fail to reject constant variance.
- **MLR.6 (normality):** residual histogram is roughly bell-shaped with a slightly heavier right tail. With 443 observations, inference is still reasonable.

## Limitations and next steps

- 2K's rating formula is proprietary, so the true functional form is unknown.
- All players are pooled. Height probably matters differently for guards and centers, so the next step is to add position and test a height-by-position interaction.
- Ratings are a video game's judgment of players, not measured on-court performance.

## Tools

R, tidyverse, stargazer, knitr, kableExtra, AER, car, lmtest, sandwich.
