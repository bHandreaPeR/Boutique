# Boutique Manager — Project Context
*Last updated: 25 April 2026 · Compiled from index.html*

---

## What This Is

A single-file mobile web app built for **Subhransu's mother** to manage her boutique/salon business ("Beauty & Beads"). Not a startup, not a SaaS — just a tool she can open on her phone and use daily. The design is intentionally overfit to her specific workflow: jewellery, clothing, accessories, salon services (hair colour, facial, mehndi, etc.), cash + PhonePe transactions, voice input in Indian English.

---

## The App

**File:** `index.html` (~894 lines)  
**App name:** Beauty & Beads Manager  
**Tech:** Pure HTML + CSS + Vanilla JS (no framework, no build step, no server)

Open directly in Safari on iPhone. Works offline (all data in localStorage).

---

## Design System

| Token | Value | Usage |
|---|---|---|
| `--ink` | `#1a0a2e` | Header, nav background, primary buttons |
| `--cream` | `#fdf6ec` | Page background |
| `--rose` | `#e8627a` | Services accent, low stock, sale alerts, danger |
| `--gold` | `#c9963a` | Accent, active nav, brand |
| `--sage` | `#4a7c6f` | Positive numbers, restocks |
| `--lavender` | `#7b5ea7` | Brand, secondary accents |

**Fonts:** Fraunces (serif, for numbers/headings) + Plus Jakarta Sans (body)  
**Layout:** Max 430px centered, `100dvh`, bottom nav, iOS safe-area-inset aware  
**Pattern:** Everything opens in a bottom sheet (`showSheet()` / `closeSheet()`)

---

## Navigation

5 tabs in the bottom nav:

1. **Home ✦** — Dashboard with greeting, action buttons, quick chips, metric cards, today's log
2. **Stock ◈** — Left sidebar categories + item list with search (was "Inventory")
3. **Services ✂** — Salon/beauty services CRUD + log service flow *(new tab)*
4. **Sales ◉** — Pending inbox + logged transaction list
5. **P&L ◈** — Profit & loss with period filter

> **Connect tab** — removed from nav. The `#sec-connect` div still exists in the DOM but is unreachable from the UI. The WhatsApp/email parsers, PhonePe CSV import, and CSV export code is still in the JS but hidden. Needs a decision: restore or remove.

---

## Data Model

All data persisted to **localStorage** under keys: `bb_items`, `bb_sales`, `bb_pending`, `bb_activity`, `bb_cats`, `bb_services`, `bb_ids`

```js
// Item
{ id, name, brand, cat, stock, cost, price, low, unit, desc }

// Service
{ id, name, icon, variants: [{label, price}], materialItemId, materialQty, desc }
// materialItemId: inventory item id (0 = none); materialQty: qty deducted per service

// Sale
{ id, item, qty, salePrice, amount, cost, method, note, time, type }
// type: 'sale' | 'purchase' | 'service'
// method: 'Cash' | 'PhonePe' | 'UPI' | 'Card'

// Pending transaction (from PhonePe/SMS, needs matching)
{ id, rawDesc, amount, type, time }
// type: 'credit' | 'debit'

// Category
{ id, icon, label, color }

// Activity log entry
{ t, text, time }
// t: 'sale' | 'restock' | 'edit' | 'service'

// ID counters (bb_ids)
{ nextItemId, nextSaleId, nextSvcId }
```

Default categories: Jewellery (J), Clothing (C), Beauty (B), Accessories (A)  
Default services: Hair Colour, Haircut & Styling, Facial, Mehndi, Waxing, Blowdry & Setting  
User can add custom categories with emoji icon + hex colour.

---

## Features Built

### Home Dashboard
- Time-based greeting row (Good morning/afternoon/evening ☀️🌸✨) with date + today's transaction count
- Two action buttons: **Quick Sell** (products) and **Log Service** (salon)
- Quick chip row: top 3 services + top 4 products (ranked by past sales, in stock), tap to pre-select
- Two metric cards: Today's revenue, Total gross profit
- Voice status bar (persistent, shows current mic state)
- Low stock alert section (items at or below threshold) with Restock shortcut
- Pending inbox banner (count of unmatched PhonePe/SMS transactions) with Review link
- Today's transaction log list (colour-coded dots: green = sale, rose = service, orange = purchase)

### Stock (Inventory)
- Left sidebar: category filter (including "All")
- "+" Add new category button at bottom of sidebar
- Each item row: name, brand, price/unit, stock qty, OK/Low pill, ± and ✎ buttons
- Search by name or brand (live filter)
- Add/Edit item form (bottom sheet): name, brand, category, unit, stock, cost price, selling price, low stock alert threshold, description
- Delete item from edit form
- Quick update sheet: sell/received/set exact qty with optional note
- Smart autocomplete suggestions on all text fields (item name, brand, unit, desc, note)

### Services *(new)*
- Services list with icon, name, description, variants with prices, material used from stock
- **Log** button per service → opens quick service flow (steps 2+)
- **✎** button → edit service (name, icon, description, variants, material)
- **+ New** button → add service sheet
- **Quick service log flow (3 steps):**
  1. Pick service (from list)
  2. Pick variant (button group) + optional discount (0/5/10/15/20%)
  3. Confirm summary + payment method picker (PhonePe / Cash / UPI / Card)
- On save: material item stock is auto-deducted; sale logged with `type:'service'`; toast shown
- `openQuickServiceDirect(id)` skips step 1 (used from home quick chips)

### Quick Sell (Products)
- 3-step bottom sheet flow:
  1. Searchable item picker list (shows stock + price)
  2. **Numpad** for quantity (large tap targets, shows running total)
  3. Payment method picker (4 buttons)
- Confirms with toast notification

### Sales
- Two metric cards: Today's total, Services total
- **Pending inbox** section: unmatched PhonePe/SMS transactions
  - "Add description & match" → opens sheet to describe, link to inventory item, adjust qty/amount
  - "Dismiss" → slides out with animation
- Log Product Sale form (legacy): item select, qty, price, total, payment, note
- Log Service shortcut button → opens quick service flow
- Sales list (reverse chronological): item name × qty, +/− amount, method pill (or "Service" pill for services), note, timestamp; service entries show material cost and original price if discounted

### P&L (Profit & Loss)
- Period tabs: Today / Week / Month / All (tabs shown, **date filter still not wired** — all data shown regardless)
- Revenue box: Product sales + Services revenue + breakdown by payment method
- Costs box: Product cost of goods + Service material costs + direct purchases
- Gross profit box with margin % bar
- Stock on hand box: at cost, at retail
- Note: "Service margins are revenue minus material cost only" shown at bottom

### Connect (Integrations — hidden from nav)
Code still present in JS but `#sec-connect` has no nav button. Features that were built:
| Integration | Method | Code status |
|---|---|---|
| PhonePe Business | Paste CSV rows | In code, UI hidden |
| Bank/UPI SMS parser | Paste SMS text, regex extracts amount | In code, UI hidden |
| WhatsApp order parser | Claude AI extracts items | In code, UI hidden |
| Email order parser | Claude AI extracts items | In code, UI hidden |
| Export sales CSV | Download `beauty-beads-sales.csv` | In code, UI hidden |
| Export inventory CSV | Download `beauty-beads-inventory.csv` | In code, UI hidden |
| Clear all data | Wipe everything, confirm dialog | In code, UI hidden |

### Voice Commands
- Mic button in header (always visible, rose colour, pulses when listening)
- Detects in-app browser (WhatsApp, etc.) and shows Safari redirect banner
- Detects missing speech recognition and shows error sheet
- **Local pattern matching first** — tries service names before product patterns:
  - If transcript includes a service name → `{action:'service', svc, variantIdx}`
  - Regex patterns for "sold X items", "restocked X items", etc. for products
- Fuzzy item matching by word overlap (`lsim()` function)
- **Voice confirm overlay**: shows what was understood, stock change preview, Yes/No buttons
- **Claude API fallback** if local matching fails: sends transcript + inventory + services list, gets back `{action, itemId, serviceId, qty, variantIdx, method}`
- **Voice fallback form**: if API also fails, pre-fills a manual form with best guesses; includes type selector (product sold / service / restocked)
- Recognition language: `en-IN`

---

## Claude API Usage (Direct Browser)

```
POST https://api.anthropic.com/v1/messages
Header: anthropic-dangerous-direct-browser-access: true
```

**Used for:** Voice command fallback — model: `claude-sonnet-4-6`

**⚠ API key is hardcoded in the HTML** — acceptable for a private single-user app; rotate if the file is ever shared.

---

## Known Issues / Things to Fix

1. **P&L period filter not wired** — tabs show Today/Week/Month/All but all show same data
2. **Connect tab integrations are unreachable** — no nav button; decide to restore or fully remove the dead code
3. **No data backup** — all data is in localStorage; clearing browser data wipes everything
4. **API key exposure** — Anthropic API key is hardcoded in the file
5. **Voice confirm on re-speak** — if user taps mic again while confirm overlay is open, it overlaps

---

## Design Principles for Future Work

- **Keep it a single file.** No npm, no build step. She opens it in Safari from a bookmark.
- **Every action should be 2 taps or fewer** from the home screen.
- **Prefer visuals over numbers** for things like stock levels.
- **Assume Indian context**: ₹ currency, PhonePe is primary digital payment, items are jewellery/clothing/accessories, services are salon/beauty.
- **Don't add features she didn't ask for.** This is not a startup.
- **Always confirm before saving** (especially for voice commands — the confirm overlay is critical).
- **All text inputs get autocomplete suggestions** from existing data.

---

## Project Owner Notes

- **User:** Subhransu Babu (son, building this)
- **End user:** His mother (boutique + salon owner)
- **Language in app:** English, but voice recognises Indian English
- **Custom instruction:** Do not overcomplicate, keep it personal, ask if unsure, make her like it
- **Global instruction:** Never sugarcoat, be upfront, no silent failures, fix root causes

---

## Session History Summary

| Session | What was done |
|---|---|
| Initial build | Full app built from scratch: all 5 tabs (Home, Inventory, Sales, P&L, Connect), inventory CRUD, sales logging, P&L, voice commands, PhonePe/SMS/WhatsApp/email parsers, CSV export, custom categories, smart autocomplete, bottom sheet UI, dark ink + gold design system |
| Refinements | Voice confirm overlay added; voice fallback form added; mic button error handling improved; in-app browser detection + Safari redirect banner; item autocomplete fix; emoji/colour picker fix; suggestion mousedown fix; API error fallback chain |
| Services + Home overhaul | Added Services tab (full CRUD, variants, discounts, material deduction from stock); Quick Sell numpad flow; Quick Service 3-step flow; Home dashboard overhauled (greeting, action buttons, quick chips, today's log); Toast notifications; Sales + P&L updated for services; Connect tab hidden from nav |

---

*This file is a living context document. Update it whenever significant features are added or changed.*
