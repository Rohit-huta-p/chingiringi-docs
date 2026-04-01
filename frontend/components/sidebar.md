# Sidebar

The main navigation drawer component. Implements `DrawerContentComponentProps` from `@react-navigation/drawer` and supports a collapsible state managed via Zustand.

**File:** `chingiring-app/src/components/Sidebar.tsx`

## Props

The component receives the standard `DrawerContentComponentProps` from React Navigation. It does not define additional props.

## Internal Components

### NavItem

A private helper component used for each navigation link.

```typescript
{
  label: string;
  icon: React.ComponentType<any>;  // lucide-react-native icon component
  isActive: boolean;
  onPress: () => void;
  isCollapsed?: boolean;
}
```

When `isActive` is true, the item background uses `Colors.primaryLight` and the icon/label switch to `Colors.primary` with bolder weight. When `isCollapsed` is true, the label is hidden and the item is centered.

## Navigation Items

| Label | Icon | Route |
|---|---|---|
| Discover | `Home` | `Home` |
| Wallet | `Wallet` | `Wallet` |
| Referrals | `Users` | `Referrals` |
| Notifications | `Bell` | `Notifications` |
| Settings | `Settings` | `Settings` |

The first three items appear in the main (top) section. Notifications and Settings appear in the bottom section.

## Collapsible Behavior

- State is managed by `useUIStore` (Zustand): `isSidebarCollapsed` and `toggleSidebar`.
- When collapsed, the logo shrinks to just the "C" icon, nav labels are hidden, and the profile section hides user details.
- A chevron toggle button switches between `ChevronLeft` (collapse) and `ChevronRight` (expand).

## Profile Section

Displayed at the bottom, below a top border. Shows:
- Avatar circle with the user's first initial (from `useAuthStore`).
- User name and balance (currently hardcoded to "1250").
- Tapping navigates to the `Profile` screen.

## Usage

```tsx
import { createDrawerNavigator } from '@react-navigation/drawer';
import { Sidebar } from '../components/Sidebar';

const Drawer = createDrawerNavigator();

<Drawer.Navigator drawerContent={(props) => <Sidebar {...props} />}>
  {/* screens */}
</Drawer.Navigator>
```
