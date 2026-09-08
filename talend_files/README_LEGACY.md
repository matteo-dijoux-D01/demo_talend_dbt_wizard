# Legacy Talend project — retail-etl

Nightly retail ETL currently implemented in Talend Open Studio. The scheduled
entry point is `ETL_RunNightlyBatch`, which calls the jobs below in sequence.

| Job | Purpose |
|---|---|
| ETL_LoadCustomers | Load + dedupe the customer master feed into `dim_customer` |
| ETL_LoadProducts | Load the product master feed into `dim_product` |
| ETL_LoadFxRates | Load daily FX rates, falling back to the last known rate |
| ETL_ImportRawOrders | Import + validate raw orders, split valid/reject, write facts directly |
| RPT_BuildDailySales | Aggregate order lines into a daily sales report |
| RPT_BuildCustomerLtv | Compute FX-normalized customer lifetime value |
| RPT_BuildInventorySnapshot | Roll up stock movements into an on-hand snapshot |
| INV_RunReorder | Flag SKUs below threshold and compute reorder quantities |
| ETL_RunNightlyBatch | Orchestrates all of the above in sequence |

No staging/intermediate layer exists today — each job reads raw feeds or
warehouse tables directly and writes straight to the report/fact tables it
owns. Business logic (validation rules, FX fallback, reorder thresholds)
lives inside `tMap` expressions rather than in version-controlled,
testable SQL.

> Note: these `.item` files are simplified, representative recreations of a
> Talend Studio job export — job metadata, components, key parameters, and
> wiring — built for this demo. They capture the same business logic as a
> real export but aren't guaranteed to open pixel-perfect in Talend Studio
> itself.
