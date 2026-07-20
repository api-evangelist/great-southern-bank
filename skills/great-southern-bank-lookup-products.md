---
name: Look up Great Southern Bank products
description: Discover and read Great Southern Bank's publicly offered banking products (accounts, deposits, loans, cards) via the unauthenticated CDR Product Reference Data API.
api: openapi/great-southern-bank-cds-banking-products-openapi.yml
operations:
  - listBankingProducts
  - getBankingProductDetail
---

# Look up Great Southern Bank products

Great Southern Bank exposes its product catalogue through the Consumer Data Right
(CDR) Product Reference Data (PRD) API. These endpoints are **public and
unauthenticated** — no API key, token, or accreditation is required. All other
CDR banking data (accounts, balances, transactions) is ADR-only and out of scope
for this skill.

## Base URLs

- Retail: `https://api.open-banking.greatsouthernbank.com.au/cds-au/v1`
- Business+: `https://od1.open-banking.business.greatsouthernbank.com.au/api/cds-au/v1`

## Required convention

Every request MUST send the version header `x-v: 3` (retail) or `x-v: 5`
(Business+, current). Omitting it or requesting an unsupported version returns
`406 Not Acceptable`. No `Authorization` header is needed.

## Steps

1. **List products** — call `listBankingProducts`:
   `GET /banking/products` with header `x-v: 3`.
   Optional query filters: `product-category` (e.g. `TERM_DEPOSITS`,
   `TRANS_AND_SAVINGS_ACCOUNTS`, `RESIDENTIAL_MORTGAGES`, `CRED_AND_CHRG_CARDS`),
   `updated-since`, `brand`, `effective`. Paginate with `page` and `page-size`
   (default 25); read `meta.totalRecords` / `meta.totalPages` and follow
   `links.next`.

2. **Get product detail** — take a `productId` from the list and call
   `getBankingProductDetail`: `GET /banking/products/{productId}` with header
   `x-v: 5`. The response embeds `features`, `fees`, `depositRates`,
   `lendingRates`, `eligibility`, `constraints`, and `bundles`.

## Error handling

Errors use the CDS envelope `{ "errors": [ { "code": "<urn>", "title", "detail" } ] }`
(not RFC 9457). Handle: `400` invalid field/version, `404`
`urn:au-cds:error:cds-all:Resource/Invalid` (unknown productId), `406` unsupported
version. See `errors/great-southern-bank-problem-types.yml`.

## References

- Conventions: `conventions/great-southern-bank-conventions.yml`
- Data model: `data-model/great-southern-bank-data-model.yml`
