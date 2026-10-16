# Phase 2 – Élaboration : diagramme de cas d'utilisation

## Acteurs et cas d'utilisation

```mermaid
flowchart LR
    IC([Ingénieur Cloud])
    RC([Responsable Cloud])
    AD([Administrateur])
    RR([Responsable Réseau])
    SUP([Système de supervision])

    subgraph CloudManager
        UC1(Consulter les ressources)
        UC2(Demander le déploiement d'une ressource)
        UC3(Valider ou refuser une demande Production)
        UC4(Modifier la configuration)
        UC5(Démarrer une ressource)
        UC6(Arrêter une ressource)
        UC7(Redémarrer une ressource)
        UC8(Supprimer une ressource)
        UC9(Consulter l'historique)
        UC10(Gérer les seuils)
        UC11(Traiter une alerte)
        UC12(Enregistrer une mesure)
        UC13(Générer une alerte)
        UC14(Tracer l'opération)
        UC15(S'authentifier)
    end

    IC --- UC1
    IC --- UC2
    IC --- UC4
    IC --- UC5
    IC --- UC6
    IC --- UC7
    IC --- UC8
    RC --- UC3
    RC --- UC11
    AD --- UC9
    AD --- UC10
    AD --- UC1
    RR --- UC11
    RR --- UC1
    SUP --- UC12

    UC2 -. include .-> UC15
    UC2 -. include .-> UC14
    UC4 -. include .-> UC14
    UC5 -. include .-> UC14
    UC6 -. include .-> UC14
    UC7 -. include .-> UC14
    UC8 -. include .-> UC14
    UC3 -. include .-> UC14
    UC12 -. extend .-> UC13
```

> Notation : `include` = inclusion obligatoire ; `extend` = extension conditionnelle (la génération d'alerte n'a lieu que si la mesure dépasse le seuil). Mermaid n'a pas de diagramme de cas d'utilisation natif : pour un rendu UML strict, refaites-le avec PlantUML ou draw.io et exportez-le dans `diagrams/img/`.

## Description des cas principaux

| Cas | Acteur | Pré-condition | Résultat |
|---|---|---|---|
| Demander le déploiement | Ingénieur Cloud | Authentifié, droit sur l'environnement | Demande créée (numéro, date, demandeur, état) |
| Valider / refuser | Responsable Cloud | Demande Production « En attente de validation » | Demande « Validée » ou « Refusée » |
| Démarrer / arrêter / redémarrer | Ingénieur Cloud | Ressource dans un état compatible | État de la ressource mis à jour et tracé |
| Traiter une alerte | Resp. Cloud / Resp. Réseau | Alerte « Nouvelle » | Alerte « Prise en charge » puis « Résolue » |
