# Asteroids Pet Project

A Unity pet project based on the classic Asteroids arcade loop. The project focuses on a layered gameplay architecture, reusable runtime services, UI Toolkit screens, addressable content, and mobile-service integrations.

## Tech Stack

- Unity 6000.3.11f1
- C#
- Zenject
- UniTask
- Addressables
- Unity Input System
- UI Toolkit
- URP 2D Renderer
- Firebase Analytics and Remote Config
- Unity Ads, Authentication, Cloud Save, and In-App Purchasing

## Features

- Ship movement with inertia-style 2D controls
- Asteroid and UFO spawning systems
- Projectile and area-of-effect weapon logic
- Score, statistics, and game state flow
- Menu, authentication, loading, shop, HUD, and gameplay scenes
- Addressable resource loading and view pools
- ScriptableObject-based gameplay configuration
- Audio, animation, and visual resource bindings
- Local/cloud save service layer
- Rewarded ad continuation flow
- Shop product catalog and purchase entitlement sync

## Architecture

The project is organized around runtime layers:

- `Models` store gameplay state and domain data.
- `Presenters` bind models to views and scene objects.
- `Views` expose Unity components and prefab-facing events.
- `Services` handle resource loading, save flow, pools, and data sync.
- `Installers` configure dependencies through Zenject.
- `Settings` and `Resources` contain ScriptableObject and JSON configuration.

Most gameplay code lives under `Assets/_Project/Runtime`.

## Opening the Project

1. Open the repository with Unity `6000.3.11f1`.
2. Let Unity restore packages from `Packages/manifest.json`.
3. Open `Assets/_Project/Scenes/Bootstrap.unity`.
4. Enter Play Mode.
