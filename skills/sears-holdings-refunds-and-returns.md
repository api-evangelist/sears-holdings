---
name: sears-holdings-refunds-and-returns
description: Issue a full or partial refund, record a return, or cancel a line on Sears Marketplace — and confirm it actually processed, because a rejected refund still answers 200.
api: Sears Marketplace Seller API
base_url: https://seller.marketplace.sears.com/SellerPortal/api
grounding: xsd/ + https://marketplace.sears.com/docs/api-guide/ (no OpenAPI exists; operations are the provider's own published URLs)
operations:
  - PUT /oms/order/adjustment/v5?sellerId={sellerId}
  - GET /oms/orderadjustment/v3?sellerId={sellerId}&ponumber={}&podate={}
  - PUT /oms/order/cancel/v1?sellerId={sellerId}
  - GET /oms/cancellationrequest/v1?sellerId={sellerId}&status={}&fromdate={}&todate={}
  - GET /oms/returnsrequest/v1?sellerId={sellerId}
  - PUT /oms/returnsresponse/v1?sellerId={sellerId}
  - GET /reports/v1/processing-report/{document-id}?sellerId={sellerId}
schemas:
  - xsd/sears-holdings-rest-oms-import-v5-order-adjustment.xsd
  - xsd/sears-holdings-rest-oms-import-v1-order-cancel.xsd
  - xsd/sears-holdings-rest-oms-export-v1-order-cancellation-request-to-seller.xsd
  - xsd/sears-holdings-rest-oms-export-v1-order-return-request-to-seller.xsd
  - xsd/sears-holdings-rest-oms-import-v1-order-returns-response-from-seller.xsd
---

# Refunds, returns and cancellations on Sears Marketplace

**This skill moves money and it is not reversible.** Read the guard rails before the mechanics.

## Guard rails

1. **A refund cannot be undone.** There is no un-refund operation.
2. **An order already marked returned rejects every further adjustment** — and it rejects it
   *downstream*. Seller Portal accepts your file with a 200 and the order management system throws
   it away. If you do not poll the status lookup you will believe you refunded a customer twice.
3. **`refund` and `return` are different sections and different outcomes.** `<refund>` means the
   customer keeps the item and gets money back. `<return>` means the item came back to you.
   Choosing the wrong one misstates inventory and settlement.
4. **Shipping refunds are order-level, never line-level.** Exactly one `<shipping-adjustment>` per
   order, and from v5 onward its reason must be the literal string `Shipping Charge` or the
   associated shipping tax will not be refunded with it.
5. **There is no idempotency key.** Do not blind-retry a timed-out adjustment.

## Cancel a line before it ships

```
PUT /oms/order/cancel/v1?sellerId={sellerId}
```

Full-line cancellation only — there is no quantity-level cancel. Pick the reason honestly from the
published set:

`DISCONTINUED-ITEM`, `OUT-OF-STOCK`, `CUSTOMER-CHANGED-THEIR-MIND`, `WRONG-ITEM-WAS-ORDERED`,
`LEAD-TIME-IS-TOO-LONG`, `ITEM-FOUND-SOMEWHERE-ELSE`, `INVALID-METHOD-SHIPMENT`,
`INVALID-ITEM-SKU`, `OTHER`.

`OTHER` is not a neutral default: it marks the cancellation as **seller-initiated**, which is a
different commercial outcome for your account. Map deliberately.

## Honour a customer-initiated cancellation

```
GET /oms/cancellationrequest/v1?sellerId={sellerId}&status={status}&fromdate={}&todate={}
```

These are cancellations customers requested through the online order centre. Check this before
shipping anything that carried a `customer-cancellation-warning` on the purchase order line.

## Issue a refund or record a return

```
PUT /oms/order/adjustment/v5?sellerId={sellerId}
```

Validates against `order-adjustment.xsd` v5. Multiple adjustments may travel in one file. Partial
refunds are iterative and may be issued at line level for item price, and at order level for
shipping. If an approved restocking fee applies, deduct it from `<return-amount>` yourself.

## Confirm it landed — the step people skip

```
GET /oms/orderadjustment/v3?sellerId={sellerId}&ponumber={po-number}&podate={po-date}
```

Read `<return-status>`: `Completed` or `Failed`. A `Failed` entry alongside a `Completed` one for
the same line is normal and shows a retry history. The provider names "a return or refund having
already been issued" as a leading cause of failure.

Also poll the processing report for the document-id, exactly as with any other PUT:

```
GET /reports/v1/processing-report/{document-id}?sellerId={sellerId}
```

## Answer a customer return request

```
GET /oms/returnsrequest/v1?sellerId={sellerId}
PUT /oms/returnsresponse/v1?sellerId={sellerId}
```

## Reconcile

Settlement lands in the remittance report, `GET /oms/remittance/v9?sellerId={sellerId}`. From v8 it
carries the shipping commission and from v9 the lease-order field, so refunded shipping commission
is visible there rather than having to be inferred.
