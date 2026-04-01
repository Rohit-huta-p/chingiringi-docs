# Getting Started

This guide walks you through setting up the ChingiRingi project for local development. The project consists of two parts: a React Native (Expo) frontend and a Node.js/Express backend.

---

## Prerequisites

- **Node.js** v18 or later
- **npm** (bundled with Node.js)
- **MongoDB** instance (local or cloud, e.g. MongoDB Atlas)
- **Expo CLI** (installed globally or via `npx`)
- **Git**

---

## Repository Structure

```
Chingiringi/
  chingiring-app/   # React Native (Expo) frontend
  backend/          # Express.js REST API
```

---

## Backend Setup

### 1. Install dependencies

```bash
cd backend
npm install
```

### 2. Configure environment variables

Create a `.env` file in the `backend/` directory with the following keys:

```
NODE_ENV=development
PORT=5000
MONGO_URI=<your-mongodb-connection-string>
JWT_ACCESS_SECRET=<your-access-token-secret>
JWT_REFRESH_SECRET=<your-refresh-token-secret>
JWT_ACCESS_EXPIRATION=15m
JWT_REFRESH_EXPIRATION=30d
COOKIE_SECRET=<your-cookie-signing-secret>
```

### 3. Start the development server

```bash
npm run dev
```

This uses `nodemon` to watch for file changes and automatically restart the server. The API will be available at `http://localhost:5000` by default.

### 4. Verify the server is running

```bash
curl http://localhost:5000/health
```

You should receive:

```json
{ "status": "success", "message": "API is running" }
```

---

## Frontend Setup

### 1. Install dependencies

```bash
cd chingiring-app
npm install
```

### 2. Configure the API URL (optional)

By default the app connects to `http://localhost:8000` on web and `http://192.168.1.90:8000` on native devices. To override this, set the `EXPO_PUBLIC_API_URL` environment variable before starting Expo:

```bash
export EXPO_PUBLIC_API_URL=http://localhost:5000
```

Make sure the URL matches the port your backend is running on.

### 3. Start the Expo development server

```bash
npm start
```

This launches the Expo CLI. From the terminal you can press:

- `w` to open the app in a web browser
- `i` to open in the iOS Simulator
- `a` to open on an Android emulator or device

You can also run platform-specific commands directly:

```bash
npm run web       # Web only
npm run ios       # iOS only
npm run android   # Android only
```

### 4. Build for web (production)

```bash
npm run build
```

This exports a static web build to the `dist/` directory.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| MongoDB connection error | Verify `MONGO_URI` is correct and the database is reachable. |
| CORS errors in the browser | Ensure the frontend origin is listed in the backend CORS config (`backend/src/app.js`). |
| 401 errors on every request | Check that `withCredentials` is enabled on the client and `credentials: true` is set in the backend CORS config. Cookies require matching origins. |
| Expo build fails | Run `npx expo doctor` to check for dependency mismatches. |
