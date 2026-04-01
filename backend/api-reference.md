# API Reference

Base path: all auth routes are mounted at `/auth` (see `app.js`).

A global rate limiter applies to all routes: **100 requests per 15 minutes** per IP.

## Health Check

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/health` | No | Returns `{ status: 'success', message: 'API is running' }` |

## Authentication Routes

All routes below are prefixed with `/auth`. Routes marked with a lock icon require a valid access token cookie.

| Method | Path | Auth | Rate Limited | Description |
|---|---|---|---|---|
| `POST` | `/auth/signup` | No | Global only | Create a new user account. Creates an associated wallet automatically. |
| `POST` | `/auth/login` | No | 5 req/min | Log in with identifier (email or username) + password, or identifier + OTP. |
| `POST` | `/auth/send-otp` | No | 5 req/min | Generate and send a 6-digit OTP to a phone number or email. |
| `POST` | `/auth/verify-otp` | No | 5 req/min | Verify an OTP. If the identifier belongs to an existing user, tokens are issued. |
| `POST` | `/auth/logout` | Yes | Global only | Revoke the current refresh token and clear auth cookies. |
| `POST` | `/auth/refresh` | No (cookie) | Global only | Exchange a valid refresh token cookie for new access + refresh tokens. |
| `POST` | `/auth/forgot-password` | No | Global only | Send a password-reset OTP to the provided email. |
| `POST` | `/auth/reset-password` | No | Global only | Reset password using email, OTP, and new password. |
| `GET` | `/auth/me` | Yes | Global only | Return the authenticated user's profile data. |

### Request / Response Details

#### POST /auth/signup

**Body:**
```json
{
  "name": "string (required)",
  "username": "string (min 3 chars, required)",
  "email": "string (optional, valid email)",
  "phone": "string (optional, min 10 chars)",
  "password": "string (min 6 chars, required)"
}
```
At least one of `email` or `phone` must be provided.

**Response (201):**
```json
{ "status": "success", "message": "Account created efficiently" }
```
Sets `accessToken` and `refreshToken` cookies.

---

#### POST /auth/login

**Body:**
```json
{
  "identifier": "string (email, username, or phone)",
  "password": "string (optional)",
  "otp": "string (optional)"
}
```
Must provide either `password` or `otp`.

**Response (200):**
```json
{ "status": "success", "message": "Logged in successfully" }
```
Sets `accessToken` and `refreshToken` cookies.

---

#### POST /auth/send-otp

**Body:**
```json
{
  "phone": "string (optional)",
  "email": "string (optional)"
}
```
At least one of `phone` or `email` required.

**Response (200):**
```json
{ "status": "success", "message": "OTP sent successfully" }
```

---

#### POST /auth/verify-otp

**Body:**
```json
{
  "identifier": "string (phone or email)",
  "otp": "string (exactly 6 chars)"
}
```

**Response (200):**
```json
{ "status": "success", "message": "OTP verified successfully", "data": { "isLogin": true } }
```
If the identifier matches an existing user, auth cookies are set and `isLogin` is `true`.

---

#### POST /auth/logout

Requires `accessToken` cookie.

**Response (200):**
```json
{ "status": "success", "message": "Logged out successfully" }
```
Clears both auth cookies and removes the refresh token from the database.

---

#### POST /auth/refresh

Requires `refreshToken` cookie (no access token needed).

**Response (200):**
```json
{ "status": "success", "message": "Tokens refreshed" }
```
Rotates both tokens. The old refresh token is revoked.

---

#### POST /auth/forgot-password

**Body:**
```json
{ "email": "string (valid email)" }
```

**Response (200):**
```json
{ "status": "success", "message": "Password reset OTP sent to email" }
```

---

#### POST /auth/reset-password

**Body:**
```json
{
  "email": "string (valid email)",
  "otp": "string (exactly 6 chars)",
  "newPassword": "string (min 6 chars)"
}
```

**Response (200):**
```json
{ "status": "success", "message": "Password reset successfully" }
```

---

#### GET /auth/me

Requires `accessToken` cookie.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "user": {
      "id": "ObjectId",
      "name": "string",
      "username": "string",
      "email": "string",
      "phone": "string",
      "role": "user | admin"
    }
  }
}
```

## Error Responses

All errors follow this shape:

```json
{
  "status": "error",
  "message": "Human-readable error message",
  "stack": "Stack trace (development only)"
}
```

Common status codes: `400` (validation), `401` (unauthorized), `404` (not found), `429` (rate limited), `500` (server error).
