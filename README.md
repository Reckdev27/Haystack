# HAYSTACK v3.0.2

## Short Description

Haystack is a lightweight Dependency Injection framework for Unity built for fast setup, clean architecture, and practical runtime workflows.  
It supports attribute-based injection, manual/container-style bindings, collider-driven injection, and editor tooling in one package.

Author: **Reckdev**

## Purpose

Haystack helps developers:

- decouple gameplay systems
- reduce hard references between components
- organize dependencies in a reusable way
- scale code from small prototypes to larger projects
- keep DI easy to use for both beginners and advanced users

## Core Benefits

- **Easy to Start**
  - Use `[Install]` and `[Inject]` directly on fields/properties.
  - Minimal setup required to get running in a scene.

- **Lightweight but Flexible**
  - Supports direct injection, manual binding, and container-style fluent bindings.
  - Includes singleton and transient lifetimes.

- **Runtime Friendly**
  - Bind, resolve, unbind, and rebind at runtime.
  - Works with dynamic gameplay flows and scene changes.

- **Gameplay-Driven DI**
  - Built-in collider/collider2D dependency injection for interaction systems.
  - Useful for pickups, triggers, combat, and overlap mechanics.

- **Safer Injection Flow**
  - Optional injection support for non-critical dependencies.
  - Validation helpers (`IsBound`, `ResolveAll`) for debugging and runtime checks.

- **Editor + Package Workflow**
  - Custom inspectors and control windows included.
  - Organized package structure (`Core`, `Runtime`, `Editor`, `Demo`, `Resources`).

## Typical Use Cases

- RPG systems (stats, weapons, quests, skills)
- Combat handlers and hit/trigger interactions
- Service-style managers (audio, save, input, UI state)
- Runtime-generated objects and modular scene systems

## Why Haystack

Haystack is designed to stay simple in daily use while still giving enough DI power for real production workflows.  
It keeps your code modular, testable, and easier to maintain without forcing heavy boilerplate.
```
