# CloudManager

Plateforme de gestion et de supervision d'une infrastructure Cloud (environnements Développement, Test, Production).

> Projet UML – Faculté des Sciences de Sfax – Département Informatique et Communication
> Auditoire : 2ème MPI – Série 1 – Responsable : M. Mhiri

## Contexte

Une entreprise souhaite développer **CloudManager** afin de gérer et superviser son infrastructure Cloud :
machines virtuelles, conteneurs, réseaux virtuels et espaces de stockage, répartis dans plusieurs environnements.
Toute opération importante est tracée dans un historique.

## Acteurs

| Acteur | Rôle principal |
|---|---|
| Ingénieur Cloud | Crée les demandes de déploiement, consulte et exploite les ressources |
| Responsable Cloud | Valide ou refuse les demandes concernant la Production |
| Administrateur | Administre la plateforme (utilisateurs, seuils, historique) |
| Responsable Réseau | Gère les réseaux virtuels et traite les alertes réseau |

## Démarche – Processus Unifié

| Phase | Contenu | Document |
|---|---|---|
| 1. Inception | Problème, objectifs, périmètre, acteurs, exigences | [inception.md](docs/01-inception/inception.md) |
| 2. Élaboration | Cas d'utilisation, classes d'analyse, séquence, activité | [cas-utilisation](docs/02-elaboration/cas-utilisation.md) · [classes-analyse](docs/02-elaboration/classes-analyse.md) · [sequence](docs/02-elaboration/sequence-deployer-ressource.md) · [activite](docs/02-elaboration/activite-deploiement.md) |
| 3. Construction | Classes raffinées, états, cohérence | [classes-raffinees.md](docs/03-construction/classes-raffinees.md) |
| 4. Transition | Diagramme de déploiement | [deploiement.md](docs/04-transition/deploiement.md) |

## Structure du dépôt

```
CloudManager/
├── README.md
├── .gitignore
├── docs/
│   ├── 01-inception/
│   ├── 02-elaboration/
│   ├── 03-construction/
│   └── 04-transition/
├── diagrams/
│   ├── src/    (sources éditables .puml / .drawio)
│   └── img/    (exports .png / .svg)
└── src/        (code éventuel)
```

## Outils

Les diagrammes sont écrits en [Mermaid](https://mermaid.js.org/) : GitHub les affiche directement dans les fichiers `.md`.

## Auteur

Votre nom – 2ème MPI
