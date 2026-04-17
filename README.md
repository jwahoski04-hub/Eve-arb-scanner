# EVE Arbitrage Scanner

A standalone, single-file market scanner for [EVE Online](https://www.eveonline.com/). Finds inter-hub and station trading arbitrage opportunities using the public [ESI API](https://esi.evetech.net/) — no installation, server, or login required.

---

## Quick Start

1. Download `eve_arb_scanner.html`
2. Open it in Chrome, Firefox, or Edge
3. Pick a tab, configure your filters, and click **SCAN ALL**

That's it. No dependencies, no build step, no account needed.

---

## Features

### Hub Trading
Scans all five major NPC trade hubs simultaneously — Jita, Amarr, Dodixie, Hek, and Rens — and finds items where a price gap exists between hubs after accounting for tax.

- Bulk region fetch: pulls every order in a region in one paginated pass (much faster than per-item lookups)
- Cross-hub price comparison entirely in memory — no extra ESI calls
- Hauling calculator: enter your cargo capacity and see trip profit for each opportunity
- Filter by route matrix: enable/disable any hub-to-hub direction
- 7-day or custom average volume filter

### Station Trading
Scans the order book at a single NPC station for bid/ask spread opportunities — items where you can buy at the buy order price and sell at the sell order price for a profit.

### Shared Features
- **IndexedDB cache** with ETag/304 validation — repeated scans reuse cached data, saving ESI quota and time
- **Rate limit monitor** — live display of ESI error and rate limit tokens with adaptive backoff
- **Category filter** — 20+ categories including Asteroid (ores), Fighters, Decryptors, SKINs, Expert Systems, and all standard categories
- **Watchlist** — star any result to save it; watchlist persists across sessions via `localStorage`
- **CSV export** — download all results for external analysis
- **Expandable rows** — click any result for a full price breakdown and hauling details

---

## How It Works

```
Bulk fetch all orders per region (paginated ESI)
        ↓
Build per-station type maps (in memory)
        ↓
Cross-hub price comparison (no ESI calls)
        ↓
Fetch 7-day volume history for candidates only
        ↓
Batch name + category resolution
        ↓
Apply filters → render results
```

All market data comes from `https://esi.evetech.net/latest/markets/{region_id}/orders/`. Only NPC station orders are visible via the public endpoint — citadel/structure orders require authenticated ESI access.

---

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| Min Margin % | 5% | Minimum net margin after tax |
| Tax % | 3.6% | Combined broker fee + transaction tax |
| Min Daily Vol | 10 | Minimum 7-day average daily volume |
| Min Profit ISK | 100,000 | Minimum net profit per unit |
| Cargo (m³) | 10,000 | Your hauler's cargo capacity for trip profit calculation |
| Throttle Floor | 40 | ESI error token level at which the scanner backs off |
| Min Sell Orders | 3 | Minimum competing sell orders at the buy hub |
| Max Price ×Avg | 3× | Outlier filter — skips items priced far above their average |

---

## Category Filter Reference

| Button | Cat ID | Contents |
|--------|--------|----------|
| MATERIALS | 4 | Minerals, moon materials, PI inputs |
| ASTEROID | 25 | Raw and compressed ores |
| CHARGES | 8 | Ammo, scripts, crystals, bombs |
| MODULES | 7 | All ship modules |
| RIGS | 157 | Ship rigs |
| DRONES | 18 | Combat, mining, and logistics drones |
| FIGHTERS | 87 | Carrier-launched fighters (Locust, Einherji, etc.) |
| IMPLANTS | 11 + 20 | Attribute enhancers, hardwirings, and boosters |
| SKILLS | 16 | Skill books |
| COMMODITY | 17 | Trade goods, salvage, PI commodities |
| BLUEPRINTS | 9 | Manufacturing blueprints |
| DECRYPTORS | 35 | Invention decryptors |
| DEPLOYABLE | 22 | Mobile depots, cynos, interdiction probes |
| SUBSYSTEMS | 32 | T3 cruiser and destroyer subsystems |
| SHIPS | 6 | All pilotable ships |
| STRUCTURES | 23 | Upwell structures |
| STR. MODULES | 34 | Structure modules |
| SKINS | 91 | Ship SKINs |
| EXPERT SYS. | 2100 | Temporary skill bundles |
| APPAREL | 30 | Character clothing |

---

## Limitations

- **NPC stations only.** The public ESI market endpoint does not return citadel or player-owned structure orders. Prices at structures (which are often better) are invisible to this tool.
- **No authentication.** The scanner uses only public ESI endpoints. Character wallet, assets, and personal orders are not accessible.
- **ESI rate limits.** Large scans make many API requests. The throttle floor setting controls how aggressively the scanner backs off when approaching ESI limits.
- **Cache.** Order page data is never cached (always fetched fresh). History and type info are cached with ESI-provided expiry times.

---

## Disclaimer

This tool is not affiliated with or endorsed by CCP Games. EVE Online and all related trademarks are the property of CCP hf. Use at your own risk.

---

## License

MIT — free to use, modify, and distribute.
