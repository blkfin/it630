# From a delivered file to a trustworthy report

A completed job tells us the program ran. To trust its result, we must also test what each step produces for the next query. Campus dining gives us a small case: charged purchases arrive in a nightly CSV, and the dashboard reports dollars by hall and local date.

## Why prepare source records?

The source system records individual dining purchases. The report needs a total for each hall and date. Copying a vendor file does not remove repeated deliveries, parse its timestamps, or calculate that total. A data pipeline applies repeatable steps to prepare source records for analysis. Extract reads records from their source. Transform changes their representation or combines them for the analytical task. Load stores them in the named destination.

For the lecture specimen, transformations keep one purchase per ID, derive the local date from each timestamp, and sum accepted dollar amounts. The source has individual purchases; the final report has one hall/day row. ETL and ELT both need these transformations.

## Where ETL and ELT perform that work

Compare both paths using the same destination, the Dining Analytics database. With **ETL (extract, transform, load)**, an external processing job reads the vendor CSV, prepares accepted purchases and totals, then loads those prepared tables into Dining Analytics. With **ELT (extract, load, transform)**, raw rows first enter Dining Analytics; queries there prepare accepted purchases and the report table. The dashboard reads the prepared report in either arrangement. Loading raw data does not make it ready for the dashboard.

The difference assigns processing to different systems. ETL must provide transformation compute outside the destination; ELT requires a destination that can store the incoming representation and run the transformations. Saving an original CSV separately fits both, so that alone does not identify the pattern.

## Why choose one arrangement?

If the destination expects predetermined reporting columns and types, ETL can prepare that representation before writing it. If the destination supports raw storage and transformation queries, ELT can use that compute and retain the original detail there for later analytical questions. A report that retains only hall/day totals cannot reconstruct individual purchases. Either arrangement can retain purchase detail if designed to do so.

Consider the target's accepted formats, available processing, retained detail, and the reports you need. Neither ETL nor ELT automatically removes duplicates or fixes units: explicit rules and checks determine correctness. Neither ordering is universally faster or cheaper.

## State the grain and requirements

Grain means what one row represents. Bronze keeps received source records, original values, batch and version. A repeated delivery may therefore appear there. Silver holds accepted purchases unique by purchase_id, with stable names, types and USD units. Invalid inputs must be surfaced. Gold holds one hall and local date, summing accepted dollar purchases. The dashboard reads gold.

The uniqueness check belongs on silver purchase_id. A unique hall/day row in gold cannot establish that its input purchases were unique: aggregation can hide duplicate IDs.

## Make retries preserve the intended state

For fixed input and fixed rules, an idempotent step preserves its intended output state when repeated. Upsert inserts a new purchase ID or replaces the existing ID. Repeating identical purchases therefore does not add new purchases. Recompute gold from the accepted silver purchases. Bronze can retain both delivery records while silver identity and the gold total stay stable. Equal prices alone do not identify duplicate purchases.

A reviewable transformation records input, output, rule and expected check. Saving its versions lets a reviewer compare the previous rule with the proposed rule, for example append versus upsert, and inspect the intended replay result. A file in version control without an explicit rule and check is not enough.

## Trace the first reader affected by a change

Schema means field names and types. An added payment_method field is harmless to our silver reader because it explicitly selects the original fields and permits unused ones. Other readers could reject the addition. Renaming purchased_at to timestamp breaks the reader that still requests purchased_at; downstream reports receive no update and might remain stale.

A semantic change alters meaning even when names and types fit. Changing amount from dollars to cents can pass a structural check while producing a wrong dollar total. Use the declared vendor version to choose an adapter, rather than guessing from the size of a number. Schema migration here means identify consumers, adapt deliberately, verify. Map the external name to silver's stable name or divide raw cents by 100 to preserve USD units.

On every retry the cents adapter must start from bronze's raw values, then upsert the dollar results. Dividing already-converted silver values again would change the result. Check names, types, purchase identity, units and the known report total separately.

## Read the optional query

The activity's SQL groups silver_purchases by hall_id and local_date and sums amount_usd. local_date is derived from purchased_at using the timestamp's local date. amount_usd is silver's accepted USD amount. You can reason through the pipeline without executing SQL.

## References and example scope

The campus dining records, changes, adapter and calculations are fictional teaching examples, authored for this lecture. They assert no particular database engine behavior.

- AWS, ETL versus ELT, “How do the ELT and ETL processes differ”: https://aws.amazon.com/compare/the-difference-between-etl-and-elt/
- Databricks, “What is Medallion Architecture,” bronze, silver and gold layer roles: https://www.databricks.com/blog/what-is-medallion-architecture
- Apache Airflow, Best Practices, “Creating a task,” fixed-input and repeated-run guidance: https://airflow.apache.org/docs/apache-airflow/stable/best-practices.html

Use the separate student inspection sheet to test your reasoning.
