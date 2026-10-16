# P22 — Prédire les échecs d'inspection sanitaire à Chicago

Tâche ML : classification | Difficulté : ★★☆ | Données : Chicago Data Portal (Food Inspections)

## Scénario

Le Department of Public Health de Chicago réalise des inspections de restauration : contrôles de routine (canvass), contrôles pour délivrance de licence et inspections à la suite de plaintes. Avec 200 000 inspections sur la période et 24 597 établissements actifs, l'agence doit arbitrer ses ressources d'inspection. L'objectif : un modèle qui prédit la probabilité qu'une inspection se solde par un échec (violations critiques), pour cibler en priorité les établissements et les moments à risque avant même la plainte.

## Enjeux métier

19,1 % des inspections aboutissent à un échec, mais le taux grimpe à 33,5 % quand l'inspection suit une plainte et à 24,5 % dans les écoles. Un échec d'inspection signifie des violations à risque sanitaire (toxi-infections alimentaires) et une éventuelle fermeture administrative. En ciblant les inspections de routine sur les profils à risque, l'agence détecte plus tôt les établissements à problème : l'accord avec le résultat précédent est de 63,9 %, donc un tiers des résultats échappe à la simple persistance.

## Utilisateurs finaux

- Inspecteurs sanitaires : planification des tournées de contrôle.
- Plateformes de livraison et guides : indicateur de fiabilité des restaurants.
- Chaînes de restauration : audit qualité interne préventif.

## Données

- Inspections sanitaires des établissements alimentaires, Chicago Data Portal (Socrata, dataset 4ijn-s7e5), 200 000 lignes (novembre 2015 - octobre 2026) : https://data.cityofchicago.org/resource/4ijn-s7e5.csv?$limit=200000
- Interface de découverte : https://data.cityofchicago.org/Health-Human-Services/Food-Inspections-Dashboard/4ijn-s7e5
- Enrichissement possible : données démographiques par quartier (Census), ancienneté de la licence, météo saisonnière.

## Formalisation ML

Cible binaire : résultat Fail (19,1 %). Régresseurs : type d'établissement (restaurant, épicerie, école...), type d'inspection (canvass, complaint, licence), résultat de l'inspection précédente du même établissement (63,9 % d'accord), quartier, ancienneté de la licence, nombre d'inspections passées. Découpage temporel strict : entraîner sur 2015-2024, tester sur 2025-2026. Baseline : persistance du résultat précédent.

## Modèles et techniques

Baseline : régression logistique sur type d'établissement et type d'inspection. Puis gradient boosting avec features d'historique par établissement (nombre d'échecs passés, délai depuis la dernière inspection). Extensions : modélisation des risques sérieux (violations critiques uniquement), analyse des quartiers.

## Métriques et arbitrage métier

Rappel sur la classe Fail et aire sous la courbe PR : manquer un établissement à risque sanitaire coûte potentiellement des toxi-infections ; inspecter à tort coûte une demi-journée d'agent. Comparer systématiquement à la persistance (63,9 %) et au taux de base (19,1 %). Publier les gains par type d'établissement : les écoles (24,5 % d'échec) justifient un traitement prioritaire même sans signal.

## Démo Hugging Face

Space Gradio : sélection d'un type d'établissement + quartier + historique simulé → probabilité d'échec, facteurs les plus influents, suggestion de priorité d'inspection avec comparaison au taux de base.

## Pièges et points de vigilance

- La colonne violations (texte des violations relevées) est une fuite directe : elle est constatée pendant l'inspection qui produit le résultat. Ne l'utiliser que dans une variante NLP clairement séparée.
- Écarter de la cible les résultats non évaluatifs : Out of Business, No Entry, Not Ready, Business Not Located.
- Le champ risks est absent du CSV Socrata : le résultat (Results) est la seule cible exploitable.
- Les réinspections (Canvass Re-Inspection, 10 % d'échec) suivent une première inspection : les traiter à part pour éviter les doublons d'établissement.

## Extensions

Prédiction du risque sérieux (violations critiques), effet de l'ancienneté du gestionnaire, recommandation de fréquence d'inspection par établissement (approche type contrôle ciblé).
