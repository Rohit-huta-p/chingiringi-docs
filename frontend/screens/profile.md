# ProfileScreen

**Source:** `src/screens/Dashboard/ProfileScreen.tsx`

**Route name:** `Profile`

## Purpose

Displays the user's profile information, wallet summary, referral stats, quick actions, and recent transaction activity. Acts as the central hub for account-related screens.

## Layout Sections

### 1. Profile Header Card

- Centered avatar placeholder (gray circle) with camera icon overlay.
- User name ("Dev Chavan") and member-since year ("2024").
- Contact details: email, phone, and location with icon prefixes.
- "Edit Profile" link that navigates to `EditProfile`.

### 2. Wallet Stats Row

Three horizontal stat cards, each with a colored top accent bar:

| Stat | Amount | Accent Color | Link Label |
|---|---|---|---|
| CONFIRMED | Rs.1250 | `#10b981` (green) | Withdraw |
| PENDING | Rs.450 | `#f59e0b` (amber) | Processing |
| COINS | 840 | `#8b5cf6` (purple) | Redeem |

### 3. Referral Program Card

Dark background card (`#1e293b`) showing:
- Title and subtitle ("Earn Rs.50 for every friend").
- Three inline stats: Total Referred (12), Earnings (Rs.600), Your Code (DEV500).
- "View Details" link.

### 4. Quick Actions

A list card with three tappable rows: "Manage Addresses", "Withdraw Money", "Account Settings". Each has a chevron arrow.

### 5. Recent Activity

Header with "View all" link. Three transaction entries showing store name, time ago, amount, and a status badge (pending/confirmed/completed).

## State

No component state. All data is hardcoded via module-level constants.

## Data

- `PROFILE_DATA` -- name, memberSince, email, phone, location
- `WALLET_STATS` -- 3 stat objects
- `QUICK_ACTIONS` -- 3 action label strings
- `RECENT_ACTIVITY` -- 3 transaction objects
- `REFERRAL_DATA` -- totalReferred, earnings, code

## Navigation

- **Inbound:** Sidebar profile card at the bottom navigates here.
- **Outbound:** "Edit Profile" link navigates to `EditProfile` via `navigation.navigate('EditProfile')`.

## Components Used

- `Card` -- wraps profile info, wallet stats, quick actions, and activity sections.
