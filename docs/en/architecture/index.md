# Architecture

## Overview

```mermaid
graph TB
    subgraph Sources
        S1[Service A]
        S2[Service B]
        S3[Webhook]
    end

    subgraph Core
        API[REST API<br/>POST /api/events]
        ES[(Elasticsearch)]
        K[Apache Kafka<br/>Topic: events.ingested]
        ML[Module Loader]
        MD[Module Dispatcher]
    end

    subgraph Modules
        M1[Fraud Detection]
        M2[Analytics]
        M3[Metrics]
        M4[Community module]
    end

    subgraph Visualization
        KB[Kibana]
    end

    S1 & S2 & S3 --> API
    API --> ES
    API --> K
    K --> MD
    ML --> MD
    MD --> M1 & M2 & M3 & M4
    ES --> KB
```

## Components

### pme-sdk

The SDK is a lightweight jar with no Spring dependency. It only contains the interfaces and models that module developers need:

- `EventModule` — main contract
- `EventContext` — context provided by the core
- `Event`, `EventType`, `Priority` — data models

### pme-core

The platform engine. It handles:

- **Ingestion** — REST API to receive events
- **Persistence** — Storage in Elasticsearch
- **Publishing** — Sending to Kafka
- **Module Loader** — Module discovery and loading
- **Module Dispatcher** — Event routing to the right modules

### Modules

Each module is an independent jar that implements `EventModule`. The core loads them at startup and dispatches events matching their `subscribesTo`.

## Event flow

1. **Reception** — `POST /api/events` receives a JSON event
2. **Persistence** — The event is indexed in Elasticsearch
3. **Publishing** — The event is published on the Kafka topic `events.ingested`
4. **Dispatch** — The `ModuleDispatcher` routes it to modules whose `EventType` matches
5. **Processing** — Each module executes its logic in `onEvent()`

## Tech stack

| Component | Technology |
|-----------|------------|
| Language | Java 25 |
| Framework | Spring Boot 3.x |
| Message Broker | Apache Kafka |
| Indexing | Elasticsearch |
| Visualization | Kibana |
| Infrastructure | Docker Compose |
