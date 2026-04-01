# Navigation Structure

**Source files:**
- `src/navigation/RootNavigator.tsx`
- `src/navigation/AuthNavigator.tsx`
- `src/navigation/DrawerNavigator.tsx`
- `src/components/Sidebar.tsx`

## Overview

Navigation is powered by React Navigation v7. The root navigator acts as an authentication gate, rendering one of two sub-navigators based on the Zustand auth store.

```
RootNavigator (NavigationContainer)
  |
  +-- isAuthenticated === false --> AuthNavigator (NativeStackNavigator)
  |
  +-- isAuthenticated === true  --> DrawerNavigator (DrawerNavigator)
```

## Auth Navigator

**Type:** `createNativeStackNavigator`

All screens hide the default header (`headerShown: false`) and use `Colors.background` as the content background.

| Route Name | Screen Component | Purpose |
|---|---|---|
| `Login` | `LoginScreen` | Email/phone + password login |
| `Signup` | `SignupScreen` | New user registration |
| `OTPVerification` | `OTPVerificationScreen` | OTP code entry and verification |
| `ForgotPassword` | `ForgotPasswordScreen` | Request password reset |
| `ResetPassword` | `ResetPasswordScreen` | Set new password |

## Drawer Navigator

**Type:** `createDrawerNavigator`

The drawer is configured as `permanent` (always visible, not swipeable). The custom sidebar component (`Sidebar`) renders as the drawer content.

**Drawer style:** Width toggles between 80px (collapsed) and 250px (expanded) based on `useUIStore.isSidebarCollapsed`. Background is `Colors.surface` with a right border.

**Initial route:** `Home`

| Route Name | Screen Component | Visible in Sidebar |
|---|---|---|
| `Home` | `HomeScreen` | Yes (labeled "Discover") |
| `Wallet` | `WalletScreen` | Yes |
| `Referrals` | `ReferScreen` | Yes |
| `Notifications` | `HomeScreen` (reused) | Yes |
| `Settings` | `SettingsScreen` | Yes |
| `Profile` | `ProfileScreen` | Yes (profile section at bottom) |
| `EditProfile` | `EditProfileScreen` | No (navigated from Profile) |
| `MyAddress` | `MyAddressScreen` | No (navigated from Profile) |
| `TransactionHistory` | `TransactionHistoryScreen` | No (navigated from Wallet) |
| `ProductDetail` | `ProductDetailScreen` | No (navigated from Home) |

## Sidebar Component

The sidebar is split into three visual sections:

1. **Logo area** -- ChingiRingi brand mark with a collapse/expand toggle button.
2. **Main navigation** -- Discover (Home), Wallet, Referrals. Uses Lucide icons (`Home`, `Wallet`, `Users`).
3. **Bottom navigation** -- Notifications (`Bell`), Settings (`Settings`), followed by a profile card showing the user's initial and wallet balance. Tapping the profile card navigates to `Profile`.

Active route highlighting uses `Colors.primaryLight` as the background with `Colors.primary` for text and icon color.

## Navigation Patterns

- **Auth gate:** Switching between auth and dashboard stacks is driven by Zustand state, not imperative navigation calls.
- **Back navigation:** `EditProfile`, `MyAddress`, and `TransactionHistory` screens include a manual back button that calls `navigation.goBack()`.
- **Profile to EditProfile:** `ProfileScreen` navigates to `EditProfile` via `navigation.navigate('EditProfile')`.
- **Notifications placeholder:** The `Notifications` route currently reuses `HomeScreen` as a placeholder.
