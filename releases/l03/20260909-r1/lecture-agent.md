# IT 630 L3 - What forms does data come in?

- Course: IT 630 - Data Science and Scalable Data Systems
- Lecture: 03 / Unit A, Meeting 3
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `ef434e72214ec139bfad6a002393051d15ffa8a51ffabbeb9829b081b9ae7dd6`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## What forms does data come in?

- Source lineage: `it630_l03#sections.cover`
- Citations: none

### IT 630 · Unit A, Meeting 3

Five files from one study. Recognize what each carries before choosing a reader.

## One study, five files

- Source lineage: `it630_l03#sections.one_study`
- Citations: `l3_storyboard`

### Image alternative

Exact fictional-study file listing: five files, including CSV, JSON, XML, Parquet, and a debrief comment.

Office / IT 630 (generated fictional specimen) · CC0-1.0

- Two participants: quiet versus noisy background; three trials each.
- Four files represent six trials; one is a participant comment.
- Which one is the data?

**Citations:** `l3_storyboard`

## Structured, semi-structured, and unstructured

- Source lineage: `it630_l03#sections.three_kinds`
- Citations: `l3_storyboard`

- Structure describes organization; format describes encoding.
- JSON and XML can also represent flat records.
- These labels suggest preparation work, not data quality.

**Citations:** `l3_storyboard`

| Kind | What it offers |
| --- | --- |
| Structured | Named fields and records |
| Semi-structured | Labels and nesting without one required flat table |
| Unstructured | Information is not already in ready-made fields |

## Unstructured: a participant's comment

- Source lineage: `it630_l03#sections.comment`
- Citations: `l3_storyboard`

### Image alternative

Exact fictional debrief comment, which has no predefined trial or guessed field.

Office / IT 630 (generated fictional specimen) · CC0-1.0

- This is data, but it has no predefined trial or guessed field.
- Can we identify the guessed trial from this comment alone?
- No: trial 2 would be a hypothesis, not an observed link.

**Citations:** `l3_storyboard`

## Structured: rows, columns, and values

- Source lineage: `it630_l03#sections.table_vocabulary`
- Citations: `l3_storyboard`

| participant_id | condition | trial | reaction_time_ms | correct |
| --- | --- | --- | --- | --- |
| P01 | quiet | 1 | 412.5 | True |
| P01 | quiet | 2 | 398.0 | True |
| P01 | quiet | 3 | 405.2 | True |
| P02 | noisy | 1 | 501.3 | True |
| P02 | noisy | 2 | 623.8 | False |
| P02 | noisy | 3 | 478.9 | True |

- A column records one named thing; a row records one occurrence; a value is one entry.
- One row here is one trial by one participant.
- Six rows and two participants are different numbers.

**Citations:** `l3_storyboard`

## CSV: comma-separated text

- Source lineage: `it630_l03#sections.csv`
- Citations: `l3_storyboard`

### Image alternative

Exact fictional CSV trial excerpt with five comma-separated fields and six rows.

Office / IT 630 (generated fictional specimen) · CC0-1.0

- CSV records fields and records as text.
- It carries characters, not declared column types.
- A reader may infer types or a caller may supply them.

**Citations:** `l3_storyboard`

## JSON and XML: nested records

- Source lineage: `it630_l03#sections.nested`
- Citations: `l3_storyboard`

### Image alternative

Exact fictional JSON and XML excerpts, both nesting trials inside participant records.

Office / IT 630 (generated fictional specimen) · CC0-1.0

- Both nest trials inside participants; neither chooses an analysis row.
- condition appears once per participant here and on every trial row in CSV.
- Both preserve values while organizing the study differently.

**Citations:** `l3_storyboard`

## Text files: encoding versus content

- Source lineage: `it630_l03#sections.text_distinction`
- Citations: `l3_storyboard`

### Image alternative

Exact structured CSV text beside an exact unstructured narrative-text excerpt.

Office / IT 630 (generated fictional specimen) · CC0-1.0

- CSV, JSON, and XML are text: that is how bytes are written.
- Unstructured describes what the content offers, not whether it is text.
- CSV is text and structured; the comment is text and unstructured.

**Citations:** `l3_storyboard`

## Parquet: a binary format that stores types

- Source lineage: `it630_l03#sections.parquet`
- Citations: `l3_storyboard`

### Image alternative

Exact fictional Parquet hex excerpt, including the PAR1 magic bytes.

Office / IT 630 (generated fictional specimen) · CC0-1.0

- Binary, not text; PAR1 announces the format.
- The schema carries types: str, str, int64, float64, bool in this run.
- This 3,410-byte file versus a 195-byte CSV demonstrates no size or speed advantage.

**Citations:** `l3_storyboard`

## Loading each file into a DataFrame

- Source lineage: `it630_l03#sections.loads`
- Citations: `l3_storyboard`

| You have | You call | You get back |
| --- | --- | --- |
| trials.csv | pd.read_csv('trials.csv') | 6 rows × 5 columns |
| trials.parquet | pd.read_parquet('trials.parquet') | 6 rows × 5 columns |
| trials.xml | pd.read_xml('trials.xml', xpath='.//trial') | 6 rows × 3 columns |
| trials.json | pd.read_json('trials.json') | 2 rows × 2 columns |

- A DataFrame is a table held in program memory, not a file.
- XML selected trial elements and therefore omitted participant attributes.
- JSON returned participant rows with nested trials; it answered a different question.

**Citations:** `l3_storyboard`

## Value meaning and Python types

- Source lineage: `it630_l03#sections.value_kinds`
- Citations: `l3_storyboard`

| Column | Example | What it means | Python type |
| --- | --- | --- | --- |
| participant_id | 'P01' | qualitative identifier | str |
| condition | 'quiet' | qualitative category | str |
| trial | 1 | ordered identifier | int |
| reaction_time_ms | 412.5 | quantitative measurement | float |
| correct | True | qualitative two-category value | bool |

- Meaning and storage type are related but not the same.
- trial sums to 12, which means nothing; being numeric is not permission to add.
- The fictional six-row specimen supports no population or causal inference.

**Citations:** `l3_storyboard`

## Three questions to ask of any file

- Source lineage: `it630_l03#sections.transfer`
- Citations: `l3_storyboard`

| Ask | Why |
| --- | --- |
| Which kind is it? | Anticipate preparation work. |
| Does it say what a row is? | Recognize a real decision. |
| Does it carry types? | Know when a reader guesses. |

- Which kind is it: structured, semi-structured, or unstructured?
- Does it say what a row is? If not, deciding is real work.
- Does it carry types, or will something guess?
- The next 20-minute notebook is a different case: a retail sales audit, not this fictional psychology study.

**Citations:** `l3_storyboard`
