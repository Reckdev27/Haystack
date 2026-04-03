# HAYSTACK v3.0.1

## Haystack Dependency Injection (Unity Engine Framework)

Haystack is a Unity dependency injection framework focused on clean architecture, runtime flexibility, and editor tooling.

Author: **Reckdev**

## Features

- Attribute-based DI (`[Install]`, `[Inject]`)
- Direct and indirect injection flows
- Manual bind/resolve APIs
- Static dependency install/inject support
- Collider / Collider2D dependency injection
- Unbind support (manual and after-injection)
- Runtime refresh/reset pipeline
- Custom inspectors for framework MonoBehaviours
- Package editor window and welcome screen
- Cross-platform ready (Desktop, Android, WebGL)

## Folder Structure

```text
Assets/Haystack/
  Core/
  Runtime/
  Editor/
  Resources/
  Demo/
  Documentation.html
  Documentation.pdf
```

## Requirements

- Unity **2022.3 LTS** (recommended)

## Setup

1. Import/copy `Assets/Haystack` into your project.
2. Open **Tools > Haystack > Haystack**.
3. Verify config in `Assets/Haystack/Resources/HayConfig.asset`.
4. Ensure demo scenes (if used) are added to Build Settings.

## Core Attributes

- `[HayDirectClass]`  
  Marks a class for direct install/inject scan.

- `[Install]`  
  Registers dependency from field/property.

- `[Inject]`  
  Resolves dependency into field/property.

- `[HayInstallingMethod]`  
  Called during install stage.

- `[HayInjectedMethod]`  
  Called after inject stage.

- `[HayPhysicsClass]`  
  Required for collider-based inject calls from that class.

## Runtime API (Common)

- `Haystack.BindDependency<T>(...)`
- `Haystack.BindStaticDependency<T>(...)`
- `Haystack.TryResolve<T>(out ...)`
- `Haystack.Resolve<T>(...)`
- `Haystack.TryResolveStatic<T>(out ...)`
- `Haystack.ResolveStatic<T>(...)`
- `Haystack.Unbind<T>(...)`
- `Haystack.UnbindStatic<T>(...)`
- `Haystack.Refresh()`
- `Haystack.Reset()`

## Injection Flows

### 1) Direct Injection

Use for scene objects already present at load.

```csharp
[HayDirectClass]
public class Installer : MonoBehaviour
{
    [SerializeField, Install] SampleDependency _dep;
}

[HayDirectClass]
public class Consumer : MonoBehaviour
{
    [Inject] SampleDependency _dep;
}
```

### 2) Indirect Builder

Use runtime/manual flow by key.

```csharp
readonly IndirectHayBuilder _builder = new();

void Awake() => _builder.InstallAll(this, _customKey);

public void InjectNow()
{
    _builder.InjectAll(this, () =>
    {
        Debug.Log("Injected");
    }, _customKey);
}
```

### 3) Manual Bind/Resolve

```csharp
Haystack.BindDependency(new SampleDependency(), customKey: "player1");

if (Haystack.TryResolve<SampleDependency>(out var dep, customKey: "player1"))
{
    Debug.Log(dep);
}
```

## Collider Injection

```csharp
other.InjectDependency<PlayerDependencies>(this, dep =>
{
    Debug.Log(dep.GameObject.name);
}, unbindAfterInjection: true);
```

- `unbindAfterInjection: true` consumes that collider-bound dependency in one pass.
- You can also manually call:
  - `collider.Unbind()`
  - `collider2D.Unbind2D()`

## Unbinding Notes

- `HayBind.UnbindAfterInjection` unbinds the exact slot after injection.
- `Haystack.Unbind<T>(key, customKey)` removes only the matching slot.
- Reinstall/rebind is required to use that slot again.

## Demos

Located under:

```text
Assets/Haystack/Demo/Sample Demo/
```

Includes direct, indirect, static, collider, manual binding, and unbinding samples.

## Troubleshooting

- **Scene index load error**: add scenes to Build Settings.
- **Injection not resolving**:
  - confirm `[Install]` source is initialized
  - match `key/customKey`
  - verify direct/indirect flow timing
- **Collider injection not working**:
  - caller class must have `[HayPhysicsClass]`
  - dependency must be bound to collider/collider2D first

## License

Internal/package license as defined by the publisher.
```
OPENSOURCE
