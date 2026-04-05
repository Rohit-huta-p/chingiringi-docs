# Screens Overview

Screens are organized under `src/screens/` by feature area.

---

## Auth Screens (`src/screens/Auth/`)

| Screen | File | API Wired | Notes |
|---|---|---|---|
| Login | `LoginScreen.tsx` | Yes | Username/password + OTP tab; error UI; redirects to home on success |
| Signup | `SignupScreen.tsx` | Yes | Full registration form; error UI; sets auth on success |
| OTP Verification | `OTPVerificationScreen.tsx` | Yes | 6-digit input; 27s resend timer; max 3 attempts |
| Forgot Password | `ForgotPasswordScreen.tsx` | Yes | Email OTP request; success/error states |
| Reset Password | `ResetPasswordScreen.tsx` | Yes | OTP + new password form |

---

## Dashboard Screens (`src/screens/Dashboard/`)

| Screen | File | API Wired | Notes |
|---|---|---|---|
| Home | `HomeScreen.tsx` | Yes | Deals grid, category pills, trending brands — fetches from API with fallback data |
| Wallet | `WalletScreen.tsx` | Yes | Balance cards (confirmed, pending, coins, lifetime); recent transactions |
| Refer & Earn | `ReferScreen.tsx` | Partial | Referral code from user store; share UI; stats (friends joined, amount) are hardcoded |
| Notifications | `NotificationsScreen.tsx` | No | Empty state placeholder; no backend API yet |
| Settings | `SettingsScreen.tsx` | Partial | Delete account wired; dark mode toggle is UI-only |
| Profile | `ProfileScreen.tsx` | Yes | Fetches from `GET /api/profile`; shows referral code, wallet summary, quick actions |
| Edit Profile | `EditProfileScreen.tsx` | Yes | `GET` + `PUT /api/profile`; name, email, phone fields |
| My Addresses | `MyAddressScreen.tsx` | Yes | Full address list; delete + set default; edit and add buttons navigate to AddEditAddress |
| **Add / Edit Address** | `AddEditAddressScreen.tsx` | **Yes** | Full form — type picker (Home/Work/Other), name, phone, address lines, city, state, 6-digit pincode, default toggle; creates or updates via address API |
| Transaction History | `TransactionHistoryScreen.tsx` | Partial | Filter UI (type + period) exists; list uses hardcoded data — needs wiring to `GET /api/wallet/transactions` |
| Product Detail | `ProductDetailScreen.tsx` | Yes | Full deal detail — cashback stat card, expiry, lock period, terms, "Shop Now" opens `affiliateUrl` via `Linking.openURL` + click tracking |

---

## Admin Screens (`src/screens/Admin/`)

| Screen | File | API Wired | Notes |
|---|---|---|---|
| **Admin Dashboard** | `AdminDashboardScreen.tsx` | **Yes** | 4 stat cards (clicks, conversions, cashback, users); 3 coins economy cards; Revenue Trend placeholder; Top Deals table; Top Users table — from `GET /api/admin/dashboard` |
| **Admin Deals** | `AdminDealsScreen.tsx` | **Yes** | Deals table (title, brand, cashback, status, expiry, actions); mini stats row (total/active/featured); Add/Edit modal with: title, merchant, category dropdown, cashback type selector (% or flat ₹), expiry date, affiliate link, description, image URL, lock period, tags, terms, featured toggle; toggle active/inactive; delete with confirm |
| Admin Conversions | `AdminPlaceholderScreen` | No | Placeholder — awaiting Admitad webhook integration |
| Admin Withdrawals | `AdminPlaceholderScreen` | No | Placeholder — awaiting Razorpay X integration |
| Admin Users | `AdminPlaceholderScreen` | No | Placeholder — backend endpoint `GET /api/admin/users` exists |
| Admin All Products | `AdminPlaceholderScreen` | No | Placeholder — awaiting Module 5 (Store) |
| Admin Categories | `AdminPlaceholderScreen` | No | Placeholder — backend CRUD exists |
| Admin Orders | `AdminPlaceholderScreen` | No | Placeholder — awaiting Module 5 |
| Admin Inventory | `AdminPlaceholderScreen` | No | Placeholder — awaiting Module 5 |
| Admin Banners | `AdminPlaceholderScreen` | No | Placeholder — backend CRUD exists |
| Admin Coupons | `AdminPlaceholderScreen` | No | Placeholder — Phase 3 |

`createAdminPlaceholder(title)` in `AdminPlaceholderScreen.tsx` is a factory function that returns a simple centered "Coming soon" screen. Replace with a real screen component when building that section.

---

## Screen Data Sources

| Screen | Primary Data Source | Fallback |
|---|---|---|
| HomeScreen | `GET /api/deals` + `GET /api/categories` | Hardcoded deal/category arrays |
| WalletScreen | `GET /api/wallet/summary` | Zero balances |
| ProfileScreen | `GET /api/profile` | — |
| MyAddressScreen | `GET /api/addresses` | Hardcoded sample addresses |
| TransactionHistoryScreen | Hardcoded (not yet wired) | — |
| ProductDetailScreen | Route params `deal` object or `GET /api/deals/:id` | — |
| AdminDashboardScreen | `GET /api/admin/dashboard` | Zero/empty fallback values |
| AdminDealsScreen | `GET /api/admin/deals` | Empty state UI |
