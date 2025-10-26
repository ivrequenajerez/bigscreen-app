```
# 🎬 Orka TV – Big Screen App (React Native TV / Expo 2025)

**Author:** Ian Requena – Full Stack Developer  
**Objective:** Build a React Native TVOS application capable of playing different channel streams, with a UX and UI optimized for a Big Screen environment.

---

## 🧠 Overview

- **Framework:** React Native 0.81 + TV fork (`react-native-tvos`)
- **Runtime:** Expo SDK 51 with `@react-native-tvos/config-tv`
- **Language:** TypeScript (strict mode)
- **Platforms:** Android TV / Google TV / Apple TV (tvOS)
- **Design source:** [Figma – Interview UI](https://www.figma.com/design/xJRiZB1CNWNWgGWX1Wu6CC/Interview?node-id=0-1)
- **API data:** [iptv-org](https://github.com/iptv-org/api) (`streams.json`, `logos.json`)
- **Focus:** Front-end first → DDD + DevOps mindset → TV UX consistency

---

## 🧩 Folder Structure (Feature-Based / DDD)


src/
├─ features/
│  └─ live-tv/        # Main feature (video player, overlay, channel rail)
│      ├─ ui/         # View components (focusable, D-Pad aware)
│      ├─ model/      # Hooks and local state
│      └─ services/   # IPTV-org data adapter
├─ entities/
│  └─ channel/        # Domain model + mappers for Channel
├─ shared/
│  ├─ tv/             # TV-specific utilities (focus map, remote keys)
│  ├─ ui/             # Generic UI components
│  ├─ theme/          # Typography, spacing, colors for 10-ft UX
│  └─ lib/            # Fetch, cache, logger, telemetry helpers
└─ app-state/         # (Optional) global player store
```

> 🧭 Each feature is self-contained — UI + logic + services — ensuring isolation, easier testing, and scalability.

---

## ⚙️ Setup & Run (Windows 11 / Expo + Android TV)

### 🧩 Requirements
- **Windows 11**
- **Yarn** (recommended for React Native TVOS)
- **Java 17 (Adoptium)**
- **Android Studio** with:
  - API Level 34
  - Image: *Google TV Intel x86 Atom System Image*
  - Resolution: *TV 1080p*
- **Important:** No folder in the Android SDK path should contain spaces  
  (e.g. rename user folder from `Ian Valentino` → `ian`).

---

### 🚀 Create and Run the Project

**1. Initialize project**

```
npx create-expo-app . -e with-tv
```

**2. Install TV config plugin**

```
npx expo install @react-native-tvos/config-tv -- --dev
```

**3. Set environment variable**

```
$env:EXPO_TV = "1"
```

**4. Prebuild and run on Android TV**

```
npx expo prebuild --clean
npx expo run:android -d
```

> 💡 The `-d` flag ensures Expo runs the build on your connected or selected Google TV emulator.

---

### 🧱 Architecture Used

**Feature-based / Domain-Driven Design (DDD-light)**
Each feature encapsulates UI, state, services, and tests.

#### Why not MVC?

* **Feature-first** groups all logic for a single use case (e.g., Live TV) → lower coupling.
* **Entities** centralize reusable domain models (like `Channel`).
* **Shared/tv** holds TV-specific utilities (D-Pad, focus, keymap).
* Facilitates **testing, metrics, and CI/CD** pipelines.
* Each feature can evolve independently and be tested in isolation.

---

## 🎮 Focus Navigation and Remote Keys

* **D-Pad** → move focus between Play/Pause ⇄ Channel Rail
* **SELECT / ENTER** → select / tune channel
* **PLAY / PAUSE** → toggle playback
* **BACK** → hide overlays or return to previous focus
* Managed via custom hooks in `src/shared/tv/` (`useTVEventHandler`, `FocusManager`, `RemoteMap`).

---

## 🧱 Data Adapter Specification

The app fetches a subset (≈ 20 – 30 channels) from iptv-org API and maps them to the domain model:

```
type Channel = {
  id: string
  name: string
  logo: string
  streamUrl: string
  country?: string
  languages?: string[]
  category?: string
  isLive: boolean
}
```

Join rule: `streams.channel` ⇄ `logos.channel` → pick the first valid HTTPS HLS stream.

---

## 🧰 DevOps / Tooling

* **TypeScript strict mode** (`tsconfig.json`)
* **ESLint + Prettier + Husky** (pre-commit lint + typecheck)
* **EditorConfig** for consistent formatting
* **Docs** under `/docs/` (`business-logic.md`, `data-adapter.md`, etc.)
* **CI-ready**: static linting and build validation on every PR
* **Testing stack:**

  * **Jest** for unit tests
  * **Maestro** for E2E automation
  * **RNTester** for component-level checks

---

## 📺 TV-Specific Configuration

* **File extensions:** `.android.tv.tsx`, `.ios.tv.tsx`, `.tv.tsx` supported by `metro.config.js`
  (disabled by default for speed; enable if needed for platform-specific code).
* **App icons and banners:** placeholders located in `assets/` — replace with your own TV assets.
  The `config-tv` plugin auto-generates required native image files.

---

## 🧾 License / Credits

This project is based on:

* [React Native TV fork](https://github.com/react-native-tvos/react-native-tvos)
* [Config-TV plugin](https://github.com/react-native-tvos/config-tv)
* [Amazon Multi-TV Sample](https://github.com/AmazonAppDev/react-native-multi-tv-app-sample)

All channel data provided by [iptv-org API](https://github.com/iptv-org/api).