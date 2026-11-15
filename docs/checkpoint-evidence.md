# Ticket 1 checkpoint evidence

## CP1 — Explain

See [cp1-explanation.md](cp1-explanation.md). It records the module map, order flow, and confirmed/suspected risks.

## CP2 — Diagram

See [shopledger-architecture.svg](shopledger-architecture.svg) for the UI, routers, library modules, configuration, data file, and order flow.

## CP3 — Characterize

The tests use in-memory orders and never import or access `data/db.json`. Against the original invoice module, the ticket fixtures showed:

| Order | Original invoice output | Checkout ledger |
|---|---:|---:|
| #1002 | discount ₹120.36, GST ₹183.60, total ₹1,083.24 | discount ₹102.00, GST ₹45.90, total ₹963.90 |
| #1004 | discount ₹264.32, GST ₹403.20, total ₹2,378.88 | discount ₹150.00, GST ₹104.50, total ₹2,194.50 |
| #1007 | GST ₹230.40, total ₹1,510.40; both lines at 18% | GST ₹127.00, total ₹1,407.00; 12% and 5% lines |

The characterization assertions were updated in `test/invoice.test.js` to require the checkout-consistent behavior. The unchanged HTML escaping check remains.

## CP4 — Fix

The correct-total test was run against the original invoice module and failed as expected: `1083.24 !== 963.9` (exit 1). After the fix, `node --test` passed all 7 tests (7 pass, 0 fail). All fixtures are in memory. The fix is limited to `lib/invoice.js`: it uses the shared HSN rate and coupon calculations, taxes post-discount line values, and displays taxable value in the invoice detail table.

## CP5 — Pull request

Local work is ready on branch `fix/invoice-gst-reconciliation`. The workshop requires an issue number in the first line (`Fixes #...`) and a hosted PR page. The issue number was not supplied, and no GitHub CLI or GitHub connector is available in this environment. The configured `origin` points to `JS12540/shopledger-legacy-workshop`; after adding the issue number and pushing the branch, open a PR from that fork into `main`. Do not merge it.
