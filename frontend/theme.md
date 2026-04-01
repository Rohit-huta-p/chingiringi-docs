# Theme System

**Source:** `src/constants/theme.ts`

The theme module exports three objects -- `Colors`, `Typography`, and `Spacing` -- that serve as the design token foundation for all screens and components.

## Colors

| Token | Value | Usage |
|---|---|---|
| `primary` | `#3b82f6` | Primary brand blue, buttons, links, active states |
| `primaryLight` | `#eff6ff` | Light blue backgrounds, active nav item highlight |
| `background` | `#f8fafc` | Page/screen background |
| `surface` | `#ffffff` | Card backgrounds, sidebar, modals |
| `text` | `#1e293b` | Primary text color (dark slate) |
| `textSecondary` | `#64748b` | Secondary/muted text, labels, placeholders |
| `success` | `#10b981` | Positive amounts, confirmed statuses |
| `danger` | `#ef4444` | Error states, destructive actions, withdrawal amounts |
| `border` | `#e2e8f0` | Card borders, dividers, input outlines |

Additional colors used inline across screens but not in the token file:

- `#0f172a` -- Hero section dark background
- `#1e293b` -- Dark cards (sidebar profile referral card, live deals)
- `#f97316` -- Orange accent for cashback badges and expiry indicators
- `#6093f4` -- Button primary variant (slightly different from `Colors.primary`)
- `#f59e0b` -- Amber/warning for pending statuses

## Typography

| Token | Font Size | Font Weight | Color |
|---|---|---|---|
| `header` | 24 | `bold` | `Colors.text` |
| `title` | 18 | `600` | `Colors.text` |
| `body` | 14 | default | `Colors.text` |
| `bodySecondary` | 14 | default | `Colors.textSecondary` |

Most screens define their own font sizes inline rather than consuming these tokens directly. Common patterns observed across screens:

- Screen titles: 24--28px, weight 800
- Section titles: 16--20px, weight 700
- Body/descriptions: 13--15px
- Labels/captions: 10--12px, weight 600--700, uppercase with letter-spacing

## Spacing

| Token | Value (px) |
|---|---|
| `xs` | 4 |
| `sm` | 8 |
| `md` | 16 |
| `lg` | 24 |
| `xl` | 32 |

Spacing tokens are used primarily in `SettingsScreen`, `ConfirmationModal`, and `WithdrawModal`. Other screens tend to use raw numeric values with similar increments.
