# Architecture Overview

ChingiRingi follows a standard client-server architecture with a React Native frontend communicating with an Express.js REST API backed by MongoDB.

---

## System Diagram

```
+---------------------------+          HTTPS / HTTP           +---------------------------+
|                           |  ───────────────────────────>   |                           |
|   React Native (Expo)     |                                 |   Express.js API          |
|   chingiring-app/         |  <───────────────────────────   |   backend/src/            |
|                           |     JSON + HTTP-Only Cookies    |                           |
+---------------------------+                                 +---------------------------+
                                                                        |
                                                                        |  Mongoose ODM
                                                                        v
                                                              +---------------------------+
                                                              |                           |
                                                              |   MongoDB                 |
                                                              |                           |
                                                              +---------------------------+
```

---

## How Frontend and Backend Connect

The frontend uses an Axios client (`chingiring-app/src/api/client.ts`) configured with `withCredentials: true` to send and receive HTTP-only cookies on every request. The base URL is determined by platform: `localhost` for web builds, a LAN IP for native device testing, or an override via the `EXPO_PUBLIC_API_URL` environment variable.

All API calls are routed through this single Axios instance, which includes a response interceptor for automatic token refresh (see Auth Flow below).

---

## Authentication Flow

ChingiRingi uses a **cookie-based JWT authentication** strategy. No tokens are stored in client-side JavaScript; instead, the server sets HTTP-only cookies that the browser or network stack attaches automatically.

### Token Pair

| Token | Storage | Lifetime | Purpose |
|---|---|---|---|
| `accessToken` | HTTP-only cookie | 15 minutes | Authorizes API requests |
| `refreshToken` | HTTP-only cookie | 30 days | Obtains a new access token silently |

### Login Sequence

```
1. User submits credentials
2. POST /auth/login
3. Server validates credentials (bcrypt compare)
4. Server generates accessToken + refreshToken (JWT)
5. Server sets both as HTTP-only cookies on the response
6. Client receives 200 OK with user data
7. Zustand auth store sets isAuthenticated = true
8. RootNavigator switches from AuthNavigator to DrawerNavigator
```

### Silent Refresh

The Axios response interceptor in `client.ts` handles 401 errors automatically:

```
1. API request returns 401
2. Interceptor fires POST /auth/refresh (cookies sent automatically)
3. Server verifies the refresh token, issues a new access token cookie
4. Interceptor retries the original failed request
5. If refresh also fails, the user is returned to the login screen
```

### Session Hydration on App Start

When the app launches, `App.tsx` calls `useAuthStore.hydrate()`, which hits `GET /auth/me`. If the access token cookie is still valid (or can be silently refreshed), the user is kept authenticated. Otherwise, the auth state resets and the login screen is shown.

### Security Measures

- **HTTP-only cookies** prevent XSS-based token theft.
- **Secure flag** is enabled in production (HTTPS only).
- **SameSite** is set to `none` in production for cross-site cookie support, `lax` in development.
- **Refresh token rotation**: tokens are stored in the user document and capped at 5 entries.
- **Rate limiting**: auth endpoints are limited to 5 requests per minute; global limit is 100 requests per 15 minutes.
- **Helmet** sets security-related HTTP headers.
- **Zod** is used for request validation on the backend.

---

## Navigation Architecture

The app uses React Navigation with a conditional root:

```
RootNavigator
  |
  +-- isAuthenticated === false
  |     AuthNavigator (Native Stack)
  |       Login
  |       Signup
  |       OTPVerification
  |       ForgotPassword
  |       ResetPassword
  |
  +-- isAuthenticated === true
        DrawerNavigator (Permanent Drawer)
          Home
          Wallet
          Referrals
          Notifications
          Settings
          Profile
          EditProfile
          MyAddress
          TransactionHistory
          ProductDetail
```

The `isAuthenticated` flag in the Zustand auth store drives which navigator tree is rendered. There is no manual navigation between auth and dashboard screens; the entire tree swaps reactively.

---

## State Management

The app uses **Zustand** for global state, split into two stores:

| Store | File | Responsibility |
|---|---|---|
| `useAuthStore` | `src/store/index.ts` | Authentication state, user data, hydration, logout |
| `useUIStore` | `src/store/uiStore.ts` | UI preferences (sidebar collapsed state) |

Server state (data fetching, caching, mutations) is handled by **TanStack React Query** via the `QueryClientProvider` in `App.tsx`.

---

## Backend Module Structure

The backend follows a **modular architecture** where each domain is self-contained:

```
backend/src/modules/
  auth/
    authController.js   # Request handlers
    authRoutes.js        # Route definitions
    authService.js       # Business logic
  otp/
    otpModel.js          # OTP Mongoose model
  users/
    userModel.js         # User Mongoose model
  wallet/
    walletModel.js       # Wallet Mongoose model
```

Shared concerns live outside modules:

- `middleware/` -- authentication guard (`protect`) and error handling
- `config/` -- database connection
- `utils/` -- JWT token generation
