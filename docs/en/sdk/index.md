# SDK Reference

The PME SDK (`pme-sdk`) is the development kit for creating modules. It has no Spring dependency — it's a lightweight jar composed only of interfaces and records.

## Structure

```
fr.capellegab.api
├── PmeSdk.java              SDK entry point
├── event/                   Event models
│   ├── Event.java           Immutable record
│   ├── EventType.java       Event types
│   └── Priority.java        Priority levels
└── module/                  Module contracts
    ├── EventModule.java     Main interface
    ├── EventContext.java    Runtime context
    ├── ModuleConfig.java    Module configuration
    └── ModuleLifecycle.java Lifecycle hooks
```

## Packages

### `fr.capellegab.api.event`

Data models. These are immutable objects (records and enums) that describe an event.

| Class | Type | Description |
|-------|------|-------------|
| [`Event`](event/event.md) | `record` | An event with its id, type, source, payload, timestamp and priority |
| [`EventType`](event/event-type.md) | `enum` | Supported event types |
| [`Priority`](event/priority.md) | `enum` | Priority levels |

### `fr.capellegab.api.module`

Contracts your module must implement to integrate with the platform.

| Class | Type | Description |
|-------|------|-------------|
| [`EventModule`](module/event-module.md) | `interface` | Main contract — required |
| [`EventContext`](module/event-context.md) | `interface` | Context provided by the core for each event |
| [`ModuleConfig`](module/module-config.md) | `record` | Declarative module configuration |
| [`ModuleLifecycle`](module/module-lifecycle.md) | `interface` | Optional lifecycle hooks |
