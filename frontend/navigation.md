# Navigation Structure

**Source files:**
- `src/navigation/RootNavigator.tsx`
- `src/navigation/AuthNavigator.tsx`
- `src/navigation/DrawerNavigator.tsx` — responsive user navigator
- `src/navigation/DesktopDrawerNavigator.tsx` — web-only permanent drawer (lazy-loaded)
- `src/navigation/AdminNavigator.tsx` — responsive admin navigator
- `src/navigation/DesktopAdminDrawer.tsx` — web-only admin permanent drawer (lazy-loaded)
- `src/components/Sidebar.tsx` — user sidebar component
- `src/components/AdminSidebar.tsx` — admin sidebar component

---

## Root Navigator

`RootNavigator` wraps everything in a `NavigationContainer` and conditionally renders one of three navigator trees based on auth state and user role:

```tsx
// RootNavigator.tsx
const isAuthenticated = useAuthStore(state => state.isAuthenticated);
const user = useAuthStore(state => state.user);

if (!isAuthenticated)       → <AuthNavigator />
if (user?.role === 'admin') → <AdminNavigator />
else                        → <ResponsiveNavigator />
```

---

## Auth Navigator

**Type:** `createNativeStackNavigator`

All screens use `headerShown: false`.

| Route | Screen | Purpose |
|---|---|---|
| `Login` | `LoginScreen` | Email/phone + password login, OTP tab |
| `Signup` | `SignupScreen` | New user registration |
| `OTPVerification` | `OTPVerificationScreen` | 6-digit OTP entry |
| `ForgotPassword` | `ForgotPasswordScreen` | Request password reset |
| `ResetPassword` | `ResetPasswordScreen` | Set new password |

---

## User Navigator (Responsive)

`ResponsiveNavigator` in `DrawerNavigator.tsx` renders differently based on platform and screen width:

```
Platform.OS !== 'web'  OR  width < 768
  → MobileNavigator (Stack + BottomTabNavigator)

Platform.OS === 'web'  AND  width >= 768
  → <Suspense><DesktopDrawerNavigator /></Suspense>  ← lazy-loaded
```

### Mobile Navigator

A `NativeStackNavigator` wraps a `BottomTabNavigator` and adds detail screens as stack routes:

**Bottom Tabs** (always visible at the bottom on mobile):

| Tab | Screen | Icon |
|---|---|---|
| Home | `HomeScreen` | `Home` (Lucide) |
| Wallet | `WalletScreen` | `Wallet` |
| Referrals | `ReferScreen` | `Users` |
| Alerts | `NotificationsScreen` | `Bell` |
| Settings | `SettingsScreen` | `Settings` |

**Stack routes** (pushed on top of tabs):

| Route | Screen |
|---|---|
| `Profile` | `ProfileScreen` |
| `EditProfile` | `EditProfileScreen` |
| `MyAddress` | `MyAddressScreen` |
| `AddEditAddress` | `AddEditAddressScreen` |
| `TransactionHistory` | `TransactionHistoryScreen` |
| `ProductDetail` | `ProductDetailScreen` |

### Desktop Drawer Navigator

`DesktopDrawerNavigator.tsx` is **only loaded on web** via `React.lazy()`. It contains `import 'react-native-gesture-handler'` and `createDrawerNavigator` — keeping these imports out of native bundles prevents the TurboModule crash on Expo Go.

**Drawer style:** Permanent (always visible). Width toggles between `250px` (expanded) and `80px` (icon-only collapsed) based on `useUIStore.isSidebarCollapsed`.

| Route | Screen | Visible in Sidebar |
|---|---|---|
| `Home` | `HomeScreen` | Yes — "Discover" |
| `Wallet` | `WalletScreen` | Yes |
| `Referrals` | `ReferScreen` | Yes |
| `Notifications` | `NotificationsScreen` | Yes |
| `Settings` | `SettingsScreen` | Yes |
| `Profile` | `ProfileScreen` | Via profile card |
| `EditProfile` | `EditProfileScreen` | No (from Profile) |
| `MyAddress` | `MyAddressScreen` | No (from Profile) |
| `AddEditAddress` | `AddEditAddressScreen` | No (from MyAddress) |
| `TransactionHistory` | `TransactionHistoryScreen` | No (from Wallet) |
| `ProductDetail` | `ProductDetailScreen` | No (from Home) |

### User Sidebar Component (`Sidebar.tsx`)

Three sections:
1. **Logo area** — ChingiRingi brand + collapse/expand toggle
2. **Main nav** — Discover, Wallet, Referrals (Lucide icons)
3. **Bottom nav** — Notifications, Settings, Profile card (shows user initial + wallet balance)

Active route highlighted with `Colors.primaryLight` background + `Colors.primary` text/icon.

---

## Admin Navigator (Responsive)

`AdminNavigator.tsx` follows the same responsive pattern as the user navigator:

```
Platform.OS !== 'web'  OR  width < 768
  → MobileAdminNavigator (NativeStackNavigator with header)

Platform.OS === 'web'  AND  width >= 768
  → <Suspense><DesktopAdminDrawer /></Suspense>  ← lazy-loaded
```

### Mobile Admin Navigator

Stack with header shown (`headerTintColor: Colors.primary`):

| Route | Screen | Header Title |
|---|---|---|
| `AdminDashboard` | `AdminDashboardScreen` | Admin Dashboard |
| `AdminDeals` | `AdminDealsScreen` | Deals |
| `AdminConversions` | Placeholder | Conversions |
| `AdminWithdrawals` | Placeholder | Withdrawals |
| `AdminUsers` | Placeholder | Users |
| `AdminAllProducts` | Placeholder | Products |
| `AdminCategories` | Placeholder | Categories |
| `AdminOrders` | Placeholder | Orders |
| `AdminInventory` | Placeholder | Inventory |
| `AdminBanners` | Placeholder | Banners |
| `AdminCoupons` | Placeholder | Coupons |

### Desktop Admin Drawer

Permanent drawer (250px) with `AdminSidebar` as drawer content. Same routes as mobile admin.

### Admin Sidebar Component (`AdminSidebar.tsx`)

- **Admin profile badge** — "SA" initials badge at the top
- **Nav items** — Dashboard, Deals, Conversions, Withdrawals, Users
- **Products submenu** — expandable with ChevronDown/Up; shows All Products, Categories, Orders, Inventory
- **Banners, Coupons**
- **Logout button** at the bottom

---

## Navigation Patterns

| Pattern | Implementation |
|---|---|
| Auth gate | Zustand `isAuthenticated` drives which root navigator renders — no imperative navigation calls |
| Role-based routing | `user?.role === 'admin'` drives AdminNavigator vs ResponsiveNavigator |
| Lazy loading | `React.lazy()` + `Suspense` for both desktop drawers — prevents reanimated from loading on native |
| Back navigation | `navigation.goBack()` in screens that have a back button |
| Address add/edit | `navigation.navigate('AddEditAddress', { address: raw })` for edit, no params for add |
| Deal detail | `navigation.navigate('ProductDetail', { deal, dealId })` — passes full deal object to avoid extra API call |
