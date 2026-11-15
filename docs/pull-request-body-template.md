Fixes #<issue-number>

Tests added first:
- `node --test` — 7 tests pass, 0 fail; in-memory fixtures only.
- Covers SAVE10 tax/discount parity (#1002), discount cap (#1004), mixed HSN slabs (#1001/#1007), unchanged all-18% order (#1008), reconciliation, and HTML escaping.

Cause: The invoice engine used a flat 18% fallback and calculated coupon discounts on the tax-inclusive amount without applying coupon limits. Checkout instead resolves each line's HSN slab and discounts the subtotal before calculating GST.

Fix: Reuse the shared tax and coupon modules in `lib/invoice.js`; calculate tax on apportioned post-discount taxable values and display those values on the invoice.

Test result: `node --test` — 7 passed, 0 failed.
