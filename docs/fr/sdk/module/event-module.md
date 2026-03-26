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
| [`ModuleConfig`](module-config.md) | Nom, version, auteur, types ecoutes, priorite |

### `onEvent(Event event, EventContext eventContext)`

Appelee a chaque evenement correspondant aux types declares dans `config().subscribesTo()`.

| Parametre | Type | Description |
|-----------|------|-------------|
| `event` | [`Event`](../event/event.md) | L'evenement a traiter |
| `eventContext` | [`EventContext`](event-context.md) | Contexte fourni par le core |

## Exemple simple

Un module qui log les transactions de plus de 1 000 EUR :

```java
public class HighValueModule implements EventModule {

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        double amount = ((Number) event.payload().get("amount")).doubleValue();

        if (amount > 1000) {
            context.log("Transaction elevee : {} EUR depuis {}", amount, event.source());
        }
    }
}
```

## Combiner avec ModuleLifecycle

En production, un module a souvent besoin de ressources externes (connexion BDD, cache, client HTTP). `ModuleLifecycle` permet de les initialiser proprement au demarrage et de les liberer a l'arret.

### Exemple : module de notification Slack

Ce module envoie une alerte Slack quand une transaction depasse un seuil. Il initialise le client HTTP au demarrage et le ferme a l'arret.

```yaml
# module.yml
name: slack-notifier
version: 1.0.0
author: capellegab
description: Envoie des alertes Slack sur les transactions elevees
priority: HIGH
subscribes-to:
  - TRANSACTION
```

```java
public class SlackNotifierModule implements EventModule, ModuleLifecycle {

    private HttpClient httpClient;
    private static final double ALERT_THRESHOLD = 5000.0;

    // --- EventModule ---

    @Override
    public ModuleConfig config() {
        return ModuleConfigReader.load();
    }

    @Override
    public void onEvent(Event event, EventContext context) {
        double amount = ((Number) event.payload().get("amount")).doubleValue();

        if (amount > ALERT_THRESHOLD) {
            String message = String.format(
                "Transaction suspecte : %.2f EUR (user: %s)",
                amount, event.payload().get("userId")
            );
            sendSlackAlert(message);
            context.log("Alerte Slack envoyee pour event {}", event.uuid());
        }
    }

    // --- ModuleLifecycle ---

    @Override
    public void onStart() {
        httpClient = HttpClient.newHttpClient();
        // Le module est pret
    }

    @Override
    public void onStop() {
        httpClient.close();
        // Ressources liberees proprement
    }

    @Override
    public void onError(Event event, Exception exception) {
        // Log l'erreur sans crasher les autres modules
        System.err.println("Echec notification pour " + event.uuid() + " : " + exception.getMessage());
    }

    // --- Logique interne ---

    private void sendSlackAlert(String message) {
        // Appel HTTP vers le webhook Slack
    }
}
```

### Ce qui se passe au runtime

```
1. Demarrage du core
   -> onStart() est appele
   -> Le HttpClient est initialise

2. Un evenement TRANSACTION arrive
   -> onEvent() est appele
   -> Si montant > 5000, envoie une alerte Slack

3. Si onEvent() leve une exception
   -> onError() est appele avec l'event et l'exception
   -> Les autres modules continuent de fonctionner normalement

4. Arret du core
   -> onStop() est appele
   -> Le HttpClient est ferme
```

### Quand utiliser ModuleLifecycle ?

| Situation | `onStart()` | `onStop()` | `onError()` |
|-----------|-------------|------------|-------------|
| Connexion a une BDD | Ouvrir la connexion | Fermer la connexion | Logger et retry |
| Client HTTP | Creer le client | Fermer le client | Logger l'erreur |
| Cache en memoire | Charger le cache | Vider le cache | — |
| File d'attente interne | Creer la queue | Flush et fermer | Re-enqueuer l'event |

!!! tip "Optionnel"
    Les trois methodes sont `default`. Implementez uniquement celles dont vous avez besoin. Un module simple sans ressource externe n'a pas besoin de `ModuleLifecycle`.
