# IT 630 L10 · How can one row answer questions about its context?

- Course: IT 630 · Data Science
- Lecture: L10 / Window functions · a row and its context
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `0f78304a3ea3bf9f23f219e45d3bc08dbf49362c5ecc26739c409d2db488b343`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## How can one row answer questions about its context?

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.cover`
- Citations: `approved_storyboard`

- One stock. One day. Three kinds of context.
- By the end, choose the context, shape the data, and state the denominator.

### One row is one stock on one trading day: 5 stocks (NVDA, AMD, INTC, AAPL, MSFT) × 24 trading days, August 31 to October 2 = 120 rows.

| ticker | date | close |
| --- | --- | --- |
| NVDA | 2026-09-08 | 225.73 |

## Can one price tell us if Tuesday was good?

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c01`
- Citations: `approved_storyboard`

NVDA closed at 225.73 on Tuesday, September 8. Predict: good day or bad day? What would you compare it with?

1. Good
2. Bad
3. Not enough context

## One row gives three answers with three contexts

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c02`
- Citations: `approved_storyboard`

### Its group

- How does Tuesday compare with NVDA’s average?
- 225.73 versus 223.62

### The row before

- What changed from the previous trading row?
- 225.73 − 230.36 = −4.63

### Everything so far

- How far is Tuesday below its peak so far?
- 225.73 is 2.0% below 230.36

The row stays. Its context changes.

## GROUP BY answers but removes the daily rows

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c03`
- Citations: `approved_storyboard`

- Question: what is each stock's average close?
- GROUP BY ticker: 120 rows become 5.
- The NVDA average is 223.62.
- September 8 is gone.
- Predict: if every day stays, how many rows come out?

### Reduce with GROUP BY

- 120 rows → 5 rows
- NVDA → 223.62

### Requested result

- keep September 8 → 225.73
- add its average → 223.62

An aggregate answers the group question but loses the row.

## A window adds context without removing rows

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c04`
- Citations: `approved_storyboard`

- Window: a calculation across related rows that keeps each row.
- 120 rows go in; 120 rows come out.
- Each NVDA row receives the 223.62 average.

### The context becomes another column.

| ticker | date | close | ticker_avg |
| --- | --- | --- | --- |
| NVDA | 2026-09-04 | 230.36 | 223.62 |
| NVDA | 2026-09-08 | 225.73 | 223.62 |

## Windows need one stock-day fact per row

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c05`
- Citations: `approved_storyboard`

- Grain: one row is one stock on one trading day.
- Partition column: ticker.
- Order column: date.
- Each ticker-date pair must be unique.

### Downloaded wide

- one row per date
- five Close columns

### Ready for windows

- one row per ticker-date
- ticker | date | close

24 dates × 5 stocks → 120 rows.

## Partition chooses the group; order chooses what comes before

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c06`
- Citations: `approved_storyboard`

- Partition: the rows this row may compare with.
- Order: the sequence inside that partition.
- Two NVDA rows on September 4 would make “previous” ambiguous.
- Predict: which row comes before Tuesday?

| ticker | date | close |
| --- | --- | --- |
| NVDA | 2026-09-03 | 228.45 |
| NVDA | 2026-09-04 | 230.36 |
| NVDA | 2026-09-08 | 225.73 |

## Subtract the previous close to measure the move

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c07`
- Citations: `approved_storyboard`

- 1. Keep the NVDA partition.
- 2. Order it by date.
- 3. Bring 230.36 beside 225.73.
- 4. Compute 225.73 − 230.36 = −4.63.
- Tuesday's previous row is Friday, September 4: there is no trading row for Monday, September 7.

| date | close | previous_close | change | Row mark |
| --- | --- | --- | --- | --- |
| 2026-09-04 | 230.36 | 228.45 | 1.91 |  |
| 2026-09-08 | 225.73 | 230.36 | −4.63 | highlighted row |

## Everything so far creates a running benchmark

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c08`
- Citations: `approved_storyboard`

- Peak so far means the maximum through this row.
- September 8 sees 230.36 as its peak.
- 225.73 ÷ 230.36 − 1 = −2.0%.

| date | close | peak_so_far | percent_below_peak | Row mark |
| --- | --- | --- | --- | --- |
| 2026-09-03 | 228.45 | 228.45 | 0.0% |  |
| 2026-09-04 | 230.36 | 230.36 | 0.0% |  |
| 2026-09-08 | 225.73 | 230.36 | −2.0% | highlighted row |

## SQL and pandas spell the same question differently

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c09`
- Citations: `approved_storyboard`

- Question: what changed from the previous row?
- Both forms partition by ticker and respect date order.
- Read the intent; do not memorize the punctuation.

SQL

```text
close - LAG(close) OVER (
  PARTITION BY ticker
  ORDER BY date
) AS change
```

pandas

```text
p = p.sort_values(
    ["ticker", "date"]
)
p["change"] = (
  p.groupby("ticker")["close"]
   .diff()
)
```

## Can you choose context and finish the method?

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c10`
- Citations: `approved_storyboard`

Name each context; calculate A

- A. Thursday: 230.86. Friday: 233.95. Find the move.
- B. How does Friday compare with its stock's average?
- C. How far is Friday below its peak so far?

**Response mode:** written

## The context choice determines the calculation

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c11`
- Citations: `approved_storyboard`

### A

- Thursday: 230.86. Friday: 233.95. Find the move.
- The row before → previous_close; 233.95 − 230.86 = 3.09

### B

- How does Friday compare with its stock's average?
- Its group → ticker_avg (average close)

### C

- How far is Friday below its peak so far?
- Everything so far → peak_so_far

## A missing first comparison changes the denominator

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c12`
- Citations: `approved_storyboard`

- The first NVDA row has no previous row.
- Its change is NULL: missing, not zero.
- There are 24 NVDA rows; 23 have a previous row.
- NVDA rose on 14 dates.

What share of days did NVDA go up?

1. 14 ÷ 24 = 58.3%
2. 14 ÷ 23 = 60.9%

## State the denominator before trusting the percentage

- Source lineage: `it630-fall-2026-l10-windows-stock-market#sections.c13`
- Citations: `approved_storyboard`

- The denominator is all 24 NVDA rows; the first row counts as not up: 58.3%.
- The denominator is the 23 rows that have a previous row: 60.9%.
- Both calculations are valid; they answer different questions.
- A window keeps the row and makes its context explicit.

### All observed rows

- denominator: 24 rows
- 14 ÷ 24 = 58.3%

### Defined comparisons

- denominator: 23 changes
- 14 ÷ 23 = 60.9%

Write the denominator sentence before the rate.
