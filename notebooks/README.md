# Notebooks — Analyse exploratoire des données (EDA)

Un notebook Jupyter par projet du catalogue. Chaque notebook suit la même structure :

1. **Téléchargement** — cellule qui télécharge les données depuis la source publique (URL vérifiée, cache local dans `data/`, idempotent).
2. **Chargement** — lecture, typage, filtrage éventuel (échantillonnage sur les très gros fichiers).
3. **Analyse** — distributions, agrégats, corrélations (Spearman), baselines naïves quand pertinent.
4. **Résultats observés** — chiffres issus de l'exécution du 16/10/2026.
5. **Variable cible et régresseurs** — cible ML identifiée, features retenues et pièges (fuites de données, déséquilibre de classes, encodage, découpe temporelle).

| Notebook | Projet | Variable cible | Tâche ML |
|---|---|---|---|
| [P01](P01-eda.ipynb) | Prix au m² des ventes immobilières (DVF) | `prix_m2` (valeur foncière / surface) | Régression |
| [P02](P02-eda.ipynb) | Gravité des accidents de la route | `grave` : tué ou blessé hospitalisé (~18 %) | Classification déséquilibrée |
| [P03](P03-eda.ipynb) | Consommation énergétique d'un logement (DPE) | `conso_5_usages_par_m2_ep` (kWh ep/m²/an) | Régression (classification A-G en variante) |
| [P04](P04-eda.ipynb) | Consommation électrique nationale (éco2mix) | `conso` en MW demi-horaire | Série temporelle (baseline : MAE naïf 4 373 MW, J-1 1 065 MW) |
| [P05](P05-eda.ipynb) | Régularité TGV (AQST) | `taux_regularite` mensuel par liaison | Régression / série temporelle |
| [P06](P06-eda.ipynb) | Prix des carburants à la pompe | `Prix` en €/L par (station, carburant) | Régression |
| [P07](P07-eda.ipynb) | Indice de qualité de l'air ATMO | `Indice ATMO` 0-10 | Classification ordinale |
| [P08](P08-eda.ipynb) | Conformité de l'eau du robinet | `plvconformitebacterio` = N (~1 %) | Classification très déséquilibrée |
| [P09](P09-eda.ipynb) | Disponibilité des stations Vélib' | `taux_occupation` (prévision T+30 min) | Régression / flux temps réel |
| [P10](P10-eda.ipynb) | Cultures agricoles par parcelle (RPG) | groupe de culture (`code_group`) | Classification multi-classe |
| [P11](P11-eda.ipynb) | Nutri-Score d'un produit alimentaire | `nutriscore_grade` (a-e) | Classification ordinale |
| [P12](P12-eda.ipynb) | Montant des marchés publics (DECP) | `montant` en € (log10) | Régression |
| [P13](P13-eda.ipynb) | Arrêtés Cat Nat par commune (GASPAR) | commune × risque × année « arrêté » | Classification (dataset à construire) |
| [P14](P14-eda.ipynb) | Réussite au bac des lycées | `Taux de réussite - Toutes séries` | Régression |
| [P15](P15-eda.ipynb) | Métiers et compétences (ROME) | grand domaine ROME (16 classes) | Classification haute dimension / NLP |
| [P16](P16-eda.ipynb) | Passages aux urgences (SurSaUD) | `nbre_pass_tot` quotidien (J+7) | Série temporelle |
| [P17](P17-eda.ipynb) | CO2 WLTP des voitures neuves (EEA) | `ewltp_gkm` (g/km, thermiques) | Régression |
| [P18](P18-eda.ipynb) | Qualité des eaux de baignade en Europe (EEA) | non-excellent en 2024 (~12,8 %) | Classification déséquilibrée |
| [P19](P19-eda.ipynb) | Qualité de l'air horaire à Madrid (2024) | jour dégradé NO2 > 40 µg/m³ (~29,5 %) | Classification temporelle |
| [P20](P20-eda.ipynb) | EU ETS : émissions vérifiées vs allocations (EEA) | émissions > allocation (~48,8 %) | Classification |
| [P21](P21-eda.ipynb) | Collisions routières à New York | au moins un blessé (~43,5 %) | Classification |
| [P22](P22-eda.ipynb) | Inspections sanitaires à Chicago | `Fail` (~19,1 %) | Classification déséquilibrée |
| [P23](P23-eda.ipynb) | Tarif des courses de taxi vert à New York | `fare_amount` en USD (courses standard) | Régression |
| [P24](P24-eda.ipynb) | Consommation des véhicules américains (EPA) | `comb08` (MPG, thermiques) | Régression |

Les notebooks P17-P24 couvrent des données européennes (Agence européenne pour l'environnement, datos.comunidad.madrid) et américaines (NYC Open Data, Chicago Data Portal, fueleconomy.gov).

Chaque notebook se réexécute de bout en bout : le téléchargement est sauté si le fichier est déjà présent dans `data/`. Les chiffres de la section 4 correspondent à l'exécution du 16 octobre 2026 ; les sources vivantes (flux temps réel, APIs paginées) peuvent évoluer d'un jour à l'autre.

Enchaînement conseillé pour les étudiants : exécuter l'EDA de son projet, puis repartir de la section 5 (cible + régresseurs) pour construire le pipeline d'entraînement et la démo Hugging Face.
