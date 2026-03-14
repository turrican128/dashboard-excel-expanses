# Excel Visual Dashboard — Project Context

## What This Is
A standalone Hebrew RTL expense dashboard — a single `dashboard.html` file that requires no build step, no server, and no dependencies beyond CDN scripts.

## File Structure
```
/
├── dashboard.html        ← The entire app (open directly in browser)
├── input file/
│   └── BankLeumi 13_3_2026.xls   ← Sample Bank Leumi credit card export
└── node_modules/xlsx/            ← xlsx installed locally for dev/testing only
```

## Key Technical Facts

### Bank Leumi File Format
- Bank Leumi exports are **HTML files saved with a `.xls` extension** — NOT real binary XLS
- The file is UTF-8 encoded HTML with a `<table>` structure
- Must be parsed as HTML (read as text, extract `<td>` cells), NOT via the xlsx library
- The parser (`parseBankLeumiHTML`) detects this automatically by checking if content starts with `<`

### Transaction Structure (Bank Leumi HTML)
The TD cells form a flat sequential list. Each transaction block starts with a date cell:
- **Date format**: `dd/mm/yy` or `dd.mm.yy`
- **Section 1** ("עסקאות אחרונות שטרם נקלטו"): 5 cells → date, time, business, type, amount
- **Section 2** ("עסקאות בש"ח במועד החיוב"): 5–6 cells → date, business, orig_amount, type, [details?], billing_ILS
- **Section 3** ("פירוט עסקאות דחויות"): same as section 2

**Parsing rule**: last numeric cell in each group = "סכום חיוב" = ILS billing amount for the **current month only**.
- For installment transactions ("עסקה בתשלומים"), this is the monthly payment, NOT the original full price
- The group is truncated at "סה"כ:" to avoid picking up section totals as transaction amounts
- User preference: show only current month's installment payment — ignore total/future payments

### Standard XLSX/XLS Fallback
For real binary Excel files, the app falls back to the `xlsx` CDN library with column detection by Hebrew/English header keywords.

## Stack
- **No framework** — vanilla HTML/CSS/JS
- **Chart.js 4.4** (CDN) — bar, doughnut, stacked bar charts
- **SheetJS/xlsx 0.18.5** (CDN) — fallback for real binary Excel files
- **Google Fonts**: Rubik (Hebrew-friendly) + JetBrains Mono (numbers)

## Design System
- **Theme**: Dark (`#070c14` bg), cyan accent (`#00e5cc`), coral for amounts (`#ff5f7e`)
- **RTL**: `dir="rtl"` on `<html>`, Hebrew UI throughout
- **CSS variables** in `:root` — all colors, radii, shadows defined there
- **Animations**: `fadeUp` keyframe with staggered `animation-delay` on cards

## Features
1. Drag & drop file upload (+ click to browse)
2. Auto-detection of HTML-as-XLS vs real XLSX
3. Auto-categorization of transactions by Hebrew/English keyword matching (12 categories)
4. Filter bar: time range pills (all / 6m / 3m / 1m) + category toggle pills
5. Animated stat counters (count-up on load)
6. Monthly bar chart, category donut chart, stacked bar (category × month)
7. Top-10 expenses table with category badges
8. CSV export (UTF-8 BOM for Hebrew Excel compatibility)
9. Clear/reset button

## Categories (12 + "אחר")
מסעדות וקפה · דלק ורכב · סופר ומזון · קניות אונליין · אופנה וקניות · בריאות ורפואה · תקשורת · בילוי ופנאי · חשבונות · ביטוח · העברות · חינוך

## Sample Data Results (BankLeumi 13_3_2026.xls)
- 48 transactions parsed across 3 sections
- Date range: Feb–Mar 2026
- Total: ₪7,920.55
