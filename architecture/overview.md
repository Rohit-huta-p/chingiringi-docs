# Architecture Overview

ChingiRingi is a multi-platform cashback and rewards app built with React Native (Expo) on the frontend and Node.js/Express on the backend, backed by MongoDB Atlas.

---

## System Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            CLIENT LAYER                                 │
│                                                                         │
│  ┌─────────────┐    ┌─────────────┐    ┌──────────────────────────────┐ │
│  │  iOS App     │    │ Android App │    │     Web Application          │ │
│  │  (Expo/RN)  │    │  (Expo/RN)  │    │     (Expo Web / RN Web)      │ │
│  └──────┬──────┘    └──────┬──────┘    └─────────────┬────────────────┘ │
│         │                  │                         │                  │
│   Bearer Token         Bearer Token            Cookie Auth              │
│   expo-secure-store    expo-secure-store        HTTP-only cookies       │
│         └──────────────────┴────────────── ─────────┘                  │
│                            │                                            │
│              ┌─────────────┴──────────────┐                             │
│              │  Axios + React Query        │                             │
│              │  (Interceptors: attach      │                             │
│              │   token, refresh on 401)    │                             │
│              └─────────────┬──────────────┘                             │
└────────────────────────────┼────────────────────────────────────────────┘
                             │ HTTPS
                             ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            API LAYER                                    │
│                                                                         │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                     Express.js / Node.js                          │  │
│  │                                                                   │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │  │
│  │  │ Helmet   │  │  CORS    │  │  Rate    │  │  Cookie Parser   │ │  │
│  │  │(Security)│  │(Origins) │  │ Limiter  │  │  (cookie-parser) │ │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────────────┘ │  │
│  │                                                                   │  │
│  │  Route Modules:                                                   │  │
│  │  /auth/*          (Built — signup, login, OTP, refresh, logout)  │  │
│  │  /api/deals/*     (Built — CRUD, search, click tracking)         │  │
│  │  /api/categories  (Built — CRUD)                                 │  │
│  │  /api/banners     (Built — CRUD)                                 │  │
│  │  /api/profile     (Built — view, update, delete)                 │  │
│  │  /api/addresses   (Built — CRUD, set default)                    │  │
│  │  /api/wallet      (Built — balance, transactions, summary)       │  │
│  │  /api/admin       (Built — dashboard, user list, deal list)      │  │
│  │  /api/webhooks    (Planned — Admitad conversion callbacks)       │  │
│  │  /api/withdrawals (Planned — Razorpay X payout)                  │  │
│  │                                                                   │  │
│  │  ┌──────────────┐  ┌────────────────┐  ┌─────────────────────┐  │  │
│  │  │  Zod         │  │  JWT Auth      │  │  Global Error       │  │  │
│  │  │  (Validation)│  │  Middleware    │  │  Handler            │  │  │
│  │  └──────────────┘  └────────────────┘  └─────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────┬───────────────────────────┘
                                              │
                                              ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            DATA LAYER                                   │
│                                                                         │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                  MongoDB Atlas (Mongoose ODM)                      │  │
│  │                                                                   │  │
│  │  Collections (Built):                                             │  │
│  │  users  wallets  otps(TTL)  deals  categories  banners            │  │
│  │  addresses  transactions                                          │  │
│  │                                                                   │  │
│  │  Planned: products  orders  notifications  merchants  coupons    │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘

                         EXTERNAL SERVICES (Planned)
┌─────────────────────────────────────────────────────────────────────────┐
│  ┌──────────┐  ┌────────────┐  ┌──────────┐  ┌────────┐  ┌──────────┐  │
│  │ Admitad  │  │ Razorpay X │  │  MSG91   │  │  FCM   │  │  S3/CDN  │  │
│  │Affiliate │  │  Payouts   │  │  OTP SMS │  │  Push  │  │  Assets  │  │
│  └──────────┘  └────────────┘  └──────────┘  └────────┘  └──────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Authentication Architecture

ChingiRingi uses a **dual authentication strategy** — the approach differs between web and native platforms because HTTP-only cookies don't persist reliably in React Native.

### Web (Expo Web / Browser)

- Backend sets `accessToken` and `refreshToken` as HTTP-only cookies
- Axios sends cookies automatically with every request (`withCredentials: true`)
- Tokens are never accessible in JavaScript — protected from XSS

### Native (iOS / Android)

- Backend returns tokens in the JSON response body (`response.data.tokens`)
- Frontend stores them in `expo-secure-store` (encrypted native keychain)
- Axios attaches `Authorization: Bearer <token>` on every request via a request interceptor
- `POST /auth/refresh` accepts `{ refreshToken }` in the request body (no cookies)

### Token Lifecycle

| Token | Lifetime | Purpose |
|---|---|---|
| `accessToken` | 15 minutes | Authorizes API calls |
| `refreshToken` | 30 days | Silently replaces expired access tokens |

### Silent Refresh (Axios Interceptor)

```
API request → 401 response
  → Interceptor calls POST /auth/refresh
    → Web: sends refreshToken cookie automatically
    → Native: sends { refreshToken } from SecureStore in request body
  → Server validates, issues new token pair
  → Interceptor retries the original request
  → If refresh also fails → logout + redirect to login screen
```

### Session Hydration on App Start

On launch, `App.tsx` calls `useAuthStore.hydrate()`, which calls `GET /auth/me`:
- If access token is valid → user stays authenticated
- If expired → silent refresh runs → if refresh succeeds → stays authenticated
- If refresh fails → auth state cleared → login screen shown

### Security Measures

- HTTP-only cookies prevent XSS token theft (web)
- `expo-secure-store` is OS-level encrypted storage (native)
- Refresh tokens are stored hashed in the user document, capped at 5 active sessions
- Auth endpoints rate-limited to 5 req/min; global limit 100 req/15min
- Helmet sets XSS protection, content-type sniffing prevention, clickjacking headers
- Zod validates all request bodies before they reach controllers

---

## Navigation Architecture

Navigation is handled by React Navigation v7, with a conditional root driven by Zustand auth state.

```
RootNavigator (NavigationContainer)
  │
  ├── isAuthenticated === false
  │     AuthNavigator (NativeStackNavigator)
  │       Login, Signup, OTPVerification, ForgotPassword, ResetPassword
  │
  └── isAuthenticated === true
        │
        ├── user.role === 'admin'
        │     AdminNavigator (responsive)
        │       Mobile  → Stack (AdminDashboard + all admin screens)
        │       Web/Desktop → lazy DesktopAdminDrawer (permanent sidebar)
        │
        └── user.role === 'user'
              ResponsiveNavigator
                Mobile/Native → MobileNavigator
                │               Stack wrapping BottomTabNavigator
                │               Tabs: Home, Wallet, Referrals, Notifications, Settings
                │               Stack routes: Profile, EditProfile, MyAddress,
                │                             AddEditAddress, TransactionHistory, ProductDetail
                │
                └── Web/Desktop (width ≥ 768) → lazy DesktopDrawerNavigator
                                                  Permanent sidebar (250px expanded / 80px collapsed)
                                                  Same routes as MobileNavigator
```

**Key design decisions:**
- `react-native-reanimated` (required by drawer) is only imported inside `DesktopDrawerNavigator.tsx` and `DesktopAdminDrawer.tsx`, which are lazy-loaded via `React.lazy()` + `Suspense`. This prevents the TurboModule crash on Android/iOS with Expo Go.
- On native platforms (`Platform.OS !== 'web'`), the mobile navigator is always used, regardless of screen width.
- Role-based routing happens at the root — admins and regular users never share a navigator tree.

---

## State Management

| Store | File | Responsibility |
|---|---|---|
| `useAuthStore` | `src/store/index.ts` | Auth state, user object, hydration on app start, logout (clears SecureStore + cookies) |
| `useUIStore` | `src/store/uiStore.ts` | Sidebar collapsed/expanded state |

Server state (data fetching, caching, mutations) is handled by **TanStack React Query** via `QueryClientProvider` in `App.tsx`.

---

## Backend Module Structure

Each domain is self-contained with its own controller, routes, and service/model:

```
backend/src/
  app.js                         ← Express setup, CORS, middleware, route mounting
  modules/
    auth/
      authController.js          ← Request handlers (signup, login, logout, refresh…)
      authRoutes.js               ← Route definitions + rate limiters
      authService.js              ← Business logic (create user, generate tokens…)
    users/
      userModel.js                ← Mongoose schema (User)
    wallet/
      walletModel.js              ← Mongoose schema (Wallet)
      walletController.js         ← Balance, summary, transaction history
      walletRoutes.js
    deals/
      dealModel.js                ← Mongoose schema (Deal)
      dealController.js           ← CRUD, search, click tracking, featured, trending brands
      dealRoutes.js
    categories/
      categoryModel.js
      categoryController.js
      categoryRoutes.js
    banners/
      bannerModel.js
      bannerController.js
      bannerRoutes.js
    users/ (profile)
      profileController.js        ← GET/PUT/DELETE /api/profile
      profileRoutes.js
    addresses/
      addressModel.js
      addressController.js        ← CRUD + set default
      addressRoutes.js
    transactions/
      transactionModel.js         ← Status lifecycle model (no dedicated routes yet)
    admin/
      adminController.js          ← Dashboard stats, user list, deal list
      adminRoutes.js              ← All protected by protect + admin middleware
    otp/
      otpModel.js                 ← TTL-indexed OTP document
  middleware/
    authMiddleware.js             ← protect (Bearer + cookie), admin role check
    errorMiddleware.js            ← Global error handler
  config/
    db.js                         ← MongoDB Atlas connection
  utils/
    generateTokens.js             ← JWT creation, cookie setting, SecureStore response
```
