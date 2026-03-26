# SDK Reference

Le SDK PME (`pme-sdk`) est le kit de developpement pour creer des modules. Il ne contient aucune dependance Spring — c'est un jar leger composee uniquement d'interfaces et de records.

## Structure

```
fr.capellegab.api
├── PmeSdk.java              Point d'entree du SDK
├── event/                   Modeles d'evenements
│   ├── Event.java           Record immutable
│   ├── EventType.java       Types d'evenements
│   └── Priority.java        Niveaux de priorite
└── module/                  Contrats pour les modules
    ├── EventModule.java     Interface principale
    ├── EventContext.java    Contexte runtime
    ├── ModuleConfig.java    Configuration du module
    └── ModuleLifecycle.java Hooks de cycle de vie
```

## Packages

### `fr.capellegab.api.event`

Les modeles de donnees. Ce sont des objets immutables (records et enums) qui decrivent un evenement.

| Classe | Type | Description |
|--------|------|-------------|
| [`Event`](event/event.md) | `record` | Un evenement avec son id, type, source, payload, timestamp et priorite |
| [`EventType`](event/event-type.md) | `enum` | Les types d'evenements supportes |
| [`Priority`](event/priority.md) | `enum` | Les niveaux de priorite |

### `fr.capellegab.api.module`

Les contrats que votre module doit implementer pour s'integrer a la plateforme.

| Classe | Type | Description |
|--------|------|-------------|
| [`EventModule`](module/event-module.md) | `interface` | Contrat principal — obligatoire |
| [`EventContext`](module/event-context.md) | `interface` | Contexte fourni par le core a chaque evenement |
| [`ModuleConfig`](module/module-config.md) | `record` | Configuration declarative du module |
| [`ModuleLifecycle`](module/module-lifecycle.md) | `interface` | Hooks optionnels de cycle de vie |
