# Phase 3 – Construction : classes raffinées, états et cohérence

## 1. Diagramme de classes raffiné

```mermaid
classDiagram
    class Environnement {
        -int id
        -String nom
        +listerRessources() List~Ressource~
    }
    class Ressource {
        <<abstract>>
        -int id
        -String nom
        -String configuration
        -EtatRessource etat
        -Date dateCreation
        +demarrer()
        +arreter()
        +redemarrer()
        +modifierConfiguration(String)
        +supprimer()
    }
    class MachineVirtuelle {
        -int vCPU
        -int ramGo
        -String systeme
    }
    class Conteneur {
        -String image
        -int port
    }
    class ReseauVirtuel {
        -String plageIP
        -String passerelle
    }
    class EspaceStockage {
        -int capaciteGo
        -String typeStockage
    }
    class Utilisateur {
        -int id
        -String nom
        -Role role
        +estAutorise(Operation, Environnement) boolean
    }
    class DemandeDeploiement {
        -int numero
        -Date date
        -EtatDemande etat
        -String justification
        -String typeRessource
        -String configuration
        +soumettre()
        +valider(Utilisateur)
        +refuser(Utilisateur)
        +demarrerDeploiement()
        +terminer()
        +echouer(String)
    }
    class Mesure {
        -TypeMesure type
        -double valeur
        -String unite
        -Date dateHeure
    }
    class Seuil {
        -TypeMesure type
        -double valeurMax
        +estDepasse(Mesure) boolean
    }
    class Alerte {
        -Gravite gravite
        -String message
        -Date date
        -EtatAlerte etat
        +prendreEnCharge(Utilisateur)
        +resoudre()
    }
    class Historique {
        -Date date
        -String operation
        -String resultat
        +tracer(...)
    }

    Ressource <|-- MachineVirtuelle
    Ressource <|-- Conteneur
    Ressource <|-- ReseauVirtuel
    Ressource <|-- EspaceStockage

    Environnement "1" *-- "0..*" Ressource : contient
    Utilisateur "1" --> "0..*" DemandeDeploiement : demandeur
    Utilisateur "0..1" --> "0..*" DemandeDeploiement : validateur
    DemandeDeploiement "0..*" --> "1" Environnement : cible
    DemandeDeploiement "0..1" --> "0..1" Ressource : produit
    Ressource "1" *-- "0..*" Mesure : reçoit
    Mesure "0..*" --> "0..1" Seuil : évaluée par
    Ressource "1" --> "0..*" Alerte : concerne
    Mesure "1" --> "0..*" Alerte : déclenche
    Utilisateur "1" --> "0..*" Historique : auteur
    Ressource "0..1" --> "0..*" Historique : trace
```

## 2. Contraintes (OCL / texte)

| Id | Contrainte |
|---|---|
| C1 | `Ressource` appartient à exactement un `Environnement` |
| C2 | Si `DemandeDeploiement.environnement = Production`, elle ne peut passer à *Validée* que par un `Utilisateur` de rôle Responsable Cloud |
| C3 | Une `Alerte` est créée seulement si `mesure.valeur > seuil.valeurMax` pour le même `TypeMesure` |
| C4 | Une opération non autorisée (état incompatible ou droits insuffisants) ne modifie pas `Ressource.etat` |
| C5 | Toute opération importante crée une ligne `Historique` |
| C6 | `Ressource.dateCreation` est non modifiable |

## 3. Diagrammes d'états

### Ressource

```mermaid
stateDiagram-v2
    [*] --> Demandée
    Demandée --> EnDéploiement : déploiement lancé
    EnDéploiement --> Active : succès
    EnDéploiement --> Demandée : échec (événement enregistré)
    Active --> Arrêtée : arrêter()
    Arrêtée --> Active : démarrer()
    Active --> Active : redémarrer()
    Active --> Supprimée : supprimer()
    Arrêtée --> Supprimée : supprimer()
    Supprimée --> [*]
```

### Demande de déploiement

```mermaid
stateDiagram-v2
    [*] --> Créée
    Créée --> EnAttenteDeValidation : environnement Production
    Créée --> Validée : Dev ou Test
    EnAttenteDeValidation --> Validée : validation
    EnAttenteDeValidation --> Refusée : refus
    Validée --> EnCours : déploiement lancé
    EnCours --> Terminée : succès
    EnCours --> Échouée : échec
    Refusée --> [*]
    Terminée --> [*]
    Échouée --> [*]
```

### Alerte

```mermaid
stateDiagram-v2
    [*] --> Nouvelle
    Nouvelle --> PriseEnCharge : prendreEnCharge()
    PriseEnCharge --> Résolue : resoudre()
    Résolue --> [*]
```

## 4. Vérification de cohérence entre diagrammes

| Vérification | Diagrammes concernés | Statut |
|---|---|---|
| Chaque cas d'utilisation a au moins une interaction | Cas d'utilisation ↔ Séquence | ✔ pour « Déployer » ; à compléter pour les autres cas |
| Les messages de la séquence correspondent à des opérations de classes | Séquence ↔ Classes | ✔ (`creerDemande`, `valider`, `deployer`, `tracer`) |
| Les états de la demande/ressource sont ceux de l'énoncé | États ↔ Énoncé | ✔ |
| Les nœuds de l'activité reprennent les états | Activité ↔ États | ✔ |
| Les classes de l'analyse sont conservées | Analyse ↔ Construction | ✔ (+ spécialisations de `Ressource`) |

> Le cycle de vie « Demandée → En déploiement → Active → Arrêtée → Active → Supprimée » de l'énoncé est respecté. Le retour *En déploiement → Demandée* en cas d'échec est une hypothèse : à justifier ou à remplacer par un état *Échec* selon votre choix.
