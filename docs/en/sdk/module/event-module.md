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
| [`ModuleConfig`](module-config.md) | Name, version, author, subscribed types, priority |

### `onEvent(Event event, EventContext eventContext)`

Called for each event matching the types declared in `config().subscribesTo()`.

| Parameter | Type | Description |
|-----------|------|-------------|
| `event` | [`Event`](../event/event.md) | The event to process |
| `eventContext` | [`EventContext`](event-context.md) | Context provided by the core |

## Simple example

A module that logs transactions above 1,000 EUR:

```java
public class HighValueModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        double amount = ((Number) event.payload().get("amount")).doubleValue();

        if (amount > 1000) {
            context.log("High transaction: {} EUR from {}", amount, event.source());
        }
    }
}
```

## Combining with ModuleLifecycle

In production, a module often needs external resources (DB connection, cache, HTTP client). `ModuleLifecycle` lets you initialize them cleanly at startup and release them at shutdown.

### Example: Slack notification module

This module sends a Slack alert when a transaction exceeds a threshold. It initializes the HTTP client at startup and closes it at shutdown.

```yaml
# module.yml
name: slack-notifier
version: 1.0.0
author: capellegab
description: Sends Slack alerts on high transactions
priority: HIGH
subscribes-to:
  - TRANSACTION
```

```java
public class SlackNotifierModule implements EventModule, ModuleLifecycle {

    private HttpClient httpClient;
    private static final double ALERT_THRESHOLD = 5000.0;

    // --- EventModule ---

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        double amount = ((Number) event.payload().get("amount")).doubleValue();

        if (amount > ALERT_THRESHOLD) {
            String message = String.format(
                "Suspicious transaction: %.2f EUR (user: %s)",
                amount, event.payload().get("userId")
            );
            sendSlackAlert(message);
            context.log("Slack alert sent for event {}", event.uuid());
        }
    }

    // --- ModuleLifecycle ---

    @Override
    public void onStart() {
        httpClient = HttpClient.newHttpClient();
        // Module is ready
    }

    @Override
    public void onStop() {
        httpClient.close();
        // Resources released cleanly
    }

    @Override
    public void onError(Event event, Exception exception) {
        // Log the error without crashing other modules
        System.err.println("Notification failed for " + event.uuid() + ": " + exception.getMessage());
    }

    // --- Internal logic ---

    private void sendSlackAlert(String message) {
        // HTTP call to Slack webhook
    }
}
```

### What happens at runtime

```
1. Core starts
   -> onStart() is called
   -> HttpClient is initialized

2. A TRANSACTION event arrives
   -> onEvent() is called
   -> If amount > 5000, sends a Slack alert

3. If onEvent() throws an exception
   -> onError() is called with the event and exception
   -> Other modules keep running normally

4. Core shuts down
   -> onStop() is called
   -> HttpClient is closed
```

### When to use ModuleLifecycle?

| Situation | `onStart()` | `onStop()` | `onError()` |
|-----------|-------------|------------|-------------|
| Database connection | Open connection | Close connection | Log and retry |
| HTTP client | Create client | Close client | Log the error |
| In-memory cache | Load cache | Clear cache | — |
| Internal queue | Create queue | Flush and close | Re-enqueue event |

!!! tip "Optional"
    All three methods are `default`. Only implement the ones you need. A simple module without external resources doesn't need `ModuleLifecycle`.
