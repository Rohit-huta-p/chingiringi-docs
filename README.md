# ChingiRingi Documentation

Welcome to the official documentation for **ChingiRingi** — India's passive income earning ecosystem.

ChingiRingi helps users earn cashback, referral rewards, and loyalty coins while shopping from their favourite brands through affiliate links.

---

## Project Status

**Overall completion: ~40%** — Phase 1 (Core Product) is ~65–70% complete.

| Area | Status |
|---|---|
| Backend API (auth, deals, wallet, profile, addresses, admin) | Complete |
| Auth screens (Login, Signup, OTP, Forgot/Reset Password) | Complete |
| Home screen (deals grid, categories, trending brands) | Complete |
| Wallet screen (balances, recent transactions) | Complete |
| Profile + Edit Profile | Complete |
| My Addresses + Add/Edit Address | Complete |
| Product Detail (Shop Now with click tracking) | Complete |
| Admin Dashboard + Admin Deals CRUD | Complete |
| Transaction History (filter UI only — data not wired) | Partial |
| Refer & Earn (referral code shown — stats hardcoded) | Partial |
| Notifications screen | Placeholder |
| OTP delivery (MSG91 SMS integration) | Not started |
| Withdrawals, Admitad webhook, push notifications | Phase 2 |

See [Development Phases](PHASES.md) for the full roadmap.

---

## Quick Links

* [Getting Started](getting-started.md)
* [Development Phases](PHASES.md)
* [Architecture Overview](architecture/overview.md)
* [Tech Stack](architecture/tech-stack.md)
* [Frontend Guide](frontend/overview.md)
* [Navigation Structure](frontend/navigation.md)
* [Screens & Components](frontend/screens/README.md)
* [Backend API Reference](backend/api-reference.md)
* [Authentication](backend/authentication.md)
* [Database Models](backend/database-models.md)

---

## Tech Summary

| Layer | Technology |
|---|---|
| Mobile / Web app | React Native (Expo SDK 55), React 19, TypeScript |
| State management | Zustand (auth/UI) + TanStack React Query (server state) |
| Navigation | React Navigation v7 (Stack + BottomTabs + Drawer) |
| HTTP client | Axios with silent refresh interceptor |
| Backend | Node.js + Express.js (ES Modules) |
| Database | MongoDB Atlas (Mongoose ODM) |
| Auth | Dual-strategy JWT: cookies (web) + Bearer + expo-secure-store (native) |
| Validation | Zod (backend schemas) |
