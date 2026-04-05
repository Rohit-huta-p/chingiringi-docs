# Components Overview

ChingiRingi's frontend is built with React Native and uses a set of reusable UI components located in `chingiring-app/src/components/`. All components are typed with TypeScript and use the shared `Colors` and `Spacing` constants from `../constants/theme`.

## Component Index

### User-Facing Components

| Component | File | Description |
|---|---|---|
| [Card](card.md) | `Card.tsx` | Generic container with elevation, outlined, and flat variants |
| [Button](button.md) | `Button.tsx` | Multi-variant button with loading state and icon support |
| [Input](input.md) | `Input.tsx` | Text input with label, error state, and icon slots |
| [DealCard](deal-card.md) | `DealCard.tsx` | Cashback deal display card with brand, description, and expiry |
| [Sidebar](sidebar.md) | `Sidebar.tsx` | Collapsible drawer navigation for the user app (web/desktop). Three sections: logo + collapse toggle, main nav (Discover, Wallet, Referrals), bottom nav (Notifications, Settings, Profile card with wallet balance). Active route highlighted. |
| [WithdrawModal](withdraw-modal.md) | `WithdrawModal.tsx` | Modal for withdrawing funds via UPI, Bank, or Paytm |
| [ConfirmationModal](confirmation-modal.md) | `ConfirmationModal.tsx` | Generic confirmation dialog with customizable action |

### Admin Components

| Component | File | Description |
|---|---|---|
| AdminSidebar | `AdminSidebar.tsx` | Permanent 250px drawer sidebar for the admin app (web/desktop). Sections: admin profile badge ("SA" initials), nav items (Dashboard, Deals, Conversions, Withdrawals, Users), Products submenu (expandable with ChevronDown/Up — All Products, Categories, Orders, Inventory), Banners, Coupons, and a Logout button at the bottom. |

## Shared Dependencies

- **Theme constants**: `Colors`, `Spacing` from `../constants/theme`
- **Icons**: `lucide-react-native` (used in Sidebar, AdminSidebar, DealCard, and throughout screens)
- **State management**: Zustand stores (`useUIStore` for sidebar collapse state, `useAuthStore` for user/wallet data)
- **Navigation**: `@react-navigation/drawer` (used in Sidebar and AdminSidebar — web only)
