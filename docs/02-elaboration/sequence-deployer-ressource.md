# Phase 2 – Élaboration : séquence détaillée « Déployer une ressource Cloud »

## Scénario nominal (environnement Production)

1. L'Ingénieur Cloud crée la demande (environnement, type, configuration, justification).
2. Le système enregistre la demande (état *En attente de validation*).
3. Le Responsable Cloud valide.
4. Le système lance le déploiement (demande *En cours*, ressource *En déploiement*).
5. Le déploiement réussit : ressource *Active*, demande *Terminée*.

## Scénarios alternatifs

- **Refus** : demande *Refusée*, aucune ressource créée.
- **Échec** : demande *Échouée*, événement enregistré dans l'historique.
- **Dev / Test** : pas de validation, passage direct au déploiement.

## Diagramme

```mermaid
sequenceDiagram
    actor IC as Ingénieur Cloud
    actor RC as Responsable Cloud
    participant UI as Interface
    participant GD as GestionnaireDemandes
    participant GR as GestionnaireRessources
    participant FC as FournisseurCloud
    participant H as Historique

    IC->>UI: saisir demande (environnement, type, config, justification)
    UI->>GD: creerDemande(...)
    GD->>GD: vérifier droits et configuration
    GD->>H: tracer(création demande)

    alt Environnement = Production
        GD-->>UI: demande "En attente de validation"
        GD->>RC: notifier la demande
        RC->>GD: decider(demande)
        alt Refusée
            GD->>H: tracer(demande refusée)
            GD-->>IC: demande "Refusée"
        else Validée
            GD->>H: tracer(demande validée)
        end
    end

    opt Demande validée ou environnement non Production
        GD->>GR: deployer(demande)
        GR->>GR: créer ressource (état "En déploiement")
        GR->>FC: provisionner(configuration)
        alt Succès
            FC-->>GR: ok
            GR->>GR: état = Active
            GR->>GD: demande "Terminée"
            GR->>H: tracer(déploiement réussi)
            GD-->>IC: notification de succès
        else Échec
            FC-->>GR: erreur
            GR->>GD: demande "Échouée"
            GR->>H: tracer(échec du déploiement)
            GD-->>IC: notification d'échec
        end
    end
```
