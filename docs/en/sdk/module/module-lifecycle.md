# ModuleLifecycle

`fr.capellegab.api.module.ModuleLifecycle`

Optional interface to react to module lifecycle events.

## Definition

```java
public interface ModuleLifecycle {
    default void onStart() {}
    default void onStop() {}
    default void onError(Event event, Exception exception) {}
}
```

## Methods

All methods are `default` — only implement the ones you need.

### `onStart()`

Called once when the module is loaded by the core. Use it to initialize resources.

```java
@Override
public void onStart() {
    // Initialize a connection, load a cache, etc.
}
```

### `onStop()`

Called when the core shuts down. Use it to release resources.

```java
@Override
public void onStop() {
    // Close connections, flush buffers, etc.
}
```

### `onError(Event event, Exception exception)`

Called when `onEvent()` throws an exception. Allows logging, alerting, or deciding on a retry strategy.

| Parameter | Type | Description |
|-----------|------|-------------|
| `event` | [`Event`](../event/event.md) | The event that caused the error |
| `exception` | `Exception` | The thrown exception |

```java
@Override
public void onError(Event event, Exception exception) {
    System.err.println("Error on " + event.uuid() + ": " + exception.getMessage());
}
```

## Full example

```java
public class MyModule implements EventModule, ModuleLifecycle {

    private Connection connection;

    @Override
    public ModuleConfig config() {
        return new ModuleConfig("my-module", Set.of(EventType.TRANSACTION), Priority.MEDIUM);
    }

    @Override
    public void onStart() {
        connection = Database.connect();
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        connection.save(event);
    }

    @Override
    public void onError(Event event, Exception exception) {
        context.log("Failed to save event {}: {}", event.uuid(), exception.getMessage());
    }

    @Override
    public void onStop() {
        connection.close();
    }
}
```
