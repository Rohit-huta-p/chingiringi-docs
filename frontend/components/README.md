# Components Overview

ChingiRingi's frontend is built with React Native and uses a set of reusable UI components located in `chingiring-app/src/components/`. All components are typed with TypeScript and use the shared `Colors` and `Spacing` constants from `../constants/theme`.

## Component Index

| Component | File | Description |
|---|---|---|
| [Card](card.md) | `Card.tsx` | Generic container with elevation, outlined, and flat variants |
| [Button](button.md) | `Button.tsx` | Multi-variant button with loading state and icon support |
| [Input](input.md) | `Input.tsx` | Text input with label, error state, and icon slots |
| [DealCard](deal-card.md) | `DealCard.tsx` | Cashback deal display card with brand, description, and expiry |
| [Sidebar](sidebar.md) | `Sidebar.tsx` | Collapsible drawer navigation with profile section |
| [WithdrawModal](withdraw-modal.md) | `WithdrawModal.tsx` | Modal for withdrawing funds via UPI, Bank, or Paytm |
| [ConfirmationModal](confirmation-modal.md) | `ConfirmationModal.tsx` | Generic confirmation dialog with customizable action |

## Shared Dependencies

- **Theme constants**: `Colors`, `Spacing` from `../constants/theme`
- **Icons**: `lucide-react-native` (used in Sidebar)
- **State management**: Zustand stores (`useUIStore`, `useAuthStore`)
- **Navigation**: `@react-navigation/drawer` (used in Sidebar)
