# CP1 — ShopLedger explanation (read-only)

**Working folder:** `shopledger-legacy-workshop`\
**Inspection mode:** read-only; no server or data file was used.

## What the app does

ShopLedger is a small retail and wholesale point-of-sale app. Staff can browse products, calculate a cart with GST and coupon discounts, bill customers by cash or Khata credit, review orders, print tax invoices, and reconcile invoice totals against ledger totals.

## Module map

| Area | Files | Role |
|---|---|---|
| Browser UI | `public/index.html`, `public/js/app.js`, `public/css/style.css` | POS, order history, Khata, and GST audit screens |
| HTTP server | `server.js` | Express setup, static UI, invoice endpoint |
| API routers | `routes/orders.js`, `routes/products.js`, `routes/customers.js`, `routes/reports.js` | Order, catalog, customer, pricing preview, and reports APIs |
| Business logic | `lib/pricing.js`, `lib/tax.js`, `lib/coupons.js`, `lib/invoice.js`, `lib/orders.js`, `lib/inventory.js`, `lib/ledger.js`, `lib/reports.js`, `lib/dates.js` | Calculations and business operations |
| Configuration and persistence | `config.js`, `lib/store.js`, `data/db.json` | Shop rules and JSON-backed application data |

## What happens when an order is placed

1. The browser posts item IDs, quantities, customer/payment details, and coupon to `POST /api/orders`.
2. `routes/orders.js` loads the JSON store, resolves product details (including HSN), and checks inventory.
3. `lib/coupons.js` validates the coupon; `lib/pricing.js` applies the discount across line items and calculates each HSN tax slab.
4. Khata billing updates the customer's ledger; inventory is reserved.
5. `lib/orders.js` creates an order with stored subtotal, discount, tax, and total; the route writes the JSON store and responds with the order.
6. The invoice endpoint calls `lib/invoice.js`; the GST audit in `lib/reports.js` compares those invoice figures with stored checkout figures.

## Risks and age

- **Sure:** persistence is a single JSON file, and routes call it directly; concurrent writes can overwrite one another.
- **Sure:** invoice math is separate from checkout math, creating a reconciliation risk (the selected ticket).
- **Sure:** `npm test` is a placeholder that exits with an error; tests run directly through `node --test`.
- **Suspect:** dependency versions and coding patterns are old; the project has no lockfile tracked in the repository and no automated test suite wired into package scripts.
- **Suspect:** the admin export uses a static key in configuration; deployment exposure depends on how the app is hosted.
