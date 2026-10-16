# Phase 1 – Inception

## 1. Problème

L'entreprise gère une infrastructure Cloud répartie sur plusieurs environnements (Développement, Test, Production).
Sans outil centralisé, le suivi des ressources, la validation des déploiements, la traçabilité des opérations et
la détection des incidents sont difficiles et sources d'erreurs.

## 2. Objectifs

- Centraliser la gestion des ressources Cloud (machines virtuelles, conteneurs, réseaux virtuels, stockage).
- Encadrer les demandes de déploiement, avec validation obligatoire pour la Production.
- Tracer toute opération importante dans un historique.
- Superviser les ressources (mesures, seuils) et générer des alertes automatiquement.

## 3. Périmètre

**Inclus**
- Gestion des environnements et des ressources (consulter, déployer, modifier, démarrer, arrêter, redémarrer, supprimer).
- Workflow des demandes de déploiement.
- Supervision : mesures, seuils, alertes.
- Historique des opérations.

**Exclu**
- Facturation et gestion des coûts.
- Conception des fournisseurs Cloud eux-mêmes (CloudManager s'appuie sur leurs API).

## 4. Acteurs

| Acteur | Description |
|---|---|
| Ingénieur Cloud | Crée les demandes de déploiement (environnement cible, type de ressource, configuration, justification) et exploite les ressources |
| Responsable Cloud | Valide ou refuse toute demande concernant la Production |
| Administrateur | Administre la plateforme *(hypothèse : utilisateurs, seuils, consultation de l'historique)* |
| Responsable Réseau | Gère les réseaux virtuels et traite les alertes réseau *(hypothèse)* |
| Système de supervision | Acteur secondaire : fournit les mesures et déclenche les alertes |

## 5. Exigences

### Fonctionnelles

| Id | Exigence |
|---|---|
| EF1 | Un utilisateur autorisé consulte les ressources |
| EF2 | L'Ingénieur Cloud crée une demande de déploiement (environnement, type, configuration, justification) |
| EF3 | Une demande possède un numéro, une date, un état et un demandeur |
| EF4 | États d'une demande : Créée, En attente de validation, Validée, Refusée, En cours, Terminée, Échouée |
| EF5 | Toute demande Production est validée ou refusée par le Responsable Cloud |
| EF6 | En cas de succès du déploiement, la ressource devient Active ; en cas d'échec, l'événement est enregistré |
| EF7 | Une ressource peut être démarrée, arrêtée, redémarrée, modifiée ou supprimée |
| EF8 | Cycle de vie : Demandée → En déploiement → Active → Arrêtée → Active → Supprimée |
| EF9 | Une opération non autorisée ne modifie pas l'état de la ressource |
| EF10 | Une ressource reçoit des mesures (CPU, mémoire, stockage, trafic réseau, disponibilité) |
| EF11 | Un type de mesure peut avoir un seuil ; son dépassement génère une alerte |
| EF12 | Une alerte (gravité, message, date) passe par les états Nouvelle, Prise en charge, Résolue |
| EF13 | Toute opération importante est tracée dans un historique |

### Non fonctionnelles

| Id | Exigence |
|---|---|
| ENF1 | Sécurité : authentification et droits selon le rôle |
| ENF2 | Traçabilité : historique non modifiable |
| ENF3 | Disponibilité de la plateforme |
| ENF4 | Ergonomie : interface web simple |
| ENF5 | Extensibilité : ajout de nouveaux types de ressources |
