---
name: travel-expense
description: |
  Use when processing travel credit card PDF statements into an Excel tracker.
  Guides a 5-phase workflow: extract, categorize, summarize, and reconcile
  multi-card transactions into a per-trip expense tab.
model: sonnet
allowed-tools:
  - Read
  - Write
  - WebSearch
user-invokable: true
argument-hint: "[trip name or folder path]"
tags:
  - type:workflow
  - tool:excel
category: content
version: 1.0.0
license: MIT
author: flyjim1969
---

# Travel Expense Categorization Skill

You are helping the user extract, categorize, and summarize credit card transactions from PDF statements into an Excel-based travel expense tracker (`Travel.xlsx`). The workbook has one tab per trip. Follow the phases below **in order, confirming with the user after each phase before proceeding**.

---

## Phase 1 — Learn the Excel Template

1. Open `Travel.xlsx` and read all sheet (tab) names.
2. Note the **tab naming pattern**: `[Destination][MonthAbbreviation][Year]`
   - Examples: `UTMarch21`, `SpainMarch23`, `JapanOct24`
   - Compound destinations use `+` or `-` as separators
   - Year is typically 2-digit (21, 22, 23…)
3. Read the column headers from the most recent 2–3 tabs to identify the active category set.
4. Note which trip tab you are working on (it usually already exists as a planning skeleton).
5. **Confirm findings with user before proceeding.**

### Standard Categories (adapt per trip as needed)

| Column | Category |
|--------|----------|
| Hotel | Cost (Hotel) |
| Flights | Cost (flight or Jet) |
| Groceries | Cost (grocery) |
| Restaurants / food | Cost (meal) |
| Gas | Cost (gas) |
| Tolls | Cost (toll) |
| Souvenirs / gifts | Cost (souvenir) |
| Museum / attraction entry | Cost (entrance) |
| Guided tours | Cost (tours) |
| Train / bus / metro | Cost (train/transit) |
| Taxi / rideshare | Cost (taxi) |
| Ferry / boat | Cost (ferry) |
| Car rental | Cost (car rental) |
| Parking | Cost (parking) |

---

## Phase 2 — Extract Transactions from PDFs

1. Identify all PDF files in the folder — there may be **multiple credit cards**.
2. Ask user to confirm the **trip date range** before reading (add 1–2 days buffer for late postings).
3. For each PDF, extract: `Transaction Date | Merchant | Amount (USD)`.
4. Flag and present to the user:
   - Transactions clearly **outside** the trip (domestic charges before departure or after return)
   - **Credits / refunds** that offset a charge — note the net amount
   - Items that look like **personal subscriptions** (streaming, pharmacy auto-ship, software)
5. **Do not proceed to Phase 3 until user confirms the transaction list.**

### Common Exclusions
- Amazon / pharmacy subscription auto-charges
- Streaming or software subscriptions
- Credit card annual fees
- Domestic restaurant/retail charges clearly outside the trip window
- Government fees unrelated to travel (vehicle registration, etc.)

### Handling Credits & Refunds

| Situation | How to handle |
|-----------|---------------|
| Charge + same-amount same-day refund | Net = $0 — exclude both |
| Flight changed (charge + larger refund) | Record net as credit against airfare category |
| Hotel partial refund on separate date | Net the two amounts; record net in hotel category |

---

## Phase 3 — Categorize

1. Map each transaction to a category from the list above.
2. For **unclear merchants**, web-search the merchant name + city to identify the business type.
3. After searching, collect **all transactions still uncertain** and present them to the user **in one batch** — do not ask one at a time.
4. Wait for user decisions before finalizing the category map.
5. Build the final categorized totals.

### Merchant Research Tips

**Payment processors / prefixes (don't reveal the merchant):**
- `CCV*` prefix = Dutch/European card payment processor
- `BKG*` prefix = Booking.com charge
- `SQ *` prefix = Square payment terminal

**Transit:**
- `ENTUR` / `Ruter` / `Skyss` / `Vy` = Norwegian public transit or rail
- `OVPay` / `OVPAY.NL` = Dutch OV-chipkaart (train/bus/tram)
- `DSB` = Danish State Railways
- `Renfe` = Spanish rail
- `SNCF` = French rail
- `Bolt.eu` = Bolt rideshare/taxi app

**Tours & activities:**
- `GetYourGuide` = tour and activity booking platform
- `Viator` = tour and activity booking platform
- `Klook` = Asia-focused activity bookings

**Ferries:**
- `Norled` = Norwegian ferry operator (fjords/coast)
- `Stena Line` = Scandinavian/UK ferry operator

**Airports:**
- `HMS Host` = airport food/restaurant operator
- `SSP` / `Select Service Partner` = airport food operator

**European grocery chains:**
- Netherlands: Albert Heijn, Plus, Coop, Dirk
- Norway: Rema 1000, Kiwi, Meny, Coop
- Germany: Rewe, Edeka, Aldi, Lidl
- Spain: Mercadona, Carrefour, Dia
- France: Carrefour, Monoprix, Leclerc

---

## Phase 4 — Write the Summary Tab

1. **Do not modify any existing tabs.**
2. Create a **new tab** named following the Phase 1 naming pattern, with `_exp` suffix to distinguish from the planning tab.
   - Example: `SpainMarch23_exp`
3. Write two sections:

### Section 1 — Totals by Category
Simple table: `Category | Total (USD)` — end with a **GRAND TOTAL** row.

### Section 2 — Itemized by Category
For each category:
- Category name as a bold header
- Column headers: `Date | Merchant | Card | Amount (USD)`
- One row per transaction
- Subtotal row at the bottom of each category

4. Include a title row with trip name and date range.
5. Note which card each charge came from (useful for reconciliation).
6. Write dates as text (MM/DD) to avoid Excel auto-converting to date serials.

---

## Phase 5 — Verify All Transactions Are Accounted For

After writing the summary tab, confirm every line item on every PDF is either included or explicitly excluded.

### Steps

1. For each statement, locate the **total purchases figure** on the summary page (e.g., "Purchases and Adjustments" or "Purchases, Balance Transfers & Other Charges").
2. Sum all transactions **included** in the summary tab from that card.
3. Sum all transactions **excluded** (non-trip charges, cancelled/refunded pairs).
4. Verify: `Included + Excluded = Statement Total`
5. Report the breakdown to the user in a table.

### Example Verification Table

| Card | Statement Total | Included | Excluded | Check |
|------|----------------|----------|----------|-------|
| Chase ...XXXX | $3,200.00 | $3,050.00 | $150.00 | ✅ |
| BofA ...XXXX | $1,800.00 | $1,750.00 | $50.00 | ✅ |

> If the numbers do not add up, re-read the statement page by page to find the missing transaction.

---

## Adding New Statements to a Completed Trip

If additional statements arrive after the `_exp` tab is already created and reconciled:

1. **Extract** transactions from the new PDF(s) — same as Phase 2. Flag exclusions and credits.
2. **Confirm** the transaction list with the user before categorizing.
3. **Categorize** — same as Phase 3. Batch unclear merchants for user decision.
4. **Update the existing `_exp` tab** (do NOT create a new tab):
   - Update the card header row to add the new card name and last 4 digits.
   - In **Section 1**, update totals for affected categories and the Grand Total. Add a new category row if needed.
   - In **Section 2**, insert new rows into each relevant category block (above the Subtotal row) and update the Subtotal.
5. **Re-verify** — confirm: `Included + Excluded = Statement Total` for each new statement.

### Key Rules
- Work **bottom to top** when inserting rows in Section 2 so earlier row numbers stay stable during edits.
- If a **new category** is needed, add it to both Section 1 (before Grand Total) and Section 2 (new block at the bottom).
- **Net credits/rebates** against the relevant charge where possible; note the netting in the merchant description.
