# EventType

`fr.capellegab.api.event.EventType`

Enum of event types supported by the platform.

## Definition

```java
public enum EventType {
    TRANSACTION,
    USER_ACTION,
    SYSTEM_LOG,
    NOTIFICATION
}
```

## Values

| Value | Description | Example |
|-------|-------------|---------|
| `TRANSACTION` | Financial transaction | Payment, transfer, refund |
| `USER_ACTION` | User action | Click, login, profile update |
| `SYSTEM_LOG` | System log | Server error, service startup, healthcheck |
| `NOTIFICATION` | Generated notification | Alert, email, push notification |

## Usage in ModuleConfig

A module declares which types it listens to via `subscribesTo`:

```java
// Single type
Set.of(EventType.TRANSACTION)

// Multiple types
Set.of(EventType.TRANSACTION, EventType.USER_ACTION)

// All types
Set.of(EventType.values())
```
