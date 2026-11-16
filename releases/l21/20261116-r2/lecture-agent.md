# ML families in a nutshell

- Course: IT 630
- Lecture: 21 / November 16, 2026
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `929335b885c970aaf73074a03ea11dd5a81e52e170a9e0cec1b2a34aa8e065d1`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## What does each model assume about our students?

- Source lineage: `lecture#sections.card_01`
- Citations: `src_nov16_islp`

By the end, you can explain each family's bet and why combining trees helps.

**Citations:** `src_nov16_islp`

- An advising team must decide whom to contact.
- Each row describes one student early in term.
- Inputs contain only information already known.
- The outcome is whether outreach is needed.

**Citations:** `src_nov16_islp`

## KNN assumes similar students need similar support

- Source lineage: `lecture#sections.card_02`
- Citations: `src_nov16_islp`

- Model: a prediction rule learned from examples.
- Assumption: what a model takes as true about data.
- Family: models that share an approach and assumptions.

**Citations:** `src_nov16_islp`

| Family | Bet | Buys | Breaks when |
| --- | --- | --- | --- |
| KNN (nearest neighbors) | Similar inputs imply similar outcomes | Flexible local predictions | Irrelevant or unscaled inputs distort similarity |

**Citations:** `src_nov16_islp`

## Linear regression adds fixed pushes to predict numbers

- Source lineage: `lecture#sections.card_03`
- Citations: `src_nov16_islp`

- Neighbors give local answers; a shared rule can explain feature effects.
- Rigid (parametric) models choose a shape; flexible (non-parametric) models let data shape it.

**Citations:** `src_nov16_islp`

### Same students, numeric outcome: credits completed

| Family | Bet | Buys | Breaks when |
| --- | --- | --- | --- |
| Linear regression | Straight feature effects add together | Readable effects on a number | Effects curve or depend on each other |

**Citations:** `src_nov16_islp`

## Logistic regression turns an additive score into probability

- Source lineage: `lecture#sections.card_04`
- Citations: `src_nov16_islp`

Probability is a chance between zero and one.

**Citations:** `src_nov16_islp`

| Family | Bet | Buys | Breaks when |
| --- | --- | --- | --- |
| Logistic regression | Fixed feature pushes add to a score | Score becomes an outreach probability | Score needs curves or interacting features |

**Citations:** `src_nov16_islp`

## Generative classifiers describe each class before comparing them

- Source lineage: `lecture#sections.card_05`
- Citations: `src_nov16_islp`

- Generative classifiers model how each class produces its inputs.
- Class means one label, such as outreach or no outreach.

**Citations:** `src_nov16_islp`

| Family | Bet about each class | Buys | Breaks when |
| --- | --- | --- | --- |
| LDA | Bell-shaped groups share their spread | Simple class boundaries | Spreads differ strongly |
| QDA | Bell-shaped groups have separate spreads | Curved class boundaries | Groups lack enough examples |
| Naive Bayes | Within a class, one input gives no information about another | Simpler fitting | Inputs remain strongly related within classes |

**Citations:** `src_nov16_islp`

## Shrinkage limits feature effects to resist fitting noise

- Source lineage: `lecture#sections.card_06`
- Citations: `src_nov16_islp`

Overfitting means learning training noise and failing to generalize.

**Citations:** `src_nov16_islp`

### Shrinkage: reduce fitted feature pushes

| Method | Bet | Buys | Breaks when |
| --- | --- | --- | --- |
| Ridge | Smaller pushes generalize better | Steadier predictions | Important effects shrink too far |
| Lasso | Only a few inputs matter | Some pushes become zero | Many small effects matter together |

**Citations:** `src_nov16_islp`

## Splines and GAMs allow curves, but effects still add up

- Source lineage: `lecture#sections.card_07`
- Citations: `src_nov16_islp`

Spline: a curve made of joined pieces.

**Citations:** `src_nov16_islp`

### GAM: generalized additive model

| Method | Bet | Buys | Breaks when |
| --- | --- | --- | --- |
| Splines | A smooth piecewise curve describes an effect | Curved responses | Sharp changes defeat smoothness |
| GAMs | Curved feature effects still add together | Readable separate curves | Effects depend strongly on each other |

**Citations:** `src_nov16_islp`

## Support vector machines build boundaries from borderline cases

- Source lineage: `lecture#sections.card_08`
- Citations: `src_nov16_islp`

- Additive curves restrict the outreach boundaries a model can express.
- Flexible models let the data shape the boundary; they need more data.

**Citations:** `src_nov16_islp`

### Margin: the gap around a class boundary; support vectors: the borderline students that set it

| Family | Bet | Buys | Breaks when |
| --- | --- | --- | --- |
| Support vector machine (SVM) | A wide margin separates outcome groups | Flexible boundaries after transforming inputs | Classes overlap heavily |

**Citations:** `src_nov16_islp`

## Neural networks learn features through successive layers

- Source lineage: `lecture#sections.card_09`
- Citations: `src_nov16_islp`

- Neural network: linked processing steps; each layer re-describes its input.
- Deep learning uses many such layers.

**Citations:** `src_nov16_islp`

### Deep learning's bet

- Enough data supports learned features
- Buys rich patterns in images and text
- Breaks when data cannot support the flexibility

### Our outreach table

- Columns already describe the students
- Extra flexibility does not guarantee better predictions

**Citations:** `src_nov16_islp`

## Decision trees split students with yes-or-no thresholds

- Source lineage: `lecture#sections.card_10`
- Citations: `src_nov16_islp`

- Threshold split: a yes-or-no test against a cutoff.
- Bet: a few yes-or-no thresholds sort students into groups.

**Citations:** `src_nov16_islp`

### Two fitted trees for Ari: 3 missed classes, 3 submitted tasks

| Training sample | Split Ari meets | Outreach prediction |
| --- | --- | --- |
| Sample A | Missed classes above 2 | Yes |
| Slightly changed sample B | Submitted tasks at least 3 | No |

**Citations:** `src_nov16_islp`

## Can a crowd outperform its imperfect members?

- Source lineage: `lecture#sections.card_11`
- Citations: `src_nov16_esl`

- One tree can change its answer when the sample changes.
- Ensemble: combine models to stabilize or improve predictions.

**Citations:** `src_nov16_esl`

### Predicting outreach with a crowd

| Situation | Given | Predict |
| --- | --- | --- |
| One voter | Right 60% of the time | 60% |
| Five independent voters | Each right 60% of the time | [blank] |

**Citations:** `src_nov16_esl`

## Bagging steadies predictions by combining resampled trees

- Source lineage: `lecture#sections.card_12`
- Citations: `src_nov16_esl`, `src_nov16_islp`

If their mistakes were independent, five 60%-right voters would give a 68.3%-right majority.

**Citations:** `src_nov16_esl`, `src_nov16_islp`

### Bagging: train on resamples, then vote

| Step | Ari's outreach example |
| --- | --- |
| Draw training samples | Same size, with replacement: students can repeat |
| Train one tree per sample | Five trees predict Yes, No, Yes, Yes, No |
| Count and choose the majority | Three Yes, two No: predict Yes |

**Citations:** `src_nov16_esl`, `src_nov16_islp`

## What does the majority predict for this student?

- Source lineage: `lecture#sections.card_13`
- Citations: `src_nov16_islp`

Count the votes, then choose the more common answer.

**Citations:** `src_nov16_islp`

### Seven resampled trees predict outreach for Bo

| Student | Tree votes | Majority prediction |
| --- | --- | --- |
| Bo | No, Yes, No, Yes, Yes, No, Yes | [blank] |

**Citations:** `src_nov16_islp`

## Random forests reduce how alike tree mistakes are

- Source lineage: `lecture#sections.card_14`
- Citations: `src_nov16_islp`, `src_nov16_esl`, `src_nov16_breiman_rf`

Bo receives four Yes votes and three No votes: predict Yes.

**Citations:** `src_nov16_islp`, `src_nov16_esl`, `src_nov16_breiman_rf`

### Correlation: how alike the trees' mistakes are

| Method | Candidate inputs at each split | Why combine |
| --- | --- | --- |
| Bagging | All inputs | Average away sample-to-sample swing |
| Random forest | A fresh random subset | Make mistakes less alike, then combine |

**Citations:** `src_nov16_islp`, `src_nov16_esl`, `src_nov16_breiman_rf`

## Boosting adds small corrections one after another

- Source lineage: `lecture#sections.card_15`
- Citations: `src_nov16_islp`, `src_nov16_esl`

Learning rate: how much of each new correction is added.

**Citations:** `src_nov16_islp`, `src_nov16_esl`

### Averaging

- Bagging and forests fit resampled trees
- Combining reduces prediction swing
- Shared errors can survive

### Boosting

- Fit small models in sequence
- AdaBoost gives mistaken students more weight
- Gradient boosting fits what remains wrong
- Small corrections can improve predictions
- Too many corrections can fit noise

**Citations:** `src_nov16_islp`, `src_nov16_esl`

## Modern boosting systems address different practical obstacles

- Source lineage: `lecture#sections.card_16`
- Citations: `src_nov16_xgboost`, `src_nov16_lightgbm`, `src_nov16_catboost`

Each keeps sequential correction; none guarantees the best outreach model.

**Citations:** `src_nov16_xgboost`, `src_nov16_lightgbm`, `src_nov16_catboost`

| System | Change | What it buys |
| --- | --- | --- |
| XGBoost | Approximate split search; fast on mostly empty or zero inputs | Scalable tree boosting |
| LightGBM | Samples important cases; bundles inputs that are rarely nonzero at the same time | Faster training with similar accuracy in its paper |
| CatBoost | Ordered training and handling of category columns such as major | Less outcome information leaking into training |

**Citations:** `src_nov16_xgboost`, `src_nov16_lightgbm`, `src_nov16_catboost`

## Stacking learns how to combine different models

- Source lineage: `lecture#sections.card_17`
- Citations: `src_nov16_esl`

Stacking: another model learns how to combine first-level predictions.

**Citations:** `src_nov16_esl`

### Outreach predictions become inputs to a combiner

| First-level prediction | Combiner's input |
| --- | --- |
| Logistic outreach prediction | Prediction for a student excluded from that fit |
| Forest outreach prediction | Prediction for a student excluded from that fit |
| Boosted outreach prediction | Prediction for a student excluded from that fit |

**Citations:** `src_nov16_esl`

## Each model's bet about the data carries a cost

- Source lineage: `lecture#sections.card_18`
- Citations: `src_nov16_tabular`, `src_nov16_islp`

- Across 45 datasets of about 10,000 rows, tree-based models remained state of the art.
- One reason: neural networks struggled with uninformative inputs.

**Citations:** `src_nov16_tabular`, `src_nov16_islp`

### Three kinds of bet for outreach

| Choice | Bet | Cost |
| --- | --- | --- |
| Rigid shape | A chosen structure fits the students | Missed curves or interactions |
| Flexible single model | Data supports a richer shape | More data; risk of fitting noise |
| Combined models | Many varied models, combined well, beat any one of them | Harder to explain each outreach decision |

**Citations:** `src_nov16_tabular`, `src_nov16_islp`
