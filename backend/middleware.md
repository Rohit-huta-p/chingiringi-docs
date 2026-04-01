# Middleware

All middleware is located in `backend/src/middleware/`.

## Auth Middleware (`protect`)

**File:** `middleware/authMiddleware.js`

Protects routes that require authentication. Used on `/auth/logout` and `/auth/me`.

### Behavior

1. Reads the `accessToken` cookie from `req.cookies`.
2. Verifies the JWT using `process.env.JWT_ACCESS_SECRET`.
3. Queries the database for the user by the decoded `id`, excluding the `passwordHash` field.
4. Attaches the user document to `req.user`.
5. Calls `next()` to proceed.

### Error Cases

| Condition | Status | Message |
|---|---|---|
| No `accessToken` cookie present | 401 | "Not authorized, no token attached to cookies" |
| JWT verification fails | 401 | "Not authorized, token failed" |
| User not found in database | 401 | "Not authorized, user not found" |

---

## Admin Middleware (`admin`)

**File:** `middleware/adminMiddleware.js`

Restricts access to admin-only routes. Must be used after the `protect` middleware so that `req.user` is available.

### Behavior

1. Checks that `req.user.role` equals `'admin'`.
2. If the user is an admin, calls `next()` to proceed.
3. If the user is not an admin, responds with a `403` error.

### Error Cases

| Condition | Status | Message |
|---|---|---|
| `req.user.role` is not `'admin'` | 403 | "Not authorized as admin" |

### Usage

Applied as the second middleware (after `protect`) on routes that require admin privileges:

```js
router.post('/', protect, admin, createDeal);
router.put('/:id', protect, admin, updateDeal);
router.delete('/:id', protect, admin, deleteDeal);
```

Used by the Deals and Categories modules for create, update, and delete operations.

---

## Error Handler (`errorHandler`)

**File:** `middleware/errorMiddleware.js`

Global error-handling middleware registered as the last middleware in the Express stack.

### Behavior

Catches all errors thrown or passed via `next(err)` and returns a standardized JSON response:

```json
{
  "status": "error",
  "message": "...",
  "stack": "... (development only)"
}
```

### Error Type Handling

| Error Type | Detection | Status Code | Message Transformation |
|---|---|---|---|
| Mongoose `CastError` (invalid ObjectId) | `err.name === 'CastError'` and `err.kind === 'ObjectId'` | 404 | "Resource not found" |
| Zod validation error | `err.name === 'ZodError'` | 400 | Concatenated issue messages joined by commas |
| JWT error | `err.name === 'JsonWebTokenError'` | 401 | "Invalid token. Please log in again." |
| Other errors | Default | Uses `res.statusCode` if non-200, otherwise 500 | Original `err.message` |

In production, the `stack` field is omitted (replaced with a placeholder). In development, the full stack trace is returned.

---

## Not Found Handler (`notFound`)

**File:** `middleware/errorMiddleware.js`

Registered via `app.all('*', notFound)` to catch any request that does not match a defined route.

### Behavior

Creates an error with the message `"Not Found - {originalUrl}"`, sets status to `404`, and passes it to the error handler via `next(error)`.

---

## Rate Limiting

Rate limiting is applied at two levels:

### Global Rate Limiter

**Configured in:** `app.js`

- **Window:** 15 minutes
- **Max requests:** 100 per IP
- **Headers:** Standard headers enabled, legacy headers disabled

### Auth Rate Limiter

**Configured in:** `modules/auth/authRoutes.js`

- **Window:** 1 minute
- **Max requests:** 5 per IP
- **Applied to:** `/auth/login`, `/auth/send-otp`, `/auth/verify-otp`
- **Message:** "Too many requests from this IP, please try again in a minute"

---

## Other Application-Level Middleware

These are configured in `app.js` (not separate files) but are part of the middleware stack:

| Middleware | Package | Purpose |
|---|---|---|
| `helmet()` | `helmet` | Sets security-related HTTP headers |
| `express.json({ limit: '10kb' })` | Express built-in | Parses JSON request bodies (max 10kb) |
| `express.urlencoded({ extended: true, limit: '10kb' })` | Express built-in | Parses URL-encoded bodies (max 10kb) |
| `cookieParser(COOKIE_SECRET)` | `cookie-parser` | Parses cookies, supports signed cookies |
| `cors({ origin, credentials: true })` | `cors` | Enables CORS for specified origins with credentials |
| `morgan('dev')` | `morgan` | HTTP request logging (development only) |
| `express-async-errors` | `express-async-errors` | Wraps async route handlers so thrown errors are caught automatically |
