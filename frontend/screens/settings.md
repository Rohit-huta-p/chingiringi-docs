# SettingsScreen

**Source:** `src/screens/Dashboard/SettingsScreen.tsx`

**Route name:** `Settings`

## Purpose

Account settings screen with notification preferences, security options, logout, and account deletion. Uses confirmation modals for destructive actions.

## Layout Sections

### 1. Header

- Back button (circular, not wired to `goBack`).
- "ACCOUNT" label and "Settings" title.

### 2. Notifications Card

Four toggle switches, each with a title and subtitle:

| Setting | Default | Description |
|---|---|---|
| Cashback Updates | On | Notified when cashback is confirmed |
| Deal Alerts | On | New deals and offers |
| Referral Updates | Off | Updates about referrals |
| Email Notifications | On | Receive updates via email |

### 3. Security & Privacy Card

Three list items with chevron arrows:

- Change Password
- Two-Factor Authentication (shows "Disabled" label)
- Linked Accounts

All items have `onPress` handlers that are currently empty.

### 4. Account Actions Card

- **Logout** -- Red-colored list item. Opens a `ConfirmationModal` with logout confirmation. On confirm, calls `authStore.logout()`.

### 5. Danger Zone Card

Styled with a red-tinted background (`#FEF2F2`) and red border (`#FECACA`).

- Warning text about permanent data deletion.
- "Delete Account" button (red, `Colors.danger`). Opens a `ConfirmationModal` with deletion warning. On confirm, runs `handleDeleteAccount` (currently a no-op placeholder).

## State

| State | Type | Default | Purpose |
|---|---|---|---|
| `cashbackUpdates` | `boolean` | `true` | Notification toggle |
| `dealAlerts` | `boolean` | `true` | Notification toggle |
| `referralUpdates` | `boolean` | `false` | Notification toggle |
| `emailNotifications` | `boolean` | `true` | Notification toggle |
| `logoutModalVisible` | `boolean` | `false` | Controls logout confirmation modal |
| `deleteModalVisible` | `boolean` | `false` | Controls delete confirmation modal |

## Store Usage

- `useAuthStore` -- calls `logout()` after modal confirmation.

## Sub-Components (defined in-file)

- `SectionHeader` -- icon + title row for card sections.
- `ToggleItem` -- title, subtitle, and `Switch` toggle.
- `ListItem` -- icon, title, optional right label, chevron, and `onPress`.

## Components Used

- `Card` -- wraps each settings group.
- `ConfirmationModal` -- used for both logout and delete account confirmations.

## Navigation

- Reachable from sidebar via route name `Settings`.
- Logout triggers auth state change, which swaps the navigator to `AuthNavigator`.
