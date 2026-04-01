# DealCard

A touchable card that displays a cashback deal with brand image area, cashback badge, description, and expiry countdown.

**File:** `chingiring-app/src/components/DealCard.tsx`

## Props

```typescript
interface DealCardProps {
  brand: string;
  description: string;
  cashback: string;
  expiresIn: string;
  imageUrl?: string;
}
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `brand` | `string` | *required* | Brand name displayed as the card title (single line, truncated) |
| `description` | `string` | *required* | Deal description (max 2 lines, truncated) |
| `cashback` | `string` | *required* | Cashback value shown in the badge (e.g. `"10% Cashback"`) |
| `expiresIn` | `string` | *required* | Expiry text shown in the footer (e.g. `"2 days left"`) |
| `imageUrl` | `string` | `undefined` | Currently unused -- a placeholder circle is rendered instead |

## Layout

The card is wrapped in a `TouchableOpacity` and consists of two sections:

1. **Image area** (140px height, light gray background) -- Contains a circular placeholder and a cashback badge positioned at the top-left corner. The badge uses `Colors.primary` background with white bold text.
2. **Content area** (16px padding) -- Brand title (18px bold), description (14px secondary), and a footer row with an orange expiry indicator.

## Sizing

The card uses `flex: 1` with `minWidth: 200` and 10px margin, making it suitable for use in a `FlatList` with `numColumns={2}` or a flex-wrap container.

## Usage

```tsx
import { DealCard } from '../components/DealCard';

<DealCard
  brand="Amazon"
  description="Get cashback on electronics purchases"
  cashback="5% Cashback"
  expiresIn="3 days left"
/>
```
