# The file lands; the vendor changes it

- Course: IT 630
- Lecture: 08 / October 19, 2026
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `d693fef74327428b4e6e320521853f8141adea702ccb3c89b3ab7cd1987f4d21`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## Why is the total wrong after a successful run?

- Source lineage: `lecture#sections.card01`
- Citations: none

By the end, you can locate transformations, state layer requirements, explain retries, review rules, and trace source changes.

Campus dining: yesterday $16, after a retry $32.

## Did the hall really sell twice as much?

- Source lineage: `lecture#sections.card02`
- Citations: none

- Campus dining exports charged purchases each night.
- One purchase has one stable purchase_id.
- The report totals dollars by hall and local date.

### H1, 2026-10-18. Vendor v1: amount is USD dollars; timestamps include −04:00.

| purchase_id | purchased_at | amount |
| --- | --- | --- |
| P101 | 12:00 −04:00 | 5.00 |
| P102 | 12:05 −04:00 | 7.00 |
| P103 | 12:10 −04:00 | 4.00 |

## The same purchases arrived in two deliveries.

- Source lineage: `lecture#sections.card03`
- Citations: none

A retry delivered the identical file; no new purchases occurred.

### Naive append into the purchase table.

| purchase_id | dollars per copy | copies | contribution |
| --- | --- | --- | --- |
| P101 | 5 | 2 | $10 |
| P102 | 7 | 2 | $14 |
| P103 | 4 | 2 | $8 |
| Total | not applicable | 6 | $32 |

## Each layer prepares data for a different use.

- Source lineage: `lecture#sections.card04`
- Citations: none

Grain means what one row represents.

### H1 example: 3 source records → 3 accepted purchases → one $16 report row.

| Layer | One row means | Requirement |
| --- | --- | --- |
| Bronze: received data | One received source record | Original values, source batch and version |
| Silver: accepted data | One unique purchase | Stable ID, names, types, USD units |
| Gold: report data | One hall and local date | Sum accepted USD purchases |

## Transformations prepare source records for the report.

- Source lineage: `lecture#sections.card05`
- Citations: `aws_etl`

A pipeline runs repeatable steps that prepare source data for analysis.

### The source records purchases. The report needs one total per hall and day.

| Source data | Transformation | Report needs |
| --- | --- | --- |
| Two deliveries of P101, P102, P103 | Keep one purchase per ID | Three accepted purchases |
| Timestamp text with local offset | Parse; derive the local date | Group by hall and date |
| Amounts: $5, $7, $4 | Sum accepted dollar amounts | H1, October 18: $16 |

## ETL and ELT run transformations in different places.

- Source lineage: `lecture#sections.card06`
- Citations: `aws_etl`

Extract copies source data; transform prepares it; load writes the destination.

### ETL: extract, transform, load

- Extract: read the dining CSV
- Transform: external job removes repeats and calculates totals
- Load: write prepared tables into Dining Analytics

### ELT: extract, load, transform

- Extract: read the same dining CSV
- Load: write raw purchases into Dining Analytics
- Transform: database queries create accepted purchases and totals

Same destination: Dining Analytics. Same result: three purchases, H1 total $16.

## Choose where to transform using the destination requirements.

- Source lineage: `lecture#sections.card07`
- Citations: `aws_etl`

The choice affects where raw data is stored and which system processes it.

### Both arrangements still need deduplication, unit checks, and a prepared report table.

| Requirement | Arrangement | Reason |
| --- | --- | --- |
| Destination requires prepared reporting tables | ETL: prepare before loading | Match required columns and types before writing |
| Destination stores raw rows and runs transformations | ELT: load raw, then prepare | Use its compute; retain detail for new queries |
| A later report needs individual purchases | Retain purchase-level detail | Hall/day totals cannot recover individual purchases |

## How many purchases remain after this batch is repeated?

- Source lineage: `lecture#sections.card08`
- Citations: none

Upsert inserts new IDs and replaces existing IDs.

### Same fixed batch and rules. Upsert by purchase_id, then recompute gold. Predict: purchases __, H1 total __.

| purchase_id | Stored USD | Incoming USD | Result USD |
| --- | --- | --- | --- |
| P101 | 5 | 5 | [blank] |
| P102 | 7 | 7 | [blank] |
| P103 | 4 | 4 | [blank] |

## Replaying the batch preserves the intended result.

- Source lineage: `lecture#sections.card09`
- Citations: none

Idempotence means repeated input and rules preserve the intended output state.

### Fixed input; upsert identical IDs; recompute the affected hall/day.

| Run | Silver purchases | Gold calculation | H1 total |
| --- | --- | --- | --- |
| First | 3 | 5 + 7 + 4 | $16 |
| Replay | 3 | 5 + 7 + 4 | $16 |

## A versioned rule makes the change reviewable.

- Source lineage: `lecture#sections.card10`
- Citations: none

A saved previous version lets a reviewer compare old and new rules.

purchase_to_report.txt, proposed revision

```text
Input: fixed H1 batch, vendor v1 dollars
Silver output: one purchase per purchase_id
Saved version: append every received row
Proposed: insert new IDs, replace existing IDs
Gold output: one hall and local date
Rule: recompute sum from accepted USD purchases
Check after replay: 3 purchases, H1 total $16
```

## Safe retries cannot protect readers from changed fields.

- Source lineage: `lecture#sections.card11`
- Citations: none

- Replaying unchanged purchases now preserves the total.
- A changed file can break the next reader.
- Schema means field names and types.

### Independent vendor change: add payment_method. H1 still contains P101/P102/P103.

| Reader | Fields read | Result |
| --- | --- | --- |
| Silver | purchase_id, hall_id, purchased_at, amount | Ignores added payment_method |
| Gold | Stable silver fields | H1 remains $16 |

## A renamed field breaks the silver reader.

- Source lineage: `lecture#sections.card12`
- Citations: none

The vendor renamed purchased_at to timestamp; silver still requests purchased_at.

### Diagram explanation

Bronze: timestamp → Silver reader → Gold: reads silver → Dashboard

- Arrow from Bronze: timestamp to Silver reader: Missing purchased_at: stops here
- Arrow from Silver reader to Gold: reads silver: No updated purchases
- Arrow from Gold: reads silver to Dashboard: May retain old result

## What happens when only the unit changes?

- Source lineage: `lecture#sections.card13`
- Citations: none

A semantic change alters meaning, even when names and types fit.

### Independent change from v1. Vendor v2 notice: amount is now cents. Reader still assumes USD dollars; no units check.

| purchase_id | Previous amount in dollars | New amount in cents |
| --- | --- | --- |
| P101 | 5 | 500 |
| P102 | 7 | 700 |
| P103 | 4 | 400 |

## Matching names and types still permit a wrong total.

- Source lineage: `lecture#sections.card14`
- Citations: none

Structural compatibility does not establish preserved meaning.

### The job succeeds; the answer is 100 times too large.

| Interpretation | Calculation | Display |
| --- | --- | --- |
| Unchanged dollar reader | 500 + 700 + 400 | $1,600 |
| Actual charged dollars | 5 + 7 + 4 | $16 |

## An adapter converts source fields into the expected format.

- Source lineage: `lecture#sections.card15`
- Citations: none

- **Adapter**: maps one declared source version to silver's names and units.
- **Schema migration**: identify readers, adapt deliberately, verify.

| Step | Dining action | Evidence |
| --- | --- | --- |
| Identify consumers | CSV → bronze → silver → gold → dashboard | Silver expects purchased_at and USD |
| Adapt deliberately | Declared rename version: timestamp → purchased_at | Stable silver name |
| Adapt deliberately | Declared cents version: raw amount ÷ 100 | 500 → 5, 700 → 7, 400 → 4 |
| Verify | Check IDs, units, and H1 total | 3 purchases; $16 |

## Replaying raw cents must not convert silver twice.

- Source lineage: `lecture#sections.card16`
- Citations: none

Each run converts raw input, then upserts the dollar result.

### Three unique silver purchases after either run; division always starts from bronze.

| Run | Raw cents | Converted USD | H1 total |
| --- | --- | --- | --- |
| First | 500, 700, 400 | 5, 7, 4 | $16 |
| Replay | 500, 700, 400 | 5, 7, 4 | $16 |

## What total should H2 report after each change?

- Source lineage: `lecture#sections.card17`
- Citations: none

Name the boundary, the consequence, and the evidence.

Inspect H2 purchases with a partner: Q201 $6, Q202 $8, Q203 $3.

- State each layer's grain
- Compare naive replay with upsert and recompute
- Trace each independent vendor change
- Trace the cents adapter through a retry

**Response mode:** written

## Check each stage before using the report.

- Source lineage: `lecture#sections.card18`
- Citations: none

The job completed. What evidence proves the dashboard is right?

### Diagram explanation

Bronze: received H2 records → Silver: unique purchases, USD → Gold: H2/day total → Dashboard: $17

- Arrow from Bronze: received H2 records to Silver: unique purchases, USD
- Arrow from Silver: unique purchases, USD to Gold: H2/day total
- Arrow from Gold: H2/day total to Dashboard: $17
