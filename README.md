# NEXA — Peer-to-Peer Offline Messenger for Android

> **Privacy-first, serverless messaging** powered by Nearby Connections and Delay-Tolerant Networking (DTN).

NEXA lets nearby Android devices discover each other and exchange encrypted messages without any internet connection or central server. When a recipient isn't directly reachable, messages are routed through intermediate peers using a store-and-forward DTN layer, making it ideal for disaster-response scenarios, remote areas, and privacy-conscious communications.

---

## Table of Contents

- [Key Features](#key-features)
- [Architecture at a Glance](#architecture-at-a-glance)
- [Project Structure](#project-structure)
- [Documentation Guide](#-documentation-guide)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Running Tests](#running-tests)
- [Key Dependencies](#key-dependencies)
- [Roadmap](#roadmap)
- [Known Gaps / TODOs](#known-gaps--todos)
- [Contributing](#contributing)
- [License](#license)

---

## Key Features

| Area | What It Does |
|---|---|
| **Identity & Sessions** | Local account registration/login, remember-me auto-login, active user tracking |
| **Nearby Discovery** | Advertising + discovery via Google Nearby Connections; connection acceptance and handshake (device ID, display name, public key) |
| **DTN Messaging** | Direct P2P send, store-and-forward routing for unreachable peers, retry + pending bundle persistence |
| **Chat UI** | Chats list with filters, in-chat conversation view, basic send/receive flow |
| **QR Onboarding** | QR code generation of device ID, scan + connect flow |
| **Security** | Device keypair generation (libsodium), optional trusted messaging mode, encrypted payloads (secretbox) |

---

## Architecture at a Glance

```
┌──────────────────────────────────────────────────────┐
│                   Nexa.kt (Application)              │
│         bootstrap · device ID · crypto init          │
├───────────────┬──────────────────┬────────────────────┤
│  CommsLayer/  │    DataLayer/    │      logic/        │
│  ├ NearbyService   ├ AppDatabase      ├ MessageCenter  │
│  └ DTN/            ├ DatabaseRepo     └ EnhancedMsg    │
│    ├ DTNMessageMgr │ └ models/           Handler       │
│    ├ DTNMessage     └──────────────────────────────────│
│    └ DTNConfig                                        │
├───────────────────────────────────────────────────────┤
│  ui/                           │   utils/             │
│  ├ HomeScreen, ChatsScreen     │   ├ KeyManager       │
│  ├ InChatsActivity             │   ├ EncryptionService │
│  ├ LoginScreen, SignUpScreen   │   ├ SessionManager   │
│  ├ DiscoveryScreen, GroupScreen│   ├ QRCodeGenerator  │
│  ├ ProfileScreen, Settings     │   ├ PermissionUtils  │
│  ├ QRCodeActivity              │   └ ProfileUtils     │
│  └ components/ (adapters,      │                      │
│    models)                     │                      │
├───────────────────────────────────────────────────────┤
│  viewModels/                   │   service/           │
│  └ NearbyViewModel             │   └ NexaMessaging    │
│                                │     Service          │
└──────────────────────────────────────────────────────┘
```

**Design philosophy:** Offline-first, no cloud dependency, modular layers with clear separation of concerns. See [`docs/DESIGN.md`](docs/DESIGN.md) for in-depth architecture details and design decisions.

---

## Project Structure

```
Nexa-Final-main/
├── app/
│   ├── build.gradle.kts          # Module-level build config (dependencies, SDK targets)
│   ├── proguard-rules.pro        # ProGuard / R8 rules
│   └── src/
│       ├── main/
│       │   ├── AndroidManifest.xml
│       │   ├── java/
│       │   │   ├── Nexa.kt                      # Application entry point
│       │   │   ├── CommsLayer/                   # Nearby Connections + DTN networking
│       │   │   │   ├── NearbyService.kt
│       │   │   │   └── DTN/
│       │   │   │       ├── DTNMessageManager.kt
│       │   │   │       ├── DTNMessage.kt
│       │   │   │       └── DTNConfig.kt
│       │   │   ├── DataLayer/                    # Database + repositories
│       │   │   │   ├── AppDatabase.kt
│       │   │   │   ├── DatabaseRepository.kt
│       │   │   │   ├── StorageManager.kt
│       │   │   │   └── models/                   # Entity data classes
│       │   │   ├── logic/                        # Business logic & message coordination
│       │   │   │   ├── MessageCenter.kt
│       │   │   │   └── EnhancedMsgHandler.kt
│       │   │   ├── ui/                           # Activities & UI components
│       │   │   │   ├── HomeScreen.kt
│       │   │   │   ├── ChatsScreen.kt
│       │   │   │   ├── InChatsActivity.kt
│       │   │   │   ├── LoginScreen.kt / SignUpScreen.kt
│       │   │   │   ├── DiscoveryScreen.kt / GroupScreen.kt
│       │   │   │   ├── ProfileScreen.kt / SettingsScreen.kt
│       │   │   │   ├── QRCodeActivity.kt
│       │   │   │   └── components/
│       │   │   │       ├── adapters/             # RecyclerView & list adapters
│       │   │   │       └── models/               # UI-specific data models
│       │   │   ├── viewModels/
│       │   │   │   └── NearbyViewModel.kt
│       │   │   ├── utils/                        # Crypto, sessions, permissions, QR
│       │   │   └── com/.../service/
│       │   │       └── NexaMessagingService.kt   # Foreground messaging service
│       │   └── res/                              # Android resources (layouts, drawables, etc.)
│       ├── test/                                 # Unit tests
│       └── androidTest/                          # Instrumented tests
├── docs/                                         # 📖 Project documentation (see below)
├── build.gradle.kts                              # Root build file
├── settings.gradle.kts                           # Gradle settings
├── gradle.properties                             # Gradle & Android properties
└── gradle/                                       # Gradle wrapper
```

---

## 📖 Documentation Guide

All detailed documentation lives in the [`docs/`](docs/) directory. Start here if you're new to the project:

| Document | What You'll Find | Start Here If… |
|---|---|---|
| [`DESIGN.md`](docs/DESIGN.md) | High-level architecture overview, project goals & non-goals, application lifecycle, and comms-layer design decisions | You want to understand **why** the code is structured the way it is and what trade-offs were made |
| [`PROJECT_BREAKDOWN.md`](docs/PROJECT_BREAKDOWN.md) | Current scope of all implemented features, a high-level file map, suggested roadmap for future work, testing strategies, and known gaps | You want a **quick map** of what's built, what's missing, and what to work on next |
| [`design-patterns-used.md`](docs/design-patterns-used.md) | Comprehensive GoF (Gang of Four) design pattern catalog — every pattern mapped to specific files with explanations, plus multi-pattern and file-by-file classifications | You want to understand the **design patterns** at play before reading or modifying any source file |

### Recommended Reading Order for New Developers

1. **This README** — get the big picture and set up your environment
2. **[`docs/DESIGN.md`](docs/DESIGN.md)** — understand the architecture and design rationale
3. **[`docs/PROJECT_BREAKDOWN.md`](docs/PROJECT_BREAKDOWN.md)** — see the feature scope, file map, and roadmap
4. **[`docs/design-patterns-used.md`](docs/design-patterns-used.md)** — reference this when diving into specific source files

---

## Prerequisites

| Requirement | Version |
|---|---|
| **Android Studio** | Ladybug (2025.x) or later recommended |
| **JDK** | 17 |
| **Android SDK** | compileSdk 35, minSdk 24 |
| **Gradle** | 8.13+ (wrapper included) |
| **Physical devices** | Two Android devices required for Nearby Connections testing (emulators don't support Nearby) |

---

## Getting Started

```bash
# 1. Clone the repository
git clone <repo-url>
cd Nexa-Final-main

# 2. Open in Android Studio
#    File → Open → select the Nexa-Final-main directory

# 3. Sync Gradle
#    Android Studio will prompt you — click "Sync Now"

# 4. Build the project
./gradlew assembleDebug        # Linux/macOS
gradlew.bat assembleDebug      # Windows

# 5. Run on a physical device
#    Select your device in the toolbar and click ▶ Run
```

### Permissions

NEXA requires the following runtime permissions on the device — the app will prompt for them on first launch:

- **Nearby Connections:** `ACCESS_FINE_LOCATION`, `BLUETOOTH_SCAN`, `BLUETOOTH_ADVERTISE`, `BLUETOOTH_CONNECT`, `NEARBY_WIFI_DEVICES`
- **Camera:** Required for QR code scanning
- **Notifications:** `POST_NOTIFICATIONS` (Android 13+)

---

## Running Tests

```bash
# Unit tests
./gradlew test                            # Linux/macOS
gradlew.bat test                          # Windows

# Instrumented tests (requires a connected device)
./gradlew connectedAndroidTest
```

### Current Test Coverage

| Test File | What It Covers |
|---|---|
| `DTNBundleTest.kt` | DTN bundle creation and serialization |
| `DTNConfigTest.kt` | DTN configuration constants |
| `PeerProfileTest.kt` | Peer profile data handling |
| `PeerVisibilityTest.kt` | Peer visibility enum logic |
| `GroupEntityTest.kt` | Group entity data class |
| `QRCodeGeneratorTest.kt` | QR code generation & verification |
| `MessagingSecurityModeTest.kt` | Security mode enum logic |
| `ProfileUtilsTest.kt` | Profile formatting utilities |

Test results are also logged to `app/build/reports/tests/unit-test-details.txt` after each run.

---

## Key Dependencies

| Library | Purpose |
|---|---|
| [Google Play Services Nearby](https://developers.google.com/nearby/connections/overview) | Short-range device discovery and peer-to-peer data transfer |
| [Lazysodium (libsodium)](https://github.com/nickelaway/lazysodium-android) | Cryptographic operations — keypair generation, secretbox encryption |
| [ZXing](https://github.com/journeyapps/zxing-android-embedded) | QR code generation and scanning |
| [AndroidX Lifecycle](https://developer.android.com/jetpack/androidx/releases/lifecycle) | ViewModel, LifecycleService, coroutine integration |
| [Kotlin Coroutines](https://kotlinlang.org/docs/coroutines-overview.html) | Asynchronous programming (StateFlow, SharedFlow) |
| [CircleImageView](https://github.com/hdodenhof/CircleImageView) | Circular profile images in the UI |

---

## Roadmap

> See [`docs/PROJECT_BREAKDOWN.md`](docs/PROJECT_BREAKDOWN.md) for the full roadmap and testing ideas.

1. **Group chat UI + flows** — the group screen is currently a stub
2. **Attachments** — images and audio support
3. **Delivery receipts** — read/delivered indicators
4. **Peer trust management** — better key verification UX
5. **Backup / export** — local database backup and restore

---

## Known Gaps / TODOs

- Group chat screen is a placeholder stub
- Some Room-style comments remain but the database uses `SQLiteOpenHelper`
- Full attachment handling is not yet implemented
- `StorageManager.kt` is entirely commented out (out of scope for now)

---

## Contributing

1. Read through the [Documentation Guide](#-documentation-guide) above
2. Check the [Roadmap](#roadmap) and [Known Gaps](#known-gaps--todos) for areas that need work
3. Reference [`docs/design-patterns-used.md`](docs/design-patterns-used.md) before modifying existing files to understand the patterns in use
4. Write unit tests for any new logic (see `app/src/test/` for examples)
5. Keep the offline-first, serverless design philosophy in mind — avoid adding cloud dependencies

---

## License

Refer to [LICENSE]
