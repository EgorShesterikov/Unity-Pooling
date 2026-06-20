# Pooling

**English** | [Русский](README.ru.md)

---

Reuse objects without allocations. No Unity dependencies — works in any C# context.

A lightweight generic object pool for Unity and pure C# projects. Provides `Pool<T>` for a single type and `MultiPool<TKey, TValue>` for managing multiple pools under a common key.

## Installation

**Via Package Manager (Git URL)**

Open `Window → Package Manager`, click `+` → `Add package from git URL` and enter:

```
https://github.com/EgorShesterikov/Unity-Pooling.git
```

To pin a specific version append `#<tag>`, e.g. `...Unity-Pooling.git#1.0.0`.

**Via `Packages/manifest.json`**

Add the entry to the `dependencies` block:

```json
"com.egorshesterikov.pooling": "https://github.com/EgorShesterikov/Unity-Pooling.git"
```

**Via `.unitypackage`**

Grab the latest release from the [Releases](https://github.com/EgorShesterikov/Unity-Pooling/releases) page and import it via `Assets → Import Package → Custom Package...`.

**Manual copy**

Clone or download the repository and copy the folder anywhere under your project's `Assets/`.

## Classes

**`Pool<T>`** — a single typed pool.

**`MultiPool<TKey, TValue>`** — a dictionary of pools keyed by any type (typically `System.Type`). Lets you manage many pools through a single entry point.

## Usage

### Pool\<T\>

```csharp
var pool = new Pool<MyObject>(
    factory: () => new MyObject(),
    actionOnGet: obj => obj.Reset(),
    actionOnRelease: obj => obj.Cleanup()
);

// Prewarm — create instances ahead of time
pool.Prewarm(10);

// Get an instance (creates a new one if the pool is empty)
var obj = pool.Get();

// Return it
pool.Release(obj);

// Permanently remove an instance without returning it
pool.Discard(obj);

// Clear all instances
pool.DiscardAll();
```

### MultiPool\<TKey, TValue\>

```csharp
var multiPool = new MultiPool<Type, IHandler>();

// Register a factory for a specific key
multiPool.RegisterFactory(typeof(MyHandler), () => new MyHandler());

// Get and release by key
var handler = multiPool.Get(typeof(MyHandler));
multiPool.Release(typeof(MyHandler), handler);
```

## Notes

- `Pool<T>` tracks both free and occupied instances — double-releasing an instance is silently ignored.
- `MultiPool` throws `KeyNotFoundException` if you call `Get` before registering a factory for that key.
- No Unity dependencies — `noEngineReferences` is enabled, so the assembly works in any C# context.

## License

Released under the [MIT License](LICENSE.md).

Authored by **Egor Shesterikov**.
