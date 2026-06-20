# Pooling

[English](README.md) | **Русский**

---

Переиспользуй объекты без аллокаций. Не зависит от Unity — работает в любом C#-контексте.

Лёгкий generic-пул объектов для Unity и чистых C#-проектов. Включает `Pool<T>` для одного типа и `MultiPool<TKey, TValue>` для управления несколькими пулами под общим ключом.

## Установка

**Через Package Manager (Git URL)**

Открой `Window → Package Manager`, нажми `+` → `Add package from git URL` и введи:

```
https://github.com/EgorShesterikov/Unity-Pooling.git
```

Чтобы зафиксировать конкретную версию, добавь `#<тег>`, например `...Unity-Pooling.git#1.0.0`.

**Через `Packages/manifest.json`**

Добавь запись в блок `dependencies`:

```json
"com.egorshesterikov.pooling": "https://github.com/EgorShesterikov/Unity-Pooling.git"
```

**Через `.unitypackage`**

Скачай последний релиз со страницы [Releases](https://github.com/EgorShesterikov/Unity-Pooling/releases) и импортируй через `Assets → Import Package → Custom Package...`.

**Ручное копирование**

Склонируй или скачай репозиторий и скопируй папку куда угодно внутрь `Assets/` своего проекта.

## Классы

**`Pool<T>`** — одиночный типизированный пул.

**`MultiPool<TKey, TValue>`** — словарь пулов с произвольным ключом (как правило `System.Type`). Позволяет управлять множеством пулов через одну точку входа.

## Использование

### Pool\<T\>

```csharp
var pool = new Pool<MyObject>(
    factory: () => new MyObject(),
    actionOnGet: obj => obj.Reset(),
    actionOnRelease: obj => obj.Cleanup()
);

// Прогрев — создать экземпляры заранее
pool.Prewarm(10);

// Получить экземпляр (создаёт новый, если пул пуст)
var obj = pool.Get();

// Вернуть обратно
pool.Release(obj);

// Удалить экземпляр без возврата в пул
pool.Discard(obj);

// Очистить все экземпляры
pool.DiscardAll();
```

### MultiPool\<TKey, TValue\>

```csharp
var multiPool = new MultiPool<Type, IHandler>();

// Зарегистрировать фабрику для ключа
multiPool.RegisterFactory(typeof(MyHandler), () => new MyHandler());

// Взять и вернуть по ключу
var handler = multiPool.Get(typeof(MyHandler));
multiPool.Release(typeof(MyHandler), handler);
```

## Примечания

- `Pool<T>` отслеживает и свободные, и занятые экземпляры — двойной `Release` игнорируется без ошибок.
- `MultiPool` выбрасывает `KeyNotFoundException`, если вызвать `Get` до регистрации фабрики для этого ключа.
- Нет зависимостей от Unity — флаг `noEngineReferences` включён, сборка работает в любом C#-контексте.

## Лицензия

Распространяется под [MIT License](LICENSE.md).

Автор — **Egor Shesterikov**.
