# Suivi du projet FinTrack

Dernière mise à jour : 18 octobre 2026

## Structure du dépôt

```
FinTrack/
├── README.md
├── exe/                          (version exécutable, évolution future)
└── Web/
    ├── Documentations/
    │   ├── suivie.md
    │   ├── 01_Cahier_des_charges/
    │   ├── 02_Specifications/
    │   │   ├── 1_Exigences_fonctionnelles/
    │   │   ├── 2_Regles_metier/
    │   │   ├── 3_Cas_utilisation/
    │   │   └── 4_Criteres_acceptation/
    │   ├── 03_Conception/
    │   │   ├── 1_MCD/
    │   │   ├── 2_MLD/
    │   │   ├── 3_Dictionnaire des données/
    │   │   └── 4_Architectures/
    │   ├── 04_API/specification des APIs/
    │   └── 05_UI_UX/
    │       ├── 1_WireFrame/
    │       └── 2_Figma/
    └── code/
        ├── frontend/
        └── backend/              (tests dans backend/tests)
```

## État d'avancement

Légende : ✅ fait · 🔎 fait, à valider · ⏳ à faire

| Élément | Dossier | Version de référence | État |
|---|---|---|---|
| Cahier des charges | 01_Cahier_des_charges | V2.2 (consolidée) | 🔎 |
| Exigences fonctionnelles et non fonctionnelles | 02_Specifications/1_Exigences_fonctionnelles | V2.3 (consolidée, 148 exigences fonctionnelles) | 🔎 |
| Règles métier | 02_Specifications/2_Regles_metier | V1.2 consolidée (V1.1 + décisions complémentaires §12, dont §12.5 à §12.8) | ✅ |
| Cas d'utilisation + diagrammes Mermaid | 02_Specifications/3_Cas_utilisation | V1.1 (64 cas, 12 modules) | 🔎 |
| Critères d'acceptation | 02_Specifications/4_Criteres_acceptation | V1.0 (89 scénarios, 148 EF couvertes) | 🔎 |
| MCD | 03_Conception/1_MCD | V1.0 (37 entités, 52 associations, 20 contraintes) | 🔎 |
| MLD | 03_Conception/2_MLD | — | ⏳ |
| Dictionnaire des données | 03_Conception/3_Dictionnaire des données | — | ⏳ |
| Architecture détaillée | 03_Conception/4_Architectures | — | ⏳ |
| Spécification des API | 04_API | — | ⏳ |
| Wireframes | 05_UI_UX/1_WireFrame | — | ⏳ |
| Maquettes Figma | 05_UI_UX/2_Figma | — | ⏳ |
| Frontend (React + Vite) | code/frontend | — | ⏳ |
| Backend (FastAPI + SQLite) | code/backend | — | ⏳ |
| Version exécutable | exe | — | ⏳ (hors première version) |

## Découpage en lots

| Lot | Contenu |
|---|---|
| 1 | Noyau : comptes, devises USD/CDF et taux historisé, revenus, dépenses avec sources de financement, transferts, catégories, qualification, annulation et historique, soldes, filtres, dashboard de base |
| 2 | Épargne (compte réel), dettes et créances, budgets, alertes budgétaires |
| 3 | Remboursements de dépenses, prévisions et récurrences, doublons, « À qualifier », analyses, rapports |
| 4 | Sauvegarde et restauration, export CSV, authentification (facultative) |
| 5 | Évolutions : export Excel/PDF, alertes avancées, notifications externes, exécutable |

## Historique des documents

| Date | Document | Changement |
|---|---|---|
| 26 août 2026 | Cahier des charges, exigences | Première rédaction |
| 28 août 2026 | Règles métier V1.0, cahier des charges V2.0 | Règles métier formalisées, annexe A au cahier |
| 29 août 2026 | Exigences V2.0 | Mise à jour selon les règles métier |
| 15 octobre 2026 | Règles métier V1.1, exigences V2.1 | Consolidation jusqu'au point 100 |
| 18 octobre 2026 | Cahier des charges V2.1, exigences V2.2 | Réécriture consolidée, lots, liens vers les règles métier |
| 18 octobre 2026 | Cas d'utilisation V1.0 + cas_utilisation.md | 61 cas, 13 diagrammes Mermaid, 19 fiches, couverture des 136 exigences fonctionnelles |
| 18 octobre 2026 | Règles métier V1.2, exigences V2.3, cahier des charges V2.2, cas d'utilisation V1.1 | Taux de change fixé par l'utilisateur et historisé dès l'initialisation, authentification (EF-AU), excédents de remboursement, doublons ; 64 cas, 22 fiches, 147 exigences couvertes |
| 18 octobre 2026 | Critères d'acceptation V1.0 ; règles métier V1.2 §12.5 ; exigences EF-TR-035 ; cas d'utilisation V1.1 | 88 scénarios Étant donné / Quand / Alors, 8 critères transversaux ; décision P-04 appliquée |
| 18 octobre 2026 | Règles métier V1.2 §12.6 à §12.8, exigences EF-TR-039, critères V1.0 | Refus faute de fonds historisé, dépense non planifiée, sauvegarde de sécurité avant restauration |
| 18 octobre 2026 | MCD V1.0 + mcd.md + diagrammes | Modèle conceptuel : opérations non destructives, mouvements de compte, spécialisations, sources de financement, journal d'historique |

## Points ouverts

- Relire les lots attribués aux exigences (colonne « Lot » du document V2.2).
- Valider les règles proposées en V1.2 §12.2 (excédents de remboursement) et §12.3 (doublons).
- Trancher les points ouverts de la section 8 du document des cas d'utilisation : P-02 (motif du taux), P-03, P-06, P-11 (résolus : P-04, P-08, P-09, P-10).
- Confirmer la lecture de « dépense non planifiée » retenue en V1.2 §12.7.
- Décider si le fichier SQLite doit lui-même être chiffré (au-delà de l'authentification).
- Valider le modèle d'historisation proposé dans le MCD (D-01 : opérations non destructives + journal écrit seulement) et les points V-01 à V-07 de la section 9 du MCD.
- Fixer les seuils par défaut (petites dépenses, doublons, alertes) et les règles d'arrondi des conversions.
- Décider si l'authentification est utile pour un usage local.
