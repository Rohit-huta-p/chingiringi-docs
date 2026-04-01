# ProductDetailScreen

**Source:** `src/screens/Dashboard/ProductDetailScreen.tsx`

**Route name:** `ProductDetail`

## Purpose

Displays detailed information about a specific cashback deal, including product stats, available sizes, terms, and a call-to-action to shop and earn cashback.

## Layout Sections

### 1. Product Image Area

- Placeholder container (300px height, gray background).
- Left/right navigation arrows for an image carousel (not yet functional).
- Share button (top-right corner).

### 2. Category Tags

Two pill-shaped tags:
- "Fashion" -- blue outline and text.
- "Active" -- orange outline and text.

### 3. Title

- Product title: "Flat 50% Off on Top Brands".
- Subtitle: "at Myntra".

### 4. Stat Cards Row

Two side-by-side `Card` components:

| Stat | Icon | Label | Value | Detail |
|---|---|---|---|---|
| Cashback | `%` | CASHBACK | 12% | On every purchase |
| Expires | Clock | EXPIRES | 3 days | Remaining |

### 5. Lock Period Card

Green-tinted card (`#ecfdf5`) explaining the 45-day lock period for cashback confirmation covering returns and cancellations.

### 6. Available Sizes

A `Card` with a row of circular size pills: 30, 32, 34, 36. Tapping a pill sets it as the selected size (primary blue background). Selection is tracked in component state.

### 7. Terms & Conditions

A `Card` listing three bullet-pointed terms:
- Max cashback 500
- Valid only for new users
- Cashback tracks in 48 hours

### 8. Call to Action

- "Shop Now & Earn Cashback" button (full width, using `Button` component). Not yet functional.
- Helper text: "You'll be tracked and cashback credited automatically".

## State

| State | Type | Default | Purpose |
|---|---|---|---|
| `selectedSize` | `string \| null` | `null` | Currently selected size pill |

## Data

- `SIZES` -- array of 4 size strings.
- `TERMS` -- array of 3 term strings.

All deal data (title, brand, cashback percentage, expiry) is hardcoded inline.

## Navigation

- **Inbound:** Registered as `ProductDetail` route in the drawer navigator. Currently no screen navigates here programmatically.
- **Outbound:** No outbound navigation. The screen does not pass or receive route params.

## Components Used

- `Card` -- wraps stat cards, sizes section, and terms section.
- `Button` -- "Shop Now" CTA.
