# PJS-test-page

A static, single-page Prebid.js harness for the **StackAdapt** bid adapter.

Live at: <https://benboonsiri.github.io/PJS-test-page/>

## What it is

`docs/index.html` is a self-contained test page that:

- Calls the `stackadapt` Prebid adapter for **four ad slots** (300×250, 728×90, 300×600, 320×50).
- Renders winning bids through **GoogleTag** (GAM test network `19968336`).
- Surfaces a **diagnostics panel** with Prebid version, render mode, timeout, publisher ID, placement ID, and auction count.
- Streams a **live event log** of `bidRequested` / `bidResponse` / `bidWon` / `noBid` / `bidTimeout` so you can see exactly what happened.
- Shows bid response details including bidder, CPM, size, creative ID, response time, and winning bids.

## URL parameters

All optional. Use to override the defaults baked into the page.

| Param         | Default | Description                                                |
| ------------- | ------- | ---------------------------------------------------------- |
| `publisherId` | `123`   | StackAdapt publisher ID sent in the bid params             |
| `placementId` | *(none)*| StackAdapt placement ID sent in the bid params             |
| `bidfloor`    | *(none)*| Floor price (float) sent in the bid params                 |
| `timeout`     | `1500`  | Prebid auction timeout in ms                               |
| `debug`       | `1`     | `0` disables `pbjs.setConfig({debug:true})`                |
| `noGPT`       | `0`     | `1` renders the winning bid directly via `pbjs.renderAd` and skips GoogleTag entirely — handy when GAM house ads keep beating your prebid bid |

Example:

```text
https://benboonsiri.github.io/PJS-test-page/?publisherId=4cd53a92ra91&timeout=2000&noGPT=1
```

## Local development

It's a single HTML file plus a pre-built `prebid.js`. Serve `docs/` with any static server:

```bash
cd docs
python3 -m http.server 8000
# then open http://localhost:8000
```

Note: many SSPs (StackAdapt included) won't return ads to a request whose `page` is `localhost`. Add an alias in `/etc/hosts` (e.g. `127.0.0.1 test.local`) and open `http://test.local:8000` if you need a non-localhost origin.

## Updating the Prebid build

`docs/prebid.js` is a pre-built Prebid.js bundle that includes the `stackadapt` adapter. To refresh it, build Prebid.js with the adapter list you want and drop the resulting bundle in `docs/`:

```bash
git clone https://github.com/prebid/Prebid.js
cd Prebid.js
npm ci
gulp build --modules=stackadaptBidAdapter,gptPreAuction
cp build/dist/prebid.js /path/to/PJS-test-page/docs/prebid.js
```

## Deploy

`docs/` is served by GitHub Pages from the `master` branch. Push to `master` and GitHub Pages will pick up the change within a minute.
