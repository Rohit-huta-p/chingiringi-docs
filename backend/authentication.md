# Authentication

ChingiRingi uses a **dual authentication strategy** — the mechanism differs between web and native platforms because HTTP-only cookies don't persist reliably in React Native.

- `modules/auth/authController.js` — Route handlers
- `modules/auth/authService.js` — Business logic (user creation, password verification, OTP)
- `middleware/authMiddleware.js` — Route protection (Bearer + cookie)
- `utils/generateTokens.js` — Token generation, cookie setting, and SecureStore response formatting

---

## Platform Strategy

| Platform | Token Delivery | Token Storage | How Axios Sends It |
|---|---|---|---|
| **Web (Expo Web / Browser)** | Set as HTTP-only cookies by the server | Browser manages cookies automatically | `withCredentials: true` on every request |
| **iOS / Android (Expo Go / native build)** | Returned in JSON response body (`response.data.tokens`) | `expo-secure-store` (OS-level encrypted keychain) | `Authorization: Bearer <accessToken>` header via Axios interceptor |

### Why two strategies?

- HTTP-only cookies are the most secure web delivery method — JavaScript can never access the token, preventing XSS attacks
- React Native (iOS / Android) does not reliably send cookies across Expo environments, so tokens are returned in the response body and stored in the encrypted native keychain

---

## Token Architecture

### Access Token

- **Purpose:** Short-lived token for authenticating API requests
- **Algorithm:** JWT signed with `JWT_ACCESS_SECRET`
- **Payload:** `{ id: userId, role: userRole }`
- **Lifetime:** 15 minutes (configured via `JWT_ACCESS_EXPIRATION`)
- **Web delivery:** `httpOnly` cookie named `accessToken` (`secure` in production, `sameSite: 'none'` in production / `'lax'` in development)
- **Native delivery:** `response.data.tokens.accessToken` in JSON body; stored in `expo-secure-store`

### Refresh Token

- **Purpose:** Long-lived token for silent token rotation without re-authentication
- **Algorithm:** JWT signed with `JWT_REFRESH_SECRET`
- **Payload:** `{ id: userId }`
- **Lifetime:** 30 days (configured via `JWT_REFRESH_EXPIRATION`)
- **Web delivery:** `httpOnly` cookie named `refreshToken`
- **Native delivery:** `response.data.tokens.refreshToken` in JSON body; stored in `expo-secure-store`
- **Database storage:** Each refresh token is hashed and stored in the user document's `refreshTokens` array (`{ token, expiresAt, createdAt }`). Capped at 5 active sessions — oldest are pruned on each new issuance

---

## Token Refresh Flow

### Web

1. Axios 401 interceptor fires
2. `POST /auth/refresh` — browser sends `refreshToken` cookie automatically
3. Server verifies JWT and confirms token exists in `user.refreshTokens`
4. Used token is removed; new pair issued as cookies
5. Original request is retried

### Native (iOS / Android)

1. Axios 401 interceptor fires
2. `POST /auth/refresh` with `{ refreshToken }` in request body (read from `expo-secure-store`)
3. Server verifies JWT and confirms token exists in `user.refreshTokens`
4. Used token is removed; new pair returned in JSON body
5. Interceptor writes new tokens to `expo-secure-store`, retries original request
6. If refresh also fails → `useAuthStore.logout()` → login screen shown

This implements **refresh token rotation** — each refresh token is single-use.

---

## Session Hydration on App Start

`App.tsx` calls `useAuthStore.hydrate()` on mount, which calls `GET /auth/me`:

| Scenario | Outcome |
|---|---|
| Access token valid | User stays authenticated |
| Access token expired, refresh token valid | Silent refresh → user stays authenticated |
| Both tokens expired or invalid | Auth state cleared → login screen |

---

## Logout

`POST /auth/logout` (requires valid access token):
1. Removes the current refresh token from `user.refreshTokens` in the database
2. **Web:** Sets both cookies to empty strings with an expired date (clears them)
3. **Native:** Axios interceptor call returns; `useAuthStore.logout()` deletes tokens from `expo-secure-store`

---

## Route Protection (`protect` middleware)

Located in `middleware/authMiddleware.js`.

**Web path:**
1. Reads `accessToken` from `req.cookies`
2. Verifies JWT using `JWT_ACCESS_SECRET`
3. Fetches user by decoded `id` (excludes `passwordHash`)
4. Attaches to `req.user` and calls `next()`

**Native path:**
1. Reads `Authorization: Bearer <token>` header
2. Same verification and user lookup steps
3. Returns `401` if any step fails

Both paths are handled in the same middleware — `protect` checks cookies first, falls back to Authorization header.

**Admin gate:** `admin` middleware (used after `protect`) checks `req.user.role === 'admin'` and returns `403` if not.

---

## OTP Authentication

### Sending OTP (`POST /auth/send-otp`)

1. Accepts `phone` or `email`
2. Generates a 6-digit random OTP
3. Hashes the OTP with bcrypt (cost 12) and stores it in the `OTP` collection with a 5-minute TTL index
4. Existing OTP for the same identifier is replaced (upsert)
5. **Development:** OTP is logged to console. **Production:** Requires MSG91 (SMS) / email provider integration (planned)

### Verifying OTP (`POST /auth/verify-otp`)

1. Accepts `identifier` (phone or email) and `otp`
2. Looks up OTP document — rejected if 3+ failed attempts recorded
3. Compares provided OTP against stored bcrypt hash
4. **On success:** OTP deleted; if a matching user exists, auth tokens are issued
5. **On failure:** Attempt counter incremented; after 3 failures the OTP is invalidated

---

## Password Hashing

- Passwords hashed using bcrypt, cost factor 12
- `User` model has a `pre('save')` hook: automatically hashes `passwordHash` whenever it is modified
- Verification uses the `matchPassword(enteredPassword)` instance method — `bcrypt.compare` against stored hash

---

## Request Validation

All auth endpoints use Zod schemas. Invalid requests receive a `400` with concatenated field-level error messages.

---

## Environment Variables

| Variable | Description |
|---|---|
| `JWT_ACCESS_SECRET` | Signing key for access tokens |
| `JWT_REFRESH_SECRET` | Signing key for refresh tokens |
| `JWT_ACCESS_EXPIRATION` | Access token lifetime (default: `'15m'`) |
| `JWT_REFRESH_EXPIRATION` | Refresh token lifetime (default: `'30d'`) |
| `COOKIE_SECRET` | Secret for signed cookies (cookie-parser) |

---

## Security Measures

| Measure | Detail |
|---|---|
| HTTP-only cookies | XSS cannot steal tokens on web |
| `expo-secure-store` | OS-level encrypted storage (iOS Keychain / Android Keystore) on native |
| Refresh token rotation | Each refresh token is single-use; reuse triggers potential compromise detection |
| Session cap | Max 5 active refresh tokens per user; oldest are pruned |
| Rate limiting | Auth endpoints: 5 req/min per IP. Global: 100 req/15min per IP |
| Helmet | Sets XSS protection, content-type sniffing prevention, clickjacking headers |
| Zod validation | All request bodies validated before reaching controllers |
