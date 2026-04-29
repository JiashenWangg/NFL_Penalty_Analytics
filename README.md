# Modeling Referee Effects on NFL Penalty Outcomes

## Project Overview

This project studies whether NFL referee identity helps explain variation in penalty outcomes after controlling for team and game context. Penalties can directly affect field position, scoring opportunities, game flow, and fan perception, so understanding whether referees differ systematically is an important sports analytics question.

We focus on three related questions:

1. Do some lead referees call more penalties than others after adjusting for context?
2. Do referees differ in the types of penalties they call?
3. Is there evidence that some referees treat home and away teams differently?

Overall, the project finds evidence that referee identity is associated with both overall penalty volume and penalty-type composition. However, evidence for systematic home-versus-away bias is weaker.

## Data

The analysis uses public NFL data from the `nflreadr` package.

### Data Sources

- `nflreadr::load_pbp()`: NFL play-by-play data
- `nflreadr::load_officials()`: NFL officials and referee assignment data

The play-by-play data provide information about penalties, teams, game situation, and play context. Important variables include:

- `penalty`
- `penalty_yards`
- `penalty_team`
- `posteam`
- `defteam`
- `score_differential`
- `down`
- `play_type`

The officials data identify the referee crew for each game. We extract the lead referee and merge this information into the play-by-play data using game identifiers, season, and week.

### Data Scope

The project focuses on regular season NFL games from 2015 to 2025.

The processed dataset contains:

- 2,895 games
- 508,914 play-by-play rows
- 29 lead referees

### Processed Datasets

Two main modeling datasets are created.

#### Team-game dataset

Each row represents one team in one game. This is used to model total penalties assessed to a team in a game.

Key variables include:

- `team_plays`: number of play-by-play rows for that team
- `team_penalties`: number of penalties assessed to that team
- `team_penalty_yards`: total penalty yards assessed to that team
- `mean_abs_score_diff`: average absolute score differential
- `pass_rate`: fraction of plays that were passes
- `early_down_rate`: fraction of plays on first or second down
- `is_home`: whether the team was the home team
- `lead_referee`: lead referee for the game

#### Penalty-event dataset

Each row represents a penalty event. This is used to study whether referees differ in the types of penalties they call.

## Methods

The project uses multilevel models with crossed random effects. This allows us to account for repeated observations across referees, teams, and opponents.

### 1. Crossed Effect Penalty Rate Model

To study whether some referees are associated with higher penalty volume, I fit a crossed random effects negative binomial model.

The response variable is:

`team_penalties`

The model includes an offset for:

`log(team_plays)`

This adjusts for the fact that teams running more plays have more opportunities to receive penalties.

The fixed effects are:

- `is_home`
- `mean_abs_score_diff`
- `pass_rate`
- `early_down_rate`

The random effects are:

- `lead_referee`
- `posteam`
- `defteam`

This model estimates whether referees differ in adjusted penalty rates while also controlling for team and opponent tendencies.

### 2. Bayesian Referee Effect Model

I also fit a Bayesian version of the crossed random effects model using brms.

The Bayesian model uses a negative binomial likelihood and weakly informative priors

The Bayesian model is used to confirm the frequentist model results and provide posterior credible intervals for referee effects.

### 3. Penalty-type models

### 4. Home-team effect model

## Results

### Referee effects

The overall penalty model suggests that referees differ in adjusted penalty tendencies.

Highest estimated referee effects:

Ed Hochuli
Walt Anderson
Alex Moore
Shawn Hochuli
Terry McAulay

Lower estimated referee effects:

Bill Vinovich
Brad Allen
Gene Steratore
Shawn Smith

The Bayesian model gives similar rankings, but also shows uncertainty, so exact ordering should not be overinterpreted.

### Team effects

Teams with the largest offensive penalty tendencies:

Seattle
Las Vegas
Cleveland

Defensive opponents associated with more penalties:

Tennessee
Pittsburgh
Dallas

### Penalty-type effects

Referees also differ in penalty-type composition, especially for more judgment-based calls such as unnecessary roughness.

### Home vs away effects

There is weaker evidence that referees systematically treat home and away teams differently. Most home-effect intervals overlap zero.
