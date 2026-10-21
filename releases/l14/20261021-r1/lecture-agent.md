# Make the promise run

- Course: IT 630
- Lecture: 15 / Make the promise run
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `63d1dcc9af67d25078354df5e4d4f725d34c58f56cb59fdca495cccc71b3df66`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## What does a completed run leave unchecked?

- Source lineage: `lecture#sections.card_1`
- Citations: none

By the end, you can write checks that find violations and explain what they miss.

### H3 dining purchases.

| Run | Report |
| --- | --- |
| Status | Completed |
| Input purchases | 3 |
| Output rows | 3 |

## Each row records one dining purchase

- Source lineage: `lecture#sections.card_2`
- Citations: none

H3 dining hall, today's batch. Amounts are US dollars. The report needs total sales.

### Total sales: $16.00.

| purchase_id | hall_id | purchased_at | amount |
| --- | --- | --- | --- |
| P101 | H3 | 2026-10-20 12:00 | 5.00 |
| P102 | H3 | 2026-10-20 12:01 | 7.00 |
| P103 | H3 | 2026-10-20 12:02 | 4.00 |

## Does this completed run establish correct sales?

- Source lineage: `lecture#sections.card_3`
- Citations: none

The same three purchases cost $16.00. What does this run establish?

Run report

```text
Status: Completed
Input purchases: 3
Output rows: 3
Reported sales: $1,600.00
```

## Completed jobs can still produce incorrect results

- Source lineage: `lecture#sections.card_4`
- Citations: none

Completion describes execution. It does not establish these data properties.

### Original purchases: P101 $5, P102 $7, P103 $4.

| Change | Observed result |
| --- | --- |
| Same file appended twice | Six rows, sales $32.00 |
| purchased_at renamed timestamp | Reader requesting purchased_at fails |
| Dollar values replaced by cents | Three rows, numeric total 1600 |

## A check returns evidence against a promise

- Source lineage: `lecture#sections.card_5`
- Citations: none

- **Promise**: a property the table is expected to satisfy, such as one row per purchase ID.
- **Check**: a query that returns the rows violating a promise.

- The run completed with the wrong total; completed status does not say which promise failed.

| Step | Dining example |
| --- | --- |
| State a promise | Each purchase ID occurs once |
| Query for violations | Find IDs occurring more than once |
| Read the result | Returned IDs violate that promise. |

## Check required columns before querying their values

- Source lineage: `lecture#sections.card_6`
- Citations: none

Schema: the column names and types defining a table. EXCEPT: rows in the first result that are missing from the second.

Renamed batch returns: missing_column = purchased_at.

C1: missing required columns

```sql
SELECT name AS missing_column
FROM (VALUES ('purchase_id'), ('hall_id'),
             ('purchased_at'), ('amount')) AS required(name)
EXCEPT
SELECT column_name
FROM information_schema.columns
WHERE table_schema = 'main'
  AND table_name = 'batch';
```

## Find purchase identifiers that violate the grain

- Source lineage: `lecture#sections.card_7`
- Citations: none

Identity: which purchase a row represents. C2 requires a nonnull, unique purchase_id. For the replayed batch, which rows should this check return?

C2: replayed P101, P102, P103

```sql
SELECT purchase_id, COUNT(*) AS occurrences
FROM batch
GROUP BY purchase_id
HAVING purchase_id IS NULL
    OR COUNT(*) > 1;
```

## Replay returns three repeated purchase identifiers

- Source lineage: `lecture#sections.card_8`
- Citations: none

Each returned ID violates the one-purchase-per-ID promise.

### C2 result for six replayed purchase rows.

| purchase_id | occurrences | Row mark |
| --- | --- | --- |
| P101 | 2 | repeated row |
| P102 | 2 | repeated row |
| P103 | 2 | repeated row |

## Find missing amounts and values outside the range

- Source lineage: `lecture#sections.card_9`
- Citations: none

Value check: a rule about field contents. C3 allows $0.50–$50.00 inclusive, with no nulls.

Cents batch returns: P101 500, P102 700, P103 400.

C3: numeric amounts 500, 700, 400

```sql
SELECT purchase_id, amount
FROM batch
WHERE amount IS NULL
   OR amount NOT BETWEEN 0.50 AND 50.00;
```

## Missing purchases require a comparison beyond delivered rows

- Source lineage: `lecture#sections.card_10`
- Citations: none

History check: compare today's batch with earlier batches. Valid delivered rows cannot reveal absent rows.

### H3 dining batches.

| Batch | Purchase count |
| --- | --- |
| Earlier batch 1 | 3 |
| Earlier batch 2 | 3 |
| Earlier batch 3 | 3 |
| Earlier batch 4 | 3 |
| Earlier batch 5 | 3 |
| Today: P101, $5.00 | 1 |

## Count the batch before comparing it with history

- Source lineage: `lecture#sections.card_11`
- Citations: none

C4 returns a diagnostic row when today's count is below half the reference mean. WITH names each one-row result; CROSS JOIN puts the two side by side.

C4: H3 demo history 3, 3, 3, 3, 3

```sql
WITH actual AS (
  SELECT COUNT(*) AS actual_count FROM batch
), reference AS (
  SELECT AVG(n) AS reference_mean
  FROM (VALUES (3),(3),(3),(3),(3)) AS h(n)
)
SELECT actual_count, reference_mean,
       0.5 * reference_mean AS threshold
FROM actual CROSS JOIN reference
WHERE actual_count < 0.5 * reference_mean;
```

## The partial batch returns one diagnostic row

- Source lineage: `lecture#sections.card_12`
- Citations: none

The batch count, 1, is below the threshold, 1.5.

### C4 result for the batch containing only P101 ($5.00).

| actual_count | reference_mean | threshold |
| --- | --- | --- |
| 1 | 3.0 | 1.5 |

## Start your investigation from this new batch

- Source lineage: `lecture#sections.card_13`
- Citations: none

You have seen one worked case. Next, you run C1–C4 in Colab on one changed copy of this batch at a time to isolate its effect.

### New starting batch. H3 reference counts: 5, 6, 5, 4, 5.

| purchase_id | hall_id | purchased_at | amount |
| --- | --- | --- | --- |
| R301 | H3 | 2026-10-20 12:00 | 4.50 |
| R302 | H3 | 2026-10-20 12:01 | 6.00 |
| R303 | H3 | 2026-10-20 12:02 | 5.25 |
| R304 | H3 | 2026-10-20 12:03 | 7.00 |
| R305 | H3 | 2026-10-20 12:04 | 3.25 |

## Predict each result before running the checks

- Source lineage: `lecture#sections.card_14`
- Citations: none

Record C1–C4 as pass, fail or skipped, with returned evidence.

Investigate five independent batches in the supplied Colab notebook.

1. Save your own notebook copy in Drive
2. Predict, run each case, then explain its returned evidence
3. Share an Anyone-with-link Viewer Colab URL in Canvas.

- Clean: keep the starting batch
- Replay: use two copies of the starting batch
- Rename: change purchased_at to timestamp
- Cents: multiply every amount by 100
- Partial: keep only R301 and R302.

**Response mode:** written

## Only C4 flags the partial batch

- Source lineage: `lecture#sections.card_15`
- Citations: none

The two delivered purchases satisfy the row rules. The batch is below the chosen history threshold.

### Partial H3 batch: R301 $4.50 and R302 $6.00, total $10.50.

| actual_count | reference_mean | threshold |
| --- | --- | --- |
| 2 | 5.0 | 2.5 |

## Which incorrect batches still pass these checks?

- Source lineage: `lecture#sections.card_16`
- Citations: none

Predict each result and identify the unchecked property.

### Consider each named check on its own.

| Case | Check | Result |
| --- | --- | --- |
| H3 replay: 10 rows, threshold 2.5 | C4 lower bound | [blank] |
| A $0.25 purchase stored as numeric 25 cents | C3 range 0.50–50.00 | [blank] |

## A passing check establishes only the property it tests

- Source lineage: `lecture#sections.card_17`
- Citations: none

Require explicit units and version metadata. Set the C4 threshold with real quiet days and seasonal changes in mind.

| Case | Result | Unchecked property |
| --- | --- | --- |
| H3 replay: 10 >= 2.5 | C4 passes | No upper bound on volume |
| Numeric amount 25: between 0.50 and 50.00 | C3 passes | Whether the value is dollars or cents. |
