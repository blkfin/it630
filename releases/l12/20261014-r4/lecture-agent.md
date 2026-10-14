# Storage and safe updates

- Course: IT 630
- Lecture: Storage and safe updates / October 14, 2026
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `3d65a3fc98706f798684b0ef289b680b4e9fefd7301c24bd7befd9b80e0aa613`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## What makes a total from stored data trustworthy?

- Source lineage: `lecture#sections.cover`
- Citations: `carrier`, `delta`

By the end, you can check row meaning, explain column reads, and trace safe updates.

Sales query

```sql
SELECT SUM(units) FROM sales;
```

One sales query can return 600, 580 or 880.

## What does each returned row represent?

- Source lineage: `lecture#sections.loan_check`
- Citations: `assessment`

12 loans, 5 members; exactly 4 loans exceed 14 days.

Library loans

```sql
SELECT loan_id, branch, days_kept
FROM loans
WHERE days_kept > 14
ORDER BY days_kept DESC;
```

Write the row count and: one row is one ___.

## Filtering and sorting preserve what each row means

- Source lineage: `lecture#sections.loan_answer`
- Citations: `assessment`

| Operation | Rows | One row means |
| --- | --- | --- |
| Start | 12 | One loan |
| Keep days_kept > 14 | 4 | One loan |
| Sort longest first | 4 | One loan |

Grain: what one row represents.

## Which table should test the supposed unique key?

- Source lineage: `lecture#sections.key_check`
- Citations: `assessment`

### students

- One row should be one student.
- S07 appears twice.

### joined result

- One row is one session matched to a student.
- Student IDs repeat once per session.

Choose students or the joined result before running the check.

## The lookup table exposes its duplicate student key

- Source lineage: `lecture#sections.key_answer`
- Citations: `assessment`

Check students

```sql
SELECT COUNT(*), COUNT(DISTINCT student_id)
FROM students;
-- Result: 6, 5
```

| Case | Joined rows | Distinct IDs |
| --- | --- | --- |
| Duplicate S07, inner join | 11 | at most 5 |
| Duplicate removed, inner join | 8 | at most 5 |

Repeated IDs after joining are compatible with a valid lookup key.

## Column chunks store each field's values together

- Source lineage: `lecture#sections.layout`
- Citations: `carrier`, `parquet`

We can check row meaning. Now the sales sum needs only units: group values by field.

Three sales rows, one row group

```text
Rows:
S01 | Strawberry | 17
S01 | Cinnamon   |  9
S01 | Blueberry  |  7

Column chunks in that row group:
store_id: S01, S01, S01
flavor: Strawberry, Cinnamon, Blueberry
units: 17, 9, 7
```

A row group is a batch of rows; each field has its own chunk.

## The sum reads units chunks from each row group

- Source lineage: `lecture#sections.read_chunks`
- Citations: `carrier`, `parquet`

Column reader

```text
SELECT SUM(units) FROM sales;

1. Read footer metadata.
2. Locate units chunks in each row group.
3. Read those chunks.
4. Add the units values.
```

- Other fields need not be read for this sum.
- Reading 1 of 7 fields does not mean 1/7 of the time.

## Repeated flavors can use a small dictionary

- Source lineage: `lecture#sections.encoding`
- Citations: `carrier`, `parquet`

Dictionary encoding stores distinct values once and uses IDs for repetitions.

Flavor chunk

```text
Values: Strawberry, Strawberry, Blueberry, Strawberry

Dictionary: 0 = Strawberry, 1 = Blueberry
Encoded values: 0, 0, 1, 0
```

Size savings depend on the values and encoding overhead.

## A warehouse gives report users managed analytical tables

- Source lineage: `lecture#sections.warehouse`
- Citations: `aws`, `delta`

Column reads help the sum. A store resends one slice: C is replaced by C_new.

Warehouse: an analytical database holding managed tables.

### Diagram explanation

An analytical engine reads a managed sales table.

- Arrow from SQL query engine to Managed sales table: reads and manages updates

Choose this for shared SQL reports; keep original logs separately if needed.

## A lake keeps original files for different future uses

- Source lineage: `lecture#sections.lake`
- Citations: `aws`, `parquet`, `iceberg`

Data lake: a repository of files for varied uses.

### Diagram explanation

A query engine reads sales and logs held in file storage.

- Arrow from Query engine to Sales Parquet files and raw logs: interprets schema and reads

Keep data before you know its questions, in files other tools can read.

## Should the sum count both replacement files?

- Source lineage: `lecture#sections.folder_check`
- Citations: `delta`, `iceberg`

| File | Units | Role |
| --- | --- | --- |
| A | 100 | Unchanged |
| B | 200 | Unchanged |
| C | 300 | Old slice |
| C_new | 280 | Replacement for C |

A writer finished C_new. All four files exist. Predict the folder total.

## Scanning every file counts the corrected slice twice

- Source lineage: `lecture#sections.folder_answer`
- Citations: `iceberg`

Naive folder scan

```text
A + B + C + C_new
100 + 200 + 300 + 280 = 880
```

File presence does not mean membership in the table.

## A commit selects the files that count together

- Source lineage: `lecture#sections.commit`
- Citations: `delta`, `iceberg`

Snapshot: the committed file set a reader uses.

Replacement update

```text
1. Read version 1: A + B + C = 600
2. Finish replacement C_new: 280 units
3. Validate; commit version 2: A + B + C_new = 580

Started before commit: version 1, 600
Started after commit: version 2, 580
```

Warehouse engines commit their own table updates; plain files need a commit protocol.

## The visible version switches completely or stays unchanged

- Source lineage: `lecture#sections.atomic`
- Citations: `delta`, `iceberg`

Atomic commit: all related changes become visible together, or none do.

### Commit succeeds

- Current version selects A, B and C_new.
- New readers total 580.

### Commit fails

- Current version still selects A, B and C.
- New readers total 600.

A coherent older result can differ from a coherent newer result.

## A lakehouse adds safe table updates to retained files

- Source lineage: `lecture#sections.lakehouse`
- Citations: `delta`, `iceberg`

Snapshots select the sales files. Queries still need tools that honor that selection.

Table format: table metadata plus rules for safe updates.

| Layer | Role in the sales total |
| --- | --- |
| File storage | Keeps raw logs and sales files |
| Table format | Commits A, B, C_new as version 2 |
| Compatible engine | Reads version 2: 580 units |

Lakehouse: file storage with table management and compatible analytical engines.

## The right arrangement depends on the work and tools

- Source lineage: `lecture#sections.arrangements`
- Citations: `aws`, `delta`, `iceberg`

| Arrangement | Choose when you need | You must arrange |
| --- | --- | --- |
| Warehouse | Shared SQL sales reports | Load report tables; retain raw logs if needed |
| Lake files | Original logs for varied uses | Query tools; safe table updates if needed |
| Lakehouse | Avoid syncing a second loaded table copy | Compatible file tools and SQL read one table |

- How raw must the data stay?
- Who queries it, and with which tools?
- How often do corrections arrive?
- What does it cost at your volume?

Neither is cheaper or faster by name.

## How would you keep the shop report consistent?

- Source lineage: `lecture#sections.transfer`
- Citations: `delta`, `iceberg`

- A campus shop retains raw register logs.
- A daily sales report must run while corrections arrive.

| Teaching files | Units |
| --- | --- |
| A | 100 |
| B | 200 |
| C, old slice | 300 |
| C_new, replacement | 260 |

Choose an arrangement and say why. Name who coordinates reads, the new total and counted files.

## Trusted totals need correct rows and coordinated reads

- Source lineage: `lecture#sections.close`
- Citations: `delta`, `iceberg`

Check row meaning, read needed fields, and use one committed version.

One defensible shop design

```text
Keep raw logs in file storage.
Publish sales as a lakehouse table.
Use an engine that honors its table format.

Version 2 counts A + B + C_new:
100 + 200 + 260 = 560
```

Which files count is decided by the committed snapshot, not the folder.

Also defensible: lake raw logs plus warehouse report tables, if the team uses SQL and accepts loading a copy.
