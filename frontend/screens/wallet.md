# WalletScreen

**Source:** `src/screens/Dashboard/WalletScreen.tsx`

**Route name:** `Wallet`

## Purpose

Displays the user's wallet balances and recent transaction history. Accessible from the sidebar.

## Layout Sections

### 1. Header

- "OVERVIEW" label and "My Wallet" title.
- User name (from auth store, falls back to "Dev Chavan") and "Member" role badge.
- Avatar image.

### 2. Balance Cards Row

Three cards in a horizontal flex row:

| Card | Background | Key Value | Detail |
|---|---|---|---|
| Confirmed Balance | `#3b82f6` (primary blue) | Rs.1250 | "Available to withdraw" + "Withdraw Funds" button |
| Pending | White with border | Rs.450 | "In lock period" |
| Coins | White with border | 840 | "Reward points" |

### 3. Total Earned Row

A single-row card showing lifetime earnings (Rs.1700) with a trend icon and "All time" link.

### 4. Transaction History

- Section title with filter pills: All, Cashback, Coins, Withdrawals.
- Active filter is tracked via `useState` (default: "All").
- Transaction list with 5 hardcoded entries. Each row shows:
  - Direction icon (green down-arrow for income, red up-arrow for withdrawal).
  - Brand/description and time ago.
  - Signed amount in green (income) or red (withdrawal).

## State

| State | Type | Default | Purpose |
|---|---|---|---|
| `activeFilter` | `string` | `'All'` | Selected transaction filter tab |

## Data

- `FILTER_TABS` -- 4 filter label strings.
- `TRANSACTIONS` -- 5 objects with `id`, `brand`, `timeAgo`, `amount`, `type` (income or withdrawal).

All data is hardcoded. No API calls.

## Store Usage

- `useAuthStore` -- reads `user?.name` for the header display.

## Navigation

- Reachable from sidebar via route name `Wallet`.
- No outbound navigation links.
