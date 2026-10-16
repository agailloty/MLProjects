# P08 — Performance des services d'eau potable et priorisation des investissements de réseau

Tâche ML : classification / régression sur données publiques territoriales | Difficulté : ★★☆ | Données : SISPEA, SISE-Eaux

## Scénario

Un syndicat mixte regroupe 60 communes rurales et doit arbitrer 3 millions d'euros de subventions entre les réseaux d'eau potable les plus urgents à rénover (renouvellement de canalisations = 300 EUR le mètre en moyenne, des centaines de km en jeu). Les données publiques de performance (rendements de réseau, indice de non-conformité sanitaire, prix au m³) existent service par service : un modèle de scoring de risque permet de classer objectivement les services et de défendre l'arbitrage devant le comité syndical.

## Enjeux métier

Les réseaux français perdent en moyenne près d'une cinquième de l'eau potable en fuites, bien davantage sur les réseaux anciens. Les canalisations arrivent en fin de vie en masse dans les décennies à venir, et les délais administratifs (enquêtes publiques) imposent d'anticiper de plusieurs années. La non-conformité sanitaire déclenche des obligations préfectorales de mise aux normes, très coûteuses pour les petites communes. La priorisation data-driven est un vrai métier dans les services d'eau.

## Utilisateurs finaux

- Collectivités et syndicats des eaux : programmation pluriannuelle des travaux.
- Préfectures (police de l'eau) : ciblage des contrôles et des arrêtés de mise en conformité.
- Bureaux d'études : diagnostics de patrimoine.

## Données

- Services publics d'eau potable : description et indicateurs de performance (SISPEA) : https://www.data.gouv.fr/datasets/services-publics-deau-potable-description-et-indicateurs-de-performance-2
- Résultats du contrôle sanitaire de l'eau distribuée commune par commune : https://www.data.gouv.fr/datasets/resultats-du-controle-sanitaire-de-leau-distribuee-commune-par-commune
- Résultats du contrôle sanitaire de l'eau du robinet (base SISE-Eaux, une ligne par analyse) : https://www.data.gouv.fr/datasets/resultats-du-controle-sanitaire-de-leau-du-robinet
- INSEE pour les variables territoriales (population, densité, revenu fiscal).

## Formalisation ML

Formulation A (risque sanitaire) : prédire la probabilité qu'une commune ait au moins un prélèvement non conforme l'année suivante à partir de l'historique sanitaire et des caractéristiques du service. Formulation B (performance) : prédire le rendement du réseau à partir des caractéristiques du service (longueur, âge estimé, mode de gestion, mode de protection de captage), et scorer l'écart.

## Modèles et techniques

Classification sur données tabulaires déséquilibrées (gradient boosting, calibration des probabilités), régression sur indicateurs agrégés. Attention : les jeux sont publiés par an ; la construction d'un panel pluriannuel est en soi un exercice de data engineering.

## Métriques et arbitrage métier

AUC et rappel à précision fixée pour la formulation A. Pour la formulation B, l'erreur de prédiction du rendement en points de pourcentage : sous 3 points, le score est utilisable en réunion de programmation. Le livrable réel attendu : une liste ordonnée des communes à inspecter en priorité.

## Démo Hugging Face

Space Gradio : carte de France ou sélecteur de département, scoring des services par risque (sanitaire, fuites), fiche détaillée par commune (historique des contrôles, prix au m³, rendement), et simulateur d'investissement : si je renforce le réseau, projection de l'effet sur les indicateurs.

## Pièges et points de vigilance

- Les non-conformités microbiologiques sont ponctuelles et parfois liées à des événements isolés : distinguer non-conformité structurelle et accidentelle.
- Le secret statistique masque des petits services : la couverture est partielle.
- Les analyses au robinet sont par nature des événements rares : bien calibrer.

## Extensions

Prédire le prix au m³ (acceptabilité sociale de l'investissement), relier aux données de sécheresse (arrêtés de restriction), analyse des contrats de délégation (affermage vs régie).
