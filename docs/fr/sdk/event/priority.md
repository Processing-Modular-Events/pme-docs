# Priority

`fr.capellegab.api.event.Priority`

Enum des niveaux de priorite pour les evenements et les modules.

## Definition

```java
public enum Priority {
    LOW,
    MEDIUM,
    HIGH,
    CRITICAL
}
```

## Valeurs

| Valeur | Description | Cas d'usage |
|--------|-------------|-------------|
| `LOW` | Priorite basse | Logs, metriques non-critiques |
| `MEDIUM` | Priorite standard | Evenements courants |
| `HIGH` | Priorite haute | Transactions financieres, actions sensibles |
| `CRITICAL` | Priorite critique | Alertes de fraude, incidents de securite |

## Double usage

`Priority` est utilise a deux endroits :

1. **Sur un `Event`** — indique l'urgence de l'evenement
2. **Sur un `ModuleConfig`** — indique la priorite de traitement du module (un module `CRITICAL` est execute avant un module `LOW`)
