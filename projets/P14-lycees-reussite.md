# P14 — Détection des lycées à fort risque de décrochage et allocation des moyens

Tâche ML : régression / classification sur données publiques territoriales | Difficulté : ★★☆ | Données : indicateurs lycées, IPS, INSEE

## Scénario

Une académie prépare sa carte des moyens (postes, dispositifs de réussite). Le recteur veut un modèle qui anticipe, à partir des caractéristiques publiques des établissements (résultats, valeur ajoutée, indice de position sociale, secteur), les lycées dont le taux de réussite au bac risque de se dégrader l'an prochain, pour déclencher des accompagnements avant l'année, pas après les résultats.

## Enjeux métier

Les indicateurs de valeur ajoutée des lycées existent précisément parce que la valeur ajoutée n'est pas le résultat brut : un lycée populaire peut avoir un bon effet propre malgré des résultats modestes. L'enjeu de pilotage est double : ne pas stigmatiser les établissements qui réussissent avec des publics difficiles, et détecter ceux qui dérivent. Une dotation de moyens académiques représente des centaines de milliers d'euros par poste et par an : l'arbitrage a un coût concret. Enfin, les familles utilisent ces mêmes indicateurs publics pour l'affectation : la publication des scores est un enjeu de réputation (il existe des sites de presse qui classent les lycées chaque année).

## Utilisateurs finaux

- Rectores, DASEN : carte des moyens, déclenchement d'accompagnements pédagogiques.
- Chefs d'établissement : auto-diagnostic et argumentation de projet.
- Équipes de conseil et associations (type attachés de promotion de la réussite).
- Familles : lecture raisonnée des indicateurs.

## Données

- Indicateurs de résultat des lycées d'enseignement général et technologique : https://www.data.gouv.fr/datasets/indicateurs-de-resultat-des-lycees-denseignement-general-et-technologique
- Page officielle des indicateurs : https://www.education.gouv.fr/recherche-ival
- Indices de position sociale (IPS) des collèges et lycées (data.gouv.fr, chercher IPS).
- INSEE : caractéristiques sociodémographiques des communes.

## Formalisation ML

Cible A : taux de réussite au bac de l'an prochain par établissement (régression). Cible B : probabilité de dégradation de plus de 3 points de la valeur ajoutée (classification). Variables : valeurs passées des indicateurs, IPS, type d'établissement (public/privé, général/techno/pro), secteur géographique, taille des cohorts.

## Modèles et techniques

Baseline : valeur de l'année courante (les indicateurs sont persistants). Puis régression avec régularisation, gradient boosting. Le point pédagogique central : l'évaluation de la valeur ajoutée prédite (effet propre) plutôt que du taux brut.

## Métriques et arbitrage métier

MAE en points de taux de réussite, et rappel sur la classe en dérive. L'asymétrie est forte : rater un lycée qui dérive coûte des élèves en difficulté ; déclencher un accompagnement à tort coûte une visite d'inspection. Définir la matrice de coût avec les utilisateurs.

## Démo Hugging Face

Space Gradio : carte de l'académie ou sélecteur de département, scores par lycée, fiche par établissement (historique de l'indicateur, IPS, prédiction), classement des 10 établissements à surveiller, et avertissement méthodologique permanent sur l'usage (ne pas classer les établissements mais détecter les dérives).

## Pièges et points de vigilance

- Point d'éthique central du catalogue : un modèle de prédiction de réussite scolaire peut dériver vers du tri des élèves ; cadrer strictement l'usage (pilotage des moyens, jamais de l'individu) et le dire dans la démo.
- Petites cohortes : les taux sont instables (lycée de 80 élèves).
- Corrélation IPS/résultats très forte : le modèle risque d'apprendre la sociologie des communes et non l'action de l'établissement ; c'est précisément la discussion sur la valeur ajoutée.

## Extensions

Analyse de l'effet des heures supérieures, comparaison public/privé, analyse du lien avec les indicateurs de vie scolaire, prédiction du taux de mentions.
