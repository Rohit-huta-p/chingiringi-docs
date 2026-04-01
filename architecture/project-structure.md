# Project Structure

```
Chingiringi/
|
+-- chingiring-app/               # React Native (Expo) frontend
|   +-- App.tsx                   # App entry point, providers, auth hydration
|   +-- index.ts                  # Expo entry point
|   +-- app.json                  # Expo configuration
|   +-- tsconfig.json             # TypeScript configuration
|   +-- package.json
|   +-- assets/                   # Static assets (images, fonts)
|   +-- dist/                     # Web build output
|   +-- src/
|       +-- api/
|       |   +-- client.ts         # Axios instance, base URL, interceptors
|       |   +-- auth.ts           # Auth API methods (login, signup, OTP, etc.)
|       +-- components/
|       |   +-- Sidebar.tsx       # Drawer sidebar component
|       |   +-- ...
|       +-- constants/
|       |   +-- theme.ts          # Colors and design tokens
|       +-- hooks/                # Custom React hooks
|       +-- navigation/
|       |   +-- RootNavigator.tsx       # Auth-gated root navigator
|       |   +-- AuthNavigator.tsx       # Unauthenticated stack screens
|       |   +-- DrawerNavigator.tsx     # Authenticated drawer screens
|       +-- screens/
|       |   +-- Auth/
|       |   |   +-- LoginScreen.tsx
|       |   |   +-- SignupScreen.tsx
|       |   |   +-- OTPVerificationScreen.tsx
|       |   |   +-- ForgotPasswordScreen.tsx
|       |   |   +-- ResetPasswordScreen.tsx
|       |   +-- Dashboard/
|       |       +-- HomeScreen.tsx
|       |       +-- WalletScreen.tsx
|       |       +-- ReferScreen.tsx
|       |       +-- ProfileScreen.tsx
|       |       +-- EditProfileScreen.tsx
|       |       +-- SettingsScreen.tsx
|       |       +-- MyAddressScreen.tsx
|       |       +-- TransactionHistoryScreen.tsx
|       |       +-- ProductDetailScreen.tsx
|       +-- store/
|       |   +-- index.ts          # Auth store (Zustand)
|       |   +-- uiStore.ts        # UI state store (Zustand)
|       +-- types/                # TypeScript type definitions
|       +-- utils/                # Utility functions
|
+-- backend/                      # Express.js REST API
|   +-- package.json
|   +-- src/
|       +-- server.js             # Entry point, starts HTTP server
|       +-- app.js                # Express app config, middleware, routes
|       +-- config/
|       |   +-- db.js             # MongoDB connection (Mongoose)
|       +-- middleware/
|       |   +-- authMiddleware.js     # JWT cookie verification guard
|       |   +-- errorMiddleware.js    # 404 and global error handlers
|       +-- modules/
|       |   +-- auth/
|       |   |   +-- authController.js # Auth request handlers
|       |   |   +-- authRoutes.js     # Auth route definitions
|       |   |   +-- authService.js    # Auth business logic
|       |   +-- otp/
|       |   |   +-- otpModel.js       # OTP Mongoose model
|       |   +-- users/
|       |   |   +-- userModel.js      # User Mongoose model
|       |   +-- wallet/
|       |       +-- walletModel.js    # Wallet Mongoose model
|       +-- utils/
|           +-- generateToken.js  # JWT token pair generation + cookie setting
|
+-- Designs/                      # Design assets
+-- Docs/                         # Documentation
+-- PRD.md                        # Product requirements document
```

---

## Key Conventions

### Frontend

- **Screens** are organized by authentication context: `Auth/` for unauthenticated flows, `Dashboard/` for authenticated flows.
- **API layer** is centralized in `src/api/`. All HTTP calls go through the shared Axios client, which handles cookie-based auth and automatic token refresh.
- **State** is split by concern: `useAuthStore` for authentication, `useUIStore` for interface preferences. Server data is managed by TanStack React Query rather than global stores.
- **Navigation** uses React Navigation v7 with a native stack for auth screens and a permanent drawer for dashboard screens.

### Backend

- **Modular structure**: each domain (auth, users, wallet, OTP) lives in its own folder under `src/modules/` with its own controller, routes, service, and model files as needed.
- **Shared middleware** (auth guard, error handling) lives in `src/middleware/`.
- **ES Modules**: the backend uses native ES module syntax (`import`/`export`) via `"type": "module"` in `package.json`.
- **Environment configuration** is loaded from a `.env` file via `dotenv` at the top of `app.js`.
