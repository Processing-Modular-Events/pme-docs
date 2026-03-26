# ModuleConfig

`fr.capellegab.api.module.ModuleConfig`

Record that defines the declarative configuration of a module. All fields are **required** — a module with an incomplete config will be rejected by the core at loading time.

## Definition

```java
public record ModuleConfig(
    String moduleName,
    String version,
    String author,
    String description,
    Set<EventType> subscribesTo,
    Priority priority
) {}
```

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `moduleName` | `String` | Yes | Unique module identifier. No two modules can share the same name. |
| `version` | `String` | Yes | Module version (e.g. `"1.0.0"`). |
| `author` | `String` | Yes | Author or organization. |
| `description` | `String` | Yes | Short module description. |
| `subscribesTo` | `Set<EventType>` | Yes | Event types the module receives. Must contain at least one type. |
| `priority` | [`Priority`](../event/priority.md) | Yes | Processing priority. |

## Validation

The record validates fields at construction time. If a field is missing or empty, an `IllegalArgumentException` is thrown:

```java
// This throws an exception
new ModuleConfig(null, "1.0.0", "author", "desc", Set.of(EventType.TRANSACTION), Priority.LOW);
// IllegalArgumentException: moduleName is required

// This throws an exception
new ModuleConfig("my-module", "1.0.0", "author", "desc", Set.of(), Priority.LOW);
// IllegalArgumentException: subscribesTo must contain at least one EventType
```

The core also rejects modules with a name already used by another module.

## Examples

### Full module

```java
new ModuleConfig(
    "fraud-detection",
    "1.0.0",
    "capellegab",
    "Detects suspicious transactions in real-time",
    Set.of(EventType.TRANSACTION),
    Priority.CRITICAL
);
```

### Module listening to everything

```java
new ModuleConfig(
    "logger",
    "1.0.0",
    "capellegab",
    "Logs all events",
    Set.of(EventType.values()),
    Priority.LOW
);
```
