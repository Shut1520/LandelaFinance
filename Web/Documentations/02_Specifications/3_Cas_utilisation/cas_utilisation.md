# FinTrack — Cas d’utilisation (diagrammes Mermaid)

Version 1.1 — 18 octobre 2026. Ce fichier contient le code Mermaid des diagrammes de cas d’utilisation. Le document complet (catalogue, fiches, couverture) est `FinTrack_Cas_Utilisation_V1.1.docx`.

## Conventions

Mermaid n’a pas de diagramme de cas d’utilisation UML natif. Les diagrammes ci-dessous utilisent un `flowchart` :

- rectangle bleu : acteur ;
- ovale : cas d’utilisation, numéroté `UC-nnn` ;
- ovale en pointillés : cas d’un autre module, montré pour la relation ;
- flèche pleine : l’acteur déclenche le cas ;
- flèche en pointillés `«include»` : le cas source inclut toujours le cas cible ;
- flèche en pointillés `«extend»` : le cas source étend le cas cible dans certaines conditions (la flèche part de l’extension).

Acteurs : **Utilisateur** (seul acteur humain) et **Planificateur système** (déclenchements automatiques : clôture de budget, alertes, occurrences récurrentes, sauvegarde automatique).

## 0. Vue d’ensemble des modules

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    P["Planificateur système"]:::actor
    subgraph SYS["FinTrack — modules"]
    M_CO(["Comptes et devises<br/>8 cas"])
    M_CA(["Catégories et<br/>qualification<br/>3 cas"])
    M_TR(["Saisie des opérations<br/>9 cas"])
    M_HC(["Historique et<br/>corrections<br/>7 cas"])
    M_DB(["Tableau de bord<br/>2 cas"])
    M_EP(["Épargne<br/>5 cas"])
    M_DE(["Dettes et créances<br/>4 cas"])
    M_BU(["Budgets<br/>4 cas"])
    M_AL(["Alertes et paramètres<br/>4 cas"])
    M_PV(["Prévisions et<br/>récurrences<br/>4 cas"])
    M_AN(["Analyses et rapports<br/>5 cas"])
    M_SA(["Sauvegarde, export et<br/>accès<br/>9 cas"])
    end
    U --> M_CO
    U --> M_CA
    U --> M_TR
    U --> M_HC
    U --> M_DB
    U --> M_EP
    U --> M_DE
    U --> M_BU
    U --> M_AL
    U --> M_PV
    U --> M_AN
    U --> M_SA
    P --> M_BU
    P --> M_AL
    P --> M_PV
    P --> M_SA
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class M_CO,M_CA,M_TR,M_HC,M_DB,M_EP,M_DE,M_BU,M_AL,M_PV,M_AN,M_SA uc
```

## 1. Comptes et devises

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Comptes et devises"]
    co_creer(["UC-001<br/>Créer un compte"])
    co_consulter(["UC-002<br/>Consulter les comptes et<br/>les soldes"])
    co_modifier(["UC-003<br/>Modifier un compte"])
    co_desactiver(["UC-004<br/>Désactiver un compte"])
    fx_ref(["UC-005<br/>Définir la devise de<br/>référence"])
    fx_taux(["UC-006<br/>Définir ou modifier le<br/>taux de change USD/CDF"])
    fx_histo(["UC-007<br/>Consulter l’historique<br/>des taux"])
    fx_conv(["UC-008<br/>Convertir un montant<br/>(inclus)"])
    tr_transfert(["UC-015<br/>Enregistrer un transfert"]):::ext
    tr_depense(["UC-013<br/>Enregistrer une dépense"]):::ext
    de_remb_creance(["UC-037<br/>Enregistrer le<br/>remboursement d’une<br/>créance"]):::ext
    end
    U --> co_creer
    U --> co_consulter
    U --> co_modifier
    U --> co_desactiver
    U --> fx_ref
    U --> fx_taux
    U --> fx_histo
    fx_conv -.->|"«extend»"| tr_transfert
    fx_conv -.->|"«extend»"| tr_depense
    fx_conv -.->|"«extend»"| de_remb_creance
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class co_creer,co_consulter,co_modifier,co_desactiver,fx_ref,fx_taux,fx_histo,fx_conv uc
```

## 2. Catégories et qualification

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Catégories et qualification"]
    ca_gerer(["UC-009<br/>Gérer les catégories"])
    ca_categoriser(["UC-010<br/>Catégoriser une<br/>transaction (inclus)"])
    ca_qualifier(["UC-011<br/>Qualifier une dépense"])
    tr_depense(["UC-013<br/>Enregistrer une dépense"]):::ext
    tr_revenu(["UC-012<br/>Enregistrer un revenu"]):::ext
    end
    U --> ca_gerer
    U --> ca_qualifier
    tr_depense -.->|"«include»"| ca_categoriser
    tr_revenu -.->|"«include»"| ca_categoriser
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class ca_gerer,ca_categoriser,ca_qualifier uc
```

## 3. Saisie des opérations

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Saisie des opérations"]
    tr_revenu(["UC-012<br/>Enregistrer un revenu"])
    tr_depense(["UC-013<br/>Enregistrer une dépense"])
    tr_financer(["UC-014<br/>Contrôler les fonds et<br/>affecter les sources<br/>(inclus)"])
    tr_transfert(["UC-015<br/>Enregistrer un transfert"])
    tr_paiements(["UC-016<br/>Régler une dépense en<br/>paiements partiels"])
    tr_remb_dep(["UC-017<br/>Enregistrer un<br/>remboursement de dépense"])
    tr_aqualifier(["UC-018<br/>Enregistrer une<br/>opération<br/>« À qualifier »"])
    tr_qualifier_op(["UC-019<br/>Qualifier une opération<br/>en attente"])
    tr_doublon(["UC-020<br/>Traiter un doublon<br/>potentiel"])
    ca_categoriser(["UC-010<br/>Catégoriser une<br/>transaction (inclus)"]):::ext
    fx_conv(["UC-008<br/>Convertir un montant<br/>(inclus)"]):::ext
    hc_recalcul(["UC-027<br/>Historiser et recalculer<br/>les effets (inclus)"]):::ext
    ep_contribuer(["UC-032<br/>Contribuer à un objectif<br/>d’épargne"]):::ext
    ep_retrait(["UC-033<br/>Retirer de l’épargne"]):::ext
    pv_realiser(["UC-049<br/>Réaliser une prévision"]):::ext
    end
    U --> tr_revenu
    U --> tr_depense
    U --> tr_transfert
    U --> tr_paiements
    U --> tr_remb_dep
    U --> tr_aqualifier
    U --> tr_qualifier_op
    U --> tr_doublon
    tr_depense -.->|"«include»"| tr_financer
    tr_depense -.->|"«include»"| ca_categoriser
    tr_revenu -.->|"«include»"| ca_categoriser
    fx_conv -.->|"«extend»"| tr_transfert
    fx_conv -.->|"«extend»"| tr_depense
    tr_paiements -.->|"«extend»"| tr_depense
    tr_doublon -.->|"«extend»"| tr_depense
    tr_doublon -.->|"«extend»"| tr_revenu
    tr_doublon -.->|"«extend»"| tr_transfert
    tr_remb_dep -.->|"«include»"| hc_recalcul
    tr_qualifier_op -.->|"«include»"| hc_recalcul
    ep_contribuer -.->|"«include»"| tr_transfert
    ep_retrait -.->|"«include»"| tr_transfert
    pv_realiser -.->|"«include»"| tr_depense
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class tr_revenu,tr_depense,tr_financer,tr_transfert,tr_paiements,tr_remb_dep,tr_aqualifier,tr_qualifier_op,tr_doublon uc
```

## 4. Historique et corrections

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Historique et corrections"]
    hc_historique(["UC-021<br/>Consulter l’historique<br/>des transactions"])
    hc_rechercher(["UC-022<br/>Rechercher et filtrer<br/>les transactions"])
    hc_events(["UC-023<br/>Consulter l’historique<br/>des événements"])
    hc_modifier(["UC-024<br/>Modifier une transaction"])
    hc_annuler(["UC-025<br/>Annuler une transaction"])
    hc_reaffecter(["UC-026<br/>Réaffecter les sources<br/>de financement"])
    hc_recalcul(["UC-027<br/>Historiser et recalculer<br/>les effets (inclus)"])
    tr_remb_dep(["UC-017<br/>Enregistrer un<br/>remboursement de dépense"]):::ext
    tr_qualifier_op(["UC-019<br/>Qualifier une opération<br/>en attente"]):::ext
    end
    U --> hc_historique
    U --> hc_rechercher
    U --> hc_events
    U --> hc_modifier
    U --> hc_annuler
    U --> hc_reaffecter
    hc_modifier -.->|"«include»"| hc_recalcul
    hc_annuler -.->|"«include»"| hc_recalcul
    hc_reaffecter -.->|"«include»"| hc_recalcul
    tr_remb_dep -.->|"«include»"| hc_recalcul
    tr_qualifier_op -.->|"«include»"| hc_recalcul
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class hc_historique,hc_rechercher,hc_events,hc_modifier,hc_annuler,hc_reaffecter,hc_recalcul uc
```

## 5. Tableau de bord

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Tableau de bord"]
    db_consulter(["UC-028<br/>Consulter le tableau de<br/>bord"])
    db_periode(["UC-029<br/>Choisir la période<br/>d’analyse"])
    end
    U --> db_consulter
    U --> db_periode
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class db_consulter,db_periode uc
```

## 6. Épargne

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Épargne"]
    ep_compte(["UC-030<br/>Désigner ou créer un<br/>compte d’épargne"])
    ep_objectif(["UC-031<br/>Créer un objectif<br/>d’épargne"])
    ep_contribuer(["UC-032<br/>Contribuer à un objectif<br/>d’épargne"])
    ep_retrait(["UC-033<br/>Retirer de l’épargne"])
    ep_suivre(["UC-034<br/>Suivre l’épargne"])
    tr_transfert(["UC-015<br/>Enregistrer un transfert"]):::ext
    end
    U --> ep_compte
    U --> ep_objectif
    U --> ep_contribuer
    U --> ep_retrait
    U --> ep_suivre
    ep_contribuer -.->|"«include»"| tr_transfert
    ep_retrait -.->|"«include»"| tr_transfert
    ep_objectif -.->|"«include»"| ep_compte
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class ep_compte,ep_objectif,ep_contribuer,ep_retrait,ep_suivre uc
```

## 7. Dettes et créances

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Dettes et créances"]
    de_dette(["UC-035<br/>Enregistrer une dette ou<br/>un prêt reçu"])
    de_creance(["UC-036<br/>Enregistrer une créance"])
    de_remb_creance(["UC-037<br/>Enregistrer le<br/>remboursement d’une<br/>créance"])
    de_remb_dette(["UC-038<br/>Rembourser une dette"])
    fx_conv(["UC-008<br/>Convertir un montant<br/>(inclus)"]):::ext
    end
    U --> de_dette
    U --> de_creance
    U --> de_remb_creance
    U --> de_remb_dette
    fx_conv -.->|"«extend»"| de_remb_creance
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class de_dette,de_creance,de_remb_creance,de_remb_dette uc
```

## 8. Budgets

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    P["Planificateur système"]:::actor
    subgraph SYS["FinTrack — Budgets"]
    bu_definir(["UC-039<br/>Définir un budget"])
    bu_modifier(["UC-040<br/>Modifier un budget en<br/>cours de période"])
    bu_suivre(["UC-041<br/>Suivre un budget"])
    bu_cloturer(["UC-042<br/>Clôturer un budget en<br/>fin de période"])
    end
    U --> bu_definir
    U --> bu_modifier
    U --> bu_suivre
    P --> bu_cloturer
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class bu_definir,bu_modifier,bu_suivre,bu_cloturer uc
```

## 9. Alertes et paramètres

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    P["Planificateur système"]:::actor
    subgraph SYS["FinTrack — Alertes et paramètres"]
    al_configurer(["UC-043<br/>Configurer les seuils et<br/>paramètres d’analyse"])
    al_evaluer(["UC-044<br/>Évaluer et émettre les<br/>alertes"])
    al_consulter(["UC-045<br/>Consulter les alertes et<br/>leur historique"])
    al_notif(["UC-046<br/>Configurer les<br/>notifications externes"])
    end
    U --> al_configurer
    P --> al_evaluer
    U --> al_consulter
    U --> al_notif
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class al_configurer,al_evaluer,al_consulter,al_notif uc
```

## 10. Prévisions et récurrences

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    P["Planificateur système"]:::actor
    subgraph SYS["FinTrack — Prévisions et récurrences"]
    pv_creer(["UC-047<br/>Créer une prévision ou<br/>une récurrence"])
    pv_modifier(["UC-048<br/>Modifier, reporter,<br/>suspendre ou annuler une<br/>prévision"])
    pv_realiser(["UC-049<br/>Réaliser une prévision"])
    pv_generer(["UC-050<br/>Générer les occurrences<br/>et mettre à jour les<br/>états"])
    tr_depense(["UC-013<br/>Enregistrer une dépense"]):::ext
    end
    U --> pv_creer
    U --> pv_modifier
    U --> pv_realiser
    P --> pv_generer
    pv_realiser -.->|"«include»"| tr_depense
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class pv_creer,pv_modifier,pv_realiser,pv_generer uc
```

## 11. Analyses et rapports

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    subgraph SYS["FinTrack — Analyses et rapports"]
    an_depenses(["UC-051<br/>Analyser les dépenses"])
    an_habitudes(["UC-052<br/>Détecter petites<br/>dépenses et dépenses<br/>répétitives"])
    an_comparer(["UC-053<br/>Comparer deux périodes"])
    an_mensuel(["UC-054<br/>Consulter le bilan<br/>mensuel"])
    an_annuel(["UC-055<br/>Consulter la synthèse<br/>annuelle"])
    end
    U --> an_depenses
    U --> an_habitudes
    U --> an_comparer
    U --> an_mensuel
    U --> an_annuel
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class an_depenses,an_habitudes,an_comparer,an_mensuel,an_annuel uc
```

## 12. Sauvegarde, export et accès

```mermaid
flowchart LR
    U["Utilisateur"]:::actor
    P["Planificateur système"]:::actor
    subgraph SYS["FinTrack — Sauvegarde, export et accès"]
    sa_sauvegarder(["UC-056<br/>Sauvegarder les données"])
    sa_auto(["UC-057<br/>Sauvegarder<br/>automatiquement"])
    sa_restaurer(["UC-058<br/>Restaurer une sauvegarde"])
    sa_csv(["UC-059<br/>Exporter les<br/>transactions en CSV"])
    sa_export_av(["UC-060<br/>Exporter en Excel ou en<br/>PDF"])
    sa_auth_creer(["UC-061<br/>Créer l’accès à<br/>l’application"])
    sa_auth(["UC-062<br/>S’authentifier et se<br/>déconnecter"])
    sa_auth_mdp(["UC-063<br/>Changer le mot de passe"])
    sa_auth_recup(["UC-064<br/>Récupérer l’accès avec<br/>le code de récupération"])
    end
    U --> sa_sauvegarder
    P --> sa_auto
    U --> sa_restaurer
    U --> sa_csv
    U --> sa_export_av
    U --> sa_auth_creer
    U --> sa_auth
    U --> sa_auth_mdp
    U --> sa_auth_recup
    sa_auth_recup -.->|"«extend»"| sa_auth
    style SYS fill:#F7F9FC,stroke:#1F3A5F
    classDef actor fill:#DCE6F2,stroke:#1F3A5F,stroke-width:2px,color:#1F3A5F
    classDef ext fill:#FFFFFF,stroke:#7A8699,stroke-dasharray: 4 3,color:#444
    classDef uc fill:#FFFFFF,stroke:#1F3A5F,color:#111
    class sa_sauvegarder,sa_auto,sa_restaurer,sa_csv,sa_export_av,sa_auth_creer,sa_auth,sa_auth_mdp,sa_auth_recup uc
```

## Catalogue des cas d’utilisation

| ID | Cas d’utilisation | Module | Acteur | Lot |
|---|---|---|---|---|
| UC-001 | Créer un compte | Comptes et devises | Utilisateur | 1 |
| UC-002 | Consulter les comptes et les soldes | Comptes et devises | Utilisateur | 1 |
| UC-003 | Modifier un compte | Comptes et devises | Utilisateur | 1 |
| UC-004 | Désactiver un compte | Comptes et devises | Utilisateur | 1 |
| UC-005 | Définir la devise de référence | Comptes et devises | Utilisateur | 1 |
| UC-006 | Définir ou modifier le taux de change USD/CDF | Comptes et devises | Utilisateur | 1 |
| UC-007 | Consulter l’historique des taux | Comptes et devises | Utilisateur | 1 |
| UC-008 | Convertir un montant (inclus) | Comptes et devises | Aucun (cas inclus) | 1 |
| UC-009 | Gérer les catégories | Catégories et qualification | Utilisateur | 1 |
| UC-010 | Catégoriser une transaction (inclus) | Catégories et qualification | Aucun (cas inclus) | 1 |
| UC-011 | Qualifier une dépense | Catégories et qualification | Utilisateur | 1 |
| UC-012 | Enregistrer un revenu | Saisie des opérations | Utilisateur | 1 |
| UC-013 | Enregistrer une dépense | Saisie des opérations | Utilisateur | 1 |
| UC-014 | Contrôler les fonds et affecter les sources (inclus) | Saisie des opérations | Aucun (cas inclus) | 1 |
| UC-015 | Enregistrer un transfert | Saisie des opérations | Utilisateur | 1 |
| UC-016 | Régler une dépense en paiements partiels | Saisie des opérations | Utilisateur | 3 |
| UC-017 | Enregistrer un remboursement de dépense | Saisie des opérations | Utilisateur | 3 |
| UC-018 | Enregistrer une opération « À qualifier » | Saisie des opérations | Utilisateur | 3 |
| UC-019 | Qualifier une opération en attente | Saisie des opérations | Utilisateur | 3 |
| UC-020 | Traiter un doublon potentiel | Saisie des opérations | Utilisateur | 3 |
| UC-021 | Consulter l’historique des transactions | Historique et corrections | Utilisateur | 1 |
| UC-022 | Rechercher et filtrer les transactions | Historique et corrections | Utilisateur | 1 |
| UC-023 | Consulter l’historique des événements | Historique et corrections | Utilisateur | 1 |
| UC-024 | Modifier une transaction | Historique et corrections | Utilisateur | 1 |
| UC-025 | Annuler une transaction | Historique et corrections | Utilisateur | 1 |
| UC-026 | Réaffecter les sources de financement | Historique et corrections | Utilisateur | 1 |
| UC-027 | Historiser et recalculer les effets (inclus) | Historique et corrections | Aucun (cas inclus) | 1 |
| UC-028 | Consulter le tableau de bord | Tableau de bord | Utilisateur | 1 → 3 |
| UC-029 | Choisir la période d’analyse | Tableau de bord | Utilisateur | 1 |
| UC-030 | Désigner ou créer un compte d’épargne | Épargne | Utilisateur | 2 |
| UC-031 | Créer un objectif d’épargne | Épargne | Utilisateur | 2 |
| UC-032 | Contribuer à un objectif d’épargne | Épargne | Utilisateur | 2 |
| UC-033 | Retirer de l’épargne | Épargne | Utilisateur | 2 |
| UC-034 | Suivre l’épargne | Épargne | Utilisateur | 2 |
| UC-035 | Enregistrer une dette ou un prêt reçu | Dettes et créances | Utilisateur | 2 |
| UC-036 | Enregistrer une créance | Dettes et créances | Utilisateur | 2 |
| UC-037 | Enregistrer le remboursement d’une créance | Dettes et créances | Utilisateur | 2 |
| UC-038 | Rembourser une dette | Dettes et créances | Utilisateur | 2 |
| UC-039 | Définir un budget | Budgets | Utilisateur | 2 |
| UC-040 | Modifier un budget en cours de période | Budgets | Utilisateur | 2 |
| UC-041 | Suivre un budget | Budgets | Utilisateur | 2 |
| UC-042 | Clôturer un budget en fin de période | Budgets | Planificateur système | 2 |
| UC-043 | Configurer les seuils et paramètres d’analyse | Alertes et paramètres | Utilisateur | 2 |
| UC-044 | Évaluer et émettre les alertes | Alertes et paramètres | Planificateur système | 2 → 3 |
| UC-045 | Consulter les alertes et leur historique | Alertes et paramètres | Utilisateur | 2 |
| UC-046 | Configurer les notifications externes | Alertes et paramètres | Utilisateur | 5 |
| UC-047 | Créer une prévision ou une récurrence | Prévisions et récurrences | Utilisateur | 3 |
| UC-048 | Modifier, reporter, suspendre ou annuler une prévision | Prévisions et récurrences | Utilisateur | 3 |
| UC-049 | Réaliser une prévision | Prévisions et récurrences | Utilisateur | 3 |
| UC-050 | Générer les occurrences et mettre à jour les états | Prévisions et récurrences | Planificateur système | 3 |
| UC-051 | Analyser les dépenses | Analyses et rapports | Utilisateur | 2 |
| UC-052 | Détecter petites dépenses et dépenses répétitives | Analyses et rapports | Utilisateur | 3 |
| UC-053 | Comparer deux périodes | Analyses et rapports | Utilisateur | 3 |
| UC-054 | Consulter le bilan mensuel | Analyses et rapports | Utilisateur | 3 |
| UC-055 | Consulter la synthèse annuelle | Analyses et rapports | Utilisateur | 3 |
| UC-056 | Sauvegarder les données | Sauvegarde, export et accès | Utilisateur | 4 |
| UC-057 | Sauvegarder automatiquement | Sauvegarde, export et accès | Planificateur système | 4 |
| UC-058 | Restaurer une sauvegarde | Sauvegarde, export et accès | Utilisateur | 4 |
| UC-059 | Exporter les transactions en CSV | Sauvegarde, export et accès | Utilisateur | 4 |
| UC-060 | Exporter en Excel ou en PDF | Sauvegarde, export et accès | Utilisateur | 5 |
| UC-061 | Créer l’accès à l’application | Sauvegarde, export et accès | Utilisateur | 4 |
| UC-062 | S’authentifier et se déconnecter | Sauvegarde, export et accès | Utilisateur | 4 |
| UC-063 | Changer le mot de passe | Sauvegarde, export et accès | Utilisateur | 4 |
| UC-064 | Récupérer l’accès avec le code de récupération | Sauvegarde, export et accès | Utilisateur | 4 |
