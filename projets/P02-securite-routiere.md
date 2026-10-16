# P02 — Prédiction de la gravité des accidents routiers et ciblage des zones à traiter

Tâche ML : classification multiclasse déséquilibrée + analyse géographique | Difficulté : ★★☆ | Données : BAAC/ONISR

## Scénario

Un bureau d'études travaille pour une métropole qui doit arbitrer son programme d'investissement de sécurisation routière (ronds-points, abaissement de vitesse, éclairage) entre 40 sites candidats. L'enjeu : ne pas se tromper de priorité, car un aménagement coûte entre 150 000 EUR (simple requalification) et plusieurs millions (giratoire complet). Le bureau propose un modèle qui hiérarchise les points noirs en croisant 20 ans d'accidents corporels géolocalisés.

## Enjeux métier

Chaque accident corporel a un coût humain et économique énorme (le coût moyen d'un tué est évalué à environ 3 millions EUR par les méthodes de la sécurité routière). Les collectivités ont des budgets contraints et des obligations de résultat via les plans locaux de sécurité. Un modèle de hiérarchisation objectif remplace le critère des plaintes des riverains, méthode habituelle et biaisée.

## Utilisateurs finaux

- Services voirie et mobilité des collectivités : arbitrage des investissements.
- Bureaux d'études : production des diagnostics de sécurité (DUER type EDS).
- Assureurs : analyse du risque de sinistralité par zone.
- Observatoires (ONISR, Cerema) : suivi.

## Données

- Bases annuelles des accidents corporels de la circulation, 2005 à 2024 : https://www.data.gouv.fr/datasets/bases-de-donnees-annuelles-des-accidents-corporels-de-la-circulation-routiere-annees-de-2005-a-2024
- Portail open data de l'ONISR : https://www.onisr.securite-routiere.gouv.fr/outils-statistiques/open-data
- Quatre tables liées : caractéristiques, lieux, usagers, véhicules. Exercice de jointure en soi.

## Formalisation ML

Deux formulations possibles. A) Multiclasse : prédire la gravité (indemne/blessé léger/blessé grave/tué) à partir des circonstances (météo, luminosité, type voie, tracé, usagers). B) Agrégation géographique : compter les accidents par tronçon et prédire les tronçons à fort risque à partir des caractéristiques du réseau. La formulation A est plus simple, la B plus fidèle à la décision métier.

## Modèles et techniques

Gradient boosting sur features tabulaires pour la gravité. Pour la version géographique : agrégation par carroyage ou par tronçon, puis régression de comptage (Poisson/négatif binomial), une pratique réelle de la sécurité routière. Détection de points noirs par scoring résiduel : endroits où le modèle prédit moins d'accidents qu'observé.

## Métriques et arbitrage métier

Le rappel sur la classe blessé grave/tué importe plus que l'accuracy globale : rater un futur point noir coûte des vies, alerter à tort coûte une visite de site. Utiliser F2-score ou pondération de coût. Présenter une matrice de confusion en euros, pas en pourcentages.

## Démo Hugging Face

Space Gradio à deux onglets. Onglet 1 : simulateur de circonstances (nuit, pluie, route départementale, moto) avec probabilité de gravité par classe. Onglet 2 : carte de la métropole avec les tronçons colorés par score de risque et le classement des 10 priorités d'aménagement.

## Pièges et points de vigilance

- Déséquilibre extrême des classes (les tués sont rares) : techniques de retraitement obligatoires.
- Biais de déclaration : les accidents sans blessure corporelle n'apparaissent pas dans la base ; le modèle n'apprend pas le risque mais la matérialité. À documenter explicitement.
- Corrélation trafic/accidents absente des données : le modèle ne dit pas tout.

## Extensions

Effet de l'abaissement à 80 km/h (étude avant/après), prédiction de la saisonnalité des accidents, croisement avec les données de trafic ouvertes des métropoles.
