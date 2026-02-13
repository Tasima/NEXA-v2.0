# Design Patterns Used (GoF)

This document maps GoF patterns to Kotlin production code under `app/src/main/java`.

## Pattern Catalog

### Creational
| Pattern | Where | Explanation |
| --- | --- | --- |
| Singleton | `app/src/main/java/Nexa.kt`<br>`app/src/main/java/DataLayer/AppDatabase.kt`<br>`app/src/main/java/CommsLayer/DTN/DTNConfig.kt`<br>`app/src/main/java/utils/PermissionUtils.kt`<br>`app/src/main/java/utils/ProfileUtils.kt` | Single shared instance or access point for app-wide state or utilities. |
| Factory Method | `app/src/main/java/logic/MessageCenter.kt` | Creation function centralizes construction and enforces invariants before returning an instance. |

### Structural
| Pattern | Where | Explanation |
| --- | --- | --- |
| Adapter | `app/src/main/java/ui/components/adapters/ChatAdapter.kt`<br>`app/src/main/java/ui/components/adapters/ChatListAdapter.kt`<br>`app/src/main/java/ui/components/adapters/MessageAdapter.kt`<br>`app/src/main/java/ui/components/adapters/NearbyPeersAdapter.kt`<br>`app/src/main/java/ui/components/adapters/PeerListAdapter.kt` | Adapters translate domain data into ListView or RecyclerView rows. |
| Facade | `app/src/main/java/Nexa.kt`<br>`app/src/main/java/CommsLayer/NearbyService.kt`<br>`app/src/main/java/DataLayer/DatabaseRepository.kt`<br>`app/src/main/java/utils/KeyManager.kt`<br>`app/src/main/java/utils/EncryptionServcice.kt`<br>`app/src/main/java/utils/SessionManager.kt`<br>`app/src/main/java/utils/QRCodeGenerator.kt` | Simplified interface over complex subsystems (Nearby, DB, crypto, prefs, QR). |

### Behavioral
| Pattern | Where | Explanation |
| --- | --- | --- |
| Observer | `app/src/main/java/CommsLayer/NearbyService.kt`<br>`app/src/main/java/CommsLayer/DTN/DTNMessageManager.kt`<br>`app/src/main/java/logic/EnhancedMsgHandler.kt`<br>`app/src/main/java/logic/MessageCenter.kt`<br>`app/src/main/java/com/example/nexausingnearby/service/NexaMessagingService.kt`<br>`app/src/main/java/viewModels/NearbyViewModel.kt`<br>`app/src/main/java/ui/HomeScreen.kt`<br>`app/src/main/java/ui/ChatsScreen.kt`<br>`app/src/main/java/ui/InChatsActivity.kt` | StateFlow/SharedFlow or callbacks notify subscribers of state or message events. |
| Mediator | `app/src/main/java/CommsLayer/DTN/DTNMessageManager.kt`<br>`app/src/main/java/logic/MessageCenter.kt`<br>`app/src/main/java/com/example/nexausingnearby/service/NexaMessagingService.kt` | Central coordinators route messages between components to reduce direct coupling. |
| Template Method | `app/src/main/java/Nexa.kt`<br>`app/src/main/java/DataLayer/AppDatabase.kt`<br>`app/src/main/java/viewModels/NearbyViewModel.kt`<br>`app/src/main/java/ui/HomeScreen.kt`<br>`app/src/main/java/ui/ChatsScreen.kt`<br>`app/src/main/java/ui/InChatsActivity.kt`<br>`app/src/main/java/ui/LoginScreen.kt`<br>`app/src/main/java/ui/SettingsScreen.kt`<br>`app/src/main/java/ui/SignUpScreen.kt`<br>`app/src/main/java/ui/QRCodeActivity.kt`<br>`app/src/main/java/ui/ProfileScreen.kt`<br>`app/src/main/java/ui/DiscoveryScreen.kt`<br>`app/src/main/java/com/example/nexausingnearby/service/NexaMessagingService.kt` | Android framework defines lifecycle skeleton; subclasses override hooks to customize steps. |

## Multi-pattern Cases
| File | Patterns | Explanation |
| --- | --- | --- |
| `app/src/main/java/Nexa.kt` | Singleton, Facade, Template Method | Application singleton exposes subsystem accessors (Facade) while participating in lifecycle callbacks. |
| `app/src/main/java/logic/MessageCenter.kt` | Factory Method, Mediator, Observer | Factory creates validated instances; the class mediates between DTN and persistence and emits message events. |
| `app/src/main/java/CommsLayer/DTN/DTNMessageManager.kt` | Mediator, Observer | Coordinates routing between peers and publishes delivery state. |
| `app/src/main/java/com/example/nexausingnearby/service/NexaMessagingService.kt` | Template Method, Mediator, Observer | LifecycleService hooks manage startup while the service mediates messaging and re-emits events. |
| `app/src/main/java/DataLayer/AppDatabase.kt` | Singleton, Template Method | Singleton database helper overriding framework lifecycle methods. |
| `app/src/main/java/CommsLayer/NearbyService.kt` | Facade, Observer | Wraps Nearby Connections and publishes peer state via flows and callbacks. |
| `app/src/main/java/viewModels/NearbyViewModel.kt` | Template Method, Observer | AndroidViewModel lifecycle hook plus observable peer stream. |
| `app/src/main/java/ui/HomeScreen.kt` | Template Method, Observer | Activity lifecycle plus peer stream collection. |
| `app/src/main/java/ui/ChatsScreen.kt` | Template Method, Observer | Activity lifecycle plus peer stream collection. |
| `app/src/main/java/ui/InChatsActivity.kt` | Template Method, Observer | Activity lifecycle plus message stream collection. |

## File-by-file Classification
| File | Pattern(s) | Notes |
| --- | --- | --- |
| `app/src/main/java/Nexa.kt` | Singleton, Facade, Template Method | Application singleton that exposes subsystem accessors and overrides lifecycle hooks. |
| `app/src/main/java/viewModels/NearbyViewModel.kt` | Observer, Template Method | ViewModel exposes peer StateFlow and overrides `onCleared`. |
| `app/src/main/java/logic/EnhancedMsgHandler.kt` | Observer | Registers DTN callbacks and forwards results to consumers. |
| `app/src/main/java/logic/MessageCenter.kt` | Factory Method, Mediator, Observer | Central message coordinator and event emitter. |
| `app/src/main/java/utils/PermissionUtils.kt` | Singleton | Kotlin object consolidating permission sets. |
| `app/src/main/java/utils/MessagingSecurityMode.kt` | None | Enum defining messaging security modes. |
| `app/src/main/java/utils/KeyManager.kt` | Facade | Simplifies key storage and crypto operations. |
| `app/src/main/java/utils/EncryptionServcice.kt` | Facade | Simplifies libsodium crypto API usage. |
| `app/src/main/java/utils/QRCodeGenerator.kt` | Facade | Simplifies ZXing QR generation and verification. |
| `app/src/main/java/utils/ProfileUtils.kt` | Singleton | Stateless profile formatting helpers. |
| `app/src/main/java/utils/SessionManager.kt` | Facade | Simplified interface to SharedPreferences session data. |
| `app/src/main/java/ui/InChatsActivity.kt` | Template Method, Observer | Activity lifecycle plus message stream collection. |
| `app/src/main/java/ui/HomeScreen.kt` | Template Method, Observer | Activity lifecycle plus Nearby flows collection. |
| `app/src/main/java/ui/GroupScreen.kt` | None | Placeholder Activity with no overrides yet. |
| `app/src/main/java/ui/DiscoveryScreen.kt` | Template Method | Activity lifecycle overrides (`onCreate`). |
| `app/src/main/java/ui/QRCodeActivity.kt` | Template Method | Activity lifecycle overrides (`onCreate`, `onActivityResult`, `onDestroy`). |
| `app/src/main/java/ui/ProfileScreen.kt` | Template Method | Activity lifecycle overrides (`onCreate`). |
| `app/src/main/java/ui/LoginScreen.kt` | Template Method | Activity lifecycle overrides (`onCreate`, `onStart`). |
| `app/src/main/java/ui/SettingsScreen.kt` | Template Method | Activity lifecycle overrides (`onCreate`, `onResume`). |
| `app/src/main/java/ui/SignUpScreen.kt` | Template Method | Activity lifecycle overrides (`onCreate`, `onBackPressed`). |
| `app/src/main/java/ui/ChatsScreen.kt` | Template Method, Observer | Activity lifecycle plus Nearby flows collection. |
| `app/src/main/java/CommsLayer/NearbyService.kt` | Facade, Observer | Wrapper around Nearby Connections with observable state. |
| `app/src/main/java/DataLayer/StorageManager.kt` | None | Entire file is commented out; treated as out of scope. |
| `app/src/main/java/DataLayer/AppDatabase.kt` | Singleton, Template Method | SQLiteOpenHelper singleton with `onCreate` and `onUpgrade`. |
| `app/src/main/java/DataLayer/DatabaseRepository.kt` | Facade | High-level data access API over AppDatabase. |
| `app/src/main/java/CommsLayer/DTN/DTNMessageManager.kt` | Mediator, Observer | Routes bundles and exposes delivery status. |
| `app/src/main/java/CommsLayer/DTN/DTNMessage.kt` | None | Data types for DTN bundles and metadata. |
| `app/src/main/java/CommsLayer/DTN/DTNConfig.kt` | Singleton | Kotlin object holding shared DTN configuration constants. |
| `app/src/main/java/DataLayer/models/UserEntity.kt` | None | Data class for user records. |
| `app/src/main/java/DataLayer/models/StoredMessageEntity.kt` | None | Data class for stored message records. |
| `app/src/main/java/DataLayer/models/PendingBundleEntity.kt` | None | Data class for pending bundle records. |
| `app/src/main/java/DataLayer/models/PeerVisibility.kt` | None | Enum for peer visibility values. |
| `app/src/main/java/DataLayer/models/PeerProfile.kt` | None | Data class for peer profiles. |
| `app/src/main/java/DataLayer/models/PeerEntity.kt` | None | Data class for discovered peers. |
| `app/src/main/java/DataLayer/models/MessageEntity.kt` | None | Data class for message payloads and helpers. |
| `app/src/main/java/DataLayer/models/GroupEntity.kt` | None | Data class for group metadata and helpers. |
| `app/src/main/java/ui/components/models/ChatMessage.kt` | None | UI model for chat messages. |
| `app/src/main/java/ui/components/models/Chat.kt` | None | UI model for conversation previews. |
| `app/src/main/java/ui/components/models/Message.kt` | None | UI model for message rows. |
| `app/src/main/java/ui/components/models/NearbyPeerItem.kt` | None | UI model for nearby peer list entries. |
| `app/src/main/java/ui/components/adapters/PeerListAdapter.kt` | Adapter | ArrayAdapter binding peers into list rows. |
| `app/src/main/java/ui/components/adapters/NearbyPeersAdapter.kt` | Adapter | RecyclerView adapter for nearby peers. |
| `app/src/main/java/ui/components/adapters/MessageAdapter.kt` | Adapter | RecyclerView adapter for message bubbles. |
| `app/src/main/java/ui/components/adapters/ChatListAdapter.kt` | Adapter | RecyclerView adapter for chat list rows. |
| `app/src/main/java/ui/components/adapters/ChatAdapter.kt` | Adapter | BaseAdapter binding legacy chat rows. |
| `app/src/main/java/com/example/nexausingnearby/service/NexaMessagingService.kt` | Template Method, Mediator, Observer | Service lifecycle hooks plus message routing and event streaming. |
