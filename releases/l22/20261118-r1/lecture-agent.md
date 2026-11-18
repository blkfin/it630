# Pick your ML algorithm

- Course: IT 630
- Lecture: 12 / November 18: Pick your ML algorithm
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `3a6e2494e8fd6bf3c8657cdde79da93f6c05d60dc87fc21a5784a304bbd8494b`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## Which ML family fits the job?

- Source lineage: `lecture#sections.opening`
- Citations: `islp`, `seed`

Choose a family for the data and the job.

1. Name the input and the required output.
2. Match the data characteristics to plausible families.
3. Defend a first choice and a reasonable alternative.

**Response mode:** written

Takeaway: Machine learning learns patterns from examples to handle new cases.

## Classify tumour images as malignant or benign

- Source lineage: `lecture#sections.images_task`
- Citations: `islp`, `seed`

### Hypothetical hospital research task

| Characteristic | The task |
| --- | --- |
| Job | Flag tumour images for specialist review |
| Data | Thousands of tissue images; expert-confirmed benign or malignant labels |
| New input and output | A new image; a predicted benign or malignant category |
| Useful signal | Cell shapes, textures and their arrangement |
| Why learn? | Manual review is slow; a simple colour rule misses varied appearances |

Takeaway: Classification predicts a category; here, the input is an image.

## Which families can use the image evidence?

- Source lineage: `lecture#sections.images_families`
- Citations: `islp`, `seed`

### Image candidates and their tradeoffs

| Family | Why it fits | What it needs or gives up |
| --- | --- | --- |
| Neural network: CNN | Learns shapes and textures across image regions | Many relevant labelled images; less transparent decisions |
| Linear classifier: logistic regression | Uses measured shape and texture columns | Experts must extract useful measurements first |
| Nearest neighbours | Compares with similar labelled images | Needs a meaningful image representation and similarity |
| Single tree on raw pixels | Can make category decisions | Weak starting point: ignores spatial structure unless engineered |

Takeaway: The same images can support different approaches, with different preparation.

## Defend a first model for the image backlog

- Source lineage: `lecture#sections.images_choice`
- Citations: `islp`, `seed`

Recommend a family and a runner-up for the hospital team.

1. Write your choices and one data characteristic behind each.
2. Compare with a partner; name a disadvantage of your first choice.
3. Discuss what evidence you need before trusting it on new patients.

**Response mode:** written

Takeaway: A plausible image model still needs evidence on new patients.

## Predict delivery time when an order arrives

- Source lineage: `lecture#sections.delivery_task`
- Citations: `islp`, `seed`

### Hypothetical meal-delivery task

| Characteristic | The task |
| --- | --- |
| Job | Give a customer an estimated arrival time |
| Data | Tens of thousands of completed orders, with actual delivery times |
| Inputs available now | Distance, restaurant, rain, rush hour and current queue |
| Useful signal | Rain may matter more during rush hour |
| Why learn? | Manual estimates do not scale; one fixed rule misses changing conditions |

Takeaway: Regression predicts a number; this job needs delivery minutes.

## Which families fit the delivery table?

- Source lineage: `lecture#sections.delivery_families`
- Citations: `islp`, `seed`

### Delivery candidates and their tradeoffs

| Family | Why it fits | What it needs or gives up |
| --- | --- | --- |
| Linear regression | A readable starting point using named inputs | Combined effects need deliberate input design |
| Decision tree | Can treat rain differently during rush hour | One fitted tree can change with the sample |
| Random forest | Combines varied trees for flexible predictions | Harder to explain than one tree |
| Nearest neighbours | Uses outcomes of similar completed orders | Needs enough comparable orders and sensible similarity |

Takeaway: Choose between simple explanations and flexible responses to conditions.

## Choose for the delivery service and defend it

- Source lineage: `lecture#sections.delivery_choice`
- Citations: `islp`, `seed`

Recommend a family and a runner-up for delivery estimates.

1. Write the characteristic that favours your first choice.
2. With a partner, compare readability with handling changing conditions.
3. Name a future-order check that could overturn your recommendation.

**Response mode:** written

Takeaway: The service needs a useful estimate that works on future orders.

## Flag suspicious card payments for human review

- Source lineage: `lecture#sections.payments_task`
- Citations: `islp`, `seed`

### Hypothetical card-payment screening task

| Characteristic | The task |
| --- | --- |
| Job | Flag likely fraudulent payments for a limited review team |
| Data | Many historical payments; confirmed fraud labels arrive later |
| Inputs available now | Amount, merchant, device and recent account activity |
| Useful signal | Suspicious combinations; most payments are legitimate |
| Why learn? | Reviewing every payment is impossible; fixed limits miss changing patterns |

Takeaway: The input stays tabular; the output becomes a category again.

## Which families fit the payment review task?

- Source lineage: `lecture#sections.payments_families`
- Citations: `islp`, `seed`

### Payment candidates and their tradeoffs

| Family | Why it fits | What it needs or gives up |
| --- | --- | --- |
| Linear classifier: logistic regression | A simple, readable first comparison | Complex combinations need useful input design |
| Random forest | Handles conditional patterns across named columns | Less readable; test false alarms carefully |
| Boosted trees | Builds a flexible classifier for tabular patterns | Needs tuning and careful checks on later payments |
| Image-oriented CNN | Useful when the evidence is an image | Poor default here: these columns have no natural image layout |

Takeaway: The useful comparison is among table classifiers, not image architectures.

## Defend a choice under limited review capacity

- Source lineage: `lecture#sections.payments_choice`
- Citations: `islp`, `seed`

Recommend a family and a runner-up for payment screening.

1. Write why the table characteristics support each candidate.
2. Compare with a partner: what does your first choice sacrifice?
3. Explain how you would check both missed fraud and false alarms.

**Response mode:** written

Takeaway: Detecting fraud is useful only if the review team can act.

## What would make you change your choice?

- Source lineage: `lecture#sections.transfer`
- Citations: `islp`, `seed`

### Revisit the same tasks when the evidence changes

| Scenario | A new fact | Your revised choice and reason |
| --- | --- | --- |
| Tumour images | Useful expert measurements already exist as columns | [blank] |
| Delivery times | Dispatchers must explain each estimate in simple terms | [blank] |
| Payment screening | Labels exist for only a small, unrepresentative subset | [blank] |

Takeaway: Choose a family for the data and the job.
