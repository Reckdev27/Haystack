# HAYSTACK PRO v3.0.3

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



## Quick Sample Code

### 1) Direct Install + Inject (Beginner Friendly)

```csharp
using UnityEngine;
using RD.Framework.Injection.Haystack;

[HayDirectClass]
public class GameInstaller : MonoBehaviour
{
    [SerializeField, Install] private GameConfig _config;
}

[System.Serializable]
public class GameConfig
{
    public int MaxLives = 3;
    public float MoveSpeed = 5f;
}

[HayDirectClass]
public class PlayerConsumer : MonoBehaviour
{
    [Inject] private GameConfig _config;

    [HayInjectedMethod]
    private void OnInjected()
    {
        Debug.Log($"Lives: {_config.MaxLives}, Speed: {_config.MoveSpeed}");
    }
}
```

---

### 2) Manual Bind + Resolve

```csharp
using UnityEngine;
using RD.Framework.Injection.Haystack;

public class ManualSample : MonoBehaviour
{
    private void Start()
    {
        Haystack.BindDependency(new ScoreService(10), key: "score");

        if (Haystack.TryResolve(out ScoreService service, key: "score"))
        {
            Debug.Log($"Score: {service.Value}");
        }
    }
}

public class ScoreService
{
    public int Value;
    public ScoreService(int value) => Value = value;
}
```

---

### 3) Factory + Lifetime (New Lightweight Container Style)

```csharp
using UnityEngine;
using RD.Framework.Injection.Haystack;

public interface IEnemyAI
{
    string Name { get; }
}

public class GoblinAI : IEnemyAI
{
    public string Name => "Goblin";
}

public class BindingInstaller : MonoBehaviour
{
    private void Awake()
    {
        // Singleton
        Haystack.BindFactory<IEnemyAI>(() => new GoblinAI(), HayLifetime.Singleton, key: "ai-single");

        // Transient
        Haystack.BindFactory<IEnemyAI>(() => new GoblinAI(), HayLifetime.Transient, key: "ai-transient");

        // Fluent API
        Haystack.CreateBinding<IEnemyAI>()
            .To<GoblinAI>()
            .AsSingleton();
    }
}
```

---

### 4) Optional Injection

```csharp
using UnityEngine;
using RD.Framework.Injection.Haystack;

public interface IQuestService
{
    bool IsComplete { get; }
}

[HayDirectClass]
public class QuestUI : MonoBehaviour
{
    [Inject("mainQuest", true)] private IQuestService _quest; // optional

    private void Start()
    {
        Debug.Log(_quest == null ? "Quest not installed" : $"Quest complete: {_quest.IsComplete}");
    }
}
```

---

### 5) ResolveAll + IsBound

```csharp
using UnityEngine;
using RD.Framework.Injection.Haystack;
using System.Collections.Generic;

public interface IEnemy
{
    string Id { get; }
}

public class EnemyDebug : MonoBehaviour
{
    private void Update()
    {
        bool hasQuest = Haystack.IsBound<IQuestService>(key: "mainQuest");
        List<IEnemy> enemies = Haystack.ResolveAll<IEnemy>();

        Debug.Log($"QuestBound: {hasQuest} | EnemyCount: {enemies.Count}");
    }
}
```

---

### 6) Collider Injection + Unbind After Injection

```csharp
using UnityEngine;
using RD.Framework.Injection.Haystack;

[HayDirectClass]
public class LootInstaller : MonoBehaviour
{
    [SerializeField, Install(HayComponent.Collider)]
    private LootData _loot;
}

[System.Serializable]
public class LootData
{
    public string ItemName;
    public int Amount;
}

[HayPhysicsClass]
public class LootPickup : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        other.InjectDependency<LootData>(this, loot =>
        {
            Debug.Log($"Picked: {loot.ItemName} x{loot.Amount}");
        }, unbindAfterInjection: true);
    }
}
```

---

## Notes

- Use `Haystack.Refresh()` if you instantiate direct classes at runtime.
- Use `Haystack.Unbind<T>()` when you want to clear a specific slot.
- Prefer `TryResolve` when a dependency may not exist.


## License 
```
OPEN-SOURCE

