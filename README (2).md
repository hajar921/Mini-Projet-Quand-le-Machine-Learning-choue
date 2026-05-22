# Mini-Projet : Quand le Machine Learning Échoue

**ECC — Printemps 2026 | Introduction à l'IA et au Machine Learning**

**Auteurs :** Hafid Douae, Ouamalich Aasmaa, Sraouni Hajar
**Enseignantes :** Kawtar Zerhouni & Rym Nassih
**Date :** Mai 2026

---

## Présentation

Ce projet diagnostique et corrige trois modes de défaillance d'un Random Forest sur le jeu de données **Online Shoppers Purchasing Intention (UCI 468)** :

| Mode | Statut | Effet observé |
|---|---|---|
| **Déséquilibre de classe** (principal) | ✅ Confirmé | Effondrement du rappel sous 5 % de positifs ; correction synergique pondération + régularisation : **+45 pts de rappel** |
| **Overfitting** (bonus 1) | ✅ Confirmé | Écart Train-Test de **36 pts** avec profondeur illimitée → 12 pts avec `max_depth=8` |
| **Raccourci Month** (bonus 2) | ❌ Non confirmé | Month porte un signal causal saisonnier légitime |

## Contenu du dossier

```
.
├── README.md                              # Ce fichier
├── requirements.txt                       # Dépendances Python
├── Mini_Projet_ML_When_ML_Fails.ipynb     # Notebook reproductible (toutes les expériences)
├── Rapport_Mini_Projet_ML.docx            # Rapport au format Word
├── Rapport_Mini_Projet_ML.pdf             # Rapport au format PDF
├── online_shoppers_intention.csv          # Jeu de données
└── plots/                                 # Figures générées
    ├── fig1_reference_confusion.png
    ├── fig2_feature_importances.png
    ├── fig3_imbalance_curves.png
    ├── fig4_confusion_5pct.png
    ├── fig5_overfitting.png
    └── fig6_month_investigation.png
```

## Reproductibilité

Toutes les expériences utilisent une graine principale `PRIMARY_SEED = 42` et la liste `SEEDS = [42, 7, 123, 456, 2024]` pour les analyses multi-graines. Les chiffres rapportés sont des moyennes ± écarts-types sur 5 graines distinctes.

## Comment exécuter le notebook

### 1. Créer un environnement et installer les dépendances

```bash
python -m venv venv
source venv/bin/activate   # Linux/Mac
# ou: venv\Scripts\activate sur Windows
pip install -r requirements.txt
```

### 2. Lancer le notebook

```bash
jupyter notebook Mini_Projet_ML_When_ML_Fails.ipynb
```

Puis exécuter toutes les cellules dans l'ordre (`Cell → Run All`).

## Pipelines comparés

Conformément au §8.1 de l'énoncé, le pipeline **défaillant** (modèle Standard) et le pipeline **corrigé** (modèle D) sont exécutables indépendamment :

**Pipeline défaillant** (sections 3 et 4 du notebook, configuration `A_standard`) :
```python
RandomForestClassifier(n_estimators=100, random_state=42, n_jobs=-1)
```

**Pipeline corrigé** (configuration `D_full_fix`) :
```python
RandomForestClassifier(n_estimators=100, random_state=42, n_jobs=-1,
                        class_weight='balanced',
                        max_depth=8, min_samples_leaf=5)
```

À un déséquilibre de 5 % :
- Pipeline défaillant : Rappel = 24,61 % ± 0,94 %, F1 = 38,70 % ± 1,11 %
- Pipeline corrigé : Rappel = **69,90 %** ± 0,79 %, F1 = **64,40 %** ± 0,25 %

Le test t apparié donne p = 3 × 10⁻⁷ pour le gain de rappel.

## Structure du rapport

Le rapport (`Rapport_Mini_Projet_ML.docx`) suit la structure obligatoire §8.2 de l'énoncé :

1. Question de recherche et jeu de données choisi
2. Modèle de référence et symptôme observé
3. Hypothèse causale et expérience contrôlée
4. Correction proposée et évaluation
5. Menaces à la validité
6. Conclusion et enseignements
7. Bonus 1 — Diagnostic d'Overfitting
8. Bonus 2 — Enquête sur Month comme Raccourci Suspect

## Lien avec les Labs

- **Lab 1 — SECOM** (contrôle qualité semi-conducteurs, ~5,9 % de défauts) : situation représentative du mode principal étudié ici. La correction validée (pondération + régularisation) est directement transférable.
- **Lab 2 — MetroPT-3** (maintenance prédictive compresseurs, pannes très rares) : applicable avec en plus un split temporel rigoureux (mentionné dans les menaces à la validité §5.5).
