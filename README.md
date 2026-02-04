# Projet — Analyse Exploratoire & Modélisation Prédictive (Compétition Kaggle)

## Réalisé par  
**Oscar Stemmelin, Cristian Carp et Damien Lebas**

---

## Objectif du projet  
Ce projet vise à prédire le **devenir clinique des patients** (classes : *C*, *CL*, *D*) à partir d’un ensemble de variables biologiques et cliniques.  
L’approche combine :

- une **analyse exploratoire approfondie** (EDA)  
- un **nettoyage rigoureux** des données  
- la **gestion avancée des valeurs manquantes**  
- la construction et l’évaluation de plusieurs modèles de machine learning  
- la sélection du **meilleur modèle** selon la *log‑loss* en validation croisée

Le modèle final retenu est un **XGBoost Classifier**, optimisé et validé par une cross‑validation en 10 folds.

---

## Structure du projet  

```
Projet-Analyse-Prediction/
├── analyse_exploratoire.ipynb
├── modelisation_xgboost.ipynb
├── autres_modeles/
│   ├── random_forest.ipynb
│   ├── hist_gradient_boosting.ipynb
│   ├── lightgbm.ipynb
├── data/
│   ├── train.csv
│   ├── test.csv
├── submission/
│   └── submission.csv
└── README.md
```

---

## Analyse exploratoire des données (EDA)

### Détection des valeurs manquantes  
Une fonction dédiée (`missing_values_table`) permet d’afficher :

- le nombre de valeurs manquantes  
- leur pourcentage  
- le type de variable  
- le nombre de modalités uniques  

Les deux bases présentent **plus de 40 % de valeurs manquantes** sur plusieurs variables biologiques (Tryglicerides, Cholesterol, Copper, SGOT, etc.).

### Visualisation avec *missingno*  
Deux graphiques ont été utilisés :

- **Heatmap de corrélation des valeurs manquantes**  
- **Dendrogramme de clustering des patterns de NA**

Ces visualisations montrent :

- des groupes de variables manquantes ensemble (ex. SGOT, Alk_Phos, Spiders, Drug, Hepatomegaly, Ascites)  
- des valeurs manquantes indépendantes (ex. Platelets, Prothrombin)

### Stratégie d’imputation  
- **Variables numériques** → *médiane* + indicateur de valeurs manquantes  
- **Variables catégorielles** → *"Inconnu"*  
- Justification : forte **skewness** des variables numériques → la médiane est plus robuste.

---

## Préparation des données  
Un **Pipeline sklearn** a été construit pour automatiser :

- l’imputation  
- l’encodage One‑Hot  
- l’ajout d’indicateurs NA  
- la transformation cohérente entre train et test

---

## Modélisation & Sélection du modèle

Plusieurs modèles ont été testés :

| Modèle | Log‑loss moyenne | Écart‑type | Commentaire |
|-------|------------------|------------|-------------|
| **XGBoost** | **0.362** | 0.0148 | Meilleur modèle |
| HistGradientBoosting | 0.380 | 0.0149 | Très solide |
| LightGBM | 0.383 | 0.0161 | Bon mais moins performant |
| Random Forest | 0.410 | 0.0220 | Trop imprécis |

### Modèle final : **XGBoost Classifier**

Paramètres principaux :

- `max_depth = 4`  
- `learning_rate = 0.06`  
- `n_estimators = 600`  
- `subsample = 0.6`  
- `colsample_bytree = 0.5`  
- `gamma = 0.6`  
- `min_child_weight = 5`  
- `tree_method = "hist"`

### Validation croisée (10 folds)

- Log‑loss moyenne : **0.3622**  
- Écart‑type : **0.0148**  
- Résultats stables et sans overfitting visible

---

## Génération du fichier de soumission  
Le modèle prédit les probabilités des classes **C**, **CL**, **D**.  
Un fichier `submission.csv` est ensuite généré au format attendu par Kaggle.

---

## Annexes : essais supplémentaires

### Moyenne vs médiane  
La médiane s’est révélée plus robuste en raison de la forte asymétrie des distributions.

### Gestion du déséquilibre des classes  
L’option `class_weight="balanced"` a été testée → **dégradation des performances**.

### Transformation logarithmique  
Tentative de réduire l’impact des valeurs extrêmes → **aucun gain significatif**.

### Modèles alternatifs  
Tous testés avec la même pipeline d’imputation + encodage.

---

## Conclusion  
Ce projet montre l’importance :

- d’une **analyse exploratoire rigoureuse**  
- d’une **gestion intelligente des valeurs manquantes**  
- de la **validation croisée** pour éviter l’overfitting  
- de la **comparaison systématique des modèles**

Le modèle final (**XGBoost**) offre la meilleure performance et une grande stabilité, ce qui en fait un excellent choix pour la prédiction du statut clinique.
