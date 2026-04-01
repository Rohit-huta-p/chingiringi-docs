# Screens Overview

All dashboard screens live under `src/screens/Dashboard/`. Auth screens live under `src/screens/Auth/`.

## Dashboard Screens

| Screen | File | Description |
|---|---|---|
| Home | `HomeScreen.tsx` | Main discovery page with search, categories, hero banner, trending brands, and deal cards |
| Wallet | `WalletScreen.tsx` | Wallet overview with balance cards (confirmed, pending, coins), lifetime earnings, and transaction list |
| Refer & Earn | `ReferScreen.tsx` | Referral program page with referral code, share options, stats, how-it-works steps, and recent invites |
| Profile | `ProfileScreen.tsx` | User profile with contact info, wallet stats summary, referral program card, quick actions, and recent activity |
| Edit Profile | `EditProfileScreen.tsx` | Form to edit name, email, and phone with avatar upload placeholder |
| My Addresses | `MyAddressScreen.tsx` | List of saved addresses with edit, delete, and set-as-default actions |
| Settings | `SettingsScreen.tsx` | Notification toggles, security options, logout, and account deletion with confirmation modals |
| Transaction History | `TransactionHistoryScreen.tsx` | Filterable transaction list with type and time period filters, summary card, and detailed entries |
| Product Detail | `ProductDetailScreen.tsx` | Deal detail page with image carousel placeholder, cashback/expiry stats, size selector, terms, and shop CTA |

## Auth Screens

| Screen | File | Description |
|---|---|---|
| Login | `LoginScreen.tsx` | Credential-based login form |
| Signup | `SignupScreen.tsx` | New user registration form |
| OTP Verification | `OTPVerificationScreen.tsx` | OTP code input and verification |
| Forgot Password | `ForgotPasswordScreen.tsx` | Email input to request password reset |
| Reset Password | `ResetPasswordScreen.tsx` | New password entry form |
