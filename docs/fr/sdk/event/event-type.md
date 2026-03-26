# EventType

`fr.capellegab.api.event.EventType`

Enum des types d'evenements supportes par la plateforme.

## Definition

```java
public enum EventType {
    TRANSACTION,
    USER_ACTION,
    SYSTEM_LOG,
    NOTIFICATION
}
```

## Valeurs

| Valeur | Description | Exemple |
|--------|-------------|---------|
| `TRANSACTION` | Transaction financiere | Paiement, virement, remboursement |
| `USER_ACTION` | Action d'un utilisateur | Clic, connexion, mise a jour de profil |
| `SYSTEM_LOG` | Log systeme | Erreur serveur, demarrage service, healthcheck |
| `NOTIFICATION` | Notification generee | Alerte, email, push notification |

## Utilisation dans ModuleConfig

Un module declare les types qu'il ecoute via `subscribesTo` :

```java
// Un seul type
Set.of(EventType.TRANSACTION)

// Plusieurs types
Set.of(EventType.TRANSACTION, EventType.USER_ACTION)

// Tous les types
Set.of(EventType.values())
```
