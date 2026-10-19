# Can you trust H2's total?

Ten-minute paired inspection. No code execution or setup required. One partner traces the records; the other challenges the evidence. Switch halfway.

Vendor v1: USD dollars; one completed purchase per stable purchase_id.

```csv
purchase_id,hall_id,purchased_at,amount
Q201,H2,2026-10-19T12:00:00-04:00,6.00
Q202,H2,2026-10-19T12:05:00-04:00,8.00
Q203,H2,2026-10-19T12:10:00-04:00,3.00
```

CSV → bronze received records → silver accepted purchases → gold hall/day → dashboard

## Reader assumptions

- Bronze preserves source values, source version and delivery identity. Repeated deliveries are allowed.
- Silver selects purchase_id, hall_id, purchased_at and amount by name, permits unused fields, parses the stated types, and initially assumes USD dollars without checking the declared units.
- For the safe path, silver upserts by purchase_id: insert new IDs, replace existing ones. Input and rules are fixed; no conflicting corrections occur.
- Gold reads only stable silver fields and recomputes the affected hall/day. The date is the timestamp's local date. The dashboard reads gold.
- Compare naive append as a separate unsafe path, never as the safe reader's behavior.
- Each change starts independently from the original v1 specimen. The cents retry repeats only the explicitly adapted cents specimen.

## Inspect and explain

For every change, name the boundary, consequence and evidence. Spend two minutes on grain, two on replay, four on independent changes and two on repaired retry.

1. Complete “One row means…” for bronze, silver and gold. Where would you check uniqueness of purchase_id?
2. Sum the original purchases. Deliver the identical batch twice: what total follows from naive append? What purchase count and total follow from upsert plus gold recompute? Show arithmetic.
3. Inspect three independent change cards: (A) add payment_method, with values card/card/cash; (B) rename purchased_at to timestamp, keeping its values; (C) declare cents and replace amounts with 600/800/300, keeping the field name and numeric type. For each, name (1) the first boundary that inspects the change, (2) the downstream consequence, including whether it errors, stays silent or remains correct, and (3) one piece of evidence or repair. Show the expected cents total under the unchanged reader.

| Case | Boundary | Consequence | Evidence or repair |
|---|---|---|---|
| A | | | |
| B | | | |
| C | | | |

Cents total under unchanged reader: __________________

4. Now use the declared cents-version adapter: read raw bronze amounts, divide by 100, upsert by purchase_id, recompute gold. Trace first run and retry. State silver's values, purchase count and H2 total. Which input must division read to avoid conversion twice?

## Optional SQL support

You can answer using the pipeline line above.

```sql
SELECT hall_id, local_date, SUM(amount_usd) AS charged_usd
FROM silver_purchases
GROUP BY hall_id, local_date;
```

local_date comes from purchased_at; amount_usd is silver's accepted dollar amount.
