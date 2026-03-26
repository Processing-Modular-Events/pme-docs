# First module

This guide walks you through creating your first PME module.

## 1. Create the project

Create a new Maven project and add the `pme-sdk` dependency (see [Installation](installation.md)).

## 2. Implement `EventModule`

This is the only required interface. It defines:

- **`config()`** — your module's configuration
- **`onEvent()`** — the logic executed for each received event

```java
package com.example.mymodule;

import fr.capellegab.api.event.*;
import fr.capellegab.api.module.*;

import java.util.Set;

public class MyModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return new ModuleConfig(
            "my-module",                       // unique name
            Set.of(EventType.TRANSACTION),     // event types to listen to
            Priority.MEDIUM                    // module priority
        );
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        double amount = (double) event.payload().get("amount");

        if (amount > 5000) {
            context.log("High transaction detected: {} EUR", amount);
        }
    }
}
```

## 3. Configuration details

`ModuleConfig` defines your module's behavior:

| Field | Type | Description |
|-------|------|-------------|
| `moduleName` | `String` | Unique module identifier |
| `subscribesTo` | `Set<EventType>` | Event types the module receives |
| `priority` | `Priority` | Processing priority (`LOW`, `MEDIUM`, `HIGH`, `CRITICAL`) |

### Listen to multiple event types

```java
Set.of(EventType.TRANSACTION, EventType.USER_ACTION, EventType.SYSTEM_LOG)
```

### Listen to all types

```java
Set.of(EventType.values())
```

## 4. Using the context

The `EventContext` is provided by the core on each `onEvent()` call. It allows you to:

```java
@Override
public void onEvent(Event event, EventContext context) {
    // Log a message
    context.log("Processing event {}", event.uuid());

    // Re-publish an event (chaining)
    Event alert = new Event(
        UUID.randomUUID(),
        EventType.NOTIFICATION,
        "my-module",
        Map.of("alert", "threshold exceeded"),
        Instant.now(),
        Priority.HIGH
    );
    context.publish(alert);
}
```

## 5. Lifecycle (optional)

Implement `ModuleLifecycle` to react to startup, shutdown, or errors:

```java
public class MyModule implements EventModule, ModuleLifecycle {

    @Override
    public void onStart() {
        // Called when the module is loaded
    }

    @Override
    public void onStop() {
        // Called when the core shuts down
    }

    @Override
    public void onError(Event event, Exception exception) {
        // Called when onEvent() throws an exception
    }

    // ... config() and onEvent()
}
```

All three methods are `default` — only implement the ones you need.

## 6. Deploy

Compile your module as a jar and place it on the core's classpath. The `ModuleLoader` automatically detects it at startup.
