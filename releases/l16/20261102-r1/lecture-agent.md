# IT 630 data storytelling

- Course: IT 630
- Lecture: 10 / November 2, 2026
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `65b922c86f39547007c31ed9072dc2810c3ba754154fd5ac704051dc00fc3f58`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## How can we tell a story the data supports?

- Source lineage: `lecture#sections.card_1`
- Citations: none

Campus shuttles: what can a boarding count tell us?

Takeaway: By the end, you can choose a chart, write its claim and annotations, and reject an unsupported claim.

## What does this chart say about student riders?

- Source lineage: `lecture#sections.card_2`
- Citations: none

### Chart data

Fictional example: two campus routes over five weekdays.

| Route | Boardings |
| --- | --- |
| Campus Loop | 1800 |
| Hill Route | 600 |

Scale 0 to 1800

Takeaway: What story would you tell from this chart?

## Repeated boardings do not identify different riders

- Source lineage: `lecture#sections.card_3`
- Citations: none

### Fictional example: three sample rows from the full boarding log. No rider identifier is recorded.

| Route | Day | Time |
| --- | --- | --- |
| Campus Loop | Monday | 08:00 |
| Campus Loop | Monday | 12:00 |
| Hill Route | Monday | 09:00 |

Takeaway: One row records one boarding, not one person.

## The message determines which chart we need

- Source lineage: `lecture#sections.card_4`
- Citations: `visualization_deck`

### Fictional shuttle totals: Campus Loop 1,800; Hill Route 600. A comparison does not answer every question.

| Message | Question | Starting chart |
| --- | --- | --- |
| Distribution | How often does each value occur? | Bar chart or histogram |
| Comparison | Which route has more boardings? | Bar chart |
| Relationship | Do two measurements vary together? | Scatterplot |
| Composition | How is the whole divided? | Stacked bar |

Takeaway: We can compare boardings, but different questions need different charts.

## Named categories use separate bars for their counts

- Source lineage: `lecture#sections.card_5`
- Citations: `visualization_deck`

### Question: are there more cloudy days than sunny days? Daily temperatures: 3 days shown; 362 rows omitted. Count: Cloudy 2, Sunny 1, so one bar per category.

| Day | High | Low | Sky |
| --- | --- | --- | --- |
| 1 | 55.1 | 43.7 | Cloudy |
| 2 | 57.2 | 46.0 | Sunny |
| 3 | 56.8 | 45.9 | Cloudy |

Takeaway: Categorical values name groups; numerical values measure amounts.

## Numerical distributions preserve positions on a scale

- Source lineage: `lecture#sections.card_6`
- Citations: `visualization_deck`

What percent of days are above 72? All three shown highs are below 72; 362 days are not shown.

### Diagram explanation

Question: what percent of days have a high above 72 degrees? Three shown highs lie below 72 degrees; 362 days are not shown.

- Number line from 54 to 74: Daily high temperature (degrees)
- Ticks at 54, 58, 62, 66, 70, 72, 74
- Point at 55.1: Day 1
- Point at 57.2: Day 2
- Point at 56.8: Day 3
- Interval from 54 to 58: 3 shown days
- Interval from 58 to 62: 0
- Interval from 62 to 66: 0
- Interval from 66 to 70: 0
- Interval from 70 to 74: 0

Takeaway: A histogram groups numerical values into adjoining intervals.

## A new measurement may need calculation first

- Source lineage: `lecture#sections.card_7`
- Citations: `visualization_deck`

### Question: how many days have a range above 20 degrees? Same three shown days; 362 omitted. No shown range exceeds 20 degrees.

| Day | Calculation | Range |
| --- | --- | --- |
| 1 | 55.1 − 43.7 | 11.4 |
| 2 | 57.2 − 46.0 | 11.2 |
| 3 | 56.8 − 45.9 | 10.9 |

Takeaway: Daily temperature range equals the high minus the low.

## Relationships and parts ask different questions

- Source lineage: `lecture#sections.card_8`
- Citations: `visualization_deck`

### Relationship: scatterplot

- Question: do higher highs accompany higher lows?
- One point pairs a day's High and Low.
- Horizontal position: High; vertical position: Low.

### Composition: stacked bar

- Question: what share of boardings used each route?
- One whole: 2,400 recorded boardings.
- Campus Loop: 75%; Hill Route: 25%.

Takeaway: Choose the question before choosing the shape.

## A heat map uses colour to represent numbers

- Source lineage: `lecture#sections.card_9`
- Citations: `visualization_deck`

### Plain table. A heat map would colour each cell on one shared temperature scale.

| Day | High | Low |
| --- | --- | --- |
| 1 | 55.1 | 43.7 |
| 2 | 57.2 | 46.0 |
| 3 | 56.8 | 45.9 |

Takeaway: A heat map encodes numerical values with a shared colour scale.

## A story connects the count to its meaning

- Source lineage: `lecture#sections.card_10`
- Citations: none

### Fictional example. Counts describe use; they do not measure different riders or service quality.

| Element | Meaning | Shuttle case |
| --- | --- | --- |
| Context | Place and time | Two campus routes over five weekdays |
| Character | Who or what the story follows | The two campus routes |
| Events | What happens | Campus Loop: 1,800 boardings; Hill Route: 600 |
| Effect | What that means for the character | Campus Loop carries 75% of recorded boardings |

Takeaway: A chart choice is not yet a claim: context, character, events and effect organise it.

## Campus Loop records three times as many boardings

- Source lineage: `lecture#sections.card_11`
- Citations: none

### Chart data

Fictional example. Recorded boardings, not different riders: the log has no rider identifier.

| Route | Boardings |
| --- | --- |
| Campus Loop | 1800 |
| Hill Route | 600 |

Scale 0 to 1800

Takeaway: The title states the claim; annotations show the evidence.

## A boarding relates to people without identifying them

- Source lineage: `lecture#sections.card_12`
- Citations: none

### Data points are people

- One point represents one person.
- Here one row is a boarding, so this level does not fit.

### Data relates to people

- Transport affects people's daily lives.
- Boardings describe recorded shuttle use.

### Make numbers relatable

- 1,800 boardings across five weekdays.
- That averages 360 boardings per weekday.

Takeaway: Bring people into the story without changing what a row means.

## A data card records what the story cannot claim

- Source lineage: `lecture#sections.card_13`
- Citations: none

### A data card describes a dataset's use, contents and limits. Fictional example. Next: you test a claim against a new table.

| Card line | Shuttle entry |
| --- | --- |
| Intended use | Compare recorded boardings by route |
| Coverage and unit | Two routes; five weekdays; one row per boarding |
| Specific limit | No rider identifier is recorded |
| Unsupported claim | 1,800 different students used Campus Loop |

Takeaway: We can write a clear claim, but clarity does not establish support.

## Purchase counts let us compare halls across days

- Source lineage: `lecture#sections.card_14`
- Citations: none

### Fictional dining example: completed-purchase counts grouped by day and hall. No diner identifier is recorded. Only these two days and halls are covered.

| Day | Hall | Purchases |
| --- | --- | --- |
| Monday | North | 40 |
| Monday | South | 30 |
| Tuesday | North | 60 |
| Tuesday | South | 30 |

Takeaway: One source row is one completed purchase, not one diner.

## Can your pair write one supported claim?

- Source lineage: `lecture#sections.card_15`
- Citations: none

Design one chart-based story about these purchases.

1. Choose a message and chart; give your reason.
2. Write a title that states one supported claim.
3. Write two annotations tied to exact evidence.
4. Write one claim this table does not support.

**Response mode:** written

Takeaway: Use the dining table on your sheet.

## Can another pair break your claim with the table?

- Source lineage: `lecture#sections.card_16`
- Citations: none

Swap sheets with another pair.

1. Check the title against the table's rows and totals.
2. Check each annotation's calculation and unit.
3. Find any claim about people, causes or unobserved days.
4. Return one specific challenge; revise your own claim.

**Response mode:** annotation

Takeaway: Test the story against its evidence before defending it.

## A claim must stay within what its table supports

- Source lineage: `lecture#sections.card_17`
- Citations: none

### Story

- Campus Loop records three times as many boardings.
- 1,800 versus 600 across five weekdays.

### Card

- One row per boarding; no rider identifier.
- Cannot count different students.

Fictional shuttle example.

Takeaway: The story is what you say; the card is what the data lets you say.
