# ConfirmationModal

A reusable confirmation dialog with icon, title, message, and two action buttons (Cancel / Confirm).

**File:** `chingiring-app/src/components/ConfirmationModal.tsx`

## Props

```typescript
interface ConfirmationModalProps {
  visible: boolean;
  onClose: () => void;
  onConfirm: () => void;
  title: string;
  message: string;
  confirmText: string;
  confirmColor?: string;
  icon?: string;
}
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `visible` | `boolean` | *required* | Controls modal visibility |
| `onClose` | `() => void` | *required* | Called on Cancel button press or overlay tap |
| `onConfirm` | `() => void` | *required* | Called on confirm button press |
| `title` | `string` | *required* | Dialog title |
| `message` | `string` | *required* | Descriptive body text |
| `confirmText` | `string` | *required* | Label for the confirm button (e.g. "Delete", "Log Out") |
| `confirmColor` | `string` | `Colors.danger` | Background color of the confirm button and tint for the icon circle |
| `icon` | `string` | `undefined` | Icon key -- `'warning'` or `'logout'`. Defaults to warning if unrecognized or omitted |

## Icon Mapping

The `icon` prop maps to an emoji rendered inside a tinted circle:

| Key | Display |
|---|---|
| `'warning'` | Warning symbol |
| `'logout'` | Door symbol |
| any other / undefined | Warning symbol (default) |

The icon circle background uses `confirmColor` at 15% opacity (appended as hex `'15'`).

## Layout

- Centered modal card, max width 400px, 16px border radius.
- Icon circle (56px) at the top, followed by title (20px bold), message (14px secondary, centered), and a row of two equal-width buttons.
- Cancel button has an outlined style; confirm button uses the `confirmColor` background with white text.

## Usage

```tsx
import { ConfirmationModal } from '../components/ConfirmationModal';

<ConfirmationModal
  visible={showLogout}
  onClose={() => setShowLogout(false)}
  onConfirm={handleLogout}
  title="Log Out?"
  message="You will need to sign in again to access your account."
  confirmText="Log Out"
  confirmColor="#EF4444"
  icon="logout"
/>
```
