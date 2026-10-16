# P24 — Prédire la consommation des véhicules américains (EPA)

Tâche ML : régression | Difficulté : ★★☆ | Données : fueleconomy.gov (EPA, vehicle fuel economy)

## Scénario

Une société de gestion de flotte américaine renouvelle 400 véhicules par an. Le coût carburant annuel de la flotte dépend directement de la consommation combinée (MPG) de chaque modèle, mais les fiches constructeurs arrivent tard et de façon incomplète. L'objectif : un modèle qui estime le MPG combiné d'un véhicule à partir de ses caractéristiques techniques (motorisation, masse, transmission, classe), pour arbitrer les commandes avant la publication des valeurs EPA.

## Enjeux métier

La base EPA couvre 50 407 véhicules de millésimes 1984 à 2027 et 146 marques : c'est la référence réglementaire américaine (normes CAFE). Le MPG combiné médian des thermiques est de 20, avec des extrêmes de 7 (pickups standards) à 59. Sur 400 véhicules roulant 20 000 miles par an à 3,50 USD le gallon, chaque MPG d'écart change la facture annuelle de plusieurs dizaines de milliers de dollars. La corrélation négative avec la cylindrée (-0,82) et le nombre de cylindres (-0,78) rend le problème très accessible.

## Utilisateurs finaux

- Gestionnaires de flotte : arbitrage des commandes sur coût total de possession.
- Courtiers en leasing et comparateurs : affichage du MPG estimé de configurations non publiées.
- Constructeurs et consultants : simulation de l'effet d'un choix moteur sur la moyenne CAFE.

## Données

- Base véhicules EPA (consommations, émissions, caractéristiques techniques), fueleconomy.gov : https://www.fueleconomy.gov/feg/epadata/vehicles.csv.zip (décompressé : 50 407 lignes, 84 colonnes)
- Page du jeu et dictionnaire des variables : https://www.fueleconomy.gov/feg/download.shtml
- Enrichissement possible : prix des carburants par État (EIA), barèmes CAFE par année.

## Formalisation ML

Cible : `comb08` (MPG combiné) sur les motorisations thermiques (électricité exclue, 3,2 % du jeu, mesurée en MPGe). Régresseurs : marque, classe de véhicule, cylindres (-0,78), cylindrée (-0,82), année (amélioration structurelle, corr 0,27), type de transmission, traction, carburant. Découpage aléatoire acceptable, mais valider aussi en découpe temporelle (entraîner sur 1984-2023, prédire les millésimes 2024-2027) pour simuler le cas d'usage réel. Baseline observée : régression linéaire, MAE 2,7 MPG, R2 0,52.

## Modèles et techniques

Baseline : régression linéaire sur log-cylindrée. Puis gradient boosting avec encodage cible par marque-classe. Extensions : prédiction du MPG urbain et autoroutier séparément, prise en compte des motorisations hybrides comme segment propre.

## Métriques et arbitrage métier

MAE en MPG convertie en dollars de carburant annuels (20 000 miles/an, prix du gallon) : c'est la langue du gestionnaire de flotte. Une MAE de 2 MPG est acceptable pour pré-arbitrer une commande, pas pour promettre un coût. Rapporter la MAE par classe : les vans et pickups (13-15 MPG) tolèrent des erreurs relatives plus grandes que les compactes.

## Démo Hugging Face

Space Gradio : sélection marque, classe, carburant, curseurs cylindrée/cylindres/année, transmission → MPG prédit, coût annuel estimé en carburant, classement des modèles réels les plus proches de la configuration.

## Pièges et points de vigilance

- 81 des 84 colonnes sont des sorties EPA (city08, highway08, co2, fuelCost...) : les utiliser comme features est une fuite massive. Garder uniquement les caractéristiques d'entrée.
- Les véhicules électriques (MPGe) et à hydrogène suivent une autre métrique : à exclure de la cible.
- Les millésimes 2026-2027 sont partiellement des valeurs constructeurs déclarées : les isoler pour l'évaluation.
- Le champ `drive` et `trany` sont manquants sur 2-3 % des lignes : imputation ou exclusion explicite.

## Extensions

Prédiction du coût annuel complet (carburant + émissions CO2), effet des normes CAFE par décennie, comparaison thermique vs hybride à classe constante, recommandation de flotte sous contrainte de budget.
