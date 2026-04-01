# MyAddressScreen

**Source:** `src/screens/Dashboard/MyAddressScreen.tsx`

**Route name:** `MyAddress`

## Purpose

Displays a list of the user's saved delivery addresses with options to add, edit, delete, and set a default address.

## Layout Sections

### 1. Header

- Back button (calls `navigation.goBack()`).
- "ACCOUNT" label and "My Addresses" title.
- "+ Add Address" button (primary blue, top-right). Not yet functional.

### 2. Address Cards

Each address is rendered by the `AddressCard` sub-component inside a `Card` (outlined variant). The card contains:

- **Icon** -- Colored circle (blue for home, purple for work) with a type icon.
- **Label** -- "Home" or "Work" with an optional green "Default" badge.
- **Action buttons** -- Edit (pencil icon) and Delete (X icon) in the top-right. Not yet functional.
- **Details** -- Name, phone number, two address lines.
- **Set as default** -- Shown only for non-default addresses. Not yet functional.

## Data

The `ADDRESSES` array contains 2 hardcoded entries:

| ID | Type | Label | Default | Location |
|---|---|---|---|---|
| 1 | home | Home | Yes | 301, Sunrise Apartments, MG Road, Mumbai |
| 2 | work | Work | No | Tech Park, Block B, 5th Floor, Mumbai |

### Address Interface

```typescript
interface Address {
  id: string;
  type: 'home' | 'work';
  label: string;
  isDefault: boolean;
  name: string;
  phone: string;
  line1: string;
  line2: string;
}
```

## State

No component state. No API calls. All data is hardcoded.

## Navigation

- **Inbound:** Navigated to from Profile or Quick Actions.
- **Outbound:** Back button calls `navigation.goBack()`.

## Components Used

- `Card` -- wraps each address entry (outlined variant).
