# Frontend Overview

## Technology Stack

| Layer | Technology | Version |
|---|---|---|
| Framework | Expo | ~55.0.8 |
| UI | React Native | 0.83.2 |
| Language | TypeScript | ~5.9.2 |
| Navigation | React Navigation (native-stack, drawer) | v7 |
| State Management | Zustand | ^5.0.12 |
| Server State | TanStack React Query | ^5.95.2 |
| HTTP Client | Axios | ^1.13.6 |
| Icons | lucide-react-native | ^1.6.0 |
| Secure Storage | expo-secure-store | ~55.0.9 |

## Cross-Platform Support

The app targets three platforms from a single codebase:

- **Web** -- `expo start --web` / `npx expo export -p web`
- **Android** -- `expo start --android`
- **iOS** -- `expo start --ios`

Platform-aware API base URL selection is handled in `src/api/client.ts` (localhost for web, LAN IP for native).

## Project Structure

```
chingiring-app/
  App.tsx                     # Root component (providers, hydration gate)
  src/
    api/
      client.ts               # Axios instance, interceptors, token refresh
      auth.ts                 # Auth API methods (login, signup, OTP, etc.)
    constants/
      theme.ts                # Colors, Typography, Spacing tokens
    store/
      index.ts                # Zustand auth store (user, hydrate, logout)
      uiStore.ts              # Zustand UI store (sidebar collapse state)
    navigation/
      RootNavigator.tsx       # Auth gate -- switches between Auth and Drawer
      AuthNavigator.tsx       # Native stack for unauthenticated screens
      DrawerNavigator.tsx     # Permanent drawer for authenticated screens
    screens/
      Auth/                   # Login, Signup, OTP, ForgotPassword, ResetPassword
      Dashboard/              # Home, Wallet, Refer, Profile, Settings, etc.
    components/
      Button.tsx              # Multi-variant button (primary, outline, pill, text)
      Input.tsx               # Labeled text input with error + icon support
      Card.tsx                # Card wrapper (elevated, outlined, flat)
      DealCard.tsx            # Cashback deal card for HomeScreen grid
      Sidebar.tsx             # Drawer sidebar with nav items and profile
      ConfirmationModal.tsx   # Reusable confirm/cancel modal
      WithdrawModal.tsx       # Withdrawal flow modal (UPI, Bank, Paytm)
```

## Key Architectural Patterns

### Authentication Gate

`App.tsx` hydrates the auth store on mount by calling `GET /auth/me`. While hydrating, a loading spinner is shown. Once ready, `RootNavigator` renders either the `AuthNavigator` (unauthenticated) or `DrawerNavigator` (authenticated) based on `isAuthenticated`.

### Cookie-Based Auth with Silent Refresh

The Axios client uses `withCredentials: true` to send HTTP-only cookies. A response interceptor catches 401 errors and attempts a silent refresh via `POST /auth/refresh` before retrying the original request.

### Permanent Sidebar Navigation

On authenticated screens, a permanent drawer navigator renders a collapsible sidebar. The sidebar width toggles between 80px (collapsed) and 250px (expanded), controlled by `useUIStore`.

### Static Mock Data

Most dashboard screens currently render hardcoded mock data arrays (deals, transactions, addresses) defined at the top of each screen file. These are placeholders for future API integration.
