# IT 630 L07 · Git and version history

- Course: IT 630 · Data Science
- Lecture: L07 / Git and version history
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `ffbc8bf1d8feed7ff6fc21e7b00128953e839b1c2115c9d8e3d2f02b9143602d`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## How do you change an analysis without losing what worked?

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s01`
- Citations: `approved_storyboard`

- Save a version of an analysis on purpose.
- Find the change that moved a number, and undo it.
- Keep secrets and raw data out of a repository.
- Let a teammate or an AI agent edit your work safely.

## Monday's report said $765, and Friday's says $995

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s02`
- Citations: `approved_storyboard`, `captured_specimens`

- Backline Tickets sells concert tickets online.
- You own one weekly revenue script and one orders export.
- The same week of orders produced both numbers.
- Your teammate Sam also edits the script.
- Finance wants the right number and what changed.

### Code state 1

Monday

```text
$ python3 weekly_report.py
Weekly revenue: $765.00
```

### Code state 2

Friday

```text
$ python3 weekly_report.py
Weekly revenue: $995.00
Orders counted: 6
```

## Copies of a file cannot say what changed

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s03`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

weekly-report/

```text
$ ls
api_key.env
data
weekly_report.py
weekly_report_FINAL (1).py
weekly_report_FINAL.py
weekly_report_FINAL_sam_edits.py
weekly_report_v2.py
```

- Which file made Monday's number? The names don't say.
- A copy records that something changed, not what or why.
- Version control: records changes so you can recall a version later.
- Git is the version control system most analysts will meet.

## A commit is a snapshot of the files you chose

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s04`
- Citations: `approved_storyboard`, `captured_specimens`

### Files move left to right

| Working tree | Staging area | Repository |
| --- | --- | --- |
| Files now | Next snapshot | Saved commits |

### Code state 1

Before staging

```text
$ git status
Untracked files:
  README.md
  api_key.env
  data/
  weekly_report.py
```

### Code state 2

Choose two files

```text
$ git add weekly_report.py README.md
$ git status
Changes to be committed:
  new file: README.md
  new file: weekly_report.py
Untracked: api_key.env  data/
```

### Code state 3

Save the snapshot

```text
$ git commit -m \
  'Add revenue report'
[main decbb66] committed
2 files changed
```

## The history lists every commit, author, and message

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s05`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

History

```text
$ git log --oneline
7c908f1 Add run instructions to README
9e8c867 updates
135db7e Print the number of orders counted
decbb66 Add weekly revenue report
```

### Code state 2

Open the vague commit

```text
$ git log -1 9e8c867
commit 9e8c867
Author: Sam Rivera
Date:   Thu Sep 17 16:48:00 2026 -0400

    updates
```

- `git log` lists commits, newest first.
- Each commit has an id, author, date, and message.
- The id names the snapshot; copy it, never retype it.
- Three messages explain a change. One is only updates.

## Git diff shows the lines that moved the number

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s06`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

Compare two snapshots

```text
$ git diff 135db7e 9e8c867 -- weekly_report.py
 with open("data/orders_export.csv") as f:
     for row in csv.DictReader(f):
-        if row["status"] == "refunded":
-            continue
         orders += 1
         total += int(row["qty"]) * float(row["price"])
```

- `git diff A B` compares two commits line by line.
- A line starting with `-` was removed; `+` was added.
- The removed test skipped refunded orders.
- Two refunds returned: $765 + $230 = $995.

## Git restore brings back the version that worked

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s07`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

Restore, test, commit

```text
$ git restore --source=135db7e weekly_report.py
$ git status --short
 M weekly_report.py
$ python3 weekly_report.py
Weekly revenue: $765.00
Orders counted: 4
$ git commit -am 'Restore the refund filter'
[main 999f08c] Restore the refund filter
```

- `git restore --source=COMMIT FILE` brings back that file.
- The restored file is a change; commit it.
- Plain `git restore FILE` discards uncommitted edits: no undo.

## Git add dot stages everything, including the API key

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s08`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

What Sam staged

```text
$ git add .
$ git status
Changes to be committed:
  new file: api_key.env
  new file: data/orders_export.csv
  modified: weekly_report.py
```

### Code state 2

What history retained

```text
$ git log --stat -1 9e8c867
    updates

 api_key.env            | 1 +
 data/orders_export.csv | 7 +++++++
 weekly_report.py       | 2 --
```

- `git add .` stages every new and changed file.
- The commit named updates carried three files.
- The `--stat` number is lines changed; the bars are scaled.
- `api_key.env` holds the live ticketing key.
- `orders_export.csv` holds customer orders.

## Ignoring a file does not remove it from history

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s09`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

Ignore future files

```text
$ cat .gitignore
api_key.env
data/
```

### Code state 2

Stop tracking

```text
$ git rm --cached -r api_key.env data
rm 'api_key.env'
rm 'data/orders_export.csv'
$ git commit -m 'Stop tracking files'
```

### Code state 3

The old snapshot remains

```text
$ git show \
  9e8c867:api_key.env
TICKETING_API_KEY=…
```

- .gitignore lists paths Git should leave untracked.
- `rm --cached` prints `rm`, but the file stays on disk.
- `git show COMMIT:FILE` prints the saved file.
- A committed secret is leaked; replace the key.

## A repository without the data still has to say which data it used

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s10`
- Citations: `approved_storyboard`

### Code state 1

README.md

```text
## Data

Source: Backline orders export, week of 2026-09-07,
pulled 2026-09-14, 6 rows.
The export is not in this repository.
Put it at data/orders_export.csv.
```

- You took the export out, so nothing here says which data produced $765.
- A provenance line records source, period, pull date, and row count.
- Git is strongest on text; large exports and binary assets need other storage.
- Notebook output makes notebook diffs unreadable.
- GitHub blocks files larger than 100 MiB.

## Every clone holds the full history

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s11`
- Citations: `approved_storyboard`, `captured_specimens`

### Diagram explanation

Your laptop, GitHub, and Sam's laptop each hold the repository's full six-commit history.

- GitHub origin · full history leads to Sam's laptop · full history: clone / pull
- Your laptop · full history leads to GitHub origin · full history: push
- Sam's laptop · full history leads to GitHub origin · full history: push

### Code state 1

Sam's new laptop

```text
$ git clone ../remote/weekly-report.git
Cloning into 'weekly-report'... done.
$ git log --oneline
1d71637 Stop tracking the API key and raw export
999f08c Restore the refund filter
7c908f1 Add run instructions to README
9e8c867 updates
```

- Distributed version control: every copy holds the full history.
- `git clone` copies a repository, history included.
- GitHub is a meeting point, not the only copy.

## A commit stays on your laptop until you push

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s12`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

Local only

```text
$ git status
On branch main
Ahead of origin/main by 1 commit.
```

### Code state 2

Shared copy moved first

```text
$ git push
 ! [rejected] main -> main (fetch first)
```

### Code state 3

Bring it in, then send yours

```text
$ git pull
Merge complete.
$ git push
main -> main
```

- `git push` sends new commits to the shared copy.
- `git pull` brings in commits you lack.
- Git rejects a push when shared work is missing locally.
- Git reports fetch first; pull fetches and merges.

## A home copy helped Pixar recover Toy Story 2

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s13`
- Citations: `approved_storyboard`, `pixar_movie_vanishes`

### Diagram explanation

Pixar recovered from a failed asset server and unusable backup using an out-of-date but complete home copy.

- Asset server · deleted leads to Backup · unusable: restore failed
- Home copy · out of date, complete leads to Asset server · deleted: recovery anchored here

- A delete command ran in the wrong directory; most production files went.
- The backups had been failing quietly.
- A supervisor's recent home copy anchored the recovery.
- A copy rescues you only if it is complete and somewhere else.

## Commit and branch before an agent edits the folder

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s14`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

Before the agent runs

```text
$ git status --short
$ git switch -c agent/fee-column
Switched to a new branch 'agent/fee-column'
```

- An AI agent edits several files directly.
- Commit your work; a clean status is the restore point.
- Branch: a named line of commits that moves as you commit.
- `git switch -c NAME` creates and enters a branch.
- The agent's branch leaves `main` unchanged.

## Read status and diff before keeping an agent's work

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s15`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

Inspect all changes

```text
$ git status --short
M README.md
 M weekly_report.py
?? debug_dump.csv
$ git diff weekly_report.py
- total = subtotal
+ total = subtotal * 1.12
```

### Code state 2

Restore tracked files

```text
$ git restore \
  weekly_report.py README.md
$ git status --short
?? debug_dump.csv
```

- `git status` lists every file the agent touched.
- `git diff` shows each change before you accept it.
- `git restore` discards tracked-file edits.
- Restore leaves untracked files; an agent never force-pushes.

## Every Git command saves, compares, or moves a snapshot

- Source lineage: `it630-fall-2026-l07-git-version-history#sections.s16`
- Citations: `approved_storyboard`, `captured_specimens`

### Code state 1

History and folder

```text
$ git log --oneline --graph
* c308de1 Merge branch 'main'
* 999f08c Restore refund filter
* 9e8c867 updates
$ ls -A
.git  .gitignore  README.md  api_key.env  data  weekly_report.py
```

### One model, twelve commands

| Job | Commands | Gotcha |
| --- | --- | --- |
| Save | status · add · commit | add . stages everything |
| Compare / restore | log · diff · show · restore | Vague messages hide; plain restore discards |
| Keep out | .gitignore · rm --cached | History keeps committed files |
| Move / work beside | clone · push · pull · switch -c | Unpushed work is local; review first |
