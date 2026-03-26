# EventContext

`fr.capellegab.api.module.EventContext`

Interface representant le contexte runtime fourni par le core a chaque module lors du traitement d'un evenement.

## Definition

```java
public interface EventContext {
    void publish(Event event);
    void log(String message);
    void log(String message, Object... args);
}
```

## Methodes

### `publish(Event event)`

Re-publie un evenement sur la plateforme. Utile pour le chaining de modules (un module genere un evenement qu'un autre module traitera).

```java
Event alert = new Event(
    UUID.randomUUID(),
    EventType.NOTIFICATION,
    "fraud-module",
    Map.of("reason", "Montant suspect"),
    Instant.now(),
    Priority.CRITICAL
);
context.publish(alert);
```

### `log(String message)`

Ecrit un message dans les logs de la plateforme, prefixe par le nom du module.

```java
context.log("Module demarre");
// [fraud-module] Module demarre
```

### `log(String message, Object... args)`

Ecrit un message formate avec des placeholders `{}`.

```java
context.log("Event {} traite en {}ms", event.uuid(), duration);
// [fraud-module] Event 3fa85f64-... traite en 12ms
```

## Important

!!! note "Fourni par le core"
    Vous n'implementez jamais `EventContext` — c'est le core qui fournit l'implementation. Votre module le recoit en parametre de `onEvent()`.
