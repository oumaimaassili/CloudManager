# Phase 4 – Transition : diagramme de déploiement

## Proposition d'architecture

CloudManager est déployé dans le Cloud : une application web, une API, une base de données et un module de
supervision qui interroge les ressources des environnements Développement, Test et Production.

```mermaid
flowchart TB
    subgraph Client["Poste client"]
        NAV["Navigateur web<br/>«artefact» interface-web"]
    end

    subgraph Cloud["Cloud – Plateforme CloudManager"]
        direction TB
        LB["Équilibreur de charge<br/>«nœud»"]
        subgraph App["Serveur d'applications (conteneur)"]
            API["«artefact» cloudmanager-api<br/>gestion demandes, ressources, alertes"]
            SUPV["«artefact» module-supervision<br/>collecte des mesures, seuils"]
        end
        DB[("Serveur de base de données<br/>«artefact» base-cloudmanager")]
        AUTH["Service d'authentification<br/>«artefact» auth-service"]
    end

    subgraph Infra["Infrastructure Cloud gérée"]
        DEV["Environnement Développement<br/>VM, conteneurs, réseaux, stockage"]
        TEST["Environnement Test"]
        PROD["Environnement Production"]
    end

    NAV -- "HTTPS" --> LB
    LB -- "HTTP" --> API
    API -- "SQL / TCP" --> DB
    API -- "jeton" --> AUTH
    API -- "API du fournisseur (provisionnement)" --> DEV
    API -- "API du fournisseur (provisionnement)" --> TEST
    API -- "API du fournisseur (provisionnement)" --> PROD
    SUPV -- "mesures CPU, mémoire, stockage, réseau" --> DEV
    SUPV -- "mesures" --> TEST
    SUPV -- "mesures" --> PROD
    SUPV -- "SQL" --> DB
```

## Nœuds, artefacts et relations

| Nœud | Artefacts | Relation |
|---|---|---|
| Poste client | interface-web | HTTPS vers l'équilibreur |
| Équilibreur de charge | configuration du proxy | HTTP vers le serveur d'applications |
| Serveur d'applications | cloudmanager-api, module-supervision | SQL vers la base, API Cloud vers les environnements |
| Serveur de base de données | base-cloudmanager | accessible uniquement depuis le serveur d'applications |
| Service d'authentification | auth-service | jeton vérifié par l'API |
| Environnements Dev / Test / Prod | ressources gérées | provisionnement et mesures |

## Recommandations de déploiement

- Réseau privé pour la base de données ; seul l'équilibreur est exposé.
- Sauvegardes régulières de la base, surtout de l'historique.
- Déploiement progressif : Développement → Test → Production.
