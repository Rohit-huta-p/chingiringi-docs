# ReferScreen

**Source:** `src/screens/Dashboard/ReferScreen.tsx`

**Route name:** `Referrals`

## Purpose

The referral program screen where users can share their referral code, view earnings, and understand the referral flow.

## Layout

Uses `useWindowDimensions` to detect viewport width. At 768px and above, renders a responsive two-column layout. Below that, columns stack vertically.

### Header

- "GROW TOGETHER" label and "Refer & Earn" title.
- Earned badge (green background) showing Rs.600 earned.

### Left Column

**Referral Code Card** (`Card` component):
- Displays the referral code `DEV500` in a dashed-border box.
- Copy button (styled with `Colors.primary`).
- "SHARE VIA" section with four share option circles: WhatsApp (green), Telegram (blue), SMS (navy), More (gray).
- "Share Referral Link" CTA button.

**Stats Row** -- Two side-by-side stat cards:
- Total: 12 friends joined.
- Earned: Rs.600 from referrals.

### Right Column

**How It Works Card** -- Three numbered steps:
1. Share your code
2. They sign up
3. You earn Rs.50

**Per Referral Info Card** -- Blue background card stating Rs.50 per referral with no earnings limit and 24-hour credit timeline.

**Recent Invites Card** -- Currently shows an empty state with "No invites yet" message and "View All" link.

## State

No component state. No API calls. All data is hardcoded.

## Data

- `REFERRAL_CODE` -- `'DEV500'`
- `SHARE_OPTIONS` -- 4 objects with `label`, `color`, `icon`
- `STEPS` -- 3 objects with `step`, `title`, `description`

## Components Used

- `Card` -- wraps each content section.
- `Button` -- "Share Referral Link" CTA.

## Navigation

- Reachable from sidebar via route name `Referrals`.
- No outbound navigation.
