# Haystack Dependency Injection

Haystack is a Unity-first Dependency Injection framework focused on clean architecture, runtime flexibility, and package-ready tooling for game projects.

It supports direct attribute-based injection, indirect key-based injection, manual container-style binding, static injection, and collider-driven dependency resolution for gameplay interactions.

Author: **Reckdev**

---

## Why Use Haystack

- Reduces coupling between gameplay systems through explicit dependency contracts.
- Works with scene objects, spawned runtime objects, and static flows.
- Supports coroutine-based setup methods for WebGL-safe workflows (`IEnumerator` instead of `async/await`).
- Provides manual bind/resolve APIs for lightweight container usage.
- Includes custom editor tooling and package-ready structure.
- Supports targeted unbinding for dynamic runtime control.

---

## Features

- Attribute-based injection and installation.
- Direct class scene scanning and runtime refresh.
- Indirect injection via custom keys.
- Collider / Collider2D dependency mapping.
- Static dependency install/inject support.
- Manual binding, resolve, try-resolve, and unbind APIs.
- Legacy alias compatibility for older Haystack syntax.
- Runtime reset and refresh utilities.
- Editor UI support for framework components.

---

## Package Structure

```text
Assets/Haystack/
  Core/
    HaystackCore.cs                // Core registry/injection logic
  Runtime/
    Haystack.cs                    // Public runtime API
    IndirectHayBuilder.cs          // Indirect key-based injection helper
    HaystackInitializer.cs         // Runtime bootstrap
    ...
  Editor/
    HaystackControlWindow.cs
    RDFrameworkMonoBehaviourInspector.cs
    HaystackPackageSecurity.cs
    ...
  Resources/
    HayConfig.asset
    HayEditorSettings.asset
    HayTM.bytes
    HayGuard.bytes
  Demo/
    Sample scenes and scripts
  Documentation.html
  Documentation.pdf
```

---

## Core Attributes

- `[Install]` registers a dependency.
- `[Inject]` injects a dependency.
- `[HayDirectClass]` enables direct install/inject scanning.
- `[HayPhysicsClass]` enables physics/collider injection patterns.
- `[HayInstallingMethod]` callback after install phase.
- `[HayInjectedMethod]` callback after inject phase.

Both installing/injected methods support `IEnumerator` for coroutine workflows.

---

## Direct Injection Example

```csharp
[HayDirectClass]
public class SampleInstaller : MonoBehaviour
{
    [SerializeField, Install] private SampleDependency _dependency;

    [HayInstallingMethod]
    private void OnInstalled()
    {
        Debug.Log("Installed.");
    }
}

[HayDirectClass]
public class SampleConsumer : MonoBehaviour
{
    [SerializeField, Inject] private SampleDependency _dependency;

    [HayInjectedMethod]
    private void OnInjected()
    {
        Debug.Log(_dependency.Time);
    }
}
```

If a new direct class is instantiated at runtime, call:

```csharp
Haystack.Refresh();
```

---

## Indirect Builder (Custom Key) Example

```csharp
public class CustomInstaller : MonoBehaviour
{
    [SerializeField] private string _customKey;
    [SerializeField, Install] private CustomKDependency _dependency;

    private readonly IndirectHayBuilder _builder = new IndirectHayBuilder();

    private void Awake()
    {
        _builder.InstallAll(this, _customKey);
    }
}

public class CustomConsumer : MonoBehaviour
{
    [Inject] private CustomKDependency _dependency;

    private readonly IndirectHayBuilder _builder = new IndirectHayBuilder();

    public void ResolvePlayer1()
    {
        _builder.InjectAll(this, () =>
        {
            Debug.Log(_dependency.Name);
        }, "player-1");
    }
}
```

---

## Manual Binding Example

```csharp
Haystack.BindDependency(new SampleDependency { Time = 12f });

if (Haystack.TryResolve<SampleDependency>(out var dependency))
{
    Debug.Log(dependency.Time);
}

Haystack.BindStaticDependency("1.0.0", key: "version");
Debug.Log(Haystack.ResolveStatic<string>("version"));
```

Use `TryResolve<T>` when dependencies may be absent.

---

## Collider Injection Example

```csharp
public class SamplePlayer : MonoBehaviour
{
    [SerializeField, Install(HayComponent.Collider)]
    private PlayerDependencies _playerDependencies;

    private readonly IndirectHayBuilder _builder = new IndirectHayBuilder();

    private void Awake() => _builder.InstallAll(this);
}

[HayPhysicsClass]
public class SampleTrigger : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        other.InjectDependency<PlayerDependencies>(this, dep =>
        {
            Debug.Log(dep.GameObject.name);
        });
    }
}
```

2D version is supported with `Collider2D.InjectDependency<T>()`.

---

## Static Injection Example

```csharp
[HayDirectClass]
public static class SampleStaticInstaller
{
    [Install] public static string Dependency;

    [HayInstallingMethod]
    public static void Install() => Dependency = "Reckdev";
}

[HayDirectClass]
public static class SampleStaticConsumer
{
    [Inject] public static string Dependency;
}
```

---

## Unbinding

Single-use unbind after injection:

```csharp
public class SUManager : MonoBehaviour
{
    [Inject(HayBind.UnbindAfterInjection)]
    private CustomKDependency _customDependency;
}
```

Manual unbind:

```csharp
Haystack.Unbind<CustomKDependency>(customKey: "player-1");
Haystack.UnbindStatic<GameConfig>();
collider.Unbind();
collider2D.Unbind2D();
```

Unbinding affects the exact slot (type + key + custom key).

---

## Runtime Utilities

```csharp
Haystack.Refresh(); // rescan direct classes
Haystack.Reset();   // clear and rebuild container
```

---

## Legacy Aliases (Backward Compatibility)

Haystack still accepts older names, including:

- `[Haystack.Inject]`, `[Haystack.Install]`
- `[Haystack.DirectHayClass]`, `[Haystack.PhysicsClass]`
- `[HayAwaker]`, `[HayStarter]`
- `Haystack.Component.Collider`
- `Haystack.Bind.UnbindAfterInjection`

---

## Editor Tools

Menu:
- `Tools/Haystack/Haystack` — opens Haystack Control Window
- `Tools/Haystack/Security/*` — verification/reseal/security actions
- `Tools/Haystack/Welcome` — opens welcome window

---

## Platform Notes

- Prefer coroutines (`IEnumerator`) over `async/await` for WebGL compatibility.
- Keep dependency install/inject callbacks deterministic and lightweight.
- Works across desktop, Android, and WebGL when project setup is valid.

---

## Recommended Workflow

1. Install dependencies using `[Install]` on installer classes.
2. Inject dependencies using `[Inject]` on consumer classes.
3. Use direct classes for scene bootstrap flows.
4. Use indirect builder/custom keys for runtime swap scenarios.
5. Use manual bind/resolve for lightweight utility flows.
6. Use unbind intentionally for one-shot or dynamic gameplay interactions.

---

## License / Usage
Free to all
