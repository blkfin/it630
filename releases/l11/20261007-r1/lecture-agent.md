# IT 630: star schemas

- Course: IT 630
- Lecture: 11 / Star schemas
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `5d31084ab3c6ef943c08bc7d3441fc04d72eedb79a8c72d3d3e9e6e7a017b9b7`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## How can we split sales data and trust the totals?

- Source lineage: `lecture#sections.cover`
- Citations: `summer`

You will identify row meaning, read a star, and check that a split preserves sales.

## Shared sales data repeats the same descriptions

- Source lineage: `lecture#sections.shared_orders`
- Citations: `summer`

- A retailer shares sales data with several analysts.
- Each row records one item line in an order.
- They ask for revenue by category, month, and region.

### Order 100

| Line | Product | Category | Amount |
| --- | --- | --- | --- |
| 100/1 | Mug | Home | $10 |
| 100/2 | Mug | Home | $20 |
| 100/3 | Book | Books | $30 |

## Which total actually measures revenue for this order?

- Source lineage: `lecture#sections.grain_prediction`
- Citations: `kimball`, `summer`

Grain: what one row represents. Here, one row is one order line.

### Order 100 also stores its order total on every line: choose a column to sum

| Line | line_amount | order_total |
| --- | --- | --- |
| 100/1 | $10 | $60 |
| 100/2 | $20 | $60 |
| 100/3 | $30 | $60 |

## Summing order totals counts this order three times

- Source lineage: `lecture#sections.grain_answer`
- Citations: `kimball`, `summer`

Order 100 only

```sql
SUM(line_amount) = 10 + 20 + 30 = 60
SUM(order_total) = 60 + 60 + 60 = 180
```

Both queries run. Line amounts match line grain; the order total belongs to the whole order.

## Facts measure sales while dimensions describe their context

- Source lineage: `lecture#sections.split_meanings`
- Citations: `kimball`, `microsoft`

We know line grain. Repeated descriptions still need one maintained home.

### Split by meaning, not table size, then connect with keys

| Table role | Meaning | Order 100 example |
| --- | --- | --- |
| Fact | Measurements at the declared grain | line_amount: $10 |
| Dimension | Descriptions of who, what, where, or when | Mug: Home category |

## Which columns measure sales and which describe products?

- Source lineage: `lecture#sections.sort_try`
- Citations: `summer`

### One sale line and its product

| Column | Example | Measurement, key, or description? |
| --- | --- | --- |
| line_amount | $10 | [blank] |
| quantity | 1 | [blank] |
| product_key | 7 | [blank] |
| product_name | Mug | [blank] |
| category | Home | [blank] |

## A numeric key identifies something rather than measuring it

- Source lineage: `lecture#sections.sort_answer`
- Citations: `kimball`, `microsoft`

### Roles follow meaning

| Column | Role |
| --- | --- |
| line_amount | Fact measurement |
| quantity | Fact measurement |
| product_key | Key connecting the product |
| product_name | Dimension description |
| category | Dimension description |

## A product key connects each line to one product

- Source lineage: `lecture#sections.expected_keys`
- Citations: `kimball`, `microsoft`

Primary key: identifies one dimension row. Foreign key: refers to that row.

### Expected join: fact_order_line.product_key = dim_product.product_key

| Table | Visible row |
| --- | --- |
| fact_order_line | line 100/1 | product_key 7 | amount $10 |
| fact_order_line | line 100/2 | product_key 7 | amount $20 |
| dim_product | product_key 7 | Mug | Home |

## A star connects one fact table to its dimensions

- Source lineage: `lecture#sections.read_star`
- Citations: `kimball`, `microsoft`

Star schema: a fact table linked by keys to descriptive dimension tables.

### Center: fact_order_line, one row per order line

| fact_order_line key | Dimension and expected unique key | Context |
| --- | --- | --- |
| date_key | dim_date.date_key | month |
| product_key | dim_product.product_key | category |
| customer_key | dim_customer.customer_key | region |

## One fact table answers several questions about revenue

- Source lineage: `lecture#sections.one_fact_many_questions`
- Citations: `summer`

### Keep SUM(f.line_amount); change the descriptive grouping

| Revenue by | Join this dimension | Group by |
| --- | --- | --- |
| Month | dim_date | month |
| Category | dim_product | category |
| Region | dim_customer | region |

## Three checks test whether the split preserved the sales

- Source lineage: `lecture#sections.prove_open`
- Citations: `summer`

We can read the star. Its shape alone cannot prove its data is correct.

### Test the data, not just the model

| Check | Retail split |
| --- | --- |
| Rows kept | 480 rows before the split, 480 fact rows |
| Each product stored once | Dimension rows equal distinct product keys |
| No orphan keys | Every fact product key finds a product |

## Equal staging and fact counts check for missing rows

- Source lineage: `lecture#sections.rows_kept`
- Citations: `summer`

Count before and after

```sql
SELECT COUNT(*) FROM staging_order_line; -- 480
SELECT COUNT(*) FROM fact_order_line;    -- 480
```

Staging: the sales table before the split. COUNT(*) counts rows. Equal counts are necessary, but insufficient.

## Distinct keys check the product dimension for duplicates

- Source lineage: `lecture#sections.unique_products`
- Citations: `summer`

Check the lookup table

```sql
SELECT COUNT(*),
       COUNT(DISTINCT product_key)
FROM dim_product;
```

COUNT(DISTINCT product_key) counts different key values. The two counts should match.

## A left join exposes sales with missing products

- Source lineage: `lecture#sections.find_orphans`
- Citations: `summer`

Find missing product matches

```sql
SELECT f.product_key
FROM fact_order_line f
LEFT JOIN dim_product p
  ON f.product_key = p.product_key
WHERE p.product_key IS NULL;
```

LEFT JOIN keeps every sale. IS NULL keeps only sales with no matching product. Expect zero rows.

## A matching join count can hide two errors

- Source lineage: `lecture#sections.cancelled_errors`
- Citations: `summer`

### The duplicated product is referenced by exactly one fact line

| Product join state | Rows |
| --- | --- |
| Fact rows before join | 480 |
| One orphan loses a line | 479 |
| One duplicated product adds a line | 480 |

- Joined to dim_product, each sale should appear once; a duplicated product row matches its sale twice.
- The final count matches. Uniqueness and orphan checks still fail.

## Facts measure, dimensions describe, and checks earn trust

- Source lineage: `lecture#sections.close`
- Citations: `summer`

We know the checks. Together they let us trust a split, not just admire its shape.

### Model shared data when people reuse it for different questions

| Move | Retail answer |
| --- | --- |
| Declare grain | One row per order line |
| Split by meaning | Line measurements; product, customer, date context |
| Connect by keys | Each line expects one match per dimension |
| Check the split | Rows kept, unique dimension keys, no orphans |
