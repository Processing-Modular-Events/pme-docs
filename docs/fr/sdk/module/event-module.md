# EventModule

`fr.capellegab.api.module.EventModule`

Interface principale du SDK. Tout module PME doit l'implementer.

## Definition

```java
public interface EventModule {
    ModuleConfig config();
    void onEvent(Event event, EventContext eventContext);
}
```

## Methodes

### `config()`

Retourne la configuration du module. Appelee une seule fois au chargement.

| Retour | Description |
|--------|-------------|
| [`ModuleConfig`](module-config.md) | Nom, types ecoutes, priorite |

### `onEvent(Event event, EventContext eventContext)`

Appelee a chaque evenement correspondant aux types declares dans `config().subscribesTo()`.

| Parametre | Type | Description |
|-----------|------|-------------|
| `event` | [`Event`](../event/event.md) | L'evenement a traiter |
| `eventContext` | [`EventContext`](event-context.md) | Contexte fourni par le core |

## Exemple

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
        context.log("Evenement {} traite depuis {}", event.uuid(), event.source());
    }
}
```

## Cycle de vie optionnel

Pour reagir au demarrage, a l'arret ou aux erreurs, implementez aussi [`ModuleLifecycle`](module-lifecycle.md) :

```java
public class AlertModule implements EventModule, ModuleLifecycle {
    // ...
}
```
