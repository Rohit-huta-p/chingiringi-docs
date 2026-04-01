# State Management

The app uses two Zustand stores for client-side state and TanStack React Query (configured but not yet wired to data-fetching in dashboard screens).

## Auth Store

**Source:** `src/store/index.ts`

**Hook:** `useAuthStore`

### State Shape

```typescript
interface AuthState {
  isAuthenticated: boolean;  // Whether the user has a valid session
  isReady: boolean;          // Whether hydration has completed
  user: UserType | null;     // Current user object
  hydrate: () => Promise<void>;
  logout: () => Promise<void>;
}
```

### User Type

```typescript
interface UserType {
  id: string;
  name: string;
  username: string;
  email?: string;
  phone?: string;
  role?: string;
}
```

### Actions

| Action | Behavior |
|---|---|
| `hydrate()` | Calls `GET /auth/me`. On success, sets `isAuthenticated: true`, populates `user`, and sets `isReady: true`. On failure, clears auth state and sets `isReady: true`. Called once on app mount in `App.tsx`. |
| `logout()` | Calls `POST /auth/logout` (failure is non-blocking). Immediately clears `isAuthenticated` and `user` from state, which triggers the navigation gate to switch to `AuthNavigator`. |

### Usage

- `App.tsx` -- calls `hydrate()` on mount; gates rendering on `isReady`.
- `RootNavigator` -- reads `isAuthenticated` to choose between auth and drawer navigators.
- `WalletScreen` -- reads `user?.name` for display.
- `SettingsScreen` -- calls `logout()` after confirmation modal.
- `Sidebar` -- reads `user?.name` to render the profile initial.

## UI Store

**Source:** `src/store/uiStore.ts`

**Hook:** `useUIStore`

### State Shape

```typescript
interface UIState {
  isSidebarCollapsed: boolean;   // Sidebar expanded (250px) or collapsed (80px)
  toggleSidebar: () => void;
}
```

### Actions

| Action | Behavior |
|---|---|
| `toggleSidebar()` | Flips `isSidebarCollapsed` between `true` and `false`. |

### Usage

- `DrawerNavigator` -- reads `isSidebarCollapsed` to set drawer width.
- `Sidebar` -- reads state for conditional rendering of labels; calls `toggleSidebar` on chevron button press.

## React Query

A `QueryClient` is instantiated in `App.tsx` and provided via `QueryClientProvider`. It is not yet used by any screen -- all dashboard data is currently hardcoded. It is ready for future API integration.

## API Layer

**Source:** `src/api/client.ts`, `src/api/auth.ts`

The Axios client (`apiClient`) is configured with:

- **Base URL:** `http://localhost:8000` (web) or `http://192.168.1.90:8000` (native), overridable via `EXPO_PUBLIC_API_URL`.
- **`withCredentials: true`** -- ensures HTTP-only cookies are sent with every request.
- **401 interceptor** -- on a 401 response, attempts `POST /auth/refresh` then retries the original request. Login and refresh routes are excluded from retry logic to prevent infinite loops.

### Auth API Methods

| Method | Endpoint | Purpose |
|---|---|---|
| `signup(data)` | `POST /auth/signup` | Register a new user |
| `login(data)` | `POST /auth/login` | Authenticate with credentials |
| `sendOtp(data)` | `POST /auth/send-otp` | Send OTP to phone or email |
| `verifyOtp(data)` | `POST /auth/verify-otp` | Verify OTP code |
| `logout()` | `POST /auth/logout` | End session |
| `forgotPassword(data)` | `POST /auth/forgot-password` | Request password reset email |
| `resetPassword(data)` | `POST /auth/reset-password` | Set new password with token |
| `getMe()` | `GET /auth/me` | Fetch current authenticated user |
