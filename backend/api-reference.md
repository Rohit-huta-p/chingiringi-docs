# API Reference

**Base URL:** `http://localhost:8000` (development)

**Global rate limit:** 100 requests per 15 minutes per IP (applies to all routes).

---

## Health Check

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/health` | No | Returns `{ "status": "success", "message": "API is running" }` |

---

## Authentication (`/auth`)

Rate limited to **5 requests/min** per IP on login, send-otp, and verify-otp routes.

| Method | Path | Auth | Description |
|---|---|---|---|
| `POST` | `/auth/signup` | No | Create account + wallet. Returns tokens in body (native) + sets cookies (web). |
| `POST` | `/auth/login` | No | Login with identifier + password or OTP. |
| `POST` | `/auth/send-otp` | No | Generate 6-digit OTP for phone or email. TTL: 5 min, max 3 attempts. |
| `POST` | `/auth/verify-otp` | No | Verify OTP. Issues tokens if identifier matches a registered user. |
| `POST` | `/auth/logout` | Yes | Revoke refresh token, clear cookies. |
| `POST` | `/auth/refresh` | No | Rotate token pair. Accepts cookie (web) or `{ refreshToken }` body (native). |
| `POST` | `/auth/forgot-password` | No | Send password-reset OTP to email. |
| `POST` | `/auth/reset-password` | No | Reset password with OTP verification. |
| `GET` | `/auth/me` | Yes | Return authenticated user's profile. |

### POST /auth/signup

```json
// Request body
{
  "name": "string (required)",
  "username": "string (min 3 chars, required)",
  "email": "string (optional)",
  "phone": "string (optional, min 10 chars)",
  "password": "string (min 6 chars, required)"
}
// At least one of email or phone required.

// Response 201
{
  "status": "success",
  "message": "Account created",
  "tokens": { "accessToken": "...", "refreshToken": "..." }  // native only
}
// Web: also sets accessToken + refreshToken as HTTP-only cookies
```

### POST /auth/login

```json
// Request body
{
  "identifier": "string (email, username, or phone)",
  "password": "string (optional — provide password OR otp)",
  "otp": "string (optional)"
}

// Response 200
{
  "status": "success",
  "message": "Logged in successfully",
  "user": { "_id": "...", "name": "...", "email": "...", "role": "user|admin", ... },
  "tokens": { "accessToken": "...", "refreshToken": "..." }  // native only
}
```

### POST /auth/refresh

```json
// Request body (native only — web uses cookie automatically)
{ "refreshToken": "string" }

// Response 200
{
  "status": "success",
  "tokens": { "accessToken": "...", "refreshToken": "..." }  // native
}
// Web: rotates both cookies
```

---

## Deals (`/api/deals`)

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/deals` | No | List deals — supports `page`, `limit`, `category`, `search`, `featured`, `brand` query params |
| `GET` | `/api/deals/featured` | No | Return featured deals (`isFeatured: true`) |
| `GET` | `/api/deals/trending-brands` | No | Aggregation of top brands by click count |
| `GET` | `/api/deals/:id` | No | Get single deal by ID |
| `POST` | `/api/deals/:id/click` | Yes | Increment click count, return `affiliateUrl` |
| `POST` | `/api/deals` | Yes + Admin | Create new deal |
| `PUT` | `/api/deals/:id` | Yes + Admin | Update deal fields |
| `DELETE` | `/api/deals/:id` | Yes + Admin | Delete deal |

### Deal Object Fields

| Field | Type | Notes |
|---|---|---|
| `title` | String | Required |
| `description` | String | — |
| `brand` | String | Required |
| `category` | ObjectId | Ref: Category |
| `cashbackType` | `'percentage'` \| `'flat'` | Default: `'percentage'` |
| `cashbackPercent` | Number | 0–100. Used when `cashbackType === 'percentage'` |
| `flatCashback` | Number | Used when `cashbackType === 'flat'` |
| `affiliateUrl` | String | Required — the actual affiliate link |
| `imageUrl` | String | — |
| `lockPeriodDays` | Number | Default: 30 |
| `expiresAt` | Date | Required |
| `tags` | String[] | — |
| `termsAndConditions` | String | Newline-separated terms |
| `isActive` | Boolean | Default: true |
| `isFeatured` | Boolean | Default: false |
| `clickCount` | Number | Incremented by `/click` route |
| `conversionCount` | Number | Updated by Admitad webhook (planned) |

---

## Categories (`/api/categories`)

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/categories` | No | List all active categories (sorted by `sortOrder`) |
| `GET` | `/api/categories/:slug` | No | Get category by slug |
| `POST` | `/api/categories` | Yes + Admin | Create category |
| `PUT` | `/api/categories/:id` | Yes + Admin | Update category |
| `DELETE` | `/api/categories/:id` | Yes + Admin | Delete category |

---

## Banners (`/api/banners`)

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/banners` | No | List banners. Optional `?position=hero\|sidebar` filter |
| `POST` | `/api/banners` | Yes + Admin | Create banner |
| `PUT` | `/api/banners/:id` | Yes + Admin | Update banner |
| `DELETE` | `/api/banners/:id` | Yes + Admin | Delete banner |

---

## Profile (`/api/profile`)

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/profile` | Yes | Get current user's full profile |
| `PUT` | `/api/profile` | Yes | Update name, email, phone |
| `DELETE` | `/api/profile` | Yes | Soft-delete account (30-day grace period) |

---

## Addresses (`/api/addresses`)

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/addresses` | Yes | List all addresses for current user |
| `POST` | `/api/addresses` | Yes | Create new address |
| `PUT` | `/api/addresses/:id` | Yes | Update address fields |
| `DELETE` | `/api/addresses/:id` | Yes | Delete address |
| `PATCH` | `/api/addresses/:id/default` | Yes | Set address as default |

### Address Object Fields

| Field | Type | Notes |
|---|---|---|
| `label` | `'home'` \| `'work'` \| `'other'` | Required |
| `fullName` | String | Required |
| `phone` | String | Required |
| `addressLine1` | String | Required |
| `addressLine2` | String | Optional |
| `city` | String | Required |
| `state` | String | Required |
| `pincode` | String | Required — 6 digits |
| `isDefault` | Boolean | Default: false |

---

## Wallet (`/api/wallet`)

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/wallet` | Yes | Get wallet with all 4 balance fields |
| `GET` | `/api/wallet/summary` | Yes | Wallet + recent 5 transactions in one response |
| `GET` | `/api/wallet/transactions` | Yes | Paginated transaction history. Params: `type`, `status`, `period` (`7d`\|`30d`\|`90d`\|`all`), `page`, `limit` |
| `GET` | `/api/wallet/transactions/:id` | Yes | Get single transaction by ID |

### Wallet Response

```json
{
  "wallet": {
    "pendingCashback": 250,
    "confirmedCashback": 1000,
    "coins": 450,
    "lifetimeEarned": 3200
  }
}
```

---

## Admin (`/api/admin`)

All admin routes require `protect` + `admin` middleware (user must be authenticated with `role: 'admin'`).

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/admin/dashboard` | Yes + Admin | Aggregated KPIs: clicks, conversions, cashback issued, active users; coins economy stats; top 5 deals; top 5 users |
| `GET` | `/api/admin/users` | Yes + Admin | Paginated user list. Params: `page`, `limit`, `search` |
| `GET` | `/api/admin/deals` | Yes + Admin | Paginated deal list. Params: `page`, `limit` |

### Dashboard Response Shape

```json
{
  "stats": {
    "totalClicks": 1250,
    "conversions": 87,
    "cashbackIssued": 45000,
    "activeUsers": 320
  },
  "coinsEconomy": {
    "totalCoinsIssued": 15000,
    "totalCoinsRedeemed": 3200,
    "activeCoinsHolders": 180
  },
  "topDeals": [ { "title": "...", "brand": "...", "clickCount": 120 }, ... ],
  "topUsers": [ { "name": "...", "lifetimeEarned": 2400 }, ... ]
}
```

---

## Planned Endpoints (Not Yet Built)

| Method | Path | Description |
|---|---|---|
| `POST` | `/api/webhooks/admitad` | Receive conversion callbacks from Admitad |
| `POST` | `/api/withdrawals` | Request cashback withdrawal |
| `GET` | `/api/withdrawals` | User's withdrawal history |
| `GET` | `/api/referrals/stats` | Referral count, earnings stats |
| `POST` | `/api/notifications/register` | Register device token for push notifications |
| `GET` | `/api/notifications` | User's notification feed |
| `GET` | `/api/admin/withdrawals` | Admin withdrawal queue |
| `POST` | `/api/admin/withdrawals/:id/approve` | Approve + trigger Razorpay X payout |
| `POST` | `/api/admin/conversions/:id/approve` | Approve Admitad conversion → confirm cashback |

---

## Error Response Format

All errors follow a consistent shape:

```json
{
  "status": "error",
  "message": "Human-readable error description"
}
```

Common status codes:

| Code | Meaning |
|---|---|
| `400` | Validation error or bad request |
| `401` | Not authenticated (missing or expired token) |
| `403` | Authenticated but not authorized (e.g., non-admin hitting admin route) |
| `404` | Resource not found |
| `409` | Conflict (e.g., duplicate email/username on signup) |
| `429` | Rate limit exceeded |
| `500` | Unhandled server error |
