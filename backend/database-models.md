# Database Models

ChingiRingi uses MongoDB via Mongoose. All models are located under `backend/src/modules/`.

## User

**File:** `modules/users/userModel.js`
**Collection:** `users`

### Schema

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `name` | `String` | Yes | -- | Trimmed |
| `username` | `String` | No | -- | Unique (sparse index), trimmed |
| `email` | `String` | No | -- | Unique (sparse index), lowercased, validated with regex |
| `phone` | `String` | No | -- | Unique (sparse index) |
| `passwordHash` | `String` | No | -- | Excluded from queries by default (`select: false`) |
| `role` | `String` | No | `'user'` | Enum: `'user'`, `'admin'` |
| `referralCode` | `String` | No | -- | Unique |
| `referredBy` | `ObjectId` | No | -- | Ref: `User` |
| `walletId` | `ObjectId` | No | -- | Ref: `Wallet` |
| `isPhoneVerified` | `Boolean` | No | `false` | -- |
| `isEmailVerified` | `Boolean` | No | `false` | -- |
| `refreshTokens` | `Array` | No | `[]` | Sub-documents with `{ token: String, expiresAt: Date, createdAt: Date }` |
| `createdAt` | `Date` | Auto | -- | Mongoose timestamps |
| `updatedAt` | `Date` | Auto | -- | Mongoose timestamps |

### Hooks

- **`pre('save')`** -- If `passwordHash` is modified, it is hashed with bcrypt (cost 12) before persisting.

### Instance Methods

- **`matchPassword(enteredPassword)`** -- Compares a plaintext password against the stored hash using `bcrypt.compare`. Returns a boolean.

---

## Wallet

**File:** `modules/wallet/walletModel.js`
**Collection:** `wallets`

### Schema

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `userId` | `ObjectId` | Yes | -- | Ref: `User`, unique (one wallet per user) |
| `pendingCashback` | `Number` | No | `0` | Min: 0 |
| `confirmedCashback` | `Number` | No | `0` | Min: 0 |
| `coins` | `Number` | No | `0` | Min: 0 |
| `lifetimeEarned` | `Number` | No | `0` | Min: 0 |
| `createdAt` | `Date` | Auto | -- | Mongoose timestamps |
| `updatedAt` | `Date` | Auto | -- | Mongoose timestamps |

A wallet is created automatically during user signup (see `authService.createUser`).

---

## OTP

**File:** `modules/otp/otpModel.js`
**Collection:** `otps`

### Schema

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `phone` | `String` | No | -- | Phone number the OTP was sent to |
| `email` | `String` | No | -- | Email the OTP was sent to |
| `otpHash` | `String` | Yes | -- | Bcrypt-hashed OTP |
| `expiresAt` | `Date` | Yes | -- | TTL index -- MongoDB auto-deletes the document at expiration |
| `attempts` | `Number` | No | `0` | Failed verification attempts (max 3 allowed) |
| `createdAt` | `Date` | Auto | -- | Mongoose timestamps |
| `updatedAt` | `Date` | Auto | -- | Mongoose timestamps |

### Instance Methods

- **`matchOtp(enteredOtp)`** -- Compares a plaintext OTP against the stored hash using `bcrypt.compare`. Returns a boolean.

### TTL Behavior

The `expiresAt` field has a Mongoose index with `{ expires: 0 }`, which tells MongoDB to automatically remove the document once the `expiresAt` timestamp is reached. OTPs are generated with a 5-minute TTL.
