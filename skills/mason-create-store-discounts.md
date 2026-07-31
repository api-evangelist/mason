---
name: Create and clean up store discounts
description: Use Mason's Promotion Engine (Scrooge) API to create tiered/bulk/BxGy discounts for a store and remove them.
api: openapi/mason-discounts-openapi.json
operations:
  - create_bulk_discounts_api_v1_scrooge_discounts_bulk_put
  - create_tiered_discounts_api_v1_scrooge_tiered_discounts_put
  - cleanup_tiered_discounts_api_v1_scrooge_discounts_bulk_delete_put
  - cleanup_tiered_discounts_api_v1_scrooge_tiered_discounts_delete_put
---

# Create and clean up store discounts

Mason's Promotion Engine ("Scrooge") creates discounts scoped to a `store`.
Base host: `https://api.getmason.io`. Create/cleanup use `PUT` (idempotent by
HTTP semantics); Mason returns a `discount_ids_map` mapping your client-supplied
`uid` values to Mason discount ids.

## Steps

1. **Create discounts in bulk.** Call `create_bulk_discounts_api_v1_scrooge_discounts_bulk_put`
   (`PUT /api/v1/scrooge/discounts/bulk`) with a `BulkDiscountPayload`: a `store`
   and a `discount_info[]` list of `TieredDiscountModel`, `BxGyDiscountModel`,
   `TimedDiscountModel` or `CustomBxGyDiscountModel` objects. Supply a unique `uid`
   per discount so you can correlate results via the returned `discount_ids_map`.
2. **Or create only tiered discounts.** Call `create_tiered_discounts_api_v1_scrooge_tiered_discounts_put`
   (`PUT /api/v1/scrooge/tiered-discounts`) with a `TieredDiscountPayload`.
3. **Clean up.** Call `cleanup_tiered_discounts_api_v1_scrooge_discounts_bulk_delete_put`
   (`PUT /api/v1/scrooge/discounts/bulk/delete`) or
   `cleanup_tiered_discounts_api_v1_scrooge_tiered_discounts_delete_put`
   (`PUT /api/v1/scrooge/tiered-discounts/delete`) with a `DeleteTieredDiscountPayload`.

## Rules

- Discounts target products/variants (`buy[].id`, `variants[]`) and/or collections
  (`applies_to.collections`); shape them per the model type.
- `422` is a validation error — inspect `detail[]` (errors/mason-problem-types.yml).
- Persist the returned `discount_ids_map` — you need Mason's discount ids to clean up.
