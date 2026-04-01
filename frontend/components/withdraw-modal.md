# WithdrawModal

A full-screen modal for withdrawing funds. Supports three withdrawal methods (UPI, Bank, Paytm) with quick-amount selection.

**File:** `chingiring-app/src/components/WithdrawModal.tsx`

## Props

```typescript
interface WithdrawModalProps {
  visible: boolean;
  onClose: () => void;
}
```

| Prop | Type | Description |
|---|---|---|
| `visible` | `boolean` | Controls modal visibility |
| `onClose` | `() => void` | Called when the user dismisses the modal (overlay tap, close button, or after confirmation) |

## Internal State

| State | Type | Description |
|---|---|---|
| `activeTab` | `'UPI' \| 'Bank' \| 'Paytm'` | Currently selected withdrawal method (default: `'UPI'`) |
| `accountInput` | `string` | Destination account identifier |
| `amount` | `string` | Withdrawal amount |

## Sections

1. **Header** -- Title ("Withdraw Funds") and a close button.
2. **Available Balance** -- Green banner showing the current balance (currently hardcoded to 1250).
3. **Withdraw To** -- Three tab buttons (UPI, Bank, Paytm). Each tab changes the account input placeholder:
   - UPI: "Enter UPI ID"
   - Bank: "Enter bank account number"
   - Paytm: "Enter Paytm number"
4. **Amount** -- Numeric input with "Minimum 100" placeholder.
5. **Quick Amount Pills** -- Predefined amounts: 100, 500, 1000. Tapping sets the amount field. The active pill is highlighted with `Colors.primaryLight` border.
6. **Confirm Button** -- Triggers `handleConfirm` (withdrawal API integration is a TODO) and closes the modal.

Dismissing the modal (overlay press or close button) resets all internal state.

## Usage

```tsx
import { WithdrawModal } from '../components/WithdrawModal';

const [showWithdraw, setShowWithdraw] = useState(false);

<WithdrawModal
  visible={showWithdraw}
  onClose={() => setShowWithdraw(false)}
/>
```
