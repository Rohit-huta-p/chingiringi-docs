# Authentication

ChingiRingi uses a cookie-based JWT authentication system with access/refresh token rotation. The implementation spans several files:

- `modules/auth/authController.js` -- Route handlers
- `modules/auth/authService.js` -- Business logic (user creation, password verification, OTP)
- `middleware/authMiddleware.js` -- Route protection
- `utils/generateToken.js` -- Token generation and cookie management

## Token Architecture

### Access Token

- **Purpose:** Short-lived token for authenticating API requests.
- **Algorithm:** JWT signed with `JWT_ACCESS_SECRET`.
- **Payload:** `{ id: userId, role: userRole }`.
- **Expiry:** Configured via `JWT_ACCESS_EXPIRATION` env var (default: 15 minutes).
- **Delivery:** `httpOnly` cookie named `accessToken`.
- **Cookie options:** `secure` in production, `sameSite: 'none'` in production / `'lax'` in development.

### Refresh Token

- **Purpose:** Long-lived token used to obtain new access tokens without re-authentication.
- **Algorithm:** JWT signed with `JWT_REFRESH_SECRET`.
- **Payload:** `{ id: userId }`.
- **Expiry:** Configured via `JWT_REFRESH_EXPIRATION` env var (default: 30 days).
- **Delivery:** `httpOnly` cookie named `refreshToken`.
- **Storage:** Each refresh token is stored in the user document's `refreshTokens` array with its expiry date. A maximum of 5 tokens are kept per user (oldest are pruned on each issuance).

## Token Refresh Flow

1. Client sends `POST /auth/refresh` (refresh token is read from the cookie automatically).
2. Server verifies the refresh token JWT and checks that the token exists in the user's `refreshTokens` array (not revoked).
3. The used refresh token is removed from the database.
4. New access and refresh tokens are generated and set as cookies.
5. The new refresh token is appended to the user's stored tokens.

This implements **refresh token rotation** -- each refresh token is single-use.

## Logout

`POST /auth/logout` (requires access token):
1. Removes the current refresh token from the user's `refreshTokens` array.
2. Sets both `accessToken` and `refreshToken` cookies to empty strings with an expired date.

## Route Protection (`protect` middleware)

Located in `middleware/authMiddleware.js`.

1. Reads `accessToken` from `req.cookies`.
2. Verifies the JWT using `JWT_ACCESS_SECRET`.
3. Looks up the user by the decoded `id` (excluding `passwordHash`).
4. Attaches the user to `req.user` and calls `next()`.
5. Returns `401` if any step fails (missing token, invalid token, user not found).

## OTP Authentication

### Sending OTP

1. `POST /auth/send-otp` accepts `phone` or `email`.
2. A 6-digit random OTP is generated.
3. The OTP is hashed with bcrypt (cost 12) and stored in the `OTP` collection with a 5-minute TTL.
4. If an OTP already exists for the identifier, it is replaced (upsert).
5. In development, the OTP is logged to console. In production, integration with an SMS/email provider is required.

### Verifying OTP

1. `POST /auth/verify-otp` accepts `identifier` and `otp`.
2. The OTP entry is looked up by phone or email.
3. If 3+ failed attempts have been recorded, the request is rejected.
4. The provided OTP is compared against the stored hash.
5. On success, the OTP entry is deleted and (if a matching user exists) auth tokens are issued.
6. On failure, the attempt counter is incremented.

## Password Hashing

Passwords are hashed using bcrypt with a cost factor of 12. The `User` model has a `pre('save')` hook that automatically hashes `passwordHash` whenever it is modified. Verification uses the `matchPassword` instance method.

## Request Validation

All auth endpoints use Zod schemas for input validation. Invalid requests receive a `400` response with concatenated error messages.

## Environment Variables

| Variable | Description |
|---|---|
| `JWT_ACCESS_SECRET` | Signing key for access tokens |
| `JWT_REFRESH_SECRET` | Signing key for refresh tokens |
| `JWT_ACCESS_EXPIRATION` | Access token lifetime (default: `'15m'`) |
| `JWT_REFRESH_EXPIRATION` | Refresh token lifetime (default: `'30d'`) |
| `COOKIE_SECRET` | Secret for signed cookies (cookie-parser) |
