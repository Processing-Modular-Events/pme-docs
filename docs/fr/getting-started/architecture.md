# Architecture d'un module

Chaque module PME doit respecter une architecture obligatoire. Le [**template**](https://github.com/Processing-Modular-Events/pme-module-template) fournit deja cette structure — utilisez-le comme point de depart.

Un module non conforme sera refuse par le core au chargement.

## Structure

```
pme-module-{name}/
├── module.yml                                    <- Configuration (obligatoire)
├── pom.xml
└── src/main/java/{votre}/{package}/{name}/
    ├── {Name}Module.java                         <- Point d'entree (implements EventModule)
    ├── analyzer/                                 <- Logique metier
    │   └── ...
    └── model/                                    <- Modeles de donnees
        └── ...
```

Le package Java est **libre** — utilisez le nom de domaine de votre organisation (ex: `fr.capellegab.fraud`, `com.grafikart.analytics`, `io.steeve.metrics`).

## Regles

### 1. `module.yml` (inclus dans le template)

Fichier de configuration dans `src/main/resources/`. Modifiez les valeurs avec les infos de votre module. Tous les champs sont requis.

```yaml
name: mon-module
version: 1.0.0
author: mon-nom
description: Ce que fait mon module
priority: MEDIUM
subscribes-to:
  - TRANSACTION
```

### 2. `{Name}Module.java` (inclus dans le template)

Classe a la racine du package qui implemente `EventModule`. Renommez `ExampleModule.java` et implementez votre logique.

```java
public class MonModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        // ...
    }
}
```

### 3. `analyzer/` (inclus dans le template)

Package contenant toute la logique metier du module. Ajoutez vos classes de traitement ici.

### 4. `model/` (inclus dans le template)

Package contenant les modeles de donnees propres au module (records, enums, classes).

## Validation au chargement

Le core valide automatiquement :

- `module.yml` present et complet
- Aucun champ vide ou null
- Nom du module unique (pas de doublon)

Si la validation echoue, le module est rejete et un message d'erreur s'affiche dans les logs.

## Exemple : module fraud

```
pme-module-fraud/
├── module.yml
├── pom.xml
└── src/main/java/fr/capellegab/fraud/
    ├── FraudModule.java
    ├── analyzer/
    │   └── FraudAnalyzer.java
    └── model/
        └── FraudAlert.java
```
