# TransactionHistoryScreen

**Source:** `src/screens/Dashboard/TransactionHistoryScreen.tsx`

**Route name:** `TransactionHistory`

## Purpose

A dedicated screen for viewing and filtering the full transaction history with more detail than the wallet's inline transaction list.

## Layout Sections

### 1. Header

- Back button (calls `navigation.goBack()`).
- "ACCOUNT" label and "Transaction History" title.
- "Export" button (top-right, not yet functional).

### 2. Filters Section

A white card with two filter groups:

**Transaction Type** -- Pill-row selector with 5 options: All, Cashback, Withdrawal, Coins, Referral. Default active: `Cashback`.

**Time Period** -- Pill-row selector with 4 options: All Time, Last 7 Days, Last 30 Days, Last 90 Days. Default active: `All Time`.

Active pills use `Colors.primary` background with white text.

### 3. Summary Card

Green background card (`#10b981`) showing total net amount (Rs.450) and transaction count.

### 4. Transaction List

White card listing transaction entries. Each entry shows:

- Direction icon (green for income, red for withdrawal) in a colored container.
- Brand name, category, and time ago.
- Purchase amount.
- Cashback amount (green for income, red for withdrawal).
- Date string.
- Status badge: "Confirmed" (green tint) or "Pending" (amber tint).

## State

| State | Type | Default | Purpose |
|---|---|---|---|
| `activeType` | `string` | `'Cashback'` | Selected transaction type filter |
| `activePeriod` | `string` | `'All Time'` | Selected time period filter |

Filters update the active pill visually but do not yet filter the transaction list.

## Data

- `TRANSACTION_TYPES` -- 5 filter strings.
- `TIME_PERIODS` -- 4 period strings.
- `TRANSACTIONS` -- 2 hardcoded transaction objects with fields: `id`, `brand`, `category`, `timeAgo`, `purchaseAmount`, `cashbackAmount`, `date`, `status`, `type`.

## Navigation

- **Inbound:** Navigated to from wallet or profile screens.
- **Outbound:** Back button calls `navigation.goBack()`.
