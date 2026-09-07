---
title: "Duka POS — Cereal Edition"
draft: true
tagline: "A till that thinks in bags and kilos, for a cereal shop selling both retail and wholesale."
date: 2026-08-09
weight: 20
client: "Cereal retailer and wholesaler, Nairobi"
sector: "Wholesale & retail"
engagement: "Design, build and deployment"
year: "2026"
status: "Delivered — in daily use"
platform: "Windows desktop"
stack: ["Electron", "React", "TypeScript", "SQLite", "Tailwind CSS"]
summary: "Goods sold by weight out of 90 kg bags, with wholesale pricing that switches itself on at the right quantity."
outcomes:
  - metric: "90 kg"
    label: "bags tracked down to the kilo"
  - metric: "2"
    label: "price tiers, resolved automatically"
  - metric: "0"
    label: "network dependencies at the till"
---

## The problem

A cereal shop doesn't sell items. It sells weight — beans, maize, rice, millet —
scooped out of 90 kg bags, to customers who might want two kilos for the house or
forty for a kiosk they're restocking. The same product has two prices depending
on which of those it is.

Generic POS software models a product as a countable thing with one price. Every
part of that assumption is wrong here, and working around it means the person at
the scale is doing arithmetic in their head while a queue builds — and getting
the wholesale threshold wrong in the customer's favour, all day, every day.

## Constraints

- **Stock is continuous.** Bags are how it's counted, kilos are how it's sold,
  and the two have to stay reconciled.
- **Two price tiers.** Retail and wholesale, with the switch depending on
  quantity — and it has to be automatic, because nobody at the scale should be
  deciding it under pressure.
- **Offline, on one machine.** Same as every till I build for this market.
- **Cash and M-Pesa**, reconciled separately at close of day.

## What was built

{{< shot src="01-sell.png" caption="Enter a weight, or enter the money — the other side works itself out." >}}

**Selling by weight.** Quantity is entered in kilos, or the customer says
"three hundred bob of njahi" and the weight is derived from the amount. Both
directions run through the same pricing rules, so they can never disagree.

{{< shot src="02-wholesale.png" caption="At five kilos the price tier changes, and the line item shows which rate was applied." >}}

**Wholesale that applies itself.** Past the threshold the wholesale rate takes
over automatically, and the sale records *which* tier it used — so the margin
reporting afterwards is honest about what was actually sold at what price.

{{< shots cols="2"
          files="03-stock.png, 04-shift.png, 05-zreport.png"
          captions="Stock read the way the shop counts it: bags first, loose kilos second.|Opening a shift with a counted cash and M-Pesa float.|Blind close, then the variance and the Z-report." >}}

**Stock in the shop's own units.** The system knows a bag is 90 kg, but it
reports the way the owner counts: "9 bags 30 kg (840 kg)". Bags first, because
that's the number the owner walks the store checking. The exact weight is kept
alongside for reconciliation. Low stock is flagged below five bags, and a product
can set its own threshold higher.

**Shifts and reconciliation.** Open with a counted cash and M-Pesa float, close
blind, then see the variance and the Z-report — the same discipline as the
grocery till, because it's the part that turns a POS from a calculator into an
accounting control.

## The details that mattered

**Bags and kilos are one number, presented two ways.** Stock is stored as a
single total weight and formatted on the way out. Storing bags and remainder
separately would let them drift apart — this way they can't.

**Trailing zeros are noise.** Weights display as `840` and `30.5`, never
`840.00`. A stock column is scanned, not read, and decimal padding makes it
slower to scan.

**A leading zero is a real bug in a payment field.** A field sitting at `0` that
gets typed into produces `0100`, which a thousands separator then renders as
`0,100` — a hundred-shilling payment wearing a hundred-thousand's clothes. Worth
the four lines it takes to prevent.

## Under the hood

{{< note label="Why this mattered" >}}
The entire product model rests on this conversion. Getting it wrong doesn't
produce a visible error — it produces stock figures that slowly stop matching
the store room, which is far worse.
{{< /note >}}

```ts
export const KG_PER_BAG = 90

export function summarizeStock(totalKg: number, kgPerBag: number = KG_PER_BAG): StockSummary {
  const normalizedKg = Math.max(0, roundKg(totalKg))
  const bagSize = kgPerBag > 0 ? kgPerBag : KG_PER_BAG
  const bags = Math.floor(normalizedKg / bagSize)
  const remainderKg = roundKg(normalizedKg - bags * bagSize)

  const bagsLabel = `${bags} ${bags === 1 ? 'bag' : 'bags'}`
  const remainderLabel = remainderKg > 0 ? `${trimKg(remainderKg)} kg` : ''
  const totalLabel = `${trimKg(normalizedKg)} kg`

  // Under one full bag the total would just repeat the remainder, so the
  // parenthesised weight is dropped rather than printed twice.
  const parts = [bagsLabel, remainderLabel].filter(Boolean)
  const label = bags > 0 ? `${parts.join(' ')} (${totalLabel})` : parts.join(' ')

  return { totalKg: normalizedKg, bags, remainderKg, bagsLabel, remainderLabel, totalLabel, label }
}
```

{{< note label="Why this mattered" >}}
Pricing lives in one function that both the quantity path and the amount path
call. There is no second place where the wholesale rule could be implemented
slightly differently.
{{< /note >}}

```ts
export const WHOLESALE_MIN_KG = 5

export interface ResolvedPrice {
  price: number
  mode: 'retail' | 'wholesale'
}

export function resolveUnitPrice(
  qtyKg: number,
  retailPricePerKg: number,
  wholesalePricePerKg: number
): ResolvedPrice {
  if (wholesalePricePerKg > 0 && qtyKg >= WHOLESALE_MIN_KG) {
    return { price: wholesalePricePerKg, mode: 'wholesale' }
  }
  return { price: retailPricePerKg, mode: 'retail' }
}
```

Returning the `mode` alongside the price is the part that matters. The interface
can tell the customer which rate they got, and the reporting can separate
wholesale turnover from retail without guessing after the fact.

**Same foundations.** Electron and React over Node's built-in SQLite, with no
native modules, database and printing confined to the trusted main process, and
the whole thing shipping as a single Windows installer that needs no connection
and no account.

## Outcome

Delivered and installed on the shop's till. The scale and the screen now agree,
the wholesale threshold is applied the same way every time regardless of who is
serving, and stock reconciles against the store room in the units the owner
actually counts in.

This was the first of the line. The packaged-grocery till came next — the same
foundations, re-specialised for discrete goods, barcodes and customer credit.
