# EventModule

`fr.capellegab.api.module.EventModule`

Main SDK interface. Every PME module must implement it.

## Definition

```java
public interface EventModule {
    ModuleConfig config();
    void onEvent(Event event, EventContext eventContext);
}
```

## Methods

### `config()`

Returns the module configuration. Called once at loading time.

| Return | Description |
|--------|-------------|
| [`ModuleConfig`](module-config.md) | Name, subscribed types, priority |

### `onEvent(Event event, EventContext eventContext)`

Called for each event matching the types declared in `config().subscribesTo()`.

| Parameter | Type | Description |
|-----------|------|-------------|
| `event` | [`Event`](../event/event.md) | The event to process |
| `eventContext` | [`EventContext`](event-context.md) | Context provided by the core |

## Example

```java
public class AlertModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return new ModuleConfig(
            "alert-module",
            Set.of(EventType.TRANSACTION, EventType.USER_ACTION),
            Priority.HIGH
        );
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        Map<String, Object> payload = event.payload();
        context.log("Event {} processed from {}", event.uuid(), event.source());
    }
}
```

## Optional lifecycle

To react to startup, shutdown or errors, also implement [`ModuleLifecycle`](module-lifecycle.md):

```java
public class AlertModule implements EventModule, ModuleLifecycle {
    // ...
}
```
