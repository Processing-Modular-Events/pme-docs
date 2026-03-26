# Premier module

Ce guide vous accompagne dans la creation de votre premier module PME.

## 1. Utiliser le template

Rendez-vous sur [**pme-module-template**](https://github.com/Processing-Modular-Events/pme-module-template) et cliquez sur **"Use this template"**. Renommez le repo en `pme-module-{votre-nom}`.

Le template contient deja toute la structure obligatoire, le `pom.xml` et le `module.yml`.

## 2. Modifier `module.yml`

Ouvrez `src/main/resources/module.yml` et renseignez les infos de votre module :

```yaml
name: my-module
version: 1.0.0
author: mon-nom
description: Description de mon module
priority: MEDIUM
subscribes-to:
  - TRANSACTION
```

## 3. Modifier le code

Renommez le package et la classe par le nom de votre module, puis implementez votre logique dans `onEvent()` :

```java
public class MyModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
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

`ModuleConfigReader.load()` lit ce fichier et construit le `ModuleConfig` automatiquement.

| Champ | Type | Obligatoire | Description |
|-------|------|-------------|-------------|
| `name` | `string` | Oui | Identifiant unique du module |
| `version` | `string` | Oui | Version du module (ex: `1.0.0`) |
| `author` | `string` | Oui | Auteur ou organisation |
| `description` | `string` | Oui | Description courte du module |
| `priority` | `string` | Oui | `LOW`, `MEDIUM`, `HIGH` ou `CRITICAL` |
| `subscribes-to` | `list` | Oui | Types d'evenements ecoutes |

!!! warning "Validation"
    Un champ manquant ou vide provoque une erreur au demarrage. Le core refuse de charger le module.

### Ecouter plusieurs types d'evenements

```java
Set.of(EventType.TRANSACTION, EventType.USER_ACTION, EventType.SYSTEM_LOG)
```

### Ecouter tous les types

```java
Set.of(EventType.values())
```

## 4. Champs du `module.yml`

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
