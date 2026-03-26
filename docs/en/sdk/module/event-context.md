# EventContext

`fr.capellegab.api.module.EventContext`

Interface representing the runtime context provided by the core to each module when processing an event.

## Definition

```java
public interface EventContext {
    void publish(Event event);
    void log(String message);
    void log(String message, Object... args);
}
```

## Methods

### `publish(Event event)`

Re-publishes an event on the platform. Useful for module chaining (one module generates an event that another module will process).

```java
Event alert = new Event(
    UUID.randomUUID(),
    EventType.NOTIFICATION,
    "fraud-module",
    Map.of("reason", "Suspicious amount"),
    Instant.now(),
    Priority.CRITICAL
);
context.publish(alert);
```

### `log(String message)`

Writes a message to the platform logs, prefixed by the module name.

```java
context.log("Module started");
// [fraud-module] Module started
```

### `log(String message, Object... args)`

Writes a formatted message with `{}` placeholders.

```java
context.log("Event {} processed in {}ms", event.uuid(), duration);
// [fraud-module] Event 3fa85f64-... processed in 12ms
```

## Important

!!! note "Provided by the core"
    You never implement `EventContext` — the core provides the implementation. Your module receives it as a parameter of `onEvent()`.
