# When does a minute end? Big data through velocity

- Course: IT 630
- Lecture: 19 / November 4, 2026
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `41e6ecea7420987dc5b115c5eb92ffbe3251585ef8fd9a9d5c6e4e9c3f6c481d`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## When does a minute end?

- Source lineage: `lecture#sections.card_1`
- Citations: none

By the end, you can group ongoing observations and explain when to stop updating a minute.

A campus bus dashboard counts observations per minute.

## Which minute should receive this bus observation?

- Source lineage: `lecture#sections.card_2`
- Citations: none

- Campus buses send location observations.
- One record describes one observation.
- The dashboard counts observations per minute.
- A delayed observation reaches the dashboard.

### Event: an occurrence. Record: its stored description.

| Observation | Happened (UTC) | Dashboard receives (UTC) |
| --- | --- | --- |
| A | 12:00:40 | 12:02:10 |

## The question determines which time groups the observation.

- Source lineage: `lecture#sections.card_3`
- Citations: none

"When did it happen?" puts A in 12:00.

### Diagram explanation

One occurrence, one record, delayed delivery.

- Arrow from Bus observation A
12:00:40 to Transmitted record A: described by
- Arrow from Transmitted record A to Dashboard receives A
12:02:10: received at

## Data demands can exceed the available system.

- Source lineage: `lecture#sections.card_4`
- Citations: none

- Big data: data demands require scalable approaches beyond the available conventional setup.
- Scalable approach: add resources to handle greater demand.

### Diagram explanation

Three dimensions of data demand; follow velocity.

- Record, left to right: 3 fields
- Field 1, Volume; Amount; value Observations
- Field 2, Velocity; Flow rate; value Arrivals/second
- Field 3, Variety; Forms/sources; value Locations; notices

## Ongoing input has no predetermined final record.

- Source lineage: `lecture#sections.card_5`
- Citations: none

Can the bus dashboard wait for all observations?

### Bounded input

- Finite extent with an end
- Saved observations: a b c END

### Unbounded input

- No predetermined end
- Operating bus feed: a b c …

Input extent describes the data, not execution frequency.

## Execution frequency is a separate processing choice.

- Source lineage: `lecture#sections.card_6`
- Citations: none

A streaming system processes ongoing input; small batches are one option.

### Batch

- Process a collected finite chunk
- [a b c d e f]

### Micro-batch

- Repeat small collected chunks
- [a b] [c d] [e f]

### Record-at-a-time

- Process successive individual records
- [a] [b] [c] [d] [e] [f]

More frequent processing alone guarantees no response deadline.

## Two clocks explain why observation order can reverse.

- Source lineage: `lecture#sections.card_7`
- Citations: none

- We can process arrivals, but receipt order can differ.
- Event time: when the event happened.
- Processing time: when the processing system observes it.

### Occurrence order: A then B. Dashboard observation order: B then A.

| Observation | Event time (UTC) | Processing time (UTC) |
| --- | --- | --- |
| A | 12:00:40 | 12:02:10 |
| B | 12:01:05 | 12:01:07 |

## Minute windows group observations by a chosen clock.

- Source lineage: `lecture#sections.card_8`
- Citations: none

- Time window: a finite group defined by a chosen clock.
- Include the start; exclude the end. C at 12:02:00 belongs to the 12:02 minute.

### Diagram explanation

Include each start; exclude each end. [start,end) means this rule.

- Number line from 0 to 180: Event time, seconds since 12:00:00 UTC
- Ticks at 0, 60, 120, 180
- Point at 40: A
- Point at 65: B
- Point at 120: C
- Interval from 0 to 60: 12:00 minute [0,60)
- Interval from 60 to 120: 12:01 minute [60,120)
- Interval from 120 to 180: 12:02 minute [120,180)

## We assign each observation before counting its minute.

- Source lineage: `lecture#sections.card_9`
- Citations: none

Select the clock, locate the minute, then count one observation.

### UTC; arrival time represents dashboard processing time. Count every observation here.

| Observation | Event time | Arrival time | Event minute | Arrival minute |
| --- | --- | --- | --- | --- |
| B | 12:01:05 | 12:01:07 | 12:01 | 12:01 |
| C | 12:02:00 | 12:02:02 | 12:02 | 12:02 |
| A | 12:00:40 | 12:02:10 | 12:00 | 12:02 |
| D | 12:01:20 | 12:02:12 | 12:01 | 12:02 |
| E | 12:03:10 | 12:03:11 | 12:03 | 12:03 |

## The same observations produce different minute counts.

- Source lineage: `lecture#sections.card_10`
- Citations: none

The clock changes membership, even when the total agrees.

### Five observations in either grouping. No observations discarded.

| Minute (UTC) | Event-time count | Processing-time count |
| --- | --- | --- |
| 12:00 | 1 | 0 |
| 12:01 | 2 | 1 |
| 12:02 | 1 | 3 |
| 12:03 | 1 | 1 |

## Retained counts let delayed observations change a minute.

- Source lineage: `lecture#sections.card_11`
- Citations: none

- We know membership, but more observations may arrive.
- State: information retained between arrivals.
- Finalize: decide to stop updating a minute.

### Keeping a minute open retains its count.

| Minute (UTC) | Retained count | Next delayed observation |
| --- | --- | --- |
| 12:01 | 1 after B | D could increase it |
| 12:02 | 1 after C | More could arrive |

## A watermark estimates how far event time has progressed.

- Source lineage: `lecture#sections.card_12`
- Citations: none

- Watermark: an advancing estimate of event-time progress or completeness.
- Allowed delay: chosen tolerance behind the largest event time seen.
- Our rule: watermark = largest event time seen minus allowed delay.
- Late record: event time < watermark. Older observations can still arrive.

### Diagram explanation

A late record has event time behind the watermark. An estimate is not proof.

- Number line from 0 to 180: Event time, seconds since 12:00:00 UTC
- Ticks at 0, 60, 90, 120, 180
- Point at 90: Watermark 12:01:30
- Point at 120: Largest seen: C at 12:02:00
- Interval from 90 to 120: Allowed delay: 30 seconds

## The watermark guides when our policy retires minutes.

- Source lineage: `lecture#sections.card_13`
- Citations: none

- A retired minute has an end at or before the watermark.
- Late log: observations for retired minutes.

For each arrival, in order

```text
1. Flag event time < previous watermark
2. Minute end ≤ previous watermark?
   Yes: late-log it. No: add one to the count.
   Keep finalized counts unchanged.
3. Update largest event time; subtract allowed delay
4. Finalize/remove minutes ending ≤ new watermark
```

## Late observations can still belong to open minutes.

- Source lineage: `lecture#sections.card_14`
- Citations: none

Compare event time and minute end separately.

### After B and C: 12:01 count 1, 12:02 count 1. Allowed delay: 30 seconds.

| Arrival | Previous watermark | Handle record | New watermark |
| --- | --- | --- | --- |
| A (event 12:00:40) | 12:01:30 | Behind estimate; minute ends 12:01: log A | 12:01:30 |
| D (event 12:01:20) | 12:01:30 | Behind estimate; minute ends 12:02: count becomes 2 | 12:01:30 |

## A replay lets us test the policy on new observations.

- Source lineage: `lecture#sections.card_15`
- Citations: none

We can trace one delay; a resettable replay tests another.

A simulator runs a simplified model. Here, arrival_time represents processing time, not Colab's clock.

1. Open the student notebook, copy it, run the supplied data cell.
2. Predict J's minute, then play arrivals one at a time.
3. Compare event-time and processing-time minute counts.
4. Run with 30 seconds allowed delay; predict J's fate before releasing it.
5. Reset everything, replay with 90 seconds; compare late logs and retained counts.

- Explain I's fate separately from J's.
- All timestamps are UTC; ties use observation ID order.

**Response mode:** written

## Longer delay changes both completeness and retained state.

- Source lineage: `lecture#sections.card_16`
- Citations: none

Earlier finalization saves state but can exclude delayed observations.

### 30-second replay

- Late log: J, M, O
- Finalized: 12:00 = 1; 12:01 = 2; 12:02 = 2
- Active: 12:03 = 2; 12:04 = 2

### 90-second replay

- Late log: empty
- Finalized: 12:00 = 2; 12:01 = 3
- Active: 12:02 = 3; 12:03 = 2; 12:04 = 2

The last row leaves active minutes in both runs.

## Group membership and finalization answer different questions.

- Source lineage: `lecture#sections.card_17`
- Citations: none

The clock decides membership; the policy decides when updates stop.

Observation R happened at 12:05:50 and arrived at 12:07:10. Previous watermark: 12:06:30.

- Name its event time and processing time.
- Assign its event-time minute, including start and excluding end.
- Minute end ≤ watermark means retired. Count R or late-log it? Explain.
- Why can older observations still arrive after this watermark?

**Response mode:** written
