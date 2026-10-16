# Phase 2 – Élaboration : diagramme de classes d'analyse

```mermaid
classDiagram
    class Environnement {
        id
        nom
    }
    class Ressource {
        id
        nom
        type
        configuration
        etat
        dateCreation
    }
    class Utilisateur {
        id
        nom
        role
    }
    class DemandeDeploiement {
        numero
        date
        etat
        justification
        typeRessource
        configuration
    }
    class Mesure {
        type
        valeur
        unite
        dateHeure
    }
    class Seuil {
        typeMesure
        valeurMax
    }
    class Alerte {
        gravite
        message
        date
        etat
    }
    class Historique {
        date
        operation
        resultat
    }

    Environnement "1" --> "0..*" Ressource : contient
    Utilisateur "1" --> "0..*" DemandeDeploiement : crée
    Utilisateur "0..1" --> "0..*" DemandeDeploiement : valide
    DemandeDeploiement "0..*" --> "1" Environnement : cible
    DemandeDeploiement "1" --> "0..1" Ressource : produit
    Ressource "1" --> "0..*" Mesure : reçoit
    Mesure "0..*" --> "0..1" Seuil : comparée à
    Mesure "1" --> "0..*" Alerte : déclenche
    Alerte "0..*" --> "1" Ressource : concerne
    Utilisateur "1" --> "0..*" Historique : effectue
    Historique "0..*" --> "0..1" Ressource : porte sur
```

## Remarques d'analyse

- Une ressource appartient à **un seul** environnement.
- Les types de ressources (machine virtuelle, conteneur, réseau virtuel, stockage) sont raffinés en Construction.
- Une demande ne produit une ressource qu'en cas de succès du déploiement (cardinalité `0..1`).
