# Card

A generic container component that wraps content with consistent styling. Supports three visual variants.

**File:** `chingiring-app/src/components/Card.tsx`

## Props

```typescript
interface CardProps {
  children: React.ReactNode;
  style?: ViewStyle | ViewStyle[];
  variant?: 'elevated' | 'outlined' | 'flat';
}
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `children` | `React.ReactNode` | *required* | Content rendered inside the card |
| `style` | `ViewStyle \| ViewStyle[]` | `undefined` | Additional styles merged onto the card container |
| `variant` | `'elevated' \| 'outlined' \| 'flat'` | `'elevated'` | Visual style of the card |

## Variants

| Variant | Appearance |
|---|---|
| `elevated` | White background with a subtle drop shadow (`elevation: 3`) |
| `outlined` | White background with a 1px border using `Colors.border` |
| `flat` | Transparent background, no border or shadow |

All variants share a base style of `Colors.surface` background, 12px border radius, and 16px padding (overridden to transparent for `flat`).

## Usage

```tsx
import { Card } from '../components/Card';

// Default elevated card
<Card>
  <Text>Elevated content</Text>
</Card>

// Outlined card with custom margin
<Card variant="outlined" style={{ marginTop: 16 }}>
  <Text>Outlined content</Text>
</Card>

// Flat card (transparent background)
<Card variant="flat">
  <Text>Flat content</Text>
</Card>
```
