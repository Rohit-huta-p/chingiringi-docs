# Development Phases

ChingiRingi development is organized into 4 phases, progressing from core functionality to full platform launch.

## Current State

- **Authentication**: Fully functional (backend + frontend)
- **UI Screens**: 9 dashboard screens built with hardcoded data
- **Backend**: Only auth module exists
- **Completion**: ~10-15% of full PRD scope

---

## Phase 1 — Core Foundation

**Goal**: Make existing screens functional with real data.

**Branch strategy**: Feature branches off `develop`, merged via PR.

### Backend

| Feature | Branch | Status |
|---------|--------|--------|
| Deals API (CRUD, search, categories, banners) | `feature/deals-api` | Done |
| Wallet API (balance, transaction history) | `feature/wallet-api` | Done |
| Profile API (update profile, avatar upload) | `feature/profile-address-api` | Done |
| Address API (CRUD addresses) | `feature/profile-address-api` | Done |

### Frontend

| Feature | Branch | Status |
|---------|--------|--------|
| Wire HomeScreen to deals API | `feature/wire-frontend-apis` | Done |
| Wire WalletScreen to wallet API | `feature/wire-frontend-apis` | Done |
| Wire Profile + EditProfile to API | `feature/wire-frontend-apis` | Done |
| Wire MyAddressScreen to API | `feature/wire-frontend-apis` | Done |
| Wire SettingsScreen (delete account) | `feature/wire-frontend-apis` | Done |
| Mobile responsive layouts (all screens) | `feature/mobile-layouts` | Not started |

### Milestone

Tag `v0.1.0-phase1` on `main` when complete.

---

## Phase 2 — Monetization and Growth

**Goal**: Revenue-generating features and user acquisition.

### Backend

| Feature | Branch | Status |
|---------|--------|--------|
| Referral system API | `feature/referral-api` | Not started |
| Withdrawal API + Razorpay X | `feature/withdrawal-api` | Not started |
| Cashback operations (pending/confirm/reject) | `feature/cashback-ops` | Not started |
| Affiliate tracking (Admitad) | `feature/affiliate-tracking` | Not started |

### Frontend

| Feature | Branch | Status |
|---------|--------|--------|
| Wire ReferScreen to referral API | `feature/wire-refer` | Not started |
| Wire WithdrawModal to withdrawal API | `feature/wire-withdraw` | Not started |
| Wire TransactionHistory to real data | `feature/wire-transactions` | Not started |
| Notifications system (push + in-app) | `feature/notifications` | Not started |
| Build NotificationsScreen | `feature/notifications-screen` | Not started |

### Milestone

Tag `v0.2.0-phase2` on `main` when complete.

---

## Phase 3 — In-App Store and Integrations

**Goal**: Complete consumer experience with commerce.

| Feature | Branch | Status |
|---------|--------|--------|
| In-app store (products, cart, checkout) | `feature/store` | Not started |
| Order history and tracking | `feature/orders` | Not started |
| Google OAuth login | `feature/google-auth` | Not started |
| MSG91 OTP integration | `feature/msg91` | Not started |
| QR merchant module (scan-to-earn) | `feature/qr-merchant` | Not started |
| Razorpay payment gateway | `feature/razorpay` | Not started |

### Milestone

Tag `v0.3.0-phase3` on `main` when complete.

---

## Phase 4 — Admin Panel and Launch

**Goal**: Business operations tooling and production readiness.

| Feature | Branch | Status |
|---------|--------|--------|
| Admin dashboard (KPIs, user management) | `feature/admin-dashboard` | Not started |
| Deal management admin | `feature/admin-deals` | Not started |
| Banner and coupon management | `feature/admin-banners` | Not started |
| Store and order management | `feature/admin-store` | Not started |
| Analytics and reporting | `feature/analytics` | Not started |
| CI/CD and production deployment | `feature/devops` | Not started |

### Milestone

Tag `v1.0.0` on `main` — production launch.

---

## Git Workflow

```
main (stable, tagged releases)
  └── develop (integration branch)
       ├── feature/deals-api
       ├── feature/wallet-api
       ├── feature/wire-home
       └── ...
```

1. Create feature branch from `develop`
2. Build and test the feature
3. Open PR to `develop`
4. After review, merge to `develop`
5. When phase is complete, merge `develop` to `main` and tag the release
