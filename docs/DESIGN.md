# NEXA Design Doc

## 1) Overview
NEXA is an Android peer‑to‑peer messaging app designed to operate without central servers. It relies on **Nearby Connections** for short‑range discovery and transport, augmented by a **DTN (delay‑tolerant networking) layer** to route messages when direct connectivity is unavailable. Messages and peer state are stored locally for resilience, and an optional **trusted messaging mode** enables encryption for sensitive exchanges.

## 2) Goals
- **Offline‑first** local messaging between nearby devices.
- **Seamless mesh routing** via DTN when peers are not directly reachable.
- **Optional end‑to‑end protection** using libsodium.
- **Simple UX** for discovery, chat, and QR onboarding.

## 3) Non‑Goals
- Cloud synchronization or server‑side delivery guarantees.
- Multi‑device account roaming.
- Full contact discovery over the internet.

## 4) Architecture

### 4.1 Application Lifecycle
**`Nexa` (Application)** is the entry point. It:
- Generates/loads device ID.
- Initializes crypto keys.
- Initializes Nearby services **after** permissions are granted.
- Provides access to the DTN manager and Nearby service.

### 4.2 Comms Layer
