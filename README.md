# Claude Travel Expense Skill

A Claude Code slash command that guides you through extracting, categorizing, and summarizing credit card transactions from PDF statements into an Excel travel expense tracker.

## What It Does

Invoke `/travel-expense` at the start of a session and Claude will walk you through 5 phases:

1. **Learn your Excel template** — reads your workbook structure and tab naming pattern
2. **Extract transactions** — pulls every charge from your PDF statements, flags exclusions and credits
3. **Categorize** — maps merchants to categories, batches unclear ones for your review
4. **Write the summary tab** — creates a `_exp` tab with totals by category + full itemized list
5. **Verify** — confirms every statement line is accounted for (included + excluded = statement total)

Also handles **adding new statements to a completed trip** without breaking what's already reconciled.

## Setup

1. Copy `travel-expense.md` into your project's `.claude/commands/` folder:
   ```
   your-project/
   └── .claude/
       └── commands/
           └── travel-expense.md
   ```
2. Open Claude Code in that project folder
3. Run `/travel-expense`

## What You Need

- `Travel.xlsx` — your expense tracker (one tab per trip)
- Credit card statements as PDF files in the same folder
- Claude Code ([claude.ai/code](https://claude.ai/code))

## Category Coverage

Hotels, flights, groceries, restaurants, gas, tolls, souvenirs, museums, tours, transit, taxi, ferry, car rental, parking — adaptable per trip.

Includes merchant lookup tips for common European payment processors, transit operators, and supermarket chains.
