# Button

A multi-variant pressable button with built-in loading spinner and optional leading icon.

**File:** `chingiring-app/src/components/Button.tsx`

## Props

```typescript
type ButtonVariant = 'primary' | 'outline' | 'text' | 'pillActive' | 'pillInactive';

interface ButtonProps {
  title: string;
  onPress: () => void;
  variant?: ButtonVariant;
  disabled?: boolean;
  loading?: boolean;
  style?: ViewStyle;
  textStyle?: TextStyle;
  icon?: React.ReactNode;
}
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `title` | `string` | *required* | Button label text |
| `onPress` | `() => void` | *required* | Press handler |
| `variant` | `ButtonVariant` | `'primary'` | Visual style variant |
| `disabled` | `boolean` | `false` | Disables press and reduces opacity to 0.5 |
| `loading` | `boolean` | `false` | Replaces label with an `ActivityIndicator` and disables press |
| `style` | `ViewStyle` | `undefined` | Additional container styles |
| `textStyle` | `TextStyle` | `undefined` | Additional label styles |
| `icon` | `React.ReactNode` | `undefined` | Element rendered before the label (8px left margin on text) |

## Variants

| Variant | Background | Text Color | Shape |
|---|---|---|---|
| `primary` | `#6093f4` (blue) | White | Rounded rect, 48px height |
| `outline` | Transparent, 1px border | `Colors.text` | Rounded rect, 48px height |
| `text` | Transparent, no border | `Colors.primary` | Auto height, no horizontal padding |
| `pillActive` | `#000` (black) | White | Pill (20px radius), 36px height |
| `pillInactive` | `#f3f4f6` (light gray) | `Colors.text` | Pill (20px radius), 36px height |

## Usage

```tsx
import { Button } from '../components/Button';

// Primary action
<Button title="Sign In" onPress={handleSignIn} />

// Outline variant with loading
<Button title="Submit" variant="outline" loading={isSubmitting} onPress={handleSubmit} />

// Pill toggle pair
<Button title="Active" variant="pillActive" onPress={() => {}} />
<Button title="Inactive" variant="pillInactive" onPress={() => {}} />

// With icon
<Button title="Add" icon={<PlusIcon />} onPress={handleAdd} />
```
