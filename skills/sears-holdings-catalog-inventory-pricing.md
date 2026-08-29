---
name: sears-holdings-catalog-inventory-pricing
description: Create Sears Marketplace items in the right order, then keep inventory, price and lead time in sync — avoiding the single most common integration failure on this API.
api: Sears Marketplace Seller API
base_url: https://seller.marketplace.sears.com/SellerPortal/api
grounding: xsd/ + https://marketplace.sears.com/docs/api-guide/ (no OpenAPI exists; operations are the provider's own published URLs)
operations:
  - GET /itemclasses/v2?sellerId={sellerId}
  - GET /attributes/v4/?itemClassId={itemClassId}
  - PUT /catalog/fbm/v25?sellerId={sellerId}
  - PUT /catalog/skinny-fbm/v1?sellerId={sellerId}
  - PUT /inventory/fbm/v7?sellerId={sellerId}
  - PUT /inventory/1x1/fbm/v1?sellerId={sellerId}
  - GET /inventory/v5?sellerId={sellerId}
  - PUT /pricing/fbm/v6?sellerId={sellerId}
  - PUT /sopt/import/v3?sellerId={sellerId}
  - GET /reports/v1/processing-report/{document-id}?sellerId={sellerId}
  - GET /reports/contentMatch/v1?sellerId={sellerId}
  - GET /reports/removedItems/v1?sellerId={sellerId}
schemas:
  - xsd/sears-holdings-rest-itemclass-export-v2-itemclass-library.xsd
  - xsd/sears-holdings-rest-attribute-export-v4-attribute-library.xsd
  - xsd/sears-holdings-rest-catalog-import-v25-lmp-item.xsd
  - xsd/sears-holdings-rest-contentless-offers-import-v1-skinny-fbm.xsd
  - xsd/sears-holdings-rest-inventory-import-v7-inventory.xsd
  - xsd/sears-holdings-rest-inventory-export-v5-item-inventory-public.xsd
  - xsd/sears-holdings-rest-pricing-import-v6-pricing.xsd
  - xsd/sears-holdings-rest-sopt-import-v3-item-sopt-details.xsd
---

# Build and maintain a Sears Marketplace catalog

## The ordering rule that causes most failures

**Nothing may reference an item until that item has finished processing.** The provider names this
as the most common error on the API:

> `Cannot update 'a pricing' of the item with id 'ABC' because it doesn't exist`

Item creation is asynchronous and can take **up to 15 minutes**. The correct sequence is:

1. PUT the item feed → capture the `document-id`
2. Poll `GET /reports/v1/processing-report/{document-id}?sellerId={sellerId}` until the item is
   confirmed accepted
3. *Only then* send inventory, pricing and order prep time for that item

Firing all four feeds in parallel is the classic mistake, and it fails silently — every PUT returns
200 and the dependent records are rejected inside the processing report.

## 1. Resolve the taxonomy first

```
GET /itemclasses/v2?sellerId={sellerId}
GET /attributes/v4/?itemClassId={itemClassId}
```

The item class decides which attributes are legal *and* which commission rate applies (8% for
gaming consoles, 9% consumables, 12% automotive, 15% most, 17% seasonal, 20% jewelry). Send the
**numeric class id**, never the human-readable path — `Item Class is required` means you sent the
path. Reject any class whose name begins `zz_Do_Not_Use`.

## 2. Create items

```
PUT /catalog/fbm/v25?sellerId={sellerId}
```

Validates against `lmp-item.xsd` v25. Rules the schema and error reference enforce:

- `upc` is numeric only, 12 or 13 digits, and must pass GS1 check-digit validation. A UPC already
  used by another product anywhere on the marketplace is rejected.
- `Manufacturer Model Number` is alphanumeric, max 40 characters, no spaces.
- Long description ≤ 5000 characters; short description ≤ 2400.
- HTML is allowed in description fields only, never in the title.
- Prices must match `#####.##`. Ground shipping must fall between 0.01 and 500.00.
- Image URLs must be well-formed, must resolve, and the file extension must match the real format —
  a BMP named `.jpg` fails with "Dude server doesn't return proper asset code".
- Every variant in a variation group must fill the identical attribute set, and no two variants may
  share the same attribute combination.
- Fill the product-regulatory booleans honestly: `is-restricted`, `perishable`,
  `requires-refrigeration`, `requires-freezing`, `contains-alcohol`, `contains-tobacco`,
  `california-emissions`, `energy-star-compliant` and the choke-hazard set.

If the product already exists in the Sears catalog, use the contentless path instead — 
`PUT /catalog/skinny-fbm/v1` — and read `GET /reports/contentMatch/v1?sellerId={sellerId}` to see
how your offer matched.

## 3. Inventory

```
PUT /inventory/fbm/v7?sellerId={sellerId}      # bulk
PUT /inventory/1x1/fbm/v1?sellerId={sellerId}  # single item
GET /inventory/v5?sellerId={sellerId}          # read back
```

Budget: 360 inventory files per hour. Use the bulk feed for routine sync and reserve the 1x1
endpoint for urgent single-SKU corrections, or you will burn the hourly cap.

## 4. Pricing and lead time

```
PUT /pricing/fbm/v6?sellerId={sellerId}    # 120 files/hour
PUT /sopt/import/v3?sellerId={sellerId}    # 100 files/hour
```

For order prep time: warehouse locations must express units in **days**; pickup locations may use
days, hours or minutes.

## 5. Retire an item — and know which retirement is reversible

Both are sections of the same catalog feed, and the difference matters:

- `items-to-inactivate` — pulled from the sites, **reversible at any time**. Use this whenever you
  might sell the item again.
- `items-to-delete` — pulled from the sites and **purged after 90 days**. Recoverable only by
  re-creating the item from scratch within that window.

Check `GET /reports/removedItems/v1?sellerId={sellerId}` for items Sears removed on its own.

## Budgets

Catalog 600 files/hour, inventory 360, ASN 300, order updates 200, pricing 120, prep time 100,
manual invoice 20 — plus a hard 500 requests/minute per IP. Exhaustion returns HTTP 403 with
"rate limited"; wait 60 minutes from the first call of the hour (15 minutes for the IP throttle).
There are no rate-limit response headers, so you must count your own calls.
