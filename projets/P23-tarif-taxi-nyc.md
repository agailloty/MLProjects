# P23 — Prédire le tarif d'une course de taxi vert à New York

Tâche ML : régression | Difficulté : ★★☆ | Données : NYC TLC Trip Record Data (green taxi)

## Scénario

La Taxi and Limousine Commission de New York publie les courses réelles des taxis verts (boroughs). Une application de comparateur de courses (taxi vs VTC) veut afficher une estimation fiable du tarif taxi avant la commande, à partir de la distance et de la durée estimées par son moteur de routage. L'objectif : un modèle qui reproduit le compteur, y compris les majorations nocturnes et l'heure de pointe, pour un affichage instantané.

## Enjeux métier

Sur les courses standard (ratecodeid 1, 90,7 % des 250 000 courses extraites), le tarif médian est de 9,50 USD pour 2 miles. La corrélation distance-tarif est de 0,96 après nettoyage : le problème est très faisable, ce qui en fait un excellent projet pédagogique de régression. L'enjeu métier est la confiance : une estimation affichée à plus ou moins 1 USD sur une course de 10 USD suffit à faire basculer le choix taxi/VTC ; une estimation erronée sur les forfaits aéroport détruit la crédibilité de l'app.

## Utilisateurs finaux

- Comparateurs de mobilité : estimation préalable du tarif taxi.
- La TLC elle-même : détection de compteurs frauduleux (écart entre tarif prédit et déclaré).
- Chauffeurs : simulation du revenu par tranche horaire.

## Données

- Courses de taxis verts 2019, NYC Open Data (Socrata, dataset q5mz-t52e), 250 000 courses : https://data.cityofnewyork.us/resource/q5mz-t52e.csv?$limit=250000
- Interface de découverte : https://data.cityofnewyork.us/Transportation/2019-Green-Taxi-Trip-Data/q5mz-t52e
- Grille tarifaire officielle TLC : https://www.nyc.gov/site/tlc/passengers/taxi-fare.page
- Enrichissement possible : météo (majoration pluie), événements, données de trafic.

## Formalisation ML

Cible : `fare_amount` (USD, hors pourboires et surcharges) restreinte aux courses standard (ratecodeid 1), après nettoyage des anomalies : fares négatifs (0,4 %), nuls (0,2 %), distances nulles (1,6 %), dates aberrantes (quelques courses 2009). Régresseurs : distance en miles (corr 0,96), durée de course en minutes (0,96), heure de prise en charge (majoration 20 h - 6 h), jour de semaine, zones de départ et d'arrivée, nombre de passagers. Découpage temporel : entraîner sur janvier-mai, tester sur juin-juillet.

## Modèles et techniques

Baseline : tarif = 2,50 USD + 0,50 USD/1/5 mile + majorations, calibré sur la grille TLC officielle, MAE en dollars. Puis gradient boosting. Extensions : détection d'anomalies (écart tarif déclaré vs prédit) pour la TLC, modèle séparé par ratecode (JFK forfait 52 USD).

## Métriques et arbitrage métier

MAE en USD et pourcentage d'estimations à plus ou moins 1 USD : c'est le seuil d'utilisabilité du comparateur. Comparer la MAE à la baseline grille officielle : si le modèle appris ne bat pas la grille tarifaire publique, il n'apporte rien. Publier la MAE par plage de distance : les longues courses supportent une erreur absolue plus grande, pas l'utilisateur.

## Démo Hugging Face

Space Gradio : départ/arrivée (zones TLC) + heure + distance estimée → tarif prédit, intervalle, comparaison avec la grille officielle, décomposition (base + distance + temps + majoration).

## Pièges et points de vigilance

- Exclure les ratecodes non standard : forfait JFK (52 USD), négocié (ratecode 5), courses en groupe — sinon le modèle mélange des régimes tarifaires.
- La congestion surcharge et les taxes sont des colonnes séparées : définir précisément ce qu'on prédit (fare_amount seul, ou total).
- Quelques dates aberrantes (2009) dans l'extraction : filtrage sur la période 2019 obligatoire.
- Le pourboire (tip) reflète le paiement par carte : ne jamais l'inclure dans la cible.

## Extensions

Détection de compteurs anormaux (résidus), prédiction du montant total avec surcharges, prédiction de la demande par zone et par heure, comparaison des revenus taxi vert vs jaune.
