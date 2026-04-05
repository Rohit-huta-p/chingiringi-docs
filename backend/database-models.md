# Database Models

ChingiRingi uses MongoDB via Mongoose. All models live under `backend/src/modules/`.

---

## User

**File:** `modules/users/userModel.js` | **Collection:** `users`

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `name` | String | Yes | — | Trimmed |
| `username` | String | No | — | Unique sparse index, trimmed |
| `email` | String | No | — | Unique sparse index, lowercased, email regex |
| `phone` | String | No | — | Unique sparse index |
| `passwordHash` | String | No | — | `select: false` — excluded from queries by default |
| `role` | String | No | `'user'` | Enum: `'user'`, `'admin'` |
| `referralCode` | String | No | — | Unique — auto-generated on signup |
| `referredBy` | ObjectId | No | — | Self-ref: `User` |
| `walletId` | ObjectId | No | — | Ref: `Wallet` |
| `isPhoneVerified` | Boolean | No | `false` | — |
| `isEmailVerified` | Boolean | No | `false` | — |
| `refreshTokens` | Array | No | `[]` | Sub-docs: `{ token, expiresAt, createdAt }`. Capped at 5. |
| `createdAt` | Date | Auto | — | Mongoose timestamps |
| `updatedAt` | Date | Auto | — | Mongoose timestamps |

**Hooks:** `pre('save')` — if `passwordHash` is modified, it is bcrypt-hashed (cost 12) before persisting.

**Methods:** `matchPassword(enteredPassword)` — `bcrypt.compare` against stored hash. Returns boolean.

---

## Wallet

**File:** `modules/wallet/walletModel.js` | **Collection:** `wallets`

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `userId` | ObjectId | Yes | — | Ref: `User`, unique (one wallet per user) |
| `pendingCashback` | Number | No | `0` | Min: 0. Tracked purchases not yet confirmed. |
| `confirmedCashback` | Number | No | `0` | Min: 0. Validated and withdrawable. |
| `coins` | Number | No | `0` | Min: 0. Loyalty currency — not redeemable for cash. |
| `lifetimeEarned` | Number | No | `0` | Min: 0. Running total of all cashback ever confirmed. |
| `createdAt` | Date | Auto | — | Auto-created alongside user during signup |

---

## OTP

**File:** `modules/otp/otpModel.js` | **Collection:** `otps`

| Field | Type | Required | Notes |
|---|---|---|---|
| `phone` | String | No | Phone number OTP was sent to |
| `email` | String | No | Email OTP was sent to |
| `otpHash` | String | Yes | bcrypt-hashed 6-digit OTP |
| `expiresAt` | Date | Yes | **TTL index** — MongoDB auto-deletes at expiry (5 minutes) |
| `attempts` | Number | No | Failed verification attempts. Max 3 before OTP invalidated. |

**Methods:** `matchOtp(enteredOtp)` — `bcrypt.compare` against hash. Returns boolean.

---

## Deal

**File:** `modules/deals/dealModel.js` | **Collection:** `deals`

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `title` | String | Yes | — | — |
| `description` | String | No | — | — |
| `brand` | String | Yes | — | Indexed |
| `category` | ObjectId | No | — | Ref: `Category`. Indexed. |
| `cashbackType` | String | No | `'percentage'` | Enum: `'percentage'`, `'flat'` |
| `cashbackPercent` | Number | No | `0` | 0–100. Used when `cashbackType === 'percentage'`. |
| `flatCashback` | Number | No | `0` | Used when `cashbackType === 'flat'`. |
| `affiliateUrl` | String | Yes | — | The tracking URL opened when user taps "Shop Now" |
| `imageUrl` | String | No | — | — |
| `lockPeriodDays` | Number | No | `30` | Days before cashback moves from pending to confirmed |
| `expiresAt` | Date | Yes | — | Indexed. Deal removed from feed after this date. |
| `tags` | String[] | No | `[]` | — |
| `termsAndConditions` | String | No | — | Newline-separated; split for display |
| `isActive` | Boolean | No | `true` | Indexed (compound with expiresAt) |
| `isFeatured` | Boolean | No | `false` | Indexed. Featured deals shown in hero section. |
| `clickCount` | Number | No | `0` | Incremented via `POST /api/deals/:id/click` |
| `conversionCount` | Number | No | `0` | Updated by Admitad webhook (planned) |
| `createdAt` | Date | Auto | — | — |

**Indexes:** `brand`, `category`, `{ isActive, expiresAt }`, `{ isFeatured, isActive }`, text index on `title + description + brand` (for search).

---

## Category

**File:** `modules/categories/categoryModel.js` | **Collection:** `categories`

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `name` | String | Yes | — | Unique |
| `slug` | String | Yes | — | Unique, lowercase (e.g., `'electronics'`) |
| `icon` | String | No | — | Icon identifier or URL |
| `isActive` | Boolean | No | `true` | — |
| `sortOrder` | Number | No | `0` | Display ordering in category pills |

**Indexes:** `slug`, `{ isActive, sortOrder }`.

---

## Banner

**File:** `modules/banners/bannerModel.js` | **Collection:** `banners`

| Field | Type | Required | Notes |
|---|---|---|---|
| `imageUrl` | String | Yes | — |
| `title` | String | No | — |
| `linkUrl` | String | No | Deep link target when banner is tapped |
| `position` | String | No | Enum: `'hero'`, `'sidebar'`, `'inline'` |
| `priority` | Number | No | Display ordering — lower number = higher priority |
| `startDate` | Date | No | Banner not shown before this date |
| `endDate` | Date | No | Banner not shown after this date |
| `isActive` | Boolean | No | Default: `true` |

---

## Address

**File:** `modules/addresses/addressModel.js` | **Collection:** `addresses`

| Field | Type | Required | Notes |
|---|---|---|---|
| `userId` | ObjectId | Yes | Ref: `User`. Indexed. |
| `label` | String | Yes | Enum: `'home'`, `'work'`, `'other'` |
| `fullName` | String | Yes | — |
| `phone` | String | Yes | — |
| `addressLine1` | String | Yes | — |
| `addressLine2` | String | No | — |
| `city` | String | Yes | — |
| `state` | String | Yes | — |
| `pincode` | String | Yes | 6-digit Indian postal code |
| `isDefault` | Boolean | No | Default: `false`. Only one address per user can be default. |

---

## Transaction

**File:** `modules/transactions/transactionModel.js` | **Collection:** `transactions`

Tracks the full lifecycle of cashback earnings and other wallet movements.

| Field | Type | Required | Notes |
|---|---|---|---|
| `userId` | ObjectId | Yes | Ref: `User`. Indexed. |
| `walletId` | ObjectId | Yes | Ref: `Wallet` |
| `type` | String | Yes | Enum: `'cashback'`, `'referral'`, `'coin_earn'`, `'coin_spend'`, `'withdrawal'`, `'bonus'` |
| `amount` | Number | Yes | In ₹ (or coins for coin transactions) |
| `status` | String | Yes | Enum: `'pending'`, `'confirmed'`, `'rejected'`, `'processing'`, `'completed'` |
| `dealId` | ObjectId | No | Ref: `Deal` — which deal triggered this transaction |
| `orderId` | ObjectId | No | Ref: `Order` — for store purchases (planned) |
| `description` | String | No | Human-readable description |
| `metadata` | Object | No | Admitad conversion ID, Razorpay payout ID, etc. |
| `createdAt` | Date | Auto | — |

**Status lifecycle for cashback:**
```
pending → confirmed → withdrawable
       ↘ rejected
```
Lock period (deal.lockPeriodDays) must pass before `pending` can move to `confirmed`.

---

## Planned Models (Not Yet Built)

| Model | Collection | Purpose |
|---|---|---|
| `Product` | `products` | In-app store catalog |
| `Order` | `orders` | Store purchase orders |
| `Cart` | `carts` | Persistent shopping carts |
| `Notification` | `notifications` | In-app notification feed |
| `Merchant` (QR) | `merchants` | Offline QR merchant profiles |
| `Coupon` | `coupons` | Discount codes for store checkout |
| `Withdrawal` | `withdrawals` | Withdrawal request queue |
