# Pulseboard

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

**Clarity for every business decision.**

Pulseboard is a business analytics dashboard. It turns order data into revenue trends, a business health score, profit, customer and cash metrics, goal tracking, a forecast simulator and a ranked list of what to focus on next. It can read a Shopify store live, or work from a CSV or Shopify's own order export. The whole app is **one file with no dependencies**.

## Features

- **Overview:** revenue, orders, average order value and refunds against the previous period, with a revenue chart.
- **Business health score (0-100):** a weighted average of only the factors the data supports, with adjustable weights. It is a transparent heuristic, not an industry benchmark.
- **What should I focus on next?:** rule-based, ranked recommendations with the reason, the numbers and a suggested action. Mark each done, saved or dismissed.
- **Profit, Customers and Cash tabs:** revenue, expenses, profit and margins; total, new and returning customers, CAC and LTV; cash inflows, outflows, burn and runway. Only figures that can be calculated from the data are shown, and missing ones are listed instead of invented.
- **Goal tracker and forecast simulator:** best, expected and worst cases, break-even and saved scenarios, always labelled as estimates.
- **Dark and light themes**, hide-able sections, a summary report and CSV export.

</details>

## Quick start

1. Install [Node.js](https://nodejs.org) 18 or newer.
2. Get `pulseboard.js` (the only file needed to run it) and run `node pulseboard.js` in its folder. From a clone, `npm start` does the same.
3. Your browser opens at http://localhost:3000.

On Windows, type `cmd` in the folder's address bar and press Enter to open a terminal there.

## Bring in your data

Three options, all optional:

1. **Shopify orders export (no setup).** In Shopify admin choose **Orders, Export, All orders**, then click **Import CSV** and pick the file. A sample is in [`samples/sample-shopify-export.csv`](samples/sample-shopify-export.csv).
2. **Any CSV.** Columns `date`, `product`, `amount`, and optionally `category`, `channel`, `region`, `refunded`, `customer`. A sample is in [`samples/sample-orders.csv`](samples/sample-orders.csv).
3. **Live Shopify sign-in.** Click **Connect Shopify**, type your store name and click **Allow** on Shopify's own permission page. You never type a Shopify password into Pulseboard. Shopify only shows that page for an app, so the first time you do a one-time setup: create a free app on dev.shopify.com, set the redirect URL shown in the form, give it the read-only scopes `read_reports`, `read_orders`, `read_customers` and `read_marketing_events`, and paste its Client ID and Secret. After that it is just your store name and Allow. The connection is saved next to `pulseboard.js` (`.pulseboard-store.json`, git-ignored) and **Reconnect** and **Disconnect** are built in.

## How it works

`pulseboard.js` is built from two readable source files by `node build.js`, which compresses the dashboard page into the server file. The same build writes `docs/index.html` for GitHub Pages.

| Path | Role |
|---|---|
| `src/index.html` | The dashboard: HTML, CSS and JavaScript (hand-written SVG charts, `localStorage` for settings) |
| `src/server.js` | Node server using only built-in modules. Serves the page, runs the Shopify OAuth flow and forwards read-only queries |
| `build.js` | Builds `pulseboard.js` and `docs/index.html` |
| `test/pulseboard.test.js` | Server tests (`npm test`) |
| `samples/` | Example files to import |
| `docs/` | GitHub Pages site and screenshots |

Inside `index.html`, data from the demo generator, a CSV, a Shopify export or live Shopify becomes one row shape. Pure calculation functions (`win`, `agg`, `finCalc`, `goalCalc`, `health`, `recs`) feed small render functions, so charts and cards always agree. Rules followed: no division by zero, "unavailable" is never shown as 0, and estimates are labelled.

**Security:** the browser never talks to Shopify. The server holds the token, sends only allow-listed read-only queries (`sales`, `sessions`, `payments`, `customers`), listens on `127.0.0.1` only, rejects requests from other origins, and verifies Shopify's signature and a one-time state value on the OAuth callback.

## Testing

```
npm test
```

Builds the app, then runs 24 server tests: static serving, query validation, cross-origin blocking, token connect and disconnect, and the full OAuth flow (permission redirect, forged and unknown callbacks rejected, signed callback accepted). The layout was also checked in a real browser across widths, zoom levels and fonts, comparing every element against its parent and its siblings. CI runs `npm test` on every push.

## Limits

- The Shopify sign-in needs the local server, so it does not work on the GitHub Pages demo.
- Cost of goods appears only if product costs are set in Shopify. Shopify does not provide operating expenses, subscriptions or the payments balance here, so those are optional manual inputs.
- The Shopify order export is a snapshot: export again for fresh numbers.

## Roadmap

Expense trends over time, month, quarter and year comparisons, PDF reports, and a hosted sign-in helper so other people can connect without creating their own Shopify app.

## License

MIT. See [LICENSE](LICENSE).

## Author

Built by Riley Maynard ([@Zecktix](https://github.com/Zecktix)). Questions or feedback: ril3ymaynard@gmail.com.
