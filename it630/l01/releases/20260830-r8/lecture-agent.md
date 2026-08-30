# IT 630 L1 - Course frame and the data lifecycle

- Course: IT 630 - Data Science and Scalable Data Systems
- Lecture: 01 / Unit 1, Meeting 1
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `af8731f7fd4b2e8cf2a9cc98e86d0c485c539c141aa633da75682e1f24c2edfa`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## IT 630: Data Science and Scalable Data Systems

- Source lineage: `it630_l01#sections.cover`
- Citations: none

### Unit 1, Meeting 1. Course frame and the data lifecycle.

A reliable five-stage framework for defining data science projects up front, keeping the work on track, and connecting results to useful decisions.

## Data science joins collection, management, and analysis into one job.

- Source lineage: `it630_l01#sections.what_the_field_is`
- Citations: `openstax_pds_1_1`, `turing_way_project_design`

- **data**: Evidence or observations that can be analyzed to produce insight.
- **data science**: The study of how to collect, manage, and analyze many types of data to produce meaningful information.

**Citations:** `openstax_pds_1_1`

- Historically, domain experts collected data, engineers managed it, and statisticians analyzed it.
- Today, data scientists combine expertise across all three disciplines.

**Citations:** `openstax_pds_1_1`

## A problem statement fixes four things before any data is touched.

- Source lineage: `it630_l01#sections.four_elements`
- Citations: `openstax_pds_1_1`, `turing_way_project_design`

- The question or problem being addressed.
- Who will use the result.
- What decision the result informs.
- The objectives and the scope of the project.

**Citations:** `openstax_pds_1_1`, `turing_way_project_design`

- **problem statement**: A precise statement that establishes the project's goal, objectives, and scope.
- **scope**: The project's goals, possible outcomes, required resources, people, and constraints.

**Citations:** `openstax_pds_1_1`, `turing_way_project_design`

## The audience and the decision are fixed at the start, not chosen at the end.

- Source lineage: `it630_l01#sections.users_and_decision`
- Citations: `turing_way_stakeholders`, `turing_way_project_design`, `openstax_pds_1_1`

- **stakeholders**: The people and organisations involved in a collaboration, and the individuals and communities that are impacted by or may impact a project.

**Citations:** `turing_way_stakeholders`

- Identify collaborators, users, and the target audience while defining scope.
- Include people affected by the project or able to affect it as stakeholders.
- Name the decision the result should inform.
- Set objectives, possible outcomes, resources, and constraints before analysis begins.

**Citations:** `turing_way_project_design`, `turing_way_stakeholders`, `openstax_pds_1_1`

## Do not confuse a problem definition with a dataset or a tool.

- Source lineage: `it630_l01#sections.not_yet_a_definition`
- Citations: `openstax_pds_1_1`, `turing_way_project_design`, `turing_way_stakeholders`

- Problem definition: states the question, audience, decision, objectives, and scope.
- Dataset: supplies observations that may help answer the question.
- Tool: supplies a capability for collecting, preparing, analyzing, or reporting data.

**Citations:** `openstax_pds_1_1`, `turing_way_project_design`, `turing_way_stakeholders`

## The cycle runs from problem definition to reporting.

- Source lineage: `it630_l01#sections.five_stages`
- Citations: `openstax_pds_1_1`, `course_week1_review`, `course_inventory_u1`

### Diagram explanation

The five stages of the cycle in order: problem definition, data collection, data preparation, data analysis, and data reporting. Each stage takes the previous stage's output as its input.

- Problem definition leads to Data collection
- Data collection leads to Data preparation
- Data preparation leads to Data analysis
- Data analysis leads to Data reporting

**Citations:** `openstax_pds_1_1`

- **problem definition**: Establishes clear objectives for the goal and scope of the project.
- **data collection**: Systematically gathers information about the variables of interest.
- **data preparation**: Converts collected data into a form suitable for analysis.
- **data analysis**: Examines prepared data to discover meaningful insights.
- **data reporting**: Presents the results so the information learned from analysis is communicated clearly.

**Citations:** `openstax_pds_1_1`

## Data management runs underneath the cycle rather than being a sixth stage.

- Source lineage: `it630_l01#sections.data_management`
- Citations: `openstax_pds_1_1`

- **data management**: The storing and managing of large volumes of data from various sources in a central location, so it can be efficiently retrieved and analysed.

**Citations:** `openstax_pds_1_1`

- Centralized data management supports business intelligence and decision making.
- Cloud warehouses such as Amazon Redshift and Google BigQuery store and process data remotely.

**Citations:** `openstax_pds_1_1`

## About half of the process goes to collection and cleaning.

- Source lineage: `it630_l01#sections.effort_and_address`
- Citations: `openstax_pds_1_1`, `course_inventory_u1`

### Reported distribution of effort across the process

- Data scientists spend about half of the entire process on data collection and cleaning. (Anaconda, 2020, reported in OpenStax Principles of Data Science · reported for 2020) [`openstax_pds_1_1`]
- Data analysis and communication take about a quarter to a third of the time each. (Anaconda, 2020, reported in OpenStax Principles of Data Science · reported for 2020) [`openstax_pds_1_1`]

**Attributed to:** Anaconda, 2020, reported in OpenStax Principles of Data Science

**Constraints:** no_derived_value

**Citations:** `openstax_pds_1_1`

- Wrong variables gathered: collection failure.
- Data not made analyzable: preparation failure.
- Breaking the process into stages helps locate where an analysis went wrong.

**Citations:** `course_inventory_u1`, `openstax_pds_1_1`

## A storm forecast ran the cycle from a stated question to prepared data.

- Source lineage: `it630_l01#sections.walmart_case`
- Citations: `openstax_pds_1_2`, `openstax_pds_1_1`, `turing_way_project_design`

- Problem definition: a week before Hurricane Frances, staff were asked to forecast demand from Hurricane Charley's sales data. (OpenStax, Principles of Data Science, section 1.2) [`openstax_pds_1_2`]
- Data collection: multiple petabytes of unstructured data every hour, from millions of customers. (OpenStax, Principles of Data Science, section 1.2) [`openstax_pds_1_2`]
- Preparation and analysis: the prior storm's sales were compared against normal demand. (OpenStax, Principles of Data Science, section 1.2) [`openstax_pds_1_2`]

**Attributed to:** OpenStax, Principles of Data Science, section 1.2

**Constraints:** preserve_exact_values

**Citations:** `openstax_pds_1_2`

## The analysis ended in a shipping decision, not a report.

- Source lineage: `it630_l01#sections.walmart_result`
- Citations: `openstax_pds_1_2`, `openstax_pds_1_1`, `turing_way_project_design`

- **About 7x** — Result: strawberry Pop-Tart sales increased about sevenfold. (OpenStax, Principles of Data Science, section 1.2) [`openstax_pds_1_2`]
- Reporting and decision: extra Pop-Tarts shipped to stores in the storm's path, and the analysis also set checkout staffing and product placement. (OpenStax, Principles of Data Science, section 1.2) [`openstax_pds_1_2`]

**Attributed to:** OpenStax, Principles of Data Science, section 1.2

**Constraints:** preserve_exact_values

**Citations:** `openstax_pds_1_2`

## The stages are worked repeatedly, and later work can revise the question.

- Source lineage: `it630_l01#sections.iteration_and_variance`
- Citations: `r4ds_intro`, `openstax_pds_1_1`, `course_week1_review`, `turing_way_project_design`

- The stage order is a map of goals, not a one-way schedule.
- Transforming, visualizing, and modeling repeat because each reveals different information.
- New findings can send the work back to revise the problem statement.

**Citations:** `r4ds_intro`

## Two textbooks cut the same cycle differently and neither is official.

- Source lineage: `it630_l01#sections.two_published_models`
- Citations: `openstax_pds_1_1`, `r4ds_intro`, `course_week1_review`

### OpenStax, Principles of Data Science (OpenStax, Principles of Data Science, section 1.1)

- problem definition
- data collection
- data preparation
- data analysis
- data reporting

### R for Data Science, 2nd edition (Wickham, Cetinkaya-Rundel and Grolemund, R for Data Science 2e, chapter 1)

- import
- tidy
- transform
- visualize
- model
- communicate

Two textbooks, two cuts, and neither is official. What they share is the shape: a question, data, an answer, and a stakeholder who uses the result.

**Citations:** `openstax_pds_1_1`, `r4ds_intro`, `course_week1_review`

## A problem statement and an iterative lifecycle keep data science work oriented to decisions.

- Source lineage: `it630_l01#sections.outcome_recap`
- Citations: `openstax_pds_1_1`, `openstax_pds_1_2`, `turing_way_project_design`, `turing_way_stakeholders`, `r4ds_intro`, `course_week1_review`

- Data science investigates how to collect, manage, and analyse data of all types in order to retrieve meaningful information.
- A problem statement fixes the question or problem addressed, who will use the result, what decision it informs, and the objectives and scope. A dataset or a tool supplies none of these.
- The cycle is problem definition, collection, preparation, analysis, reporting, each with its own goal, and a failure can be located to a stage.
- The stages are cycled rather than marched through once, and later work can raise new questions that revise earlier decisions.
- Published lifecycle models differ in their stage labels but share a question-data-answer-communication shape.

**Citations:** `openstax_pds_1_1`, `openstax_pds_1_2`, `turing_way_project_design`, `turing_way_stakeholders`, `r4ds_intro`, `course_week1_review`
