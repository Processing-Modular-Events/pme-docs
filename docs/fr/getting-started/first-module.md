# Premier module

Ce guide vous accompagne dans la creation de votre premier module PME.

## 1. Creer le projet

Creez un nouveau projet Maven et ajoutez la dependance `pme-sdk` (voir [Installation](installation.md)).

## 2. Implementer `EventModule`

C'est la seule interface obligatoire. Elle definit :

- **`config()`** — la configuration de votre module
- **`onEvent()`** — la logique executee a chaque evenement recu

```java
package com.example.mymodule;

import fr.capellegab.api.event.*;
import fr.capellegab.api.module.*;

import java.util.Set;

public class MyModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return new ModuleConfig(
            "my-module",                       // nom unique
            Set.of(EventType.TRANSACTION),     // types d'evenements ecoutes
            Priority.MEDIUM                    // priorite du module
        );
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        double amount = (double) event.payload().get("amount");

        if (amount > 5000) {
            context.log("Transaction elevee detectee : {} EUR", amount);
        }
    }
}
```

## 3. Configuration detaillee

Le `ModuleConfig` definit le comportement de votre module :

| Champ | Type | Description |
|-------|------|-------------|
| `moduleName` | `String` | Identifiant unique du module |
| `subscribesTo` | `Set<EventType>` | Types d'evenements que le module recoit |
| `priority` | `Priority` | Priorite de traitement (`LOW`, `MEDIUM`, `HIGH`, `CRITICAL`) |

### Ecouter plusieurs types d'evenements

```java
Set.of(EventType.TRANSACTION, EventType.USER_ACTION, EventType.SYSTEM_LOG)
```

### Ecouter tous les types

```java
Set.of(EventType.values())
```

## 4. Utiliser le contexte

Le `EventContext` est fourni par le core a chaque appel de `onEvent()`. Il permet de :

```java
@Override
public void onEvent(Event event, EventContext context) {
    // Logger un message
    context.log("Traitement de l'evenement {}", event.uuid());

    // Re-publier un evenement (chaining)
    Event alert = new Event(
        UUID.randomUUID(),
        EventType.NOTIFICATION,
        "my-module",
        Map.of("alert", "seuil depasse"),
        Instant.now(),
        Priority.HIGH
    );
    context.publish(alert);
}
```

## 5. Cycle de vie (optionnel)

Implementez `ModuleLifecycle` pour reagir au demarrage, a l'arret, ou aux erreurs :

```java
public class MyModule implements EventModule, ModuleLifecycle {

    @Override
    public void onStart() {
        // Appele au chargement du module
    }

    @Override
    public void onStop() {
        // Appele a l'arret du core
    }

    @Override
    public void onError(Event event, Exception exception) {
        // Appele quand onEvent() leve une exception
    }

    // ... config() et onEvent()
}
```

Les trois methodes sont `default` — vous n'implementez que celles dont vous avez besoin.

## 6. Deployer

Compilez votre module en jar et placez-le sur le classpath du core. Le `ModuleLoader` le detecte automatiquement au demarrage.
