# ModuleConfig

`fr.capellegab.api.module.ModuleConfig`

Record that defines the declarative configuration of a module.

## Definition

```java
public record ModuleConfig(
    String moduleName,
    Set<EventType> subscribesTo,
    Priority priority
) {}
```

## Fields

| Field | Type | Description |
|-------|------|-------------|
| `moduleName` | `String` | Unique module identifier. Must be unique across the platform. |
| `subscribesTo` | `Set<EventType>` | Event types the module receives. The core only dispatches matching events. |
| `priority` | [`Priority`](../event/priority.md) | Processing priority. `CRITICAL` modules are executed before `LOW` modules. |

## Examples

### Module listening to a single type

```java
new ModuleConfig(
    "payment-validator",
    Set.of(EventType.TRANSACTION),
    Priority.HIGH
);
```

### Module listening to everything

```java
new ModuleConfig(
    "logger",
    Set.of(EventType.values()),
    Priority.LOW
);
```

### Critical module

```java
new ModuleConfig(
    "fraud-detection",
    Set.of(EventType.TRANSACTION),
    Priority.CRITICAL
);
```
