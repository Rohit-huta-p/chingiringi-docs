# Input

A styled text input that extends React Native's `TextInputProps` with optional label, error message, and icon slots.

**File:** `chingiring-app/src/components/Input.tsx`

## Props

```typescript
interface InputProps extends TextInputProps {
  label?: string;
  error?: string;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
}
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `label` | `string` | `undefined` | Label text rendered above the input |
| `error` | `string` | `undefined` | Error message rendered below the input; also changes border color to `Colors.danger` |
| `leftIcon` | `React.ReactNode` | `undefined` | Element rendered inside the input container on the left |
| `rightIcon` | `React.ReactNode` | `undefined` | Element rendered inside the input container on the right |
| `...props` | `TextInputProps` | -- | All standard React Native `TextInput` props are forwarded (e.g. `placeholder`, `value`, `onChangeText`, `secureTextEntry`) |

## Styling Details

- Container has 16px bottom margin for consistent form spacing.
- Input container is 48px tall with 1px border (`Colors.border`), 8px border radius, and `Colors.surface` background.
- Placeholder text uses `Colors.textSecondary`.
- When `error` is set, the border color switches to `Colors.danger` and the error text appears in 12px red below the field.

## Usage

```tsx
import { Input } from '../components/Input';
import { Lock } from 'lucide-react-native';

// Basic labeled input
<Input
  label="Email"
  placeholder="you@example.com"
  value={email}
  onChangeText={setEmail}
  keyboardType="email-address"
/>

// Password input with left icon and error
<Input
  label="Password"
  placeholder="Enter password"
  secureTextEntry
  leftIcon={<Lock size={18} color="#999" />}
  error={passwordError}
  value={password}
  onChangeText={setPassword}
/>
```
