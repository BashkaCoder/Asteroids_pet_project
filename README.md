# Asteroids Pet Project

Gameplay demonstration: https://youtu.be/OH1R7QYCoC0

Asteroids Demo is a Unity showcase project built around a classic 2D arcade shooter loop. The goal of the project is to show both gameplay implementation and the surrounding runtime architecture: scene flow, dependency injection, async loading, saves, analytics, remote configuration, ads, purchases, UI, audio, and content loading.

The project is intended as a cross-platform Unity gameplay and architecture demo. It uses Unity Input System bindings for keyboard, gamepad, and joystick-style controls, while also including service integrations that are common in live Unity projects.

## Quick Start

1. Open the repository in Unity Editor `6000.3.11f1`.
2. Let Unity restore packages from `Packages/manifest.json`.
3. Open `Assets/_Project/Scenes/Bootstrap.unity`.
4. Enter Play Mode.

## What to Review First

If you are reviewing the project, these areas give the best overview of the implementation:

- `Assets/_Project/Runtime/Installers` - Zenject composition roots for global and scene-specific systems.
- `Assets/_Project/Runtime/Services` - local saves, cloud saves, player data sync, autosave, resources, and view pools.
- `Assets/_Project/Runtime/RemoteConfig` - JSON-based numeric balancing with Firebase Remote Config and local defaults.
- `Assets/_Project/Runtime/AssetManagement` - Addressables loading abstraction for prefabs and resources.
- `Assets/_Project/Runtime/Presenters` - coordination layer between state, services, views, and gameplay objects.
- `Assets/_Project/Runtime/Ship`, `Asteroid`, `Ufo`, `Weapons` - core gameplay systems.
- `Assets/_Project/UI` and `Assets/_Project/Runtime/UI` - UI Toolkit layouts, styles, and controllers.

## Assignment Coverage

| Requirement | Implementation | Where to look |
| --- | --- | --- |
| Bootstrap scene | Separate startup scene that initializes global dependencies and moves into the application flow. | `Assets/_Project/Scenes/Bootstrap.unity`, `Runtime/LoadingServices`, `Runtime/Installers` |
| Zenject | Zenject is used for global and scene-level dependency registration. | `Runtime/Installers` |
| Presenter-driven UI/runtime flow | Runtime is split into models, presenters, views, services, and UI controllers. | `Runtime/Models`, `Runtime/Presenters`, `Runtime/Views`, `Runtime/UI` |
| UniTask instead of coroutines | Loading, save flow, service initialization, and asset access use UniTask-based async code. | `Runtime/LoadingServices`, `Runtime/Services`, `Runtime/AssetManagement` |
| Local saves | Player data is serialized as one JSON model and stored through PlayerPrefs. | `Runtime/Services/LocalSaveService.cs`, `Runtime/Data/PlayerData.cs` |
| Firebase Analytics | Gameplay events are sent through an analytics interface instead of direct gameplay dependencies on Firebase. | `Runtime/Analytics`, `Runtime/Analytics/Firebase` |
| Addressables | Runtime prefabs and settings are loaded through Addressables-facing loaders and resource services. | `Runtime/AssetManagement`, `Runtime/Constants/AddresablesPaths.cs`, `Assets/AddressableAssetsData` |
| Ads | Rewarded and interstitial ad flow is isolated behind interfaces. | `Runtime/Abstract/Ads`, `Runtime/Ads` |
| Remote config | Numeric gameplay parameters are stored as JSON with local defaults and Firebase Remote Config override support. | `Runtime/RemoteConfig`, `Assets/Resources/RemoteConfig/numeric_config_default.json` |
| In-app purchases | Shop products and purchase state are handled through a Unity IAP service abstraction. | `Runtime/InAppPurchase`, `Assets/Resources/IAPProductCatalog.json` |
| Cloud saves | Unity Cloud Save is wrapped behind a save interface and synchronized with local player data. | `Runtime/Services/UnityCloudSaveService.cs`, `Runtime/Services/PlayerDataSyncService.cs` |
| Cloud/local save conflict | If local data is newer than cloud data, the flow can ask the player which save source to keep. | `Runtime/Services/PlayerDataSyncService.cs`, `Runtime/Presenters/AuthenticationPresenter.cs` |
| Visual effects and sounds | Projectiles, lasers, explosions, background music, and hit/shot sounds are configured as runtime resources. | `Assets/_Project/Animations`, `Assets/_Project/Sound`, `Assets/_Project/Settings/General` |
| Build/game setup | Build profiles, render pipeline, input, scenes, Addressables, and project settings are committed with the project. | `ProjectSettings`, `Assets/Settings`, `Assets/Input`, `Assets/Rendering` |

## Gameplay

- Inertia-style 2D ship movement with world-boundary wrapping.
- Projectile weapon, laser-style area attack, and power shield logic.
- Asteroid spawning, splitting, destruction, and score rewards.
- UFO spawning, movement, chasing, and projectile attacks.
- Run state handling: preparation, gameplay, defeat, continuation, and game over.
- Score and endgame statistics tracking.
- Keyboard, gamepad, and joystick bindings through Unity Input System.

## Architecture

The project uses scene-oriented composition rather than a single monolithic runtime. Bootstrap, authentication, menu, loading, and gameplay are separate stages, so initialization and transitions stay explicit.

Runtime code is organized around a small set of responsibilities:

- `Models` own state and gameplay rules.
- `Presenters` coordinate runtime behavior and connect logic to views.
- `Views` expose Unity-facing objects, prefab events, and visual state.
- `Services` isolate infrastructure concerns such as loading, saves, synchronization, pools, and integrations.
- `Installers` register dependencies through Zenject.
- `Settings` and `Resources` contain ScriptableObject and JSON configuration.

Most project-specific gameplay code lives under `Assets/_Project/Runtime`.

## Runtime Systems

### Async Loading

Startup and scene preparation are handled through dedicated loading task processors. Addressable prefabs and ScriptableObject resources are loaded asynchronously and exposed through services before gameplay presenters start using them.

Relevant files:

- `Runtime/Abstract/Services/BaseLoadingTasksProcessor.cs`
- `Runtime/LoadingServices`
- `Runtime/AssetManagement`
- `Runtime/Services/GameResourcesService.cs`

### Saves and Synchronization

Player data is represented as a model and saved as a single JSON payload. Local save and cloud save implementations share the same `ISaveService` contract, which keeps persistence details out of gameplay code.

The sync flow supports offline startup, online cloud loading, and a conflict case where local data is newer than cloud data.

Relevant files:

- `Runtime/Services/LocalSaveService.cs`
- `Runtime/Services/UnityCloudSaveService.cs`
- `Runtime/Services/PlayerDataSyncService.cs`
- `Runtime/Services/PlayerAutoSaveService.cs`
- `Runtime/Data/PlayerData.cs`

### Remote Configuration

Numeric gameplay tuning is stored in one JSON structure. The project loads local defaults first, then merges Firebase Remote Config values when available.

Examples of remotely tunable values:

- ship, asteroid, and UFO movement
- asteroid and UFO spawn settings
- score rewards
- weapon cooldowns, projectile speed, laser size, shield duration
- background motion parameters

Relevant files:

- `Runtime/RemoteConfig/FirebaseRemoteConfigProvider.cs`
- `Runtime/RemoteConfig/NumericConfigParser.cs`
- `Runtime/Constants/RemoteConfigKeys.cs`
- `Assets/Resources/RemoteConfig/numeric_config_default.json`

### Analytics, Ads, and Purchases

Gameplay code does not call Firebase Analytics, Unity Ads, or Unity IAP directly. These integrations are wrapped behind interfaces/services so the SDK layer can be replaced or extended without changing the core gameplay flow.

Relevant files:

- `Runtime/Analytics`
- `Runtime/Ads`
- `Runtime/InAppPurchase`
- `Runtime/Abstract/Ads`

## Tech Stack

- Unity `6000.3.11f1`
- C#
- Zenject
- UniTask
- Addressables
- Unity Input System
- UI Toolkit
- URP 2D Renderer
- Unity Services Authentication
- Unity Cloud Save
- Unity Ads
- Unity IAP
- Firebase Analytics
- Firebase Remote Config
- External Dependency Manager for Unity

## Project Structure

```text
Assets/_Project/
  Runtime/                Project gameplay code, services, presenters, views
  Scenes/                 Bootstrap, authentication, menu, loading, gameplay scenes
  Prefabs/                Actor, context, scene object, and UI prefabs
  Settings/               ScriptableObject gameplay and integration settings
  UI/                     UI Toolkit layouts and styles
  Art/                    Sprites and visual assets
  Animations/             Explosion, flame, laser, shield, and destruction animations
  Sound/                  Music and gameplay sound effects

Assets/AddressableAssetsData/   Addressables groups, builders, profiles
Assets/Input/                   Unity Input System actions and generated wrapper
Assets/Resources/               Runtime JSON/configuration resources
ProjectSettings/                Unity project, build, renderer, input, and package settings
Packages/                       Unity package manifest and lock file
```

## Notes

- The repository is structured as a reviewable Unity project rather than a packaged release.
- External service integrations are implemented as project code and settings, but live service behavior still depends on the configured Unity/Firebase project environment.
- Addressables are configured in the project; generated remote bundle output can be rebuilt from the Unity editor when needed.
