# Beauty & Beads Manager — Roadmap

*Updated: 25 April 2026*

---

## ✅ Done

### Core App (v1.0)
- [x] Single-file mobile web app — opens in Safari, saves to home screen
- [x] Full offline support — all data in localStorage, no server needed
- [x] Dark ink + gold design system, Fraunces + Plus Jakarta Sans fonts
- [x] Bottom nav with iOS safe-area support

### Home Dashboard
- [x] Time-based greeting (morning/afternoon/evening)
- [x] Quick Sell + Log Service action buttons
- [x] Quick chip row — top services + top products (ranked by sales history)
- [x] Today's revenue + total profit metric cards
- [x] Today's transaction log
- [x] Low stock alert section with inline Restock shortcut
- [x] Pending inbox banner

### Inventory (Stock)
- [x] Category sidebar (Jewellery, Clothing, Beauty, Accessories + custom)
- [x] Add / Edit / Delete items with full form
- [x] Quick stock update sheet (Sold / Received / Set exact)
- [x] Live search by name and brand
- [x] Smart autocomplete on all text fields
- [x] Low-stock threshold per item with OK / Low pill

### Services
- [x] Service CRUD (add / edit / delete) with emoji icon
- [x] Pricing variants per service (e.g. Basic / Premium / Highlights)
- [x] Optional material deduction from inventory on service log
- [x] Discount selector (0 / 5 / 10 / 15 / 20 %)
- [x] 3-step quick log flow: pick → variant + discount → payment
- [x] Direct log from home quick chips

### Sales
- [x] Today's total + Services total metric cards
- [x] Pending inbox — match PhonePe/SMS transactions with descriptions
- [x] Dismiss pending transactions with slide animation
- [x] Sales list (reverse chronological) with service + product entries
- [x] Log Product Sale form (legacy path)

### P&L
- [x] Period tabs: Today / Week / Month / All — **now wired to actual date filtering**
- [x] Revenue split: Product sales vs Services revenue
- [x] Costs split: Product COGS vs Service material costs
- [x] Gross profit + margin % bar
- [x] Stock on hand (at cost + at retail)

### Voice Commands
- [x] Web Speech API (`en-IN` language)
- [x] In-app browser detection + Safari redirect banner
- [x] Local pattern matching (services first, then products)
- [x] Claude API fallback for unmatched commands
- [x] Voice confirm overlay (yes/no before saving)
- [x] Voice fallback manual form (type toggle: product / service / restock)
- [x] Re-tap guard — no double overlay

### Integrations (code present, UI hidden — see below)
- [x] PhonePe Business CSV import
- [x] Bank/UPI SMS parser (regex)
- [x] WhatsApp order parser (Claude AI)
- [x] Email order parser (Claude AI)
- [x] Export sales CSV
- [x] Export inventory CSV
- [x] Clear all data

### Android APK (v1.0 — apk-build branch)
- [x] Capacitor project scaffolded (`com.beautyandbead.manager`)
- [x] PWA manifest.json added
- [x] android/ project generated + synced
- [x] build.gradle configured (minSdk 26, targetSdk 34, v1.0.0)
- [x] RECORD_AUDIO + INTERNET permissions declared
- [ ] **APK compile** — waiting on Java JDK 17 + Android SDK install (see below)

---

## 🔧 To build the APK (one-time setup)

Run these commands once on this Mac, then come back and run the compile step:

```bash
# 1. Install Java 17
brew install --cask temurin@17

# 2. Install Android command-line tools
brew install --cask android-commandlinetools

# 3. Install SDK components
sdkmanager --sdk_root=$HOME/Library/Android/sdk \
  "platform-tools" "platforms;android-34" "build-tools;34.0.0"

# 4. Set env vars (add to ~/.zshrc for permanence)
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/platform-tools

# 5. Build debug APK
cd "/Users/subhransubaboo/Claude Projects/Boutique Manager/android"
./gradlew assembleDebug

# Output: android/app/build/outputs/apk/debug/app-debug.apk
# Rename and commit: beauty-beads-v1.0.apk
```

---

## 📋 Planned (Backlog)

### High priority
- [ ] **Restore Connect tab** to nav — or decide to remove dead code entirely
- [ ] **API key management** — prompt user to enter key on first launch, store in localStorage (remove hardcoded placeholder)
- [ ] **Auto-backup prompt** — offer to export data to CSV when app opens after 7 days without backup
- [ ] **Production APK signing** — generate release keystore, sign with `assembleRelease`

### Medium priority
- [ ] **Customer notes** — attach a customer name/note to a sale (useful for repeat clients)
- [ ] **Service history per client** — filter sales list by customer name
- [ ] **Daily summary notification** — end-of-day toast or local push with revenue total
- [ ] **Reorder suggestions** — when stock hits low threshold, suggest WhatsApp message to supplier
- [ ] **P&L: expense tracking** — log non-inventory expenses (rent, electricity, consumables)
- [ ] **Receipt printing** — generate a simple text receipt for a sale (share via WhatsApp)

### Low priority / Nice to have
- [ ] **Dark mode** — invert cream/ink for night use
- [ ] **Bulk restock** — import a CSV of stock levels from supplier sheet
- [ ] **Google Pay / UPI deep-link** — tap to open GPay for the exact sale amount
- [ ] **PIN lock** — optional 4-digit PIN on app open
- [ ] **iCloud / Google Drive backup** — export JSON backup to cloud storage
- [ ] **iPad layout** — two-column layout for larger screens

---

## 🐛 Known Issues

| # | Issue | Status |
|---|---|---|
| K1 | Connect tab integrations unreachable from nav | Deferred — needs decision |
| K2 | API key is a blank placeholder — voice fallback won't work until filled | By design for public repo |
| K3 | P&L period filter uses display date string parsing — edge case on year rollover (Dec→Jan) handled, but untested | Low risk |
| K4 | No data recovery if localStorage is cleared | Planned: auto-backup prompt |

---

*Keep this file updated with every session's changes.*
