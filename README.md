### LYNX Product Showcase
A looping, animated preview of LYNX — PH → Endpoint Network Monitor

Self-hosted — no public demo (the agent and API run on an internal PC by design).

![LYNX showcase preview](LYNX.gif)

LYNX was built to answer a question a speedtest can't: not "is the internet fast right now," but *is the PH–AU path behaving the way it's configured to expect* — right now, and over time, with a record to back it up. It continuously measures the real path between the two offices, stores every reading in SQLite, and turns raw latency/loss/jitter/routing data into a plain-language verdict instead of a wall of numbers.

It exists because of a real incident: a chronic ~180–260 ms latency gap between two ISPs reaching the same Brisbane endpoint, eventually root-caused to an asymmetric BGP path through a specific Singapore transit hop. LYNX is built to catch that class of problem automatically next time, and hand over evidence for the support ticket without someone reconstructing it by hand.

## What it does

- Continuous latency, loss, and jitter measurement between PH and AU, stored in SQLite with automatic rollup and retention
- A rolling status engine (NORMAL / DEGRADED / CRITICAL) that requires consecutive bad samples before flagging anything — no single noisy ping triggers an incident — and explains every status in plain language, not just a label
- Automatic route-change detection: every traceroute is diffed against the prior run, with configurable chokepoint watch patterns (e.g. a known problem hop like `level3.net`) flagged the moment they appear or disappear
- Local-vs-international differentiation, using a synthetic local-gateway target to tell a LAN issue apart from an upstream/international one
- A human-maintained route map (Manila → Singapore → Sydney → Endpoint) editable from the dashboard — deliberately not auto-derived from incomplete traceroute data
- CSV/JSON export and one-click PDF report generation, built specifically for attaching evidence to an ISP support ticket
- A single-page dashboard viewable by the whole team from anywhere, via a Cloudflare Tunnel — without exposing the monitoring PC directly

## Built with

- Node.js + TypeScript — one process runs both the monitoring agent and the read/write API, backed by the same SQLite file
- SQLite via `better-sqlite3`, with schema migrations handled in-place
- React + Vite + TypeScript + Tailwind + Recharts + Framer Motion + Lenis
- Windows Service (`node-windows`) for always-on operation with auto-restart on crash
- Cloudflare Tunnel + Cloudflare Pages for secure remote dashboard access, with Cloudflare Access recommended as the login gate
- `pdfkit` for report generation — no headless Chromium required on the mini PC

![LYNX showcase preview](LYNX2.gif)
