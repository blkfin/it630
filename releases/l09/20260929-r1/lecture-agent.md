# IT 630 L09 · Can We Trust the Company's Trial Result?

- Course: IT 630 · Data Science
- Lecture: L09 / Joins · trial audit
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `141871e6a2e93a698b04f7cb07899ec0b39d7a45540a4cbe7b31b4c12d45e665`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## Can We Trust the Company's Trial Result?

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.cover`
- Citations: `approved_storyboard`

The company reports that its drug lowers blood pressure. Can we trust the result?

You will be able to explain how keys connect tables, predict what inner and left joins keep, and check whether a join changed the evidence.

### Trial result

| Arm | Average |
| --- | --- |
| drug | 125 mmHg |
| placebo | 140 mmHg |

## Eight readings went in, and eight rows came out

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c01`
- Citations: `approved_storyboard`

### Trial result

- Drug average: 125 mmHg
- Placebo average: 140 mmHg

### Row-count check

- Inner join: 8 rows in, 8 rows out
- Trust the 15-point difference?

## Each table stores one kind of fact

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c02`
- Citations: `approved_storyboard`

- readings: one row per follow-up visit
- enrollment: one row per participant
- The arm belongs to the participant
- Store that fact once, then connect it

## readings and enrollment

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c02b`
- Citations: `approved_storyboard`

### readings

| participant_id | visit | systolic_bp |
| --- | --- | --- |
| P01 | 1 | 130 |
| P01 | 2 | 126 |
| P02 | 1 | 138 |
| P02 | 2 | 142 |

### enrollment

| participant_id | arm |
| --- | --- |
| P01 | drug |
| P02 | placebo |
| P03 | drug |
| P04 | placebo |

## Keys tell rows how to find partners

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c03`
- Citations: `approved_storyboard`

- Primary key: a value that uniquely identifies each record
- enrollment.participant_id identifies a participant
- Foreign key: a value that identifies a unique record in another table
- readings.participant_id points to enrollment

### The same P01 value has two roles

| table | participant_id | role |
| --- | --- | --- |
| readings | P01 | foreign key |
| enrollment | P01 | primary key |

## A key is a promise the data may break

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c04`
- Citations: `approved_storyboard`

- The promise: one enrollment row per participant ID
- Missing P04: no partner exists
- Two P02 rows: two partners exist
- SQL will still run

### clean · missing: no P04 row · repeated: a second P02 row

| clean | missing | repeated |
| --- | --- | --- |
| P01 drug | P01 drug | P01 drug |
| P02 placebo | P02 placebo | P02 placebo |
| P03 drug | P03 drug | P02 placebo |
| P04 placebo | [blank] | P03 drug |
| [blank] | [blank] | P04 placebo |

## Every matching pair becomes one output row

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c05`
- Citations: `approved_storyboard`

- Join rule: one output row for every matching pair
- Without the key condition, every possible pair appears
- P01 has two readings and one enrollment partner
- 2 × 1 matching pairs produce 2 rows
- A missing match produces 0 rows

### Diagram explanation

Two P01 reading records each match the one P01 enrollment record and produce two output records.

- Arrow from P01 visit 1 to P01 drug: participant_id matches
- Arrow from P01 visit 2 to P01 drug: participant_id matches
- Arrow from P01 drug to P01 visit 1 drug
- Arrow from P01 drug to P01 visit 2 drug

## The join runs before the average

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c06`
- Citations: `approved_storyboard`

join runs before the average

```sql
SELECT arm, AVG(systolic_bp)
FROM readings
JOIN enrollment
  ON readings.participant_id = enrollment.participant_id
GROUP BY arm;
```

- Logical order: FROM / JOIN / ON, then GROUP BY, then SELECT
- Inner join: keeps only keys present in both tables
- Plain JOIN means INNER JOIN

## A clean lookup preserves all eight readings

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c07`
- Citations: `approved_storyboard`

- 1. Start with 8 reading rows.
- 2. Each ID finds exactly 1 enrollment partner.
- 3. The inner join returns 8 × 1 = 8 rows.
- 4. Drug = 500 ÷ 4 = 125; placebo = 500 ÷ 4 = 125: the clean desk shows no difference.

Lookup: each row finds exactly one partner, so the row count holds.

## Each row finds exactly one partner

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c07b`
- Citations: `approved_storyboard`

### Drug: 130 + 126 + 124 + 120 = 500 · Placebo: 138 + 142 + 108 + 112 = 500

| participant_id | visit | systolic_bp | arm |
| --- | --- | --- | --- |
| P01 | 1 | 130 | drug |
| P01 | 2 | 126 | drug |
| P02 | 1 | 138 | placebo |
| P02 | 2 | 142 | placebo |
| P03 | 1 | 124 | drug |
| P03 | 2 | 120 | drug |
| P04 | 1 | 108 | placebo |
| P04 | 2 | 112 | placebo |

## An inner join silently drops missing matches

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c08`
- Citations: `approved_storyboard`

- P04 has no enrollment partner
- Its 2 readings produce 0 matching pairs
- Result: 6 rows, with no SQL error

## Its 2 readings produce 0 matching pairs

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c08b`
- Citations: `approved_storyboard`

### Inner-join result; the two P04 readings have no match

| participant_id | systolic_bp | arm | Row mark |
| --- | --- | --- | --- |
| P01 | 130 | drug |  |
| P01 | 126 | drug |  |
| P02 | 138 | placebo |  |
| P02 | 142 | placebo |  |
| P03 | 124 | drug |  |
| P03 | 120 | drug |  |
| P04 | 108 | [blank] | dropped row: no match |
| P04 | 112 | [blank] | dropped row: no match |

## A left join exposes the missing partner

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c09`
- Citations: `approved_storyboard`

- Left join: keeps every row from the first table
- Put readings on the left
- P04's 2 readings remain
- Their enrollment columns are NULL

## P04's 2 readings remain

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c09b`
- Citations: `approved_storyboard`

### readings LEFT JOIN enrollment; P04's arm comes back NULL

| participant_id | systolic_bp | arm |
| --- | --- | --- |
| P01 | 130 | drug |
| P01 | 126 | drug |
| P02 | 138 | placebo |
| P02 | 142 | placebo |
| P03 | 124 | drug |
| P03 | 120 | drug |
| P04 | 108 | NULL |
| P04 | 112 | NULL |

## Try the matching-pair rule on a repeated key

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c10`
- Citations: `approved_storyboard`

- enrollment is clean except that P02 appears twice (P04 is back).
- P02 has 2 readings
- Each reading finds 2 partners
- Multiplication: if the key repeats on the lookup side, rows multiply
- Predict the total inner-join row count
- Predict the placebo average

## Each reading finds 2 partners

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c10b`
- Citations: `approved_storyboard`

### P02 readings

| participant_id | systolic_bp |
| --- | --- |
| P02 | 138 |
| P02 | 142 |

### P02 enrollment records

| participant_id | arm | Row mark |
| --- | --- | --- |
| P02 | placebo |  |
| P02 | placebo | repeated row: repeated lookup key |

## Predict the total inner-join row count

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c10c`
- Citations: `approved_storyboard`

Predict the total inner-join row count and the placebo average.

- Total rows
- Placebo average

**Response mode:** written

## P02 produces 2 × 2 = 4 rows

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c11b`
- Citations: `approved_storyboard`

### Six placebo rows

| participant_id | systolic_bp | arm | Row mark |
| --- | --- | --- | --- |
| P02 | 138 | placebo |  |
| P02 | 138 | placebo | repeated row: repeated P02 pair |
| P02 | 142 | placebo |  |
| P02 | 142 | placebo | repeated row: repeated P02 pair |
| P04 | 108 | placebo |  |
| P04 | 112 | placebo |  |

## The repeated key gives P02 extra weight

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c11`
- Citations: `approved_storyboard`

- P02 produces 2 × 2 = 4 rows
- Total join: 4 drug rows + 6 placebo rows = 10
- A left join repeats P02 too: 10 rows
- Placebo = (2 × 138 + 2 × 142 + 108 + 112) ÷ 6
- Placebo = 780 ÷ 6 = 130 mmHg

## Two defects can cancel the row count

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c12`
- Citations: `approved_storyboard`

- Missing P04 removes 2 inner-join rows
- Repeated P02 adds 2 inner-join rows
- 8 readings become 8 inner-join rows
- Placebo is still 140 mmHg

## 8 readings become 8 inner-join rows

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c12b`
- Citations: `approved_storyboard`

### Missing

- −2 P04 rows

### Repeated

- +2 P02 rows

### Row-count check

- 8 → 8

## Placebo is still 140 mmHg

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c12c`
- Citations: `approved_storyboard`

### Dirty placebo rows

| systolic_bp |
| --- |
| 138 |
| 142 |
| 138 |
| 142 |

## Trust requires checking the key promises

- Source lineage: `it630-fall-2026-l09-joins-trial-audit#sections.c13`
- Citations: `approved_storyboard`

- Rows before and after: did anything vanish or multiply?
- COUNT(*): how many enrollment rows exist?
- COUNT(DISTINCT participant_id): how many participants exist?
- Left-join unmatched count: who found no partner?

### Audit the four enrollment desks

| enrollment desk | inner rows | left rows | enrollment rows / distinct IDs | unmatched readings |
| --- | --- | --- | --- | --- |
| clean | 8 | 8 | 4 / 4 | 0 |
| P04 missing | 6 | 8 | 3 / 3 | 2 |
| P02 repeated | 10 | 10 | 5 / 4 | 0 |
| both (the company's desk) | 8 | 10 | 4 / 3 | 2 |
