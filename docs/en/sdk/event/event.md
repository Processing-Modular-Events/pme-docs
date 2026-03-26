# Event

`fr.capellegab.api.event.Event`

Immutable record representing an event in the PME platform.

## Definition

```java
public record Event(
    UUID uuid,
    EventType type,
    String source,
    Map<String, Object> payload,
    Instant timestamp,
    Priority priority
) {}
```

## Fields

| Field | Type | Description |
|-------|------|-------------|
| `uuid` | `UUID` | Unique event identifier |
| `type` | [`EventType`](event-type.md) | Event type |
| `source` | `String` | Service or system that emitted the event |
| `payload` | `Map<String, Object>` | Event data (key-value) |
| `timestamp` | `Instant` | Creation date and time |
| `priority` | [`Priority`](priority.md) | Priority level |

## Usage

### Create an event

```java
Event event = new Event(
    UUID.randomUUID(),
    EventType.TRANSACTION,
    "payment-service",
    Map.of(
        "userId", "usr_42",
        "amount", 250.00,
        "currency", "EUR"
    ),
    Instant.now(),
    Priority.MEDIUM
);
```

### Read fields

A record automatically generates accessors (without `get` prefix):

```java
UUID id = event.uuid();
String source = event.source();
double amount = (double) event.payload().get("amount");
```

## Immutability

`Event` is a Java record — its fields cannot be modified after creation. This guarantees that an event processed by multiple modules in parallel cannot be corrupted.

!!! warning "Mutable payload"
    The `Map<String, Object>` is mutable by reference. Avoid modifying the payload contents in your module — create a new event if needed.
