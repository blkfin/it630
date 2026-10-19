# Purchases, corrections, and pipeline choices

**IT 630 · October 19 · 10 minutes · Work in pairs**<br>
<p>Name(s): <span style="display:inline-block;width:105mm;border-bottom:1px solid #89949e">&nbsp;</span></p>

Use what the lecture taught to reason through new cases. Show arithmetic and explain which records your result counts. Spend about three minutes each on Cases 1 and 2, and four minutes on Case 3.

## Starting records and rules

The accepted purchases for hall H2 on October 19 are:

| purchase_id | amount_usd |
|---|---:|
| Q201 | 6.00 |
| Q202 | 8.00 |
| Q203 | 3.00 |

All purchases belong to the same hall and date. One silver row represents one purchase, identified by **purchase_id**, not by its price. Gold recomputes the hall/day sum from silver. Bronze retains received records, delivery identity and source values; a delivery may repeat an earlier record.

Compare two silver write rules: **append** inserts every received row; **upsert** inserts a new purchase_id or replaces that ID's existing row. The starting state contains the three rows above exactly once. **Cases 1 and 2 each start independently from this state.**

## Case 1 · A delivery partly overlaps (3 minutes)

A later delivery contains:

| purchase_id | amount_usd |
|---|---:|
| Q202 | 8.00 |
| Q204 | 8.00 |

Q202 repeats the original purchase; Q204 is a different, valid purchase.

1. Calculate the starting total. After this delivery, give the silver row count and gold total for **append** and for **upsert**.
2. A teammate proposes dropping repeated amounts instead of matching purchase_id. Explain which legitimate purchase that could remove and why equal prices do not establish identity.
3. If the same later delivery arrives once more, does the upsert result change? Explain.

<div class="answer tall"></div>

<div class="page-break"></div>

## Case 2 · An existing purchase is corrected (3 minutes)

Start again with only Q201, Q202 and Q203 from the starting table. The vendor sends **Q202, 5.00**, explicitly marked as a later correction that replaces Q202's earlier amount of 8.00. Process deliveries in order; no newer correction is present.

1. What silver row count and gold total follow from **append**? What count and total follow from **upsert**? Show the arithmetic.
2. The correction is delivered a second time. Give both totals after this retry.
3. The correction and the original both remain in bronze. Does that require counting both in the report? Explain the different purposes of bronze and silver.

<div class="answer medium"></div>

## Case 3 · Where should transformation run? (4 minutes)

The source is still the dining purchase feed. Both destinations need duplicate removal, timestamp parsing and daily aggregation. For **each** destination, choose ETL or ELT **relative to the named destination**. State where transformation runs and identify the requirement that supports your choice.

**A. Finance reporting database.** Its approved input accepts only final hall/day totals. It cannot store purchase-level raw rows. A separate processing service can prepare those totals before loading them.

**B. Analytics database.** It can store raw purchase rows and run SQL transformations. Analysts need to revise grouping rules later and recompute reports from retained purchase detail.

<div class="answer medium"></div>

A copy of the original CSV is retained outside either destination. **Does that fact alone tell you whether the pipeline is ETL or ELT? Why?**

<div class="answer short"></div>
