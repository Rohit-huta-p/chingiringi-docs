# HomeScreen

**Source:** `src/screens/Dashboard/HomeScreen.tsx`

**Route name:** `Home`

## Purpose

The main discovery screen for browsing cashback deals and brands. It is the initial route of the drawer navigator and is labeled "Discover" in the sidebar.

## Layout Sections

### 1. Header

- Search bar with a `Search` icon (Lucide) and text input for filtering deals/brands.
- "Today's Deals" button with a `Zap` icon.
- Filter icon button (`SlidersHorizontal` from Lucide).
- User avatar image (loaded from `pravatar.cc`).

### 2. Category Pills

Horizontal scrollable row of category filter pills: All, Fashion, Electronics, Home, Pharmacy, Travel, Food. The first pill ("All") is rendered in the active style by default. Selection logic is visual only (no filtering state).

### 3. Hero Section

A two-column flex layout:

- **Main card** (flex: 2) -- Dark background (`#0f172a`), "FEATURED" badge, headline promoting up to 20% cashback on fashion brands, "Explore Fashion" CTA button.
- **Side column** (flex: 1) -- Two stacked cards:
  - Live Deals count card (dark background, shows "8+").
  - Brand spotlight card (Myntra, Electronics Sale, 15% cashback).

### 4. Trending Now

Horizontal scrollable row of brand circles. Each shows the brand's first letter, a cashback badge (orange), brand name, and category label. Data from the `TRENDING_BRANDS` array (5 items: Myntra, Amazon, Swiggy, Nykaa, Campus Sutra).

### 5. All Deals Grid

A flex-wrap row of `DealCard` components. Each card shows brand name, deal description, cashback percentage, and expiry time. Data from the `ALL_DEALS` array (8 items).

## Data

All data is hardcoded in module-level constants:

- `CATEGORIES` -- 7 category strings
- `TRENDING_BRANDS` -- 5 objects with `name`, `category`, `cashback`
- `ALL_DEALS` -- 8 objects with `id`, `brand`, `description`, `cashback`, `expiresIn`

## State

No component state. No API calls. Purely presentational.

## Navigation

- Sidebar navigates here via route name `Home`.
- No outbound navigation from this screen (deal cards are touchable but have no `onPress` handler wired to navigation).

## Components Used

- `DealCard` -- renders each deal in the grid.
- Lucide icons: `Search`, `Zap`, `SlidersHorizontal`.
