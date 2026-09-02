# IT 630 L2 - The repository someone else has to rerun

- Course: IT 630 - Data Science and Scalable Data Systems
- Lecture: 02 / Unit 1, Meeting 2
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `3d54cc58dd80632bba2c17157e7eff17d5cb6f5d0b32ad9c405f106a2b825e1f`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## IT 630: Data Science and Scalable Data Systems

- Source lineage: `it630_l02#sections.cover`
- Citations: none

### Unit 1, Meeting 2. The repository someone else has to rerun.

Four questions that tell you whether a project someone hands you can be trusted, and whether the one you hand over can be picked up by anyone else.

## Can you produce the same number from this folder?

- Source lineage: `it630_l02#sections.the_folder`
- Citations: `course_inventory_u1`, `turing_way_metadata`

### Code state 1

Illustrative example: a made-up project folder, as received

```text
pop-tart-restock/
    analysis_FINAL_v3 (2).ipynb
    analysis_FINAL_v3 (2) - Copy.ipynb
    data.csv
    data_new.csv
    chart.png
    creds.txt

```

- The person who made it has left. The number they reported is in chart.png.
- Running on the author's own machine is not the test. The test is whether someone else gets there, and that someone is often you, months later.

**Citations:** `course_inventory_u1`, `turing_way_metadata`

## Reproducible has a definition, and it excludes things.

- Source lineage: `it630_l02#sections.reproducible_defined`
- Citations: `turing_way_definitions`

- **reproducible**: The same analysis steps performed on the same dataset consistently produce the same answer.
- **reproducible research**: Work that can be independently recreated from the same data and the same code the original team used.

**Citations:** `turing_way_definitions`

## Two questions, not four labels: same data? same analysis?

- Source lineage: `it630_l02#sections.four_quadrants`
- Citations: `turing_way_definitions`, `course_week1_review`

| Analysis | Same data | Different data |
| --- | --- | --- |
| Same analysis | Reproducible | Replicable |
| Different analysis | Robust | Generalisable |

**Citations:** `turing_way_definitions`

- **replicable**: The same analysis on different datasets produces qualitatively similar answers.
- **robust**: The same dataset put through different analysis workflows, for example one pipeline in R and another in Python, produces a qualitatively similar or identical answer. The result does not depend on the language chosen.
- **generalisable**: Replicable and robust findings combined. Changing the software and the dataset at once does not by itself produce a generalised result; many further steps are needed.

**Citations:** `turing_way_definitions`

- Reproducible is the weakest of the four: same data, same analysis.
- It is the one this course holds work to.

**Citations:** `turing_way_definitions`, `course_week1_review`

## Clearing the weakest bar takes four specific things.

- Source lineage: `it630_l02#sections.four_inputs`
- Citations: `course_inventory_u1`, `turing_way_definitions`, `turing_way_metadata`

- The code that produced the result.
- The data, or instructions for retrieving that version of it.
- The environment and dependencies the code needs to run.
- The parameters it was run with.

**Citations:** `course_inventory_u1`, `turing_way_definitions`

- **parameter**: A value the analysis was run with, recorded alongside the data as part of its documentation: parameters, variables, column headings, and symbols used.

**Citations:** `turing_way_metadata`

## The folder fails all four, and not because anyone was careless.

- Source lineage: `it630_l02#sections.folder_fails`
- Citations: `course_inventory_u1`, `turing_way_metadata`

### Four requirements, each checked against the illustrative folder.

| Requirement | What the folder shows |
| --- | --- |
| Code | Two notebooks differing only by a copy suffix. Nothing says which produced the number. |
| Data, and which version | data.csv and data_new.csv. The difference is recorded nowhere. |
| Environment | Nothing names the software the notebook needs. |
| Parameters | No documentation carries the values it was run with. |

**Attributed to:** Course illustrative example

**Citations:** `course_inventory_u1`, `turing_way_metadata`

## Different question: where did this one number come from?

- Source lineage: `it630_l02#sections.provenance_defined`
- Citations: `course_inventory_u1`, `turing_way_metadata`

- **provenance**: A reported value can be traced to the specific inputs, transformations, and versions that produced it.

**Citations:** `course_inventory_u1`

- Reproducibility asks whether the whole thing runs again. Provenance asks where one number came from.
- Trace it: which CSV, then which notebook, then run with what. The chain stops at the first fork.
- Having the four requirements makes the trace possible. Documentation linking this value to them is what makes it hold.

**Citations:** `course_inventory_u1`

## Documentation is what carries the trace.

- Source lineage: `it630_l02#sections.what_carries_provenance`
- Citations: `turing_way_metadata`

- **documentation**: Information that lets collaborators, colleagues, and future you understand what has been done and why, written in clear plain language.
- **README**: A file describing data or software so it can be correctly interpreted and used by yourself or others. It can serve as the landing page of a project repository.
- **data dictionary**: A listing of every variable and label in a dataset with value ranges, units of measurement, and sources. The test is whether another person could interpret the data from it alone.

**Citations:** `turing_way_metadata`

- Without metadata to provide provenance and context, data cannot be used effectively.
- A table of numbers is useless if no headings describe what the rows and columns contain.
- Documentation is what lets a user judge the source, strengths, weaknesses, and limits of data, and so decide whether to use it.

**Citations:** `turing_way_metadata`

## A repository gives a project one legible entry point.

- Source lineage: `it630_l02#sections.repository_structure`
- Citations: `course_inventory_u1`, `turing_way_storage`, `turing_way_metadata`, `course_week1_review`

- **project repository**: A project's own storage and organisation: a legible entry point, with code, documentation, inputs, and outputs separated rather than pooled.

**Citations:** `course_inventory_u1`

- Use a clear folder structure, with enough subfolders that files are not scattered or piled into one place.
- Do not invent one. Use an existing project template.
- A README can be the landing page, describing what the project is and how the work was done.
- File names should be friendly to machines and humans: version numbers rather than FINAL, no special characters or spaces, not too long.

**Citations:** `turing_way_storage`, `turing_way_metadata`, `course_week1_review`

## Some things must never enter the repository at all.

- Source lineage: `it630_l02#sections.what_never_enters`
- Citations: `course_inventory_u1`, `turing_way_storage`, `turing_way_definitions`

- Personal and sensitive data is bound by data-protection rules that constrain where it may be stored at all, and it should not appear even in folder or file names.
- Work can be reproducible without being open: some research uses sensitive data that cannot be shared, so reproducibility does not require putting the data in the folder.
- A repository is not a dumping ground for secrets. creds.txt should never have been there.

**Citations:** `turing_way_storage`, `turing_way_definitions`, `course_inventory_u1`

## The same project, organised.

- Source lineage: `it630_l02#sections.the_repair`
- Citations: `course_inventory_u1`, `turing_way_storage`, `turing_way_metadata`

### Code state 1

Illustrative example: the folder as received

```text
pop-tart-restock/
    analysis_FINAL_v3 (2).ipynb
    analysis_FINAL_v3 (2) - Copy.ipynb
    data.csv
    data_new.csv
    chart.png
    creds.txt

```

### Code state 2

The same project, organised

```text
pop-tart-restock/
    README.md      <- what this is, how to rerun it
    data/          <- inputs
    code/
    results/       <- outputs, kept apart from inputs
    environment / dependency list
    (credentials are not in this picture)

```

## Four questions to ask of any project you are handed.

- Source lineage: `it630_l02#sections.four_questions`
- Citations: `turing_way_definitions`, `course_inventory_u1`, `turing_way_storage`, `turing_way_metadata`

- Is it reproducible: same analysis, same data, same answer, as distinct from replicable, robust, or generalisable?
- Can it be rerun: code, data and which version, environment, parameters?
- Can one reported value be traced to its inputs, transformations, and versions?
- Does it have a legible entry point, separated inputs and outputs, and nothing in it that should never have been there?

**Citations:** `turing_way_definitions`, `course_inventory_u1`, `turing_way_storage`, `turing_way_metadata`
