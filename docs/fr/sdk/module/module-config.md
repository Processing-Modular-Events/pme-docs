# ModuleConfig

`fr.capellegab.api.module.ModuleConfig`

Record qui definit la configuration declarative d'un module.

## Definition

```java
public record ModuleConfig(
    String moduleName,
    Set<EventType> subscribesTo,
    Priority priority
) {}
```

## Champs

| Champ | Type | Description |
|-------|------|-------------|
| `moduleName` | `String` | Identifiant unique du module. Doit etre unique sur la plateforme. |
| `subscribesTo` | `Set<EventType>` | Types d'evenements que le module recoit. Le core ne dispatch que les evenements correspondants. |
| `priority` | [`Priority`](../event/priority.md) | Priorite de traitement. Les modules `CRITICAL` sont executes avant les modules `LOW`. |

## Exemples

### Module qui ecoute un seul type

```java
new ModuleConfig(
    "payment-validator",
    Set.of(EventType.TRANSACTION),
    Priority.HIGH
);
```

### Module qui ecoute tout

```java
new ModuleConfig(
    "logger",
    Set.of(EventType.values()),
    Priority.LOW
);
```

### Module critique

```java
new ModuleConfig(
    "fraud-detection",
    Set.of(EventType.TRANSACTION),
    Priority.CRITICAL
);
```
