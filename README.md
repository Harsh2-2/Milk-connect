# MilkConnect — Dairy Delivery Platform

A full-stack mobile app that connects customers with local dairy farms for fresh milk and dairy product delivery, featuring Swiggy-style real-time live tracking, subscriptions, inventory, and finance management. Built with React Native + Expo and Firebase.



---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Authentication Flow](#authentication-flow)
- [Customer App](#customer-app)
- [Milkman App](#milkman-app)
- [Real-Time Delivery Tracking (Swiggy-style)](#real-time-delivery-tracking-swiggy-style)
- [Maps & Location Services](#maps--location-services)
- [Backend Integration (Firebase)](#backend-integration-firebase)
- [Data Models](#data-models)
- [State Management](#state-management)
- [Scripts](#scripts)
- [Building & Deploying](#building--deploying)
- [Configuration Files](#configuration-files)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [License](#license)

---

## Overview

MilkConnect is a two-sided marketplace mobile app for daily dairy deliveries. It serves two user types:

1. **Customers** — browse nearby Chandigarh dairy farms, subscribe to milk/dairy products, place one-time orders, and track their milkman's live location on a map in real time (just like Swiggy/Zomato food delivery tracking).
2. **Milkmen (Dairy owners)** — manage their dairy business: dashboard with stats, customer list, delivery schedule, inventory management, and finance/revenue reports.

The app currently ships with seeded **Chandigarh dairy data** (Verka, Sharma Dairy, etc.) and a service layer that can run against mock data or a live Firebase Firestore backend. Authentication is OTP-based (demo OTP flow) with role selection (Customer / Milkman).

---

## Key Features

### Customer
- OTP login with role selection
- Home dashboard with quick actions and active deliveries
- **Find Milkmen** — browse Chandigarh dairies sorted by distance, ratings, products, working hours
- **Subscriptions** — daily/alternate-day/weekly milk subscriptions, pause/resume/cancel
- **One-time orders** — order milk, curd, paneer, ghee, lassi, etc.
- **Real-time delivery tracking** — live map showing the milkman's current location, route to your address, remaining distance, and ETA (Swiggy-style)
- Profile management with multiple saved addresses
- Notifications for delivery reminders, payment dues, and order status

### Milkman (Dairy Owner)
- OTP login with role selection
- **Dashboard** — today's stats (deliveries, revenue, customers, pending), quick navigation
- **Deliveries** — list of today's deliveries with customer details, map view, mark delivered / not home / cancelled, proof photos
- **Customers** — full customer list with subscription details and order history
- **Inventory** — products with current stock, low-stock alerts, add/edit products, cost vs selling price
- **Reports / Finance** — revenue charts, expense tracking (procurement, fuel, salary, etc.), profit/loss, daily/weekly/monthly breakdowns
- Profile & business settings (service area, working hours, bank details)

### Platform
- Cross-platform: iOS, Android, and Web
- Real-time GPS location via `expo-location`
- Route calculation with distance & duration (OSRM-compatible)
- Geocoding (address → coordinates) via Nominatim/OpenStreetMap
- Firebase Firestore backend (with mock-data fallback)
- File-based routing with Expo Router
- Type-safe with TypeScript strict mode
- Haptics, blur effects, and native UI components

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | React Native 0.81 + Expo SDK 54 |
| Language | TypeScript 5.9 (strict) |
| Routing | Expo Router 6 (file-based) |
| Backend | Firebase (Firestore, Auth, Storage) |
| Maps | `react-native-maps` + `expo-location` |
| State | React Query + `@nkzw/create-context-hook` + AsyncStorage |
| Icons | `lucide-react-native` + `@expo/vector-icons` |
| Validation | Zod |
| Animations | React Native Animated API |
| Package Manager | Bun |
| Build | Rork CLI / EAS |

---

## Project Structure

```
expo/
├── app/                          # Expo Router screens
│   ├── _layout.tsx               # Root layout — QueryClient, StorageProvider, AuthProvider
│   ├── index.tsx                 # Entry — routes to auth or role-based home
│   ├── auth.tsx                  # OTP login + role selection (Customer/Milkman)
│   ├── +not-found.tsx            # 404 screen
│   ├── +native-intent.tsx        # Native intent handler
│   ├── (customer)/               # Customer tab group
│   │   ├── _layout.tsx           # Customer tab navigator
│   │   ├── home.tsx              # Customer home dashboard
│   │   ├── find-milkmen.tsx      # Browse Chandigarh dairies + order
│   │   ├── subscriptions.tsx     # Manage subscriptions
│   │   ├── track-delivery.tsx    # Live Swiggy-style delivery tracking
│   │   └── profile.tsx           # Customer profile & addresses
│   └── (milkman)/                # Milkman tab group
│       ├── _layout.tsx           # Milkman tab navigator
│       ├── dashboard.tsx         # Business overview & stats
│       ├── deliveries.tsx        # Today's deliveries + map
│       ├── customers.tsx         # Customer list & subscriptions
│       ├── inventory.tsx         # Product & stock management
│       └── reports.tsx           # Finance & revenue reports
├── components/
│   ├── MapView.tsx               # Web/native map wrapper (Leaflet on web, react-native-maps on native)
│   ├── Marker.tsx                # Custom map marker
│   └── RouteMapView.tsx          # Map with route polyline + vehicle animation
├── constants/
│   ├── chandigarh-dairies.ts     # Seeded Chandigarh dairy farms data
│   └── colors.ts                 # App color palette
├── contexts/
│   ├── AuthContext.tsx           # OTP auth, session, profile (createContextHook)
│   └── StorageContext.tsx        # AsyncStorage wrapper
├── lib/
│   ├── firebase.ts               # Firebase init (Auth, Firestore, Storage)
│   ├── dairy-service.ts          # Dairy CRUD + delivery tracking service (mock/Firebase)
│   └── location-service.ts       # GPS, geocoding, routing, distance calc
├── types/
│   └── index.ts                  # All TypeScript domain models
├── docs/
│   └── LOCATION_SERVICES_GUIDE.md # How to use location/routing APIs
├── assets/images/                # App icon, splash, favicon
├── app.json                      # Expo config (permissions, plugins, scheme)
├── package.json
├── tsconfig.json
├── metro.config.js
├── babel.config.js
└── eslint.config.js
```

---

## Getting Started

### Prerequisites

- Node.js (via [nvm](https://github.com/nvm-sh/nvm))
- [Bun](https://bun.sh/docs/installation)
- Expo Go (for device testing) or the Rork app

### Install & Run

```bash
# Clone
git clone <YOUR_GIT_URL>
cd <YOUR_PROJECT_NAME>/expo

# Install dependencies
bun install

# Start web preview (auto-reload)
bun run start-web

# Start native dev server (then press 'i' for iOS or 'a' for Android)
bun run start
```

### Test on a device

1. Install **Expo Go** from the App Store / Google Play.
2. Run `bun run start`.
3. Scan the QR code with your phone camera.

---

## Environment Variables

These are injected by the Rork platform and are already present in the build. They are public (client-safe) and prefixed with `EXPO_PUBLIC_`:

| Variable | Purpose |
|----------|---------|
| `EXPO_PUBLIC_PROJECT_ID` | Rork project identifier |
| `EXPO_PUBLIC_RORK_API_BASE_URL` | Rork API base URL |
| `EXPO_PUBLIC_RORK_APP_KEY` | Rork app key |
| `EXPO_PUBLIC_RORK_AUTH_URL` | Rork auth endpoint |
| `EXPO_PUBLIC_RORK_FUNCTIONS_URL` | Cloud functions URL |
| `EXPO_PUBLIC_RORK_TOOLKIT_SECRET_KEY` | Rork toolkit secret |
| `EXPO_PUBLIC_TEAM_ID` | Rork team ID |
| `EXPO_PUBLIC_TOOLKIT_URL` | Rork toolkit URL |

Read them in-app with `process.env.EXPO_PUBLIC_VARIABLE_NAME`.

> Firebase config is currently set to demo values in `lib/firebase.ts`. Replace with your real Firebase project credentials before going to production.

---

## Authentication Flow

Implemented in `contexts/AuthContext.tsx` using `@nkzw/create-context-hook`.

1. **Role selection** — User picks Customer or Milkman on the auth screen.
2. **Phone number** — User enters a 10–15 digit phone number.
3. **OTP** — `sendOTP()` generates a 6-digit code (demo mode; wire to Firebase Auth / SMS provider for production).
4. **Verify** — `verifyOTP()` validates the OTP, creates a `User` + profile, and persists the session in AsyncStorage under `demo_user` / `demo_profile`.
5. **Routing** — On app launch, `index.tsx` checks the stored session and routes to `(customer)` or `(milkman)`, otherwise to `auth`.
6. **Sign out** — `signOut()` clears storage and returns to the auth screen.

Input validation: phone length 10–15, OTP must be 6 digits.

---

## Customer App

### Home (`home.tsx`)
- Greeting + active delivery card (tap to track)
- Quick actions: Find Milkmen, My Subscriptions, Track Delivery
- Recent orders

### Find Milkmen (`find-milkmen.tsx`)
- Loads Chandigarh dairies from `DairyService` (mock or Firebase)
- Sorted by distance from the customer's live GPS location
- Each card: business name, owner, rating, distance, products, price range, working hours
- Tap a dairy → view products → place a subscription or one-time order
- Uses `LocationService.getCurrentLocation()` to anchor the list

### Subscriptions (`subscriptions.tsx`)
- Active / paused / cancelled tabs
- Daily, alternate-day, weekly, or custom frequency
- Pause, resume, cancel actions
- Price per delivery and next delivery date

### Track Delivery (`track-delivery.tsx`) — Swiggy-style
- Full-screen map with the milkman's live location marker
- Polyline route from milkman → customer address
- Live distance remaining and ETA
- Status progression: `preparing` → `out_for_delivery` → `nearby` → `arrived` → `delivered`
- Call / message the milkman buttons
- Animated progress and status banners

### Profile (`profile.tsx`)
- Name, email, profile photo
- Multiple saved addresses (Home, Office, etc.) with geo-coordinates
- Sign out

---

## Milkman App

### Dashboard (`dashboard.tsx`)
- Today's stats: deliveries (pending/total), revenue, active customers, low-stock alerts
- Quick navigation to Deliveries, Customers, Inventory, Reports
- Business profile summary

### Deliveries (`deliveries.tsx`)
- Today's delivery list with customer name, phone, address, products, time slot
- Map view with all delivery pins
- Status updates: pending → out for delivery → delivered / not home / cancelled
- Proof-of-delivery photo capture
- Navigate-to-customer (opens maps app)

### Customers (`customers.tsx`)
- Full customer list with active subscriptions and order history
- Contact (call/SMS) shortcuts
- Subscription details per customer

### Inventory (`inventory.tsx`)
- Product list: name, category (Milk, Curd, Paneer, Ghee, Lassi, etc.), unit, stock, prices
- Low-stock threshold alerts
- Add / edit / deactivate products
- Cost price vs selling price for margin tracking

### Reports (`reports.tsx`)
- Revenue bar chart (daily/weekly/monthly)
- Expense tracking by category (procurement, fuel, salary, maintenance, packaging, equipment)
- Profit/Loss calculation
- Top products and top customers
- Export / download summary

---

## Real-Time Delivery Tracking (Swiggy-style)

The flagship feature, in `app/(customer)/track-delivery.tsx` + `lib/dairy-service.ts` + `lib/location-service.ts`.

**How it works:**
1. When a delivery is `out_for_delivery`, `DairyService` exposes an `ActiveDelivery` record with `pickupLocation`, `deliveryLocation`, and a live `currentLocation`.
2. The track-delivery screen polls the milkman's `currentLocation` and re-centers the map.
3. `LocationService.calculateDistance()` computes the remaining straight-line (Haversine) distance between the milkman and the customer.
4. ETA is estimated from distance + average speed; status auto-advances as the milkman gets closer (`nearby` < 1 km, `arrived` < 100 m).
5. The route polyline is drawn via the OSRM routing API (`router.project-osrm.org`) — same approach as the Python `DeliveryRouterUI` reference design.
6. The customer can call or message the milkman directly from the tracking screen.

**Demo mode:** When no live milkman GPS is available, the screen simulates movement along the route so the tracking experience is fully visible for presentations.

---

## Maps & Location Services

Implemented in `lib/location-service.ts` (singleton `LocationService`).

### Capabilities
- **Permissions** — `requestForegroundPermissionsAsync()` (and background on Android/iOS where enabled).
- **Current location** — `getCurrentLocation()` returns `{ latitude, longitude, address }`.
- **Geocoding** — `geocodeAddress(address)` → coordinates via Nominatim/OpenStreetMap.
- **Reverse geocoding** — `reverseGeocode(coords)` → human-readable address.
- **Routing** — `calculateRoute(origin, destination)` → `{ distance, duration, coordinates[] }` via OSRM.
- **Distance** — `calculateDistance(a, b)` — Haversine formula in km.

### Map components
- `components/MapView.tsx` — Cross-platform map. Uses `react-native-maps` on native and a Leaflet-based web fallback (so the web preview works without the native-only `react-native-maps` modules that break Metro on web).
- `components/Marker.tsx` — Custom-styled markers (pickup, delivery, user, vehicle).
- `components/RouteMapView.tsx` — Map + route polyline + animated vehicle marker moving along the route (mirrors the Python `startAnimation()` logic).

### Default location
The app defaults to **Chandigarh** (`30.7333, 76.7794`) per `constants/chandigarh-dairies.ts` → `CHANDIGARH_CENTER`. If the user grants location permission, the map recenters to their live GPS.

> A full guide lives in `docs/LOCATION_SERVICES_GUIDE.md`.

---

## Backend Integration (Firebase)

`lib/firebase.ts` initializes Firebase App, Auth, Firestore, and Storage. `lib/dairy-service.ts` is the service layer.

### DairyService (singleton)
- `setUseMockData(boolean)` — toggle between mock Chandigarh data and live Firestore.
- `getAllDairies(userLocation?)` — list dairies, optionally sorted by distance.
- `getDairy(id)` — single dairy detail.
- `getActiveDeliveries(customerId)` — live deliveries for tracking.
- `getDelivery(id)` / `updateDeliveryStatus()` — delivery lifecycle.
- `subscribeToDairy(dairyId)` / `getSubscriptions()` — subscription CRUD.
- `createOrder()` — one-time order.

**Currently running in mock mode** with seeded Chandigarh dairies. To switch to Firebase:
1. Replace the demo config in `lib/firebase.ts` with your real Firebase project credentials.
2. Call `DairyService.getInstance().setUseMockData(false)` at app startup.
3. Create Firestore collections matching the `types/index.ts` models: `users`, `customers`, `milkmen`, `products`, `subscriptions`, `deliveries`, `orders`, `payments`, `expenses`, `reviews`, `chats`, `messages`, `notifications`.

---

## Data Models

All domain models are in `types/index.ts`. Key types:

| Type | Description |
|------|-------------|
| `User` | Base user: id, phone, userType |
| `CustomerProfile` | Customer: name, email, addresses[] |
| `MilkmanProfile` | Dairy: businessName, serviceArea, workingHours, bankDetails, rating |
| `Address` | Label, fullAddress, landmark, GeoPoint |
| `GeoPoint` | { latitude, longitude } |
| `Product` | Dairy product with stock, prices, category |
| `Subscription` | Recurring delivery (daily/alternate/weekly/custom) |
| `Delivery` | Single delivery instance with status & payment |
| `Order` | One-time order with items[] |
| `Payment` | Payment record (cash/upi/card/wallet) |
| `Expense` | Business expense by category |
| `Review` | Customer rating + comment |
| `Chat` / `Message` | In-app messaging |
| `Notification` | In-app notifications |

Enums: `UserType`, `ProductCategory`, `SubscriptionFrequency`, `SubscriptionStatus`, `DeliveryStatus`, `PaymentStatus`, `OrderStatus`, `PaymentMethod`, `ExpenseCategory`, `NotificationType`.

---

## State Management

- **Server state** — `@tanstack/react-query` (`QueryClientProvider` is the top-level provider in `app/_layout.tsx`). Use `useQuery({ queryKey, queryFn })` for fetching and `useMutation` for writes.
- **Auth + session** — `contexts/AuthContext.tsx` via `@nkzw/create-context-hook` (typed `useAuth()` hook). Persisted to AsyncStorage.
- **Storage** — `contexts/StorageContext.tsx` wraps AsyncStorage; access only via the context hook, never directly.
- **Local UI state** — `useState` within screens.

Provider nesting (outermost → innermost): `QueryClientProvider` → `StorageProvider` → `AuthProvider` → `GestureHandlerRootView` → `RootLayoutNav`.

---

## Scripts

```bash
bun run start          # Start native dev server (Rork CLI)
bun run start-web      # Start web preview
bun run start-web-dev  # Web preview with debug logging
bun run lint           # ESLint (expo lint)
```

---

## Building & Deploying

### App Store (iOS)
```bash
bun i -g @expo/eas-cli
eas build:configure
eas build --platform ios
eas submit --platform ios
```

### Google Play (Android)
```bash
eas build --platform android
eas submit --platform android
```

### Web
```bash
eas build --platform web
# or deploy dist/ to Vercel / Netlify
```

See the [Expo deployment docs](https://docs.expo.dev/submit/) for details.

---

## Configuration Files

- **`app.json`** — Expo config. App name `MilkConnect App`, slug `milkconnect-app`, portrait orientation, new architecture enabled. Location permissions configured for iOS (`NSLocationWhenInUseUsageDescription`, background location mode) and Android (`ACCESS_FINE_LOCATION`, `ACCESS_BACKGROUND_LOCATION`, foreground service). `expo-location` plugin enabled with background location.
- **`tsconfig.json`** — Strict TypeScript, path alias `@/*` → root.
- **`metro.config.js`** — Resolves web/native platform splits for `react-native-maps`.
- **`babel.config.js`** — Expo babel preset.

---

## Troubleshooting

### `react-native-maps` import error on web
This is expected — `react-native-maps` is native-only. The `MapView.tsx` component platform-splits to a Leaflet-based web map so the web preview keeps working. If you see `Importing native-only module ... on web`, make sure you're importing from `@/components/MapView`, not directly from `react-native-maps`.

### App not loading on device
1. Phone and computer on the same WiFi.
2. Try tunnel mode: `bun start -- --tunnel`.
3. Check firewall settings.

### Build failing
1. Clear cache: `bunx expo start --clear`.
2. Reinstall: `rm -rf node_modules && bun install`.
3. See [Expo's troubleshooting guide](https://docs.expo.dev/troubleshoot/).

### Sign out not working
The sign-out flow lives in `AuthContext.signOut()`. It clears `demo_user` and `demo_profile` from AsyncStorage and redirects to `/auth`. If it fails, ensure `StorageProvider` is mounted above the screen calling `useAuth()`.

### Maps not showing
- Confirm location permission is granted (the app requests it on first use).
- On web, ensure you're using the `MapView` wrapper, not `react-native-maps` directly.
- Default center is Chandigarh; the map recenter to your GPS only after permission is granted.

---

## Roadmap

- [ ] Wire OTP to a real SMS provider (Firebase Auth / Twilio / MSG91)
- [ ] Move fully from mock data to Firebase Firestore
- [ ] Real-time milkman GPS streaming via Firestore `onSnapshot`
- [ ] In-app chat between customer and milkman
- [ ] Online payments (Razorpay / Stripe)
- [ ] Push notifications via Expo Notifications
- [ ] Admin/web dashboard for dairy analytics
- [ ] Multi-city expansion beyond Chandigarh

---
