# MCD FinTrack — code Mermaid

Version 1.0 — 18 octobre 2026. Ce fichier contient le code Mermaid (`erDiagram`) du modèle conceptuel de données, avec les attributs. Le document complet est `FinTrack_MCD_V1.0.docx`.

## Conventions

- Pattes de corbeau : `||` un exactement, `|o` zéro ou un, `}o` zéro ou plusieurs, `}|` un ou plusieurs.
- Cardinalités de Merise : voir les tables du document (section 4).
- `PK` identifiant, `FK` clé étrangère, `UK` unique.
- Les noms d’entités et d’attributs sont sans accents ; les libellés des associations sont en français.

## Vue d’ensemble (simplifiée)

```mermaid
erDiagram
  DEVISE ||--o{ COMPTE : "exprime"
  DEVISE ||--o{ TAUX_CHANGE : "base ou cible"
  COMPTE ||--o{ MOUVEMENT_COMPTE : "affecte"
  OPERATION ||--|{ MOUVEMENT_COMPTE : "produit"
  OPERATION ||--o{ CONVERSION : "utilise"
  CONVERSION }o--o| TAUX_CHANGE : "s'appuie sur"
  CATEGORIE ||--o{ OPERATION : "classe (revenu, dépense)"
  OPERATION ||--o{ LIEN_OPERATION : "relie"
  OPERATION |o--o{ EVENEMENT_HISTORIQUE : "est tracée par"
  TIERS ||--o{ ENGAGEMENT : "concerne"
  OPERATION |o--o| ENGAGEMENT : "initialise ou rembourse"
  COMPTE ||--o{ OBJECTIF_EPARGNE : "supporte"
  PREVISION |o--o{ OPERATION : "est réalisée par"
  BUDGET ||--o{ CATEGORIE : "limite"
```

## Devises, taux de change et comptes

Le taux de change est une suite de fixations jamais modifiées. Chaque conversion réalisée garde son propre taux.

```mermaid
erDiagram
  DEVISE ||--o{ COMPTE : "exprime le solde de"
  DEVISE ||--o{ TAUX_CHANGE : "est la devise de base de"
  DEVISE ||--o{ TAUX_CHANGE : "est la devise cotée de"
  CONVERSION }o--o| TAUX_CHANGE : "s'appuie sur"
  DEVISE {
    string code PK "USD, CDF"
    string nom
    string symbole
    int decimales
    boolean active
  }
  TAUX_CHANGE {
    int id PK
    string devise_base FK
    string devise_cible FK
    decimal taux "unités de la cible pour 1 de la base"
    decimal ancien_taux "vide à l'initialisation"
    datetime fixe_le
    string motif "facultatif"
  }
  COMPTE {
    int id PK
    string nom
    string type_compte "espèces, banque, monnaie mobile, épargne"
    string devise_code FK
    decimal solde_initial "fixé à la création"
    boolean actif
    datetime cree_le
    datetime desactive_le
  }
  CONVERSION {
    int id PK
    int operation_id FK
    string devise_source FK
    decimal montant_source
    string devise_cible FK
    decimal montant_cible
    decimal taux
    string sens "USD vers CDF ou CDF vers USD"
    string origine_taux "COURANT ou SAISI"
    int taux_change_id FK "optionnel"
  }
```

## Opérations, mouvements et spécialisations

Toute opération produit au moins un mouvement sur un compte. Le solde d’un compte se déduit du solde initial et des mouvements des opérations actives : il n’est jamais saisi. Les types d’opération (revenu, dépense, transfert, remboursement) sont des spécialisations de l’opération.

```mermaid
erDiagram
  COMPTE ||--o{ MOUVEMENT_COMPTE : "est débité ou crédité par"
  OPERATION ||--|{ MOUVEMENT_COMPTE : "produit"
  OPERATION ||--o{ CONVERSION : "utilise"
  OPERATION ||--o| REVENU : "est un"
  OPERATION ||--o| DEPENSE : "est une"
  OPERATION ||--o| TRANSFERT : "est un"
  OPERATION ||--o| REMBOURSEMENT_DEPENSE : "est un"
  DEPENSE ||--o{ REMBOURSEMENT_DEPENSE : "est remboursée par"
  CATEGORIE ||--o{ REVENU : "classe"
  CATEGORIE ||--o{ DEPENSE : "classe"
  TRANSFERT }o--|| COMPTE : "débite"
  TRANSFERT }o--|| COMPTE : "crédite"
  OPERATION {
    int id PK
    string type_operation "REVENU, DEPENSE, TRANSFERT, REMBOURSEMENT_DEPENSE, ENGAGEMENT_INITIAL, REMBOURSEMENT_ENGAGEMENT, INCONNU"
    date date_operation
    string description
    text note
    decimal montant "toujours positif"
    string devise_code FK
    string statut "ACTIVE ou ANNULEE"
    datetime cree_le
    datetime annule_le
    string motif_annulation
  }
  MOUVEMENT_COMPTE {
    int id PK
    int operation_id FK
    int compte_id FK
    decimal montant "signé : positif = entrée"
    string devise_code FK
    date date_valeur
    int allocation_id FK "optionnel"
  }
  COMPTE {
    int id PK
    string nom
    string type_compte "espèces, banque, monnaie mobile, épargne"
    string devise_code FK
    decimal solde_initial "fixé à la création"
    boolean actif
    datetime cree_le
    datetime desactive_le
  }
  CONVERSION {
    int id PK
    int operation_id FK
    string devise_source FK
    decimal montant_source
    string devise_cible FK
    decimal montant_cible
    decimal taux
    string sens "USD vers CDF ou CDF vers USD"
    string origine_taux "COURANT ou SAISI"
    int taux_change_id FK "optionnel"
  }
  CATEGORIE {
    int id PK
    string nom
    string sens "REVENU ou DEPENSE"
    boolean personnalisee
    boolean active
  }
  REVENU {
    int operation_id PK, FK
    int categorie_id FK
    string nature "REGULIER ou EXCEPTIONNEL"
    int tiers_id FK "optionnel"
  }
  DEPENSE {
    int operation_id PK, FK
    int categorie_id FK
    string nature "NECESSAIRE, UTILE, EVITABLE, IMPULSIVE"
    int prevision_id FK "optionnel"
  }
  TRANSFERT {
    int operation_id PK, FK
    int compte_source_id FK
    int compte_destination_id FK
  }
  REMBOURSEMENT_DEPENSE {
    int operation_id PK, FK
    int depense_id FK
  }
```

## Paiements et sources de financement

Une dépense est réglée par un ou plusieurs paiements ; chaque paiement est financé par une ou plusieurs allocations puisées dans des sources. Une même source peut financer plusieurs dépenses.

```mermaid
erDiagram
  DEPENSE ||--|{ PAIEMENT : "est réglée par"
  PAIEMENT ||--|{ ALLOCATION : "est financé par"
  SOURCE_FINANCEMENT ||--o{ ALLOCATION : "alimente"
  COMPTE ||--o{ SOURCE_FINANCEMENT : "fournit les fonds de"
  OPERATION |o--o{ SOURCE_FINANCEMENT : "est l'origine de"
  ALLOCATION |o--o| CONVERSION : "peut être convertie par"
  ALLOCATION |o--o{ MOUVEMENT_COMPTE : "provoque"
  DEPENSE {
    int operation_id PK, FK
    int categorie_id FK
    string nature "NECESSAIRE, UTILE, EVITABLE, IMPULSIVE"
    int prevision_id FK "optionnel"
  }
  PAIEMENT {
    int id PK
    int depense_id FK
    date date_paiement
    decimal montant
    string devise_code FK
  }
  ALLOCATION {
    int id PK
    int paiement_id FK
    int source_id FK
    decimal montant_depense "en devise de la dépense"
    decimal montant_compte "dans la devise du compte"
    string devise_compte FK
    int conversion_id FK "optionnel"
  }
  SOURCE_FINANCEMENT {
    int id PK
    int compte_id FK
    int operation_origine_id FK "revenu, prêt, don : optionnel"
    string nature_source "REVENU, EPARGNE, DETTE, DON, AUTRE"
    decimal montant_total "optionnel"
    string devise_code FK
    string description
  }
  COMPTE {
    int id PK
    string nom
    string type_compte "espèces, banque, monnaie mobile, épargne"
    string devise_code FK
    decimal solde_initial "fixé à la création"
    boolean actif
    datetime cree_le
    datetime desactive_le
  }
  OPERATION {
    int id PK
    string type_operation "REVENU, DEPENSE, TRANSFERT, REMBOURSEMENT_DEPENSE, ENGAGEMENT_INITIAL, REMBOURSEMENT_ENGAGEMENT, INCONNU"
    date date_operation
    string description
    text note
    decimal montant "toujours positif"
    string devise_code FK
    string statut "ACTIVE ou ANNULEE"
    datetime cree_le
    datetime annule_le
    string motif_annulation
  }
  CONVERSION {
    int id PK
    int operation_id FK
    string devise_source FK
    decimal montant_source
    string devise_cible FK
    decimal montant_cible
    decimal taux
    string sens "USD vers CDF ou CDF vers USD"
    string origine_taux "COURANT ou SAISI"
    int taux_change_id FK "optionnel"
  }
  MOUVEMENT_COMPTE {
    int id PK
    int operation_id FK
    int compte_id FK
    decimal montant "signé : positif = entrée"
    string devise_code FK
    date date_valeur
    int allocation_id FK "optionnel"
  }
```

## Liens entre opérations, doublons et « À qualifier »

Les liens de causalité (doublon de, excédent de, correction de) sont conservés dans une table unique. Une opération « À qualifier » est une opération de type INCONNU : elle a ses mouvements donc modifie le solde, mais n’a aucune spécialisation, donc n’entre ni dans les revenus ni dans les dépenses.

```mermaid
erDiagram
  OPERATION ||--o{ LIEN_OPERATION : "est l'origine de"
  OPERATION ||--o{ LIEN_OPERATION : "est liée par"
  OPERATION ||--o{ DECISION_DOUBLON : "est l'opération A de"
  OPERATION ||--o{ DECISION_DOUBLON : "est l'opération B de"
  OPERATION {
    int id PK
    string type_operation "REVENU, DEPENSE, TRANSFERT, REMBOURSEMENT_DEPENSE, ENGAGEMENT_INITIAL, REMBOURSEMENT_ENGAGEMENT, INCONNU"
    date date_operation
    string description
    text note
    decimal montant "toujours positif"
    string devise_code FK
    string statut "ACTIVE ou ANNULEE"
    datetime cree_le
    datetime annule_le
    string motif_annulation
  }
  LIEN_OPERATION {
    int id PK
    int operation_origine_id FK
    int operation_liee_id FK
    string type_lien "DOUBLON_DE, EXCEDENT_DE, CORRECTION_DE"
    datetime cree_le
  }
  DECISION_DOUBLON {
    int id PK
    int operation_a_id FK
    int operation_b_id FK
    string decision "LEGITIME ou FUSIONNEE"
    datetime decide_le
  }
```

## Épargne

L’épargne est un compte réel : une contribution est un transfert vers ce compte, rattaché à un objectif.

```mermaid
erDiagram
  COMPTE ||--o{ OBJECTIF_EPARGNE : "sert de support à"
  OBJECTIF_EPARGNE ||--o{ OPERATION_EPARGNE : "reçoit"
  TRANSFERT ||--o| OPERATION_EPARGNE : "matérialise"
  COMPTE {
    int id PK
    string nom
    string type_compte "espèces, banque, monnaie mobile, épargne"
    string devise_code FK
    decimal solde_initial "fixé à la création"
    boolean actif
    datetime cree_le
    datetime desactive_le
  }
  OBJECTIF_EPARGNE {
    int id PK
    string nom
    decimal montant_cible
    string devise_code FK
    date date_limite "facultative"
    text description
    int compte_epargne_id FK
    string statut "EN_COURS, ATTEINT, ABANDONNE"
  }
  OPERATION_EPARGNE {
    int operation_id PK, FK "le transfert"
    int objectif_id FK
    string sens "CONTRIBUTION ou RETRAIT"
  }
  TRANSFERT {
    int operation_id PK, FK
    int compte_source_id FK
    int compte_destination_id FK
  }
```

## Dettes et créances

Le restant d’une dette ou d’une créance se déduit des remboursements actifs ; il n’est pas stocké.

```mermaid
erDiagram
  TIERS ||--o{ ENGAGEMENT : "est concerné par"
  ENGAGEMENT ||--o{ REMBOURSEMENT_ENGAGEMENT : "est remboursé par"
  OPERATION ||--o| REMBOURSEMENT_ENGAGEMENT : "est un"
  OPERATION |o--o| ENGAGEMENT : "initialise"
  REMBOURSEMENT_ENGAGEMENT |o--o| CONVERSION : "peut être convertie par"
  TIERS {
    int id PK
    string nom
    string type_tiers "PERSONNE ou ORGANISATION"
    string contact
    boolean actif
  }
  ENGAGEMENT {
    int id PK
    string sens "DETTE ou CREANCE"
    int tiers_id FK
    decimal montant_initial
    string devise_code FK
    date date_engagement
    date echeance "facultative"
    string statut "EN_COURS, SOLDE, ANNULE"
    int operation_initiale_id FK "optionnel"
  }
  REMBOURSEMENT_ENGAGEMENT {
    int operation_id PK, FK
    int engagement_id FK
    decimal montant_applique "devise de l'engagement"
    int conversion_id FK "optionnel"
  }
  OPERATION {
    int id PK
    string type_operation "REVENU, DEPENSE, TRANSFERT, REMBOURSEMENT_DEPENSE, ENGAGEMENT_INITIAL, REMBOURSEMENT_ENGAGEMENT, INCONNU"
    date date_operation
    string description
    text note
    decimal montant "toujours positif"
    string devise_code FK
    string statut "ACTIVE ou ANNULEE"
    datetime cree_le
    datetime annule_le
    string motif_annulation
  }
  CONVERSION {
    int id PK
    int operation_id FK
    string devise_source FK
    decimal montant_source
    string devise_cible FK
    decimal montant_cible
    decimal taux
    string sens "USD vers CDF ou CDF vers USD"
    string origine_taux "COURANT ou SAISI"
    int taux_change_id FK "optionnel"
  }
```

## Budgets et alertes

Un budget garde toutes ses versions et leurs périodes d’application.

```mermaid
erDiagram
  BUDGET ||--|{ BUDGET_VERSION : "a pour versions"
  BUDGET_VERSION ||--o{ LIMITE_CATEGORIE : "fixe"
  CATEGORIE ||--o{ LIMITE_CATEGORIE : "est limitée par"
  BUDGET ||--o{ ALERTE : "déclenche"
  ALERTE ||--|{ ALERTE_EVOLUTION : "évolue par"
  BUDGET {
    int id PK
    string libelle
    string portee "GLOBAL ou PAR_CATEGORIE"
    string devise_code FK
    date periode_debut
    date periode_fin
    decimal seuil_alerte_pct "ex. 80"
    string statut "ACTIF ou CLOTURE"
  }
  BUDGET_VERSION {
    int id PK
    int budget_id FK
    int numero
    decimal montant_global
    date valide_du
    date valide_au
    string motif
  }
  LIMITE_CATEGORIE {
    int id PK
    int budget_version_id FK
    int categorie_id FK
    decimal montant_limite
  }
  CATEGORIE {
    int id PK
    string nom
    string sens "REVENU ou DEPENSE"
    boolean personnalisee
    boolean active
  }
  ALERTE {
    int id PK
    string type_alerte
    int budget_id FK "optionnel"
    string niveau "INFO, ATTENTION, CRITIQUE"
    text message
    datetime creee_le
    string statut "ACTIVE ou RESOLUE"
    datetime resolue_le
  }
  ALERTE_EVOLUTION {
    int id PK
    int alerte_id FK
    datetime date_heure
    string ancien_niveau
    string nouveau_niveau
    text commentaire
  }
```

## Prévisions et récurrences

Une prévision ne produit aucun mouvement : seule la dépense réelle qui la réalise modifie les soldes.

```mermaid
erDiagram
  RECURRENCE ||--o{ PREVISION : "génère"
  CATEGORIE ||--o{ PREVISION : "classe"
  PREVISION ||--o{ FRAIS_PREVU : "comprend"
  PREVISION |o--o{ DEPENSE : "est réalisée par"
  FRAIS_PREVU }o--o| DEPENSE : "est réglé par"
  RECURRENCE {
    int id PK
    string frequence
    int jour_du_mois
    string regle_jour_absent "DERNIER_JOUR ou OMISSION"
    string regle_jour_non_ouvre
    date date_debut
    date date_fin
    string etat "ACTIVE, SUSPENDUE, ANNULEE"
  }
  PREVISION {
    int id PK
    string libelle
    decimal montant
    boolean montant_estime
    string devise_code FK
    int categorie_id FK
    date date_echeance
    string etat "PREVUE, REALISEE, ANNULEE, REPORTEE, EN_RETARD"
    date date_report
    int recurrence_id FK "optionnel"
  }
  FRAIS_PREVU {
    int id PK
    int prevision_id FK
    string libelle
    decimal montant_prevu
    decimal montant_reel
    int depense_id FK "optionnel"
  }
  DEPENSE {
    int operation_id PK, FK
    int categorie_id FK
    string nature "NECESSAIRE, UTILE, EVITABLE, IMPULSIVE"
    int prevision_id FK "optionnel"
  }
  CATEGORIE {
    int id PK
    string nom
    string sens "REVENU ou DEPENSE"
    boolean personnalisee
    boolean active
  }
```

## Historique, refus et sauvegardes

Le journal d’historique et les refus sont écrits seulement : jamais modifiés ni supprimés.

```mermaid
erDiagram
  OPERATION |o--o{ EVENEMENT_HISTORIQUE : "est tracée par"
  COMPTE |o--o{ ACTION_REFUSEE : "est visé par"
  SAUVEGARDE ||--o{ RESTAURATION : "est restaurée par"
  SAUVEGARDE |o--o{ RESTAURATION : "protège (sécurité)"
  OPERATION {
    int id PK
    string type_operation "REVENU, DEPENSE, TRANSFERT, REMBOURSEMENT_DEPENSE, ENGAGEMENT_INITIAL, REMBOURSEMENT_ENGAGEMENT, INCONNU"
    date date_operation
    string description
    text note
    decimal montant "toujours positif"
    string devise_code FK
    string statut "ACTIVE ou ANNULEE"
    datetime cree_le
    datetime annule_le
    string motif_annulation
  }
  EVENEMENT_HISTORIQUE {
    int id PK
    datetime date_heure
    string type_evenement "CREATION, MODIFICATION, ANNULATION, QUALIFICATION, REAFFECTATION, FIXATION_TAUX, RESTAURATION"
    string entite_type
    int entite_id
    int operation_id FK "optionnel"
    text ancienne_valeur
    text nouvelle_valeur
    string motif
  }
  COMPTE {
    int id PK
    string nom
    string type_compte "espèces, banque, monnaie mobile, épargne"
    string devise_code FK
    decimal solde_initial "fixé à la création"
    boolean actif
    datetime cree_le
    datetime desactive_le
  }
  ACTION_REFUSEE {
    int id PK
    datetime date_heure
    string type_operation
    int compte_id FK
    decimal montant
    string devise_code FK
    string motif_refus
  }
  SAUVEGARDE {
    int id PK
    string nom_fichier
    datetime cree_le
    string type_sauvegarde "MANUELLE, AUTOMATIQUE, SECURITE"
    string empreinte
    string version_schema
  }
  RESTAURATION {
    int id PK
    int sauvegarde_id FK
    int sauvegarde_securite_id FK
    datetime date_heure
    string resultat
  }
```

## Accès et paramètres

Application locale à un seul utilisateur : aucune entité financière n’est rattachée à un propriétaire.

```mermaid
erDiagram
  UTILISATEUR ||--o{ SESSION : "ouvre"
  UTILISATEUR |o--o{ TENTATIVE_CONNEXION : "subit"
  UTILISATEUR ||--o{ PARAMETRE : "règle"
  UTILISATEUR {
    int id PK
    string nom_utilisateur UK
    string mot_de_passe_hash
    string code_recuperation_hash
    datetime cree_le
  }
  SESSION {
    int id PK
    int utilisateur_id FK
    string jeton_hash
    datetime debut
    datetime derniere_activite
    datetime fin
  }
  TENTATIVE_CONNEXION {
    int id PK
    int utilisateur_id FK "optionnel"
    datetime date_heure
    boolean succes
    int delai_impose_sec
  }
  PARAMETRE {
    string cle PK
    int utilisateur_id FK
    string valeur
    string description
    datetime modifie_le
  }
```
