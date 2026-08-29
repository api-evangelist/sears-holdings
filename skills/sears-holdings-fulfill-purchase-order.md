---
name: sears-holdings-fulfill-purchase-order
description: Pull new Sears Marketplace purchase orders, ship them, and close each line with an advance ship notice — including the verification step that the 200 OK does not give you.
api: Sears Marketplace Seller API
base_url: https://seller.marketplace.sears.com/SellerPortal/api
grounding: xsd/ + https://marketplace.sears.com/docs/api-guide/ (no OpenAPI exists; operations are the provider's own published URLs)
operations:
  - GET /oms/purchaseorder/v19?sellerId={sellerId}
  - GET /oms/shipping-carrier/v1?sellerId={sellerId}
  - PUT /oms/asn/v7?sellerId={sellerId}
  - GET /reports/v1/processing-report/{document-id}?sellerId={sellerId}
schemas:
  - xsd/sears-holdings-rest-oms-export-v19-purchase-order.xsd
  - xsd/sears-holdings-rest-oms-export-v1-shipping-carrier.xsd
  - xsd/sears-holdings-rest-oms-import-v7-asn.xsd
  - xsd/sears-holdings-shared-seller-error-report-v1.xsd
---

# Fulfil a Sears Marketplace purchase order

## Before you start

Sign every request. Build the string `<sellerId>:<emailaddress>:<UTC timestamp>`, HMAC-SHA256 it
with your base64 secret key, hex-encode the result, and send:

```
Authorization: HMAC-SHA256 emailaddress=<email>,timestamp=<yyyy-MM-ddTHH:mm:ssZ>,signature=<hex>
```

The timestamp is valid for 30 minutes. Regenerate it; do not cache a signature.

## 1. Pull orders

```
GET /oms/purchaseorder/v19?sellerId={sellerId}
```

Parse against `purchase-order.xsd` v19. **Store `po-number` and `po-date` together.** Every later
lookup for this order — the refund status check especially — requires the composite key. Record
`oms-order-item-id` per line, and `oms-parent-order-item-id` where a line belongs to a bundle.

Watch `customer-cancellation-warning` on a line: the customer has asked to cancel and shipping it
anyway creates a return you will pay for.

## 2. Resolve the carrier and method

```
GET /oms/shipping-carrier/v1?sellerId={sellerId}
```

This is a marketplace-wide reference list. Do not hard-code carrier or method strings — send only
values present in this response, or the ASN will be rejected.

## 3. Send the ASN

```
PUT /oms/asn/v7?sellerId={sellerId}
```

Body validates against `asn.xsd` v7. Multiple packages for a single line item are supported; model
them as separate shipment entries rather than concatenating tracking numbers.

The response is `<api-response><document-id>N</document-id></api-response>`.

## 4. Verify — this step is not optional

```
GET /reports/v1/processing-report/{document-id}?sellerId={sellerId}
```

The 200 you got in step 3 means *the file was accepted*, not *the shipment was recorded*. The
processing report is where the truth is. Read `summary/records-accepted`,
`summary/records-with-errors` and `summary/records-with-warnings`; a partially-accepted feed is
normal, so never infer success from the absence of a total failure. Each `<error>` carries a
`record-id` attribute pointing back at the row of your submission.

If a record errored, fix and resubmit **before the top of the hour** — the provider only emails an
ASN Rejection Notice for lines still broken when the hour turns.

## 5. Know the deadline

An order with a rejected ASN stays on a nightly recap email until it is closed, or until it reaches
**30 days overdue, at which point it is automatically cancelled and you are not paid for it.**

## Failure handling

- HTTP 403 with "rate limited" — you exceeded 500 requests/minute from one IP (15-minute throttle)
  or the 300 ASN files/hour cap (wait 60 minutes from the first call of that hour).
- HTTP 200 with `<error-detail>` — an application error. Read the body; the status line lies.
- A timed-out PUT is **not** safe to retry blindly. There is no idempotency key on this API. Poll
  the processing report for the document-id you already have, or re-pull the order to see whether
  it closed, before resending.

## Test it first

Everything above works against `https://sellersandbox.sears.com` with the same paths. Generate test
orders with the sandbox-only `PUT /sandbox/oms/createpo/v1?sellerId={sellerId}` — there is no UI
way to create one.
