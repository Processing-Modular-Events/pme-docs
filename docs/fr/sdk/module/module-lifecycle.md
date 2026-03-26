# ModuleLifecycle

`fr.capellegab.api.module.ModuleLifecycle`

Interface optionnelle pour reagir aux evenements de cycle de vie du module.

## Definition

```java
public interface ModuleLifecycle {
    default void onStart() {}
    default void onStop() {}
    default void onError(Event event, Exception exception) {}
}
```

## Methodes

Toutes les methodes sont `default` — vous n'implementez que celles dont vous avez besoin.

### `onStart()`

Appelee une fois au chargement du module par le core. Utilisez-la pour initialiser des ressources.

```java
@Override
public void onStart() {
    // Initialiser une connexion, charger un cache, etc.
}
```

### `onStop()`

Appelee a l'arret du core. Utilisez-la pour liberer des ressources.

```java
@Override
public void onStop() {
    // Fermer des connexions, flush des buffers, etc.
}
```

### `onError(Event event, Exception exception)`

Appelee quand `onEvent()` leve une exception. Permet de logger, alerter, ou decider d'une strategie de retry.

| Parametre | Type | Description |
|-----------|------|-------------|
| `event` | [`Event`](../event/event.md) | L'evenement qui a provoque l'erreur |
| `exception` | `Exception` | L'exception levee |

```java
@Override
public void onError(Event event, Exception exception) {
    System.err.println("Erreur sur " + event.uuid() + " : " + exception.getMessage());
}
```

## Exemple complet

```java
public class MonModule implements EventModule, ModuleLifecycle {

    private Connection connection;

    @Override
    public ModuleConfig config() {
        return new ModuleConfig("mon-module", Set.of(EventType.TRANSACTION), Priority.MEDIUM);
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
        context.log("Echec sauvegarde event {} : {}", event.uuid(), exception.getMessage());
    }

    @Override
    public void onStop() {
        connection.close();
    }
}
```
