# Priority

`fr.capellegab.api.event.Priority`

Enum of priority levels for events and modules.

## Definition

```java
public enum Priority {
    LOW,
    MEDIUM,
    HIGH,
    CRITICAL
}
```

## Values

| Value | Description | Use case |
|-------|-------------|----------|
| `LOW` | Low priority | Logs, non-critical metrics |
| `MEDIUM` | Standard priority | Common events |
| `HIGH` | High priority | Financial transactions, sensitive actions |
| `CRITICAL` | Critical priority | Fraud alerts, security incidents |

## Dual usage

`Priority` is used in two places:

1. **On an `Event`** — indicates the event's urgency
2. **On a `ModuleConfig`** — indicates the module's processing priority (a `CRITICAL` module is executed before a `LOW` module)
