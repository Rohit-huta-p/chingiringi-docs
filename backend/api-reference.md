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

## Deals Routes

All routes below are prefixed with `/api/deals`.

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/deals` | No | List deals with filters, search, and pagination. |
| `GET` | `/api/deals/featured` | No | Get up to 6 featured active deals. |
| `GET` | `/api/deals/trending-brands` | No | Get top 8 brands by click count. |
| `GET` | `/api/deals/:id` | No | Get a single deal by ID. |
| `POST` | `/api/deals/:id/click` | Yes | Track a deal click and return the affiliate URL. |
| `POST` | `/api/deals` | Admin | Create a new deal. |
| `PUT` | `/api/deals/:id` | Admin | Update a deal. |
| `DELETE` | `/api/deals/:id` | Admin | Delete a deal. |

### Request / Response Details

#### GET /api/deals

**Query Parameters:**

| Parameter | Type | Default | Description |
|---|---|---|---|
| `page` | `number` | `1` | Page number for pagination. |
| `limit` | `number` | `12` | Number of deals per page. |
| `category` | `string` | -- | Filter by category slug. |
| `search` | `string` | -- | Full-text search on title, brand, description. |
| `brand` | `string` | -- | Filter by brand name (case-insensitive regex). |
| `featured` | `string` | -- | Set to `"true"` to return only featured deals. |
| `sort` | `string` | `"-createdAt"` | Mongoose sort string. |

Only active deals that have not expired are returned.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "deals": [ { "...deal object with populated category..." } ],
    "pagination": {
      "page": 1,
      "limit": 12,
      "total": 42,
      "pages": 4
    }
  }
}
```

---

#### GET /api/deals/featured

Returns up to 6 active, featured, non-expired deals sorted by newest first.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "deals": [ { "...deal objects..." } ]
  }
}
```

---

#### GET /api/deals/trending-brands

Returns up to 8 brands aggregated from active deals, sorted by total click count descending. Each entry includes the brand name, total clicks, max cashback percentage, deal count, and category name.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "brands": [
      {
        "brand": "Amazon",
        "totalClicks": 150,
        "maxCashback": 12,
        "dealCount": 5,
        "category": "Shopping"
      }
    ]
  }
}
```

---

#### GET /api/deals/:id

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "deal": { "...deal object with populated category..." }
  }
}
```

**Error (404):** `"Deal not found"`

---

#### POST /api/deals/:id/click

Requires `accessToken` cookie. Increments the deal's click count and returns the affiliate URL.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "affiliateUrl": "https://example.com/affiliate/..."
  }
}
```

**Error (404):** `"Deal not found"`

---

#### POST /api/deals

Requires `accessToken` cookie and admin role.

**Body:**
```json
{
  "title": "string (required)",
  "description": "string (required)",
  "brand": "string (required)",
  "category": "ObjectId (required, ref: Category)",
  "cashbackPercent": "number (required, 0-100)",
  "cashbackType": "string (optional, 'percentage' | 'flat', default: 'percentage')",
  "flatCashback": "number (optional, min 0, default: 0)",
  "affiliateUrl": "string (required)",
  "imageUrl": "string (optional)",
  "lockPeriodDays": "number (optional, default: 30)",
  "expiresAt": "Date (required)",
  "tags": ["string"],
  "termsAndConditions": "string (optional)",
  "isActive": "boolean (optional, default: true)",
  "isFeatured": "boolean (optional, default: false)"
}
```

**Response (201):**
```json
{
  "status": "success",
  "data": {
    "deal": { "...created deal with populated category..." }
  }
}
```

---

#### PUT /api/deals/:id

Requires `accessToken` cookie and admin role. Accepts any subset of the deal fields.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "deal": { "...updated deal with populated category..." }
  }
}
```

**Error (404):** `"Deal not found"`

---

#### DELETE /api/deals/:id

Requires `accessToken` cookie and admin role.

**Response (200):**
```json
{
  "status": "success",
  "message": "Deal deleted"
}
```

**Error (404):** `"Deal not found"`

---

## Categories Routes

All routes below are prefixed with `/api/categories`.

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/categories` | No | List all active categories sorted by `sortOrder`. |
| `GET` | `/api/categories/:slug` | No | Get a single category by slug. |
| `POST` | `/api/categories` | Admin | Create a new category. |
| `PUT` | `/api/categories/:id` | Admin | Update a category. |
| `DELETE` | `/api/categories/:id` | Admin | Delete a category. |

### Request / Response Details

#### GET /api/categories

Returns all active categories sorted by `sortOrder` ascending.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "categories": [
      {
        "_id": "ObjectId",
        "name": "Shopping",
        "slug": "shopping",
        "icon": "shopping-cart",
        "isActive": true,
        "sortOrder": 0
      }
    ]
  }
}
```

---

#### GET /api/categories/:slug

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "category": { "...category object..." }
  }
}
```

**Error (404):** `"Category not found"`

---

#### POST /api/categories

Requires `accessToken` cookie and admin role. The `slug` is auto-generated from the `name` field (lowercased, spaces replaced with hyphens, non-alphanumeric characters removed).

**Body:**
```json
{
  "name": "string (required)",
  "icon": "string (optional)",
  "sortOrder": "number (optional, default: 0)"
}
```

**Response (201):**
```json
{
  "status": "success",
  "data": {
    "category": { "...created category..." }
  }
}
```

---

#### PUT /api/categories/:id

Requires `accessToken` cookie and admin role. If `name` is updated, the `slug` is automatically regenerated.

**Response (200):**
```json
{
  "status": "success",
  "data": {
    "category": { "...updated category..." }
  }
}
```

**Error (404):** `"Category not found"`

---

#### DELETE /api/categories/:id

Requires `accessToken` cookie and admin role.

**Response (200):**
```json
{
  "status": "success",
  "message": "Category deleted"
}
```

**Error (404):** `"Category not found"`

---

## Banners Routes

> **Note:** The Banners module is planned but not yet implemented. The routes below document the intended API surface based on the project design.

Banners will be managed at `/api/banners` with admin-only CRUD and public listing, following the same patterns as the Deals and Categories modules.

---

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
