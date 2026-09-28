# IT 630 L08 · What SQL is and what it does

- Course: IT 630 · Data Science
- Lecture: L08 / September 28 · What SQL is and what it does
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `58c79d2c0584875bada16092f1f9f0cd63c3ef7a5e078bb093ed75437a7003cb`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## SQL is a language for asking questions of tables

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c01`
- Citations: `approved_storyboard`, `ostax_pds_25`, `swc_sql`, `captured_specimens`

- SQL (Structured Query Language) works on tables: rows and columns, like a CSV or a DataFrame.
- You write a query. A database engine runs it. The answer comes back as another table.

### trials, the eight rows from the A2 activity. SELECT COUNT(*) AS trials FROM trials; returns one row: trials = 8

| participant_id | condition | trial | reaction_time_ms | correct |
| --- | --- | --- | --- | --- |
| P01 | quiet | 1 | 412.5 | True |
| P01 | quiet | 2 | 398.0 | True |
| P02 | noisy | 1 | 501.3 | True |
| P02 | noisy | 2 | 623.8 | False |
| P03 | quiet | 1 | 455.1 | False |
| P03 | quiet | 2 | 430.4 | True |
| P04 | noisy | 1 | 489.7 | True |
| P04 | noisy | 2 | 550.2 | True |

## Tables and SQL are the long-standing default

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c02`
- Citations: `approved_storyboard`, `so_survey_2025`, `sqlite_deployed`

| Year | What happened |
| --- | --- |
| 1970 | Codd proposes storing shared data as tables: the relational model |
| 1974 | IBM researchers publish SEQUEL, the language that became SQL |
| 1986 | SQL becomes an ANSI standard |
| 2025 | 58.6% of developers surveyed use SQL, third of all languages |
| Today | SQLite runs in every Android and iOS phone and every major browser |

- Tables and SQL are not a trend. They are the long-standing default.

## SQL is not a database

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c03`
- Citations: `approved_storyboard`

- **SQL**: a language for working with structured tables.
- **Database engine**: the software that stores or reads data and executes SQL.
- **Result**: another table.

### The same core query, three engines

| Question in SQL | Engine | Result |
| --- | --- | --- |
| SELECT ... FROM trials | DuckDB in a notebook | a table |
| SELECT ... FROM trials | an application database | a table |
| SELECT ... FROM trials | a cloud warehouse | a table |

## You describe the result; the engine chooses how

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c04`
- Citations: `approved_storyboard`, `wgaca_2024`

### Code state 1

ONE QUESTION, NO STEPS

```sql
SELECT participant_id, reaction_time_ms
FROM trials
WHERE correct = TRUE;
```

- The query does not say which row to read first.
- It does not say how to use memory, indexes, or processors.
- So engines can get faster without anyone rewriting the question.
- That is why SQL keeps winning: newer systems tried to replace it, and many ended up adding SQL.

## SQL does more than SELECT

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c05`
- Citations: `approved_storyboard`

| Job | Plain-language question | Examples |
| --- | --- | --- |
| Define structure | What tables and columns exist? | CREATE, ALTER |
| Add or change rows | What facts should be stored? | INSERT, UPDATE, DELETE |
| Ask questions | Which rows and summaries do we need? | SELECT |
| Coordinate safely | Which changes belong together, and who may act? | transactions, permissions |

- We start with asking questions. The other jobs return later.

## A query uses six recurring table operations

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c06`
- Citations: `approved_storyboard`

| Operation | What changes? | SQL |
| --- | --- | --- |
| Project | columns shown | SELECT |
| Filter | rows kept | WHERE |
| Derive | values computed | expression + AS |
| Sort | row order shown | ORDER BY |
| Aggregate | many rows summarized | GROUP BY + COUNT / AVG |
| Combine (later) | tables connected | JOIN |

## SELECT projects columns; FROM names the source

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c07`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

PROJECT TWO COLUMNS

```sql
SELECT participant_id, condition
FROM trials
ORDER BY participant_id, trial;
```

### Result: eight source rows remain; five source columns become two. SELECT * means all columns; naming them makes the dependency visible

| participant_id, rows 1 to 4 | condition, rows 1 to 4 | participant_id, rows 5 to 8 | condition, rows 5 to 8 |
| --- | --- | --- | --- |
| P01 | quiet | P03 | quiet |
| P01 | quiet | P03 | quiet |
| P02 | noisy | P04 | noisy |
| P02 | noisy | P04 | noisy |

## WHERE filters rows; ORDER BY controls their display

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c08`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

KEEP CORRECT TRIALS

```sql
SELECT participant_id, condition, reaction_time_ms
FROM trials
WHERE correct = TRUE
ORDER BY reaction_time_ms;
```

### Result: six of eight rows, fastest first. Filtering changes membership; sorting changes presentation

| participant_id, rows 1 to 3 | condition, rows 1 to 3 | reaction_time_ms, rows 1 to 3 | participant_id, rows 4 to 6 | condition, rows 4 to 6 | reaction_time_ms, rows 4 to 6 |
| --- | --- | --- | --- | --- | --- |
| P01 | quiet | 398.0 | P04 | noisy | 489.7 |
| P01 | quiet | 412.5 | P02 | noisy | 501.3 |
| P03 | quiet | 430.4 | P04 | noisy | 550.2 |

## Expressions derive a new value without changing the source

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c09`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

DERIVE SECONDS

```sql
SELECT participant_id,
       ROUND(reaction_time_ms / 1000.0, 4) AS reaction_time_s
FROM trials
ORDER BY participant_id, trial;
```

### Result: eight rows in, eight rows out. AS names the result column; the source table still stores milliseconds

| participant_id, rows 1 to 4 | reaction_time_s, rows 1 to 4 | participant_id, rows 5 to 8 | reaction_time_s, rows 5 to 8 |
| --- | --- | --- | --- |
| P01 | 0.4125 | P03 | 0.4551 |
| P01 | 0.398 | P03 | 0.4304 |
| P02 | 0.5013 | P04 | 0.4897 |
| P02 | 0.6238 | P04 | 0.5502 |

## GROUP BY turns rows into summaries

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c10`
- Citations: `approved_storyboard`, `captured_specimens`, `jvns_order`

### Code state 1

THE QUERY

```sql
SELECT condition,
       COUNT(*) AS correct_trials,
       ROUND(AVG(reaction_time_ms), 1) AS avg_reaction_ms
FROM trials
WHERE correct = TRUE
GROUP BY condition
ORDER BY condition;
```

### Result: one row per condition

| condition | correct_trials | avg_reaction_ms |
| --- | --- | --- |
| noisy | 3 | 513.7 |
| quiet | 3 | 413.6 |

- Read it as a pipeline: trials, keep correct rows, one summary per condition, then display.

## A query that runs still needs a receipt

- Source lineage: `it630-fall-2026-l08-sql-why-and-operations#sections.c11`
- Citations: `approved_storyboard`, `captured_specimens`

| Stage | Rows |
| --- | --- |
| Source | 8 |
| After WHERE correct = TRUE | 6 |
| After GROUP BY condition | 2 conditions, 3 correct trials each |

### Code state 1

SPOT-CHECK QUIET

```text
(412.5 + 398.0 + 430.4) / 3 = 1240.9 / 3 = 413.6
```

- **Ran**: valid syntax; the engine returned a table.
- **Checked**: the result matches the source rows.
