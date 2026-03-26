# First module

This guide walks you through creating your first PME module.

## 1. Use the template

Go to [**pme-module-template**](https://github.com/Processing-Modular-Events/pme-module-template) and click **"Use this template"**. Rename the repo to `pme-module-{your-name}`.

The template already contains the mandatory structure, `pom.xml` and `module.yml`.

## 2. Edit `module.yml`

Open `src/main/resources/module.yml` and fill in your module's info:

```yaml
name: my-module
version: 1.0.0
author: my-name
description: My module description
priority: MEDIUM
subscribes-to:
  - TRANSACTION
```

## 3. Edit the code

Rename the package and class to match your module, then implement your logic in `onEvent()`:

```java
public class MyModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
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

`ModuleConfigReader.load()` reads this file and builds the `ModuleConfig` automatically.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | `string` | Yes | Unique module identifier |
| `version` | `string` | Yes | Module version (e.g. `1.0.0`) |
| `author` | `string` | Yes | Author or organization |
| `description` | `string` | Yes | Short module description |
| `priority` | `string` | Yes | `LOW`, `MEDIUM`, `HIGH` or `CRITICAL` |
| `subscribes-to` | `list` | Yes | Event types to listen to |

!!! warning "Validation"
    A missing or empty field causes a startup error. The core refuses to load the module.

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
