# SAP BDC Working Capital 360 — Public (anonymous) build

A static, no-login public build of the SAP BDC Working Capital 360 dashboard, deployed to
GitHub Pages. Dashboards render from a **point-in-time data snapshot** (no Snowflake
connection, no credentials in the browser). The optional "Ask the Agent" page calls a
separate serverless Cortex agent when configured.

## Pages

Working Capital Overview · Cash & Liquidity · Accounts Receivable · Accounts Payable ·
Early Payment & SCF · Inventory · WC Opportunities · BDC Sources & Lineage · Ask the Agent

## Data honesty

- **AR and AP** balances, aging and DSO/DPO are computed from **real SAP BDC journal
  entry lines** (catalog-linked data products).
- **Payment terms, early-payment/SCF offers, inventory, cash positions and business
  partner names are demo enrichment** layered on top to illustrate the workflows.
- All amounts are shown in **USD using fixed FX rates** (not daily market rates).
- The snapshot is point-in-time; it does not update until re-exported.

## How it works

- Built with `VITE_STATIC=1`; the client reads `public/data/*.json` instead of `/api`.
- **Filters:** 3 company codes, baked as the full power set (8 combos incl. "all") for the
  default period (see `filters.json`). Selecting a different period falls back to the
  default-period snapshot (see `filterKey()` / `getKeyed()` in `src/lib/api.ts`).
- **Live agent (optional):** set the repo variable `AGENT_URL` to a deployed Cortex agent
  endpoint. If unset, Ask the Agent shows a "not available" notice; dashboards work fully.

## Refresh the data

```bash
# from working_capital_360_react, with its server running on :3010
EXPORT_BASE=http://localhost:3010 node scripts/export-static.mjs
cp client/public/data/*.json ../working-capital-360-public/public/data/
```
Commit and push — Actions redeploys.

## Local build

```bash
npm ci
VITE_STATIC=1 BASE_PATH=/working-capital-360-public/ npx vite build
BASE_PATH=/working-capital-360-public/ npx vite preview --port 4185
```

## Deploy

Push to `main` → `.github/workflows/deploy.yml` builds with
`BASE_PATH=/working-capital-360-public/` and publishes `dist/` to GitHub Pages.
