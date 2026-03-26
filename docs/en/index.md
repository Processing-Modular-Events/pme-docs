# Processing Modular Events

**PME** is a modular event processing platform. An event-driven engine that ingests events in real-time and distributes them to independent modules.

## Concept

The system works as a pipeline:

```mermaid
graph LR
    A[Source] -->|POST /api/events| B[PME Core]
    B -->|Kafka| C[Fraud Module]
    B -->|Kafka| D[Analytics Module]
    B -->|Kafka| E[Metrics Module]
    B --> F[Elasticsearch]
```

1. An event arrives via the **core** REST API
2. The core persists it in **Elasticsearch** and publishes it on **Kafka**
3. Subscribed **modules** receive and process it independently

## Create a module

Any developer can create a module by importing the SDK:

```xml
<dependency>
    <groupId>fr.capellegab</groupId>
    <artifactId>pme-sdk</artifactId>
    <version>0.0.1</version>
</dependency>
```

```java
public class MyModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return new ModuleConfig(
            "my-module",
            Set.of(EventType.TRANSACTION),
            Priority.MEDIUM
        );
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        context.log("Event received: {}", event.uuid());
    }
}
```

## Quick links

- [Installation](getting-started/installation.md)
- [First module](getting-started/first-module.md)
- [SDK Reference](sdk/index.md)
- [Architecture](architecture/index.md)
