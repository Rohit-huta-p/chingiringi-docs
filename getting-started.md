# Getting Started

This guide walks you through setting up ChingiRingi locally. The project has two parts: a React Native (Expo) frontend and a Node.js/Express backend.

---

## Prerequisites

| Tool | Version | Notes |
|---|---|---|
| Node.js | v18+ | Required for both frontend and backend |
| npm | Bundled with Node.js | — |
| MongoDB | Atlas (cloud) or local | Atlas recommended — connection string goes in `.env` |
| Expo CLI | via `npx` | No global install needed |
| Git | Any recent version | — |
| Bun | v1.0+ | Required for gstack developer tooling only |

---

## Repository Structure

```
Chingiringi/
  chingiring-app/     # React Native (Expo) frontend — iOS, Android, Web
  backend/            # Node.js + Express REST API
  Docs/               # Internal gap analysis and Figma notes
```

---

## Backend Setup

### 1. Install dependencies

```bash
cd backend
npm install
```

### 2. Configure environment variables

Create a `.env` file inside `backend/`:

```env
NODE_ENV=development
PORT=8000
MONGO_URI=<your-mongodb-atlas-connection-string>
JWT_ACCESS_SECRET=<strong-random-secret>
JWT_REFRESH_SECRET=<different-strong-random-secret>
JWT_ACCESS_EXPIRATION=15m
JWT_REFRESH_EXPIRATION=30d
COOKIE_SECRET=<strong-cookie-signing-secret>
```

> **Security**: Never commit `.env` to git. The MongoDB password `Abcd1234` is weak — rotate it before any public deployment.

### 3. Start the development server

```bash
npm run dev
```

Uses `nodemon` — auto-restarts on file changes. The API runs at `http://localhost:8000`.

### 4. Verify it's running

```bash
curl http://localhost:8000/health
# → { "status": "success", "message": "API is running" }
```

---

## Frontend Setup

### 1. Install dependencies

```bash
cd chingiring-app
npm install
```

### 2. API URL — no configuration needed for most cases

The app auto-detects the correct backend URL:

| Platform | How URL is Resolved |
|---|---|
| **Web** | `http://localhost:8000` |
| **Android / iOS (Expo Go)** | Extracts host from `Constants.expoGoConfig?.debuggerHost` — works automatically when both devices are on the same Wi-Fi |
| **Override** | Set `EXPO_PUBLIC_API_URL` in `chingiring-app/.env` only if you need a specific URL |

> **Important**: Do **not** set `EXPO_PUBLIC_API_URL=http://localhost:8000`. This breaks native device testing by forcing `localhost` instead of the LAN IP.

### 3. Start the Expo development server

```bash
npm start
```

From the Expo CLI:
- Press `w` — open in web browser (port 8082)
- Press `a` — open on Android emulator or physical device
- Press `i` — open in iOS Simulator

Or run directly:

```bash
npm run web       # Web only
npm run android   # Android only
npm run ios       # iOS only
```

### 4. Testing on a physical Android device

1. Install **Expo Go** from the Play Store on your Android phone
2. Ensure phone and development machine are on the **same Wi-Fi network**
3. Run `npm start` on your machine
4. Scan the QR code in the terminal with Expo Go

The app auto-detects the backend IP. No manual IP configuration needed.

---

## Authentication Across Platforms

ChingiRingi uses a dual auth strategy depending on platform:

| Platform | Strategy | Token Storage |
|---|---|---|
| **Web** | HTTP-only cookies set by backend | Browser cookie jar |
| **Native (iOS / Android)** | Bearer token in `Authorization` header | `expo-secure-store` |

The Axios client in `src/api/client.ts` handles this transparently.

---

## Admin Access

To access the admin panel, a user needs `role: 'admin'` in MongoDB.

Set it manually via MongoDB Atlas or Compass:
```json
{ "role": "admin" }
```

Once set, logging in as that user routes to the **Admin Navigator** instead of the regular user app. The admin panel includes:

- **Dashboard** — KPIs, coins economy, top deals/users
- **Deals Management** — full CRUD with cashback type selector and category dropdown
- Placeholder screens for: Conversions, Withdrawals, Users, Products, Banners, Coupons, Orders, Inventory

---

## Developer Tooling (gstack)

gstack is installed at `~/.claude/skills/gstack`. It provides AI-powered slash commands in Claude Code:

```
/review          — Code review with auto-fixes
/cso             — OWASP Top 10 + STRIDE security audit
/ship            — Test, audit, push, open PR
/investigate     — Root-cause debugging
/plan-eng-review — Architecture review
/qa              — Browser testing (web builds only)
/office-hours    — Product strategy session
```

To update: `cd ~/.claude/skills/gstack && git pull && ./setup`

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `NETWORK ERROR` on physical Android | Ensure phone and machine are on same Wi-Fi. Do **not** set `EXPO_PUBLIC_API_URL`. |
| MongoDB connection error | Check `MONGO_URI` in `backend/.env`. Verify Atlas network access allows your IP. |
| CORS errors in browser | Ensure your origin is listed in `backend/src/app.js` CORS config. |
| 401 on all requests (web) | Check `withCredentials: true` in Axios and `credentials: true` in backend CORS. |
| 401 on all requests (native) | Verify SecureStore has a token. Check `Authorization: Bearer` header is being attached by the Axios request interceptor. |
| TurboModule crash on Android | Do not add `import 'react-native-gesture-handler'` to `App.tsx`. It must stay inside `DesktopDrawerNavigator.tsx` (web-only lazy import). |
| Code changes not picked up | Check `chingiring-app/.env` — if `EXPO_PUBLIC_API_URL=http://localhost:8000` is set, comment it out and run `npx expo start --clear`. |
| Expo build fails | Run `npx expo doctor` to check for dependency mismatches. |
