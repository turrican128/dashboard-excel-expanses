# דשבורד הוצאות — Excel Visual Dashboard

A single-file Hebrew RTL expense dashboard for Bank Leumi credit card exports. No build step, no server, no dependencies to install — just open `dashboard.html` in a browser.

## Features

- **Drag & drop file upload** — supports Bank Leumi `.xls` exports (HTML-as-XLS format) and standard XLSX files
- **Auto-categorization** — 12 categories matched by Hebrew/English keywords (restaurants, fuel, supermarket, online shopping, etc.)
- **Category drill-down** — click any pie slice to see all transactions for that category
- **Filters** — time range pills (all / 6m / 3m / 1m) + category toggle pills
- **Charts** — category donut chart with labels (amount + %)
- **Stats** — total spend, average per transaction, highest single transaction, transaction count
- **Top 10 table** — highest expenses with category badges
- **CSV export** — UTF-8 BOM encoded for Hebrew Excel compatibility
- **Dark theme** — RTL layout, Hebrew UI throughout

## Usage

1. Clone or download the repo
2. Open `dashboard.html` directly in your browser (no server needed)
3. Drag & drop your Bank Leumi `.xls` export onto the upload zone

A sample file is included at `input file/BankLeumi 13_3_2026.xls`.

## Supported File Formats

| Format | Notes |
|---|---|
| Bank Leumi `.xls` | HTML table saved as XLS — detected and parsed automatically |
| Standard `.xlsx` / `.xls` | Real binary Excel files via SheetJS |

## Categories

מסעדות וקפה · דלק ורכב · סופר ומזון · קניות אונליין · אופנה וקניות · בריאות ורפואה · תקשורת · בילוי ופנאי · חשבונות · ביטוח · העברות · חינוך

## Stack

- Vanilla HTML/CSS/JS — no framework
- [Chart.js 4.4](https://www.chartjs.org/) — charts
- [chartjs-plugin-datalabels 2.2](https://chartjs-plugin-datalabels.netlify.app/) — pie slice labels
- [SheetJS/xlsx 0.18.5](https://sheetjs.com/) — XLSX parsing fallback
- [Rubik](https://fonts.google.com/specimen/Rubik) + [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) — fonts (Google CDN)

All dependencies are loaded from CDN — no `npm install` required to run the app.

## Project Structure

```
/
├── dashboard.html          ← The entire app
├── input file/
│   └── BankLeumi 13_3_2026.xls   ← Sample Bank Leumi export
└── docs/
    └── superpowers/specs/  ← Design documents
```
