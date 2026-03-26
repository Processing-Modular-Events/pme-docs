# Event

`fr.capellegab.api.event.Event`

Record immutable representant un evenement dans la plateforme PME.

## Definition

```java
public record Event(
    UUID uuid,
    EventType type,
    String source,
    Map<String, Object> payload,
    Instant timestamp,
    Priority priority
) {}
```

## Champs

| Champ | Type | Description |
|-------|------|-------------|
| `uuid` | `UUID` | Identifiant unique de l'evenement |
| `type` | [`EventType`](event-type.md) | Type de l'evenement |
| `source` | `String` | Service ou systeme qui a emis l'evenement |
| `payload` | `Map<String, Object>` | Donnees de l'evenement (cle-valeur) |
| `timestamp` | `Instant` | Date et heure de creation |
| `priority` | [`Priority`](priority.md) | Niveau de priorite |

## Utilisation

### Creer un evenement

```java
Event event = new Event(
    UUID.randomUUID(),
    EventType.TRANSACTION,
    "payment-service",
    Map.of(
        "userId", "usr_42",
        "amount", 250.00,
        "currency", "EUR"
    ),
    Instant.now(),
    Priority.MEDIUM
);
```

### Lire les champs

Un record genere automatiquement les accesseurs (sans prefixe `get`) :

```java
UUID id = event.uuid();
String source = event.source();
double amount = (double) event.payload().get("amount");
```

## Immutabilite

`Event` est un record Java — ses champs ne peuvent pas etre modifies apres creation. Cela garantit qu'un evenement traite par plusieurs modules en parallele ne peut pas etre corrompu.

!!! warning "Payload mutable"
    Le `Map<String, Object>` est mutable par reference. Evitez de modifier le contenu du payload dans votre module — creez un nouvel evenement si necessaire.
