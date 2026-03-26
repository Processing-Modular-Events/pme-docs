# ModuleConfig

`fr.capellegab.api.module.ModuleConfig`

Record qui definit la configuration declarative d'un module. Tous les champs sont **obligatoires** — un module avec une config incomplete sera refuse par le core au chargement.

## Definition

```java
public record ModuleConfig(
    String moduleName,
    String version,
    String author,
    String description,
    Set<EventType> subscribesTo,
    Priority priority
) {}
```

## Champs

| Champ | Type | Obligatoire | Description |
|-------|------|-------------|-------------|
| `moduleName` | `String` | Oui | Identifiant unique du module. Deux modules ne peuvent pas avoir le meme nom. |
| `version` | `String` | Oui | Version du module (ex: `"1.0.0"`). |
| `author` | `String` | Oui | Auteur ou organisation. |
| `description` | `String` | Oui | Description courte du module. |
| `subscribesTo` | `Set<EventType>` | Oui | Types d'evenements que le module recoit. Doit contenir au moins un type. |
| `priority` | [`Priority`](../event/priority.md) | Oui | Priorite de traitement. |

## Validation

Le record valide les champs a la construction. Si un champ est manquant ou vide, une `IllegalArgumentException` est levee :

```java
// Ceci leve une exception
new ModuleConfig(null, "1.0.0", "author", "desc", Set.of(EventType.TRANSACTION), Priority.LOW);
// IllegalArgumentException: moduleName is required

// Ceci leve une exception
new ModuleConfig("my-module", "1.0.0", "author", "desc", Set.of(), Priority.LOW);
// IllegalArgumentException: subscribesTo must contain at least one EventType
```

Le core refuse egalement les modules avec un nom deja utilise par un autre module.

## Exemples

### Module complet

```java
new ModuleConfig(
    "fraud-detection",
    "1.0.0",
    "capellegab",
    "Detecte les transactions suspectes en temps reel",
    Set.of(EventType.TRANSACTION),
    Priority.CRITICAL
);
```

### Module qui ecoute tout

```java
new ModuleConfig(
    "logger",
    "1.0.0",
    "capellegab",
    "Log tous les evenements",
    Set.of(EventType.values()),
    Priority.LOW
);
```
