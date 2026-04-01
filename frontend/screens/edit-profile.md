# EditProfileScreen

**Source:** `src/screens/Dashboard/EditProfileScreen.tsx`

**Route name:** `EditProfile`

## Purpose

A form screen for editing the user's profile information -- name, email, and phone number. Reached from the Profile screen.

## Layout Sections

### 1. Header

- Back button (circular, calls `navigation.goBack()`).
- "ACCOUNT" label and "Edit Profile" title.

### 2. Avatar Section

- Large avatar placeholder (120px circle).
- Camera icon overlay (blue circle).
- "Click to upload new photo" hint text. Upload is not yet functional.

### 3. Form Card

A `Card` component (max-width 500px, centered) containing:

| Field | Component | Editable | Notes |
|---|---|---|---|
| Full Name | `Input` | Yes | Pre-filled with "Dev Chavan" |
| Username | Disabled view | No | Shows "Dev Chavan", labeled "Can't be changed" |
| Email Address | `Input` | Yes | Pre-filled, helper text about notifications |
| Phone Number | `Input` | Yes | Pre-filled, helper text about OTP and cashback updates |

### 4. Action Buttons

Two buttons in a horizontal row:
- **Cancel** (`outline` variant) -- calls `navigation.goBack()`.
- **Save Changes** (`primary` variant) -- calls `navigation.goBack()` (save logic not yet implemented).

## State

| State | Type | Default | Purpose |
|---|---|---|---|
| `fullName` | `string` | `'Dev Chavan'` | Editable name field |
| `username` | `string` | `'Dev Chavan'` | Read-only display |
| `email` | `string` | `'dev.chavan@email.com'` | Editable email field |
| `phone` | `string` | `'9876543210'` | Editable phone field |

No API calls. Save currently just navigates back.

## Navigation

- **Inbound:** From `ProfileScreen` via the "Edit Profile" link.
- **Outbound:** Back button and both action buttons return to the previous screen via `goBack()`.

## Components Used

- `Card` -- wraps the form.
- `Input` -- reusable text input with label.
- `Button` -- Cancel (outline) and Save (primary) actions.
