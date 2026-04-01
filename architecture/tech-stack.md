# Tech Stack

All versions listed below are taken directly from the project's `package.json` files.

---

## Frontend (`chingiring-app/`)

### Runtime and Framework

| Technology | Version | Purpose |
|---|---|---|
| Expo | ~55.0.8 | Managed React Native toolchain |
| React | 19.2.0 | UI library |
| React Native | 0.83.2 | Cross-platform native rendering |
| React Native Web | ^0.21.0 | Web target support for React Native |
| TypeScript | ~5.9.2 | Static type checking |

### Navigation

| Package | Version |
|---|---|
| @react-navigation/native | ^7.1.34 |
| @react-navigation/native-stack | ^7.14.6 |
| @react-navigation/drawer | ^7.9.5 |
| react-native-screens | ~4.23.0 |
| react-native-safe-area-context | ~5.6.2 |
| react-native-gesture-handler | ~2.30.0 |
| react-native-reanimated | 4.2.1 |

### State Management and Data Fetching

| Package | Version | Purpose |
|---|---|---|
| Zustand | ^5.0.12 | Lightweight global state management |
| @tanstack/react-query | ^5.95.2 | Server state, caching, and mutations |
| Axios | ^1.13.6 | HTTP client with interceptors |

### UI and Assets

| Package | Version | Purpose |
|---|---|---|
| lucide-react-native | ^1.6.0 | Icon library |
| react-native-svg | 15.15.3 | SVG rendering |
| expo-status-bar | ~55.0.4 | Status bar control |

### Security

| Package | Version | Purpose |
|---|---|---|
| expo-secure-store | ~55.0.9 | Secure key-value storage on native devices |

---

## Backend (`backend/`)

### Runtime and Framework

| Technology | Version | Purpose |
|---|---|---|
| Node.js | (runtime) | Server-side JavaScript |
| Express | ^4.22.1 | HTTP framework |
| ES Modules | (`"type": "module"`) | Native ESM imports |

### Database

| Package | Version | Purpose |
|---|---|---|
| Mongoose | ^9.3.2 | MongoDB ODM |

### Authentication and Security

| Package | Version | Purpose |
|---|---|---|
| jsonwebtoken | ^9.0.3 | JWT signing and verification |
| bcrypt | ^6.0.0 | Password hashing |
| helmet | ^8.1.0 | Security HTTP headers |
| cors | ^2.8.6 | Cross-origin resource sharing |
| cookie-parser | ^1.4.7 | Parse and sign cookies |
| express-rate-limit | ^8.3.1 | Request rate limiting |

### Validation

| Package | Version | Purpose |
|---|---|---|
| Zod | ^4.3.6 | Schema-based request validation |

### Utilities

| Package | Version | Purpose |
|---|---|---|
| dotenv | ^17.3.1 | Environment variable loading |
| morgan | ^1.10.1 | HTTP request logging (development) |
| express-async-errors | ^3.1.1 | Async error propagation in Express |

### Development Tools

| Package | Version | Purpose |
|---|---|---|
| nodemon | ^3.1.14 | Auto-restart on file changes |
| eslint | ^10.1.0 | Code linting |
| prettier | ^3.8.1 | Code formatting |
| eslint-config-prettier | ^10.1.8 | Disable ESLint rules that conflict with Prettier |
| eslint-plugin-prettier | ^5.5.5 | Run Prettier as an ESLint rule |
