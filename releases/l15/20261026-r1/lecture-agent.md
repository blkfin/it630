# Performance and cost

- Course: IT 630
- Lecture: 09 / Performance and cost
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `2e32ecc46d906f3dd04cbae26dec4af91507e56087ff040df90af5c906c137a6`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## How can the same answer require less reading?

- Source lineage: `lecture#sections.card_1`
- Citations: none

By the end, you can explain skipped reading, read a small plan, and compare preparation with repeated work.

A daily sales total can stay the same while the work changes.

## We need one day's total from these sales

- Source lineage: `lecture#sections.card_2`
- Citations: none

- Same six rows: sales.csv, sales.parquet, date folders.
- One row records a date, store and product.

### Find total units for 2026-09-06.

| Date | Store | Units |
| --- | --- | --- |
| 2026-09-05 | S01 | 2 |
| 2026-09-05 | S02 | 3 |
| 2026-09-06 | S01 | 4 |
| 2026-09-06 | S02 | 6 |
| 2026-09-07 | S01 | 7 |
| 2026-09-07 | S02 | 8 |

## Which files can this date filter exclude?

- Source lineage: `lecture#sections.card_3`
- Citations: none

A partition is a folder whose name records a key's value. Which files must remain for date = 2026-09-06?

### Each file holds its store's one sales row.

| Folder | File |
| --- | --- |
| date=2026-09-05 | S01.parquet |
| date=2026-09-05 | S02.parquet |
| date=2026-09-06 | S01.parquet |
| date=2026-09-06 | S02.parquet |
| date=2026-09-07 | S01.parquet |
| date=2026-09-07 | S02.parquet |

## The date filter leaves two files to read

- Source lineage: `lecture#sections.card_4`
- Citations: none

Folder names let the reader exclude files before reading their sales data.

### 2 of 6 files remain. File count is not byte count.

| Folder | File | Decision | Row mark |
| --- | --- | --- | --- |
| date=2026-09-05 | S01.parquet | Exclude | dropped row |
| date=2026-09-05 | S02.parquet | Exclude | dropped row |
| date=2026-09-06 | S01.parquet | Read | highlighted row |
| date=2026-09-06 | S02.parquet | Read | highlighted row |
| date=2026-09-07 | S01.parquet | Exclude | dropped row |
| date=2026-09-07 | S02.parquet | Exclude | dropped row |

## Parquet can read only the needed columns

- Source lineage: `lecture#sections.card_5`
- Citations: none

Projection pushdown means reading only the columns the query needs.

### CSV

- Rows store fields together
- Read records and parse the required fields

### Parquet

- Stores column values separately
- Read date and units; skip store and product

The daily total still asks for the same values.

## Chunk summaries can rule out more reading

- Source lineage: `lecture#sections.card_6`
- Citations: none

A row group is a chunk of Parquet rows. A zonemap summarizes a chunk's minimum and maximum values.

### One file, six sales rows, two rows per chunk.

| Chunk | Minimum date | Maximum date | For September 6 |
| --- | --- | --- | --- |
| A | 2026-09-05 | 2026-09-05 | Skip |
| B | 2026-09-06 | 2026-09-06 | Read |
| C | 2026-09-07 | 2026-09-07 | Skip |

## A plan explains how the total gets computed

- Source lineage: `lecture#sections.card_7`
- Citations: none

Layout can exclude reading, but it does not show the processing order. A physical plan lists the operations used to answer a query.

### Diagram explanation

Conceptual plan for the same daily total.

- Arrow from Read sales rows to Keep September 6: candidate rows
- Arrow from Keep September 6 to Sum units: matching rows

## The query requests a filtered sum of units

- Source lineage: `lecture#sections.card_8`
- Citations: none

WHERE chooses the date; SUM combines the matching units.

Same query for each representation

```sql
SELECT SUM(units) AS total_units
FROM sales
WHERE date = DATE '2026-09-06';
```

## Two matching sales produce one total row

- Source lineage: `lecture#sections.card_9`
- Citations: none

Both layouts give 10; only the scan step changed.

### Read each layout column from top to bottom. Each sum produces 1 output row.

| Step | sales.csv | Date folders |
| --- | --- | --- |
| Scan | 2, 3, 4, 6, 7, 8 (6 rows) | 4, 6 (2 rows in 2 of 6 files) |
| Keep September 6 | 4, 6 | 4, 6 |
| Sum units | 10 | 10 |

## An executed profile records what the query did

- Source lineage: `lecture#sections.card_10`
- Citations: none

A plan predicts; a profile reports what ran.

### EXPLAIN: planned

- Scan → filter → sum
- Estimated rows before running: ~6, ~3, ~1

### EXPLAIN ANALYZE: executed

- Scan → filter → sum
- Reports each step's actual rows: 6, 2, 1

Conceptual comparison for sales.csv, not captured engine output.

## Can date folders exclude a units-only query?

- Source lineage: `lecture#sections.card_11`
- Citations: none

The filter changes to units >= 7, with no date condition. Which folders can their names alone rule out?

### The only folder key is date.

| Folder | File |
| --- | --- |
| date=2026-09-05 | S01.parquet |
| date=2026-09-05 | S02.parquet |
| date=2026-09-06 | S01.parquet |
| date=2026-09-06 | S02.parquet |
| date=2026-09-07 | S01.parquet |
| date=2026-09-07 | S02.parquet |

## Date names cannot rule out any units values

- Source lineage: `lecture#sections.card_12`
- Citations: none

Partition exclusion needs a condition on the partition key.

### Folder evidence

- No date condition
- All 6 files remain eligible

### Inside-file evidence

- A units summary might exclude chunks
- 2 matching rows do not tell you how many bytes were read

The matching units are 7 and 8; their sum is 15.

## Uneven work can leave extra workers waiting

- Source lineage: `lecture#sections.card_13`
- Citations: none

After skipping, the rest of the work costs time or machines. More workers help only when the work divides evenly; skew means one worker gets most of it.

### A larger sales snapshot: 100 rows, equal processing cost per row.

| Worker | Rows to process |
| --- | --- |
| A | 70 |
| B | 10 |
| C | 10 |
| D | 10 |

## A join may move rows between workers

- Source lineage: `lecture#sections.card_14`
- Citations: none

A shuffle moves rows between workers so matching keys meet.

### Diagram explanation

Conceptual distributed join for the sales data.

- Arrow from Sales rows on worker A to Rows grouped by store_id: move by key
- Arrow from Store rows on worker B to Rows grouped by store_id: move by key
- Arrow from Rows grouped by store_id to Join sales to stores: matching keys meet

## Preparation pays back only after enough uses

- Source lineage: `lecture#sections.card_15`
- Citations: none

Count preparation and repeated query time together before choosing a layout.

### Example costs, not measured timings. Extra storage also has a cost.

| Runs | CSV, 4 seconds per run | Prepared layout, 12 seconds once plus 1 per run |
| --- | --- | --- |
| 1 | 4 seconds | 13 seconds |
| 4 | 16 seconds | 16 seconds |
| 5 | 20 seconds | 17 seconds |

## A saved total needs a freshness rule

- Source lineage: `lecture#sections.card_16`
- Citations: none

Result reuse means keeping a computed answer for later requests.

### Unchanged snapshot

- Keep September 6 total: 10
- Reuse while the input snapshot stays unchanged

### Corrected snapshot

- Replace units 6 with 9
- Recompute: 4 + 9 = 13

Keeping the answer uses memory or storage; freshness determines whether reuse is valid.

## Reduce unnecessary work before buying more capacity

- Source lineage: `lecture#sections.card_17`
- Citations: none

Keep the answer correct, then pay for the work it needs.

- Skip irrelevant files, columns and chunks
- Use plans and profiles to explain the work
- Balance preparation, reuse, freshness and extra resources
