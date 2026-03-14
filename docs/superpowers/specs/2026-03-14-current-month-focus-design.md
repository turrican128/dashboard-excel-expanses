# Design: Current Month Focus

**Date:** 2026-03-14
**Status:** Approved

## Summary

Refocus the dashboard from multi-month historical analysis to a single-month view. Add a new "חודש נוכחי" filter mode that shows only the latest calendar month present in the uploaded data. Remove the stacked bar chart. Replace the monthly bar chart with a per-category bar chart. Replace the "monthly average" stat with "daily average".

## Changes

### 1. New Default Filter Mode: "חודש נוכחי" (`'cm'`)

Add a new filter value `'cm'` (current month) to the filter system.

**HTML**: Add a new pill button labeled "חודש נוכחי" as the **first child in DOM order** of the `.filter-group` (which renders as the rightmost pill in RTL layout). Give it `class="pill active"` and `data-range="cm"`. Remove the `active` class from the existing "הכל" pill.

**`filtered()` function**: Add a case for `'cm'`:
```js
if (activeRange === 'cm') {
  const latestMonth = [...new Set(ALL_TXS.map(t => monthKey(t.date)))].sort().at(-1);
  txs = txs.filter(t => monthKey(t.date) === latestMonth);
}
```
`latestMonth` is the lexicographically greatest `monthKey` string in `ALL_TXS` (e.g., `"2026-03"`).

**`activeRange` default**: Change initial declaration from `'all'` to `'cm'`.

**`clearData()` reset**: Update `activeRange = 'all'` → `activeRange = 'cm'`. The index-0 pill reset already works since "חודש נוכחי" is first in DOM.

### 2. Stats — Replace "ממוצע חודשי" with "ממוצע יומי"

- **Label**: `ממוצע יומי`
- **Icon**: keep `📅` (no change)
- **Value**: `total ÷ calcDaysInPeriod(txs)` — display as `₪` amount; if `calcDaysInPeriod` returns 0 show `—`

**`calcDaysInPeriod(txs)` helper**:
```js
function calcDaysInPeriod(txs) {
  if (!txs.length) return 0;
  const months = [...new Set(txs.map(t => monthKey(t.date)))];
  return months.reduce((sum, mk) => {
    const [y, m] = mk.split('-').map(Number);
    return sum + new Date(y, m, 0).getDate(); // day 0 of next month = last day of mk
  }, 0);
}
```
This sums the **full calendar days** of each distinct month represented in the filtered transactions. For a single month like `"2026-03"` it returns 31. For a range straddling Feb+Mar it returns 28+31=59. This is consistent across all filter modes.

### 3. Replace Monthly Bar Chart with Category Bar Chart

- **Reuse** canvas `monthlyChart`
- **Type**: horizontal bar (`indexAxis: 'y'`)
- **Data**: one bar per category where total spend > 0 in the filtered period (exclude zero-spend categories)
- **Sort**: descending by total amount (largest bar at top)
- **Colors**: each bar uses the category's existing `color` property
- **Title element**: change inner text from "הוצאות חודשיות" to "הוצאות לפי קטגוריה"
- **`monthlyMeta` span**: set to empty string (it remains in DOM but shows nothing)
- **`renderMonthly(txs, months)` signature**: change to `renderCategoryBar(txs)` — remove `months` parameter since it's no longer needed

### 4. Remove Stacked Bar Chart

- Remove the entire `<div class="card">` that wraps `<canvas id="stackedChart">` from HTML
- Remove `renderStacked(txs, months)` call from `renderAll()`
- Remove the `renderStacked()` function definition entirely
- In `renderAll()`, remove the `months` variable computation if it is only used by `renderStacked` and `renderMonthly` (after renaming to `renderCategoryBar`, `months` is no longer needed at call site)

## What Does NOT Change

- Installment handling: parser already takes monthly billing amount (last numeric cell) — no change needed
- Category donut chart
- Top-10 expenses table
- CSV export
- Filter pills for הכל / 6m / 3m / 1m (kept for flexibility, just no longer default)
- All other stats (סה"כ, מקסימלי, עסקאות)
- Dark theme, RTL layout, animations

## Files Modified

- `dashboard.html` — all changes in a single file (JS logic + HTML structure)
