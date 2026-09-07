# Screenshot & recording guide for `/work`

Every image below has a placeholder already rendering on the site. Drop the file
into the page bundle with the **exact filename** and it swaps in automatically —
no template or markdown edits needed.

| Target folder | Files | Status |
|---|---|---|
| `content/work/grocery-smart-pos/` or `assets/img/snippets/` | `featured.jpg`, `01-sell.png`, `02-checkout.png`, `03-receipt.png`, `04-inventory.png`, `05-zreport.png`, `06-analytics.png` | **Complete** (All 7 screenshots captured & active) |
| `content/work/duka-pos-cereal-edition/` | `featured.jpg`, `01-sell.png`, `02-wholesale.png`, `03-stock.png`, `04-shift.png`, `05-zreport.png` | *Shelved / Scrapped for now* |

---

## 0. Before you capture anything

**0.1 — Launch the app.** From the project folder:

```bash
cd C:\Users\Elvo\Desktop\GrocerySmartPOS   # or ...\POScereals
npm run dev
```

The window opens at **1600 × 980**, which is exactly the capture size we want.
Don't resize it — every screenshot then shares one aspect ratio and the grid on
the site stays even.

**0.2 — Sign in** with your admin PIN. If no shift is open the till shows
`Till locked`; open a shift first (see step G5 / C4 — you can capture the shift
screens now and reuse them).

**0.3 — Turn on Practice mode.** `Settings → Practice mode` (admin only). It
copies the database to a throwaway file and fills it with invented trading —
you'll see a toast like *"Practice mode on — 214 sales across 30 days to play
with"*.

This matters twice over:
- **No real client data can appear in any screenshot.** Everything you capture
  is invented.
- **The Report/Overview screens will have realistic history.** Without it, the
  analytics screenshots are empty charts.

> ⚠️ **Crop the practice banner out of every single capture.** While practice
> mode is on, a coloured banner sits at the very top of the window, above the
> nav bar. It must not appear in any portfolio image. Crop from just below it —
> start your crop at the top edge of the nav bar (Report / Sell / Transactions
> …). Do this consistently and every screenshot lines up.

**0.4 — Use light mode**, via the dark-mode toggle at the right of the top nav.
The site defaults to a light appearance, so light screenshots sit better. Dark
is fine too — just be consistent across all 13 images, never mixed.

**0.5 — Tidy the screen.** Before each shot: close any toast that's still
showing, make sure no menu is half-open, and check no field is mid-edit with a
blinking cursor unless the shot is specifically about typing.

**0.6 — Capture tool.** Windows `Win + Shift + S` → Rectangular snip is enough.
Crop to the app content, excluding the OS title bar and the practice banner.
Save as PNG.

---

## 1. Grocery Smart POS

Nav labels: **Report · Sell · Transactions · Stock · Customers · Settings**

### `featured.jpg` — the card image on `/work`

Shown as the thumbnail on the section landing page, cropped to 16:10 from the
top. It's the first thing a visitor sees, so use the busiest, most legible screen.

1. Go to **Sell**.
2. Build a cart of **4–6 items** with recognisable names and a healthy total.
3. Capture the **whole window** (minus banner/title bar).
4. Save as **JPG**, quality ~85. Name it `featured.jpg`.

> Because the card crops 16:10 from the *top*, keep the important content in the
> upper two-thirds.

### `01-sell.png` — the Sell screen mid-sale

The hero shot of the case study. It must read at a glance as "this is a till".

1. **Sell**.
2. Cart with **4–6 lines**, including at least one **fractional quantity**
   (a ¼ or ½ line) — that's the detail the copy calls out.
3. Make sure the running total and the product grid are both fully visible.
4. Capture the full window.

### `02-checkout.png` — one sale, several tenders

Proves the split-tender model, which is the part clients care about most.

1. From the cart in the previous step, open the **checkout modal**.
2. Enter a **split payment** — e.g. part **Cash**, part **M-Pesa**, so both
   fields carry a value and the balance reads zero.
3. Capture the full window *with the modal open* — keep the dimmed screen behind
   it, it reads as an app rather than a floating box.

### `03-receipt.png` — the 80 mm receipt

Rendered narrow on the page (max 320px, tall aspect), so this one is a
**portrait crop**, not a full window.

1. Complete the sale from step 2 so the **receipt preview** appears.
2. Capture **just the receipt paper** — the tall white strip, not the whole
   window. Crop tight to its edges.
3. Check the receipt header/footer shows a **generic shop name**, not the real
   client's. If it shows the real one, change it first in
   `Settings → Receipts`.

### `04-inventory.png` — Stock with the restock drawer open

1. Go to **Stock**.
2. Open the **restock drawer** and add **2–3 line items**, as if working through
   a delivery note, so quantities and buying prices are filled in.
3. Capture the full window — the stock list behind and the drawer both visible.

### `05-zreport.png` — blind close and variance

The accounting-control shot. Show the moment *after* the count, when the
variance is revealed.

1. **End shift** → the `End shift?` dialog.
2. Enter a counted cash and M-Pesa amount — deliberately make one **slightly
   off** so the **Variance** line shows a non-zero figure. A perfect zero looks
   staged and hides the whole point of the feature.
3. Continue to the **Z-report**, showing `Opening / Expected / Actual /
   Variance` for Cash and M-Pesa.
4. Capture the full window with the report visible.

### `06-analytics.png` — the Report page

1. Go to **Report**.
2. Set the period picker to a **range with plenty of practice history** — 30
   days works well.
3. Make sure the **metric tiles** and at least one **chart with real shape** are
   both in frame. An empty or single-bar chart undersells it.
4. Capture the full window.

---

## 2. Duka POS — Cereal Edition

Nav labels: **Overview · Sell · Transactions · Stock · Customers · Settings**
(note: `Overview`, not `Report`).

Same preparation — practice mode on, banner cropped, light mode, 1600 × 980.

### `featured.jpg` — the card image

As above: **Sell** screen with an active cart, whole window, saved as JPG,
important content in the upper two-thirds.

### `01-sell.png` — selling by weight

1. **Sell**.
2. Add a cereal line entered **by weight in kg** — ideally a non-round figure
   like `2.5 kg`, so it's obviously a scale-driven sale and not a unit count.
3. Have 2–3 lines in the cart.
4. Capture the full window.

### `02-wholesale.png` — the tier switching over

The single most distinctive screenshot in this case study. It must show
wholesale **applied**, not just available.

1. Add a line with a quantity of **5 kg or more** — the wholesale threshold
   (`WHOLESALE_MIN_KG = 5`).
2. Confirm the line shows it resolved to the **wholesale** rate.
3. **Ideal version:** have a *retail* line (under 5 kg) and a *wholesale* line
   (over 5 kg) in the same cart, so the two rates sit side by side and the rule
   explains itself with no caption needed.
4. Capture the full window.

### `03-stock.png` — bags first, kilos second

1. Go to **Stock**.
2. Find a view where several products show the **`9 bags 30 kg (840 kg)`**
   style label — that formatting is the thing being demonstrated.
3. If a **low-stock** item is flagged (under five bags), get it in frame.
4. Capture the full window.

### `04-shift.png` — opening with a counted float

1. Open a shift (or `Settings → Shift History` if you need to re-stage it).
2. Show the **opening float** entry with both **cash** and **M-Pesa** values
   filled in.
3. Capture the full window.

### `05-zreport.png` — variance and Z-report

Same as the Grocery version: close blind, enter a count that's **slightly off**,
capture the Z-report showing `Opening / Expected / Actual / Variance`.

---

## 3. Recordings — which flows, and why

Three flows are worth recording. Each one shows something a static screenshot
genuinely cannot: **cause and effect**. Don't record anything that a screenshot
already explains — a tour of the menus adds nothing.

Ranked by value:

### 3.1 — Barcode scan → cart → checkout → receipt ⭐ *record this one first*

**Grocery Smart POS.** The single most persuasive thing you have. A shop owner
watches a physical scanner beep and the item appear on screen, and immediately
understands the product.

- Start on **Sell** with an empty or 1-line cart.
- Scan **two items** with the real USB scanner.
- Open checkout, take cash, print/preview the receipt.
- **~8 seconds.** Save as `demo-scan-to-receipt.mp4`.

Record the screen only. If you can, do a separate phone video of the physical
scanner for social — but the site wants the screen.

### 3.2 — The bidirectional cart

**Grocery Smart POS.** This is the detail nobody expects and everyone recognises
— "fifty bob of rice" instead of a weight.

- On a product line, **type an amount in KES** and let the quantity derive
  itself on screen.
- Then do it the other way: type a quantity, watch the money follow.
- **~6 seconds.** Save as `demo-amount-to-quantity.mp4`.

Move slowly and pause a beat after each value lands — the whole point is the
*other* field changing, and a fast cut loses it.

### 3.3 — Crossing the wholesale threshold

**Duka POS Cereal Edition.** Shows an automatic business rule firing, which is
the core of that case study.

- Start a line at **3 kg** (retail rate showing).
- Increase past **5 kg** and let the price **flip to wholesale** on screen.
- **~5 seconds.** Save as `demo-wholesale-threshold.mp4`.

### Recording specs

- **Tool:** [ScreenToGif](https://www.screentogif.com/) — record, trim, export.
- **Prefer MP4 over GIF.** The `shot` shortcode already renders MP4 as an
  autoplaying, muted, looping video. An MP4 is typically 5–10× smaller than the
  same GIF at better quality. Use GIF only if MP4 gives you trouble — the
  shortcode handles both.
- **Width 1280px**, 15 fps, no cursor highlight effects.
- **Under 6 seconds, under 2 MB.** These loop forever; anything longer becomes
  irritating rather than informative.
- **Crop the practice banner out**, same as the screenshots.
- No audio — it's muted on the page regardless.

### Where to put them

Drop the file in the page bundle, then add one line to the markdown. For the
scan-to-receipt recording, in
`content/work/grocery-smart-pos/index.md`, just after the `01-sell.png` shot:

```
{{< shot src="demo-scan-to-receipt.mp4" caption="Scan, checkout, receipt — about eight seconds, with no connection." >}}
```

For the cereal threshold, in `content/work/duka-pos-cereal-edition/index.md`,
replacing or following the `02-wholesale.png` shot:

```
{{< shot src="demo-wholesale-threshold.mp4" caption="Past five kilos the rate changes itself." >}}
```

---

## 4. Checking your work

Rebuild and confirm nothing is still a placeholder:

```bash
hugo --gc --minify 2>&1 | grep "placeholder rendered"
```

Every line printed is a file still missing. Silence means all media is in place.

Then preview:

```bash
hugo server --disableFastRender
```

Look at `http://localhost:1313/work/` and check:

1. Both cards show a real thumbnail, not a dashed box.
2. Screenshots are sharp, not upscaled — the source must be at least as wide as
   it renders (900px in a 2-column grid, 1200px for a single shot).
3. The receipt shot is narrow and tall, not stretched.
4. Toggle light/dark on the site — screenshots should sit comfortably on both.
5. Narrow the browser to phone width — the grid collapses to one column and
   nothing overflows sideways.
6. Recordings autoplay and loop.

Finally, confirm no real client data made it in:

```bash
grep -ril "<real shop name>" public/work/
```

Nothing should match.
