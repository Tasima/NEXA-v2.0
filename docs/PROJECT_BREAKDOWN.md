# NEXA Project Breakdown

## Deliverables
- `README.md` — project overview, setup, and usage guidance
- `DESIGN.md` — architecture, flows, and design decisions
- `PROJECT_BREAKDOWN.md` — scope and implementation breakdown

## Current Scope (As Implemented)
### 1) Identity & Sessions
- Local account registration/login
- Remember‑me auto‑login
- Active user tracking

### 2) Nearby Connectivity
- Advertising + discovery
- Connection acceptance
- Handshake exchange (device ID, display name, public key)

### 3) DTN Messaging
- P2P direct send
- Store‑and‑forward routing
- Retry + pending bundle persistence

### 4) Messaging UI
- Chats list with filters
- In‑chat conversation view
- Basic send/receive flow

### 5) QR Onboarding
- QR generation of device ID
- QR scan + connect flow

### 6) Security
- Device keypair generation
- Optional trusted messaging mode
- Encrypted payloads (secretbox)

## High‑Level File Map
- `app/src/main/java/Nexa.kt` — application bootstrap
- `app/src/main/java/CommsLayer/` — Nearby + DTN logic
- `app/src/main/java/DataLayer/` — storage & repository
- `app/src/main/java/logic/` — message processing
- `app/src/main/java/ui/` — activities
- `app/src/main/java/utils/` — permissions, sessions, crypto helpers

## Roadmap (Suggested Next Steps)
1. **Group chat UI + flows**
2. **Attachments (images/audio)**
3. **Delivery receipts**
4. **Better peer trust management**
5. **Backup/export of local DB**

## Testing Ideas
### Manual
- Two‑device discovery and message exchange
- Toggle trusted mode and validate decryption
- QR scan and connect

### Automated (Potential)
- Repository logic tests for auth + storage
- DTN routing unit tests (bundle creation, TTL)

## Known Gaps / TODOs
- Group chat screen is a stub
- Some Room‑style comments remain but DB is SQLiteOpenHelper
- Full attachment handling not implemented
