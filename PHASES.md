# Development Phases

ChingiRingi development is organized into 4 phases, progressing from core authentication through a full cashback and commerce platform.

---

## Implementation Progress (April 6, 2026)

| Layer | What's Live |
|---|---|
| **Auth** | Email/password + OTP flows, token refresh, logout — fully wired on web (cookies) and native (Bearer + SecureStore) |
| **Deal Discovery** | Deals API with search/filter/pagination; HomeScreen, ProductDetailScreen with working "Shop Now" redirect + click tracking |
| **Wallet** | Read-only wallet dashboard + transaction history (APIs + UI both live) |
| **Profile & Addresses** | Full CRUD — profile, addresses with Add/Edit Address screen (type picker, pincode validation, default toggle) |
| **Admin Panel** | Role-based routing; AdminDashboardScreen (KPIs, coins, top deals/users); AdminDealsScreen (full CRUD + modal with cashback type & category dropdown); responsive mobile/web admin nav |
| **Native Android** | TurboModule crash fixed; auto-detect LAN IP in Expo Go; no hardcoded IP needed |

**Overall: ~40% of full PRD scope complete.**

---

## Phase 1 — Core Foundation ✅ Mostly Complete

**Goal**: All screens wired to live backend APIs. No hardcoded data for core user flows.

**Branch**: Merged into `develop` — pending final items before `v0.1.0` tag on `main`.

### Backend ✅

| Feature | Branch | Status |
|---|---|---|
| Deals API (CRUD, search, categories, banners, click tracking) | `feature/deals-api` | ✅ Done |
| Wallet API (balance, transaction history, summary) | `feature/wallet-api` | ✅ Done |
| Profile API (view, update, delete account) | `feature/profile-address-api` | ✅ Done |
| Address API (CRUD, set default) | `feature/profile-address-api` | ✅ Done |
| Admin API (dashboard stats, user list, deal list) | `develop` | ✅ Done |
| Bearer token auth for native apps (alongside cookie auth for web) | `develop` | ✅ Done |

### Frontend ✅

| Feature | Branch | Status |
|---|---|---|
| HomeScreen → deals/categories API | `feature/wire-frontend-apis` | ✅ Done |
| WalletScreen → wallet/summary API | `feature/wire-frontend-apis` | ✅ Done |
| Profile + EditProfile → profile API | `feature/wire-frontend-apis` | ✅ Done |
| MyAddressScreen → address API | `feature/wire-frontend-apis` | ✅ Done |
| SettingsScreen → delete account | `feature/wire-frontend-apis` | ✅ Done |
| Mobile responsive layout (bottom tab navigator) | `feature/mobile-layouts` | ✅ Done |
| Add/Edit Address screen (full form, type picker, validation) | `develop` | ✅ Done |
| ProductDetailScreen "Shop Now" → `Linking.openURL` + click tracker | `develop` | ✅ Done |
| Admin panel: Dashboard, Deals CRUD, Sidebar, role-based routing | `develop` | ✅ Done |
| TurboModule crash fix (lazy-load reanimated, web only) | `develop` | ✅ Done |
| Auto-detect LAN IP from `Constants.expoGoConfig?.debuggerHost` | `develop` | ✅ Done |
| Dual auth: cookies (web) + `expo-secure-store` Bearer tokens (native) | `develop` | ✅ Done |
| Admin API wrappers standardized (no raw `apiClient` calls in screens) | `develop` | ✅ Done |

### Still Pending in Phase 1

| Item | Notes |
|---|---|
| **MSG91 SMS OTP delivery** | ⚠️ OTP logged to console only — blocks production sign up/login via phone |
| **Seed real deal data** | Admin deal form is ready; Admitad deal URLs need to be entered |
| **HomeScreen banners carousel** | `bannersAPI` + backend CRUD exist; no UI renders banners yet |
| **Admin User Management screen** | `GET /api/admin/users` endpoint exists; only a placeholder screen |
| **Admin Banner Management screen** | Backend CRUD ready; placeholder only |
| **Admin Category Management screen** | Backend CRUD ready; placeholder only |
| **TransactionHistoryScreen real data** | Still uses hardcoded mock transactions |
| **ReferScreen stats** | "Friends joined" and "amount earned" are hardcoded strings |
| **Sidebar wallet balance** | Shows static `₹1250` — should pull from wallet store/API |

### Phase 1 Milestone

Tag `v0.1.0-phase1` on `main` once:
- MSG91 integrated (OTP delivery working end-to-end)
- Real deal data seeded via the admin panel
- OTP console logging removed (security)
- Weak MongoDB password rotated (security)

---

## Phase 2 — Monetization & Growth 🔜 Next

**Goal**: Users earn real cashback and can withdraw money. Referrals drive organic signup growth.

**Timeline**: Phase 1 completion + 6–8 weeks

### Backend

| Feature | Branch | Status |
|---|---|---|
| MSG91 OTP SMS delivery | `feature/msg91` | Not started |
| Admitad conversion webhook (`POST /api/webhooks/admitad`) | `feature/admitad` | Not started |
| Cashback state machine (pending → confirmed → withdrawable + lock period enforcement) | `feature/cashback-ops` | Not started |
| Withdrawal request API (`POST /api/withdrawals`) | `feature/withdrawal-api` | Not started |
| Razorpay X payout integration | `feature/withdrawal-api` | Not started |
| Withdrawal status tracking (requested → approved → processing → completed) | `feature/withdrawal-api` | Not started |
| Referral reward trigger (on referee's first confirmed purchase) | `feature/referral-api` | Not started |
| Referral stats endpoint (`GET /api/referrals/stats`) | `feature/referral-api` | Not started |
| Push notification service (Expo Notifications + FCM) | `feature/notifications` | Not started |
| Notification model + API (`GET /api/notifications`) | `feature/notifications` | Not started |

### Frontend

| Feature | Branch | Status |
|---|---|---|
| HomeScreen banners carousel (wire to `GET /api/banners`) | `feature/home-banners` | Not started |
| TransactionHistoryScreen → real `GET /api/wallet/transactions` | `feature/wire-transactions` | Not started |
| ReferScreen stats → referral stats API | `feature/wire-refer` | Not started |
| Sidebar wallet balance → pull from wallet store | `feature/sidebar-live-data` | Not started |
| Withdrawal flow UI (select amount, UPI/bank method, status timeline) | `feature/wire-withdraw` | Not started |
| Cashback status badges on transactions (pending/confirmed/withdrawable) | `feature/cashback-ui` | Not started |
| Push notification permission request + handler | `feature/notifications` | Not started |
| NotificationsScreen (feed with read/unread states, grouped by type) | `feature/notifications-screen` | Not started |
| Admin Conversions screen (Admitad conversion rows — approve/reject/hold) | `feature/admin-conversions` | Not started |
| Admin Withdrawals screen (queue + approve/reject) | `feature/admin-withdrawals` | Not started |
| Admin User Management screen (wire to existing `GET /api/admin/users`) | `feature/admin-users` | Not started |
| Admin Banner Management screen | `feature/admin-banners` | Not started |
| Admin Category Management screen | `feature/admin-categories` | Not started |

### Phase 2 Success Gate → Phase 3

- 10,000 registered users
- ₹5L+ total cashback confirmed across users
- 50+ active deals live across ≥ 5 categories
- First 100 withdrawals successfully processed via Razorpay X
- Admin panel fully operational (no placeholder screens remaining)

### Phase 2 Milestone

Tag `v0.2.0-phase2` on `main` when complete.

---

## Phase 3 — In-App Store & Ecosystem 🔮 Future

**Goal**: Complete consumer experience with in-app store, coins economy, QR offline merchants, and social login.

**Timeline**: Phase 2 + 8–12 weeks

### Backend

| Feature | Branch | Status |
|---|---|---|
| Product catalog API (CRUD, categories, variants, inventory) | `feature/store` | Not started |
| Shopping cart API (persistent, per-user) | `feature/store` | Not started |
| Checkout API (full coins, full Razorpay, or mixed payment) | `feature/store` | Not started |
| Order management API (create, status updates, tracking) | `feature/orders` | Not started |
| Coins earn engine (QR scans, activities, referral bonuses) | `feature/coins-engine` | Not started |
| Coins spend engine (deduct at store checkout) | `feature/coins-engine` | Not started |
| QR merchant registration + KYC-light workflow | `feature/qr-merchant` | Not started |
| QR scan-to-earn API (cooldown anti-fraud, merchant validation) | `feature/qr-merchant` | Not started |
| Google OAuth 2.0 integration | `feature/google-auth` | Not started |
| Email notifications (Nodemailer / SendGrid) for web users | `feature/email-notifications` | Not started |
| Coupon creation + redemption API | `feature/coupons` | Not started |

### Frontend

| Feature | Branch | Status |
|---|---|---|
| In-app store: product catalog, filter, search | `feature/store-ui` | Not started |
| Shopping cart + quantity management | `feature/store-ui` | Not started |
| Checkout flow (payment method selector, coupon input, order confirm) | `feature/store-ui` | Not started |
| Order history + tracking screen | `feature/orders-ui` | Not started |
| QR scanner screen (camera-based, in-app) | `feature/qr-ui` | Not started |
| Coins balance display in-store (₹ vs coins pricing) | `feature/store-ui` | Not started |
| Admin: All Products screen (product CRUD) | `feature/admin-store` | Not started |
| Admin: Categories screen (category CRUD with icon + sort order) | `feature/admin-store` | Not started |
| Admin: Orders screen (view + update status + tracking) | `feature/admin-store` | Not started |
| Admin: Inventory screen (stock levels, low-stock alerts) | `feature/admin-store` | Not started |
| Admin: Banners screen (upload, schedule, priority) | `feature/admin-banners` | Not started |
| Admin: Coupons screen (coupon CRUD, usage tracking) | `feature/admin-coupons` | Not started |
| Admin: QR Merchant Management screen | `feature/admin-qr` | Not started |
| Google OAuth login button (wired) | `feature/google-auth` | Not started |
| Dark mode (toggle exists in Settings but is not functional) | `feature/dark-mode` | Not started |
| Hindi localization | `feature/i18n` | Not started |

### Phase 3 Success Gate → Launch

- 100,000 registered users
- 5,000+ active QR merchants in top 10 Indian cities
- In-app store GMV > ₹10L / month
- Coins economy earn/burn ratio healthy and sustainable

### Phase 3 Milestone

Tag `v0.3.0-phase3` on `main` when complete.

---

## Phase 4 — Production Hardening & Launch 🚀

**Goal**: Production-grade reliability, security, CI/CD, monitoring, and native app store deployment.

| Feature | Status |
|---|---|
| GitHub Actions CI/CD pipeline (lint → test → build → deploy) | Not started |
| Jest backend test suite (unit + integration) | Not started |
| Detox or Maestro E2E tests for critical flows | Not started |
| Sentry / New Relic error monitoring + alerting | Not started |
| Mixpanel / Amplitude event analytics | Not started |
| CDN + S3 for image and asset hosting | Not started |
| MongoDB Atlas replica set (high availability) | Not started |
| Secret management (AWS Secrets Manager or HashiCorp Vault) | Not started |
| KYC for withdrawals > ₹10,000 (RBI compliance) | Not started |
| iOS App Store submission (Expo EAS Build) | Not started |
| Android Play Store submission (Expo EAS Build) | Not started |
| Remove OTP console logging | Not started |
| Rotate weak MongoDB password | Not started |

### Phase 4 Milestone

Tag `v1.0.0` on `main` — production launch.

---

## Critical Blockers for Phase 1 Go-Live

These must be resolved before opening the platform to real users:

| Blocker | Risk Level | Notes |
|---|---|---|
| MSG91 OTP delivery not integrated | 🔴 Critical | Users cannot sign up/login via OTP outside dev |
| OTP logged to console | 🔴 Critical | Security — OTPs visible in server logs in production |
| Weak MongoDB password (`Abcd1234`) | 🔴 Critical | Rotate before any public deployment |
| No real deal data | 🟠 High | Home screen shows empty/fallback data |
| No Admitad affiliate URLs in deals | 🟠 High | "Shop Now" opens links but cashback tracking doesn't work |

---

## Git Workflow

```
main (stable — tagged releases: v0.1.0, v0.2.0...)
  └── develop (current active integration branch)
       ├── feature/admitad
       ├── feature/withdrawal-api
       ├── feature/notifications
       └── ...
```

1. Create feature branch from `develop`
2. Build and test the feature
3. Open PR to `develop`
4. After review, merge to `develop`
5. When phase milestone is reached, merge `develop` → `main` and tag the release
