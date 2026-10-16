# P06 — Prix des carburants : stratégie de ravitaillement pour une flotte

Tâche ML : séries temporelles + optimisation | Difficulté : ★★☆ | Données : prix-carburants.gouv.fr

## Scénario

Une société de transport régionale exploite 120 utilitaires. Le carburant représente son second poste de coûts après les salaires. Chaque semaine, les véhicules font le plein dans des conditions de prix éclatées : entre deux stations voisines, l'écart au litre dépasse souvent 10 centimes. Le responsable d'exploitation veut une application qui, pour un itinéraire, indique quand et où faire du plein au coût total minimal.

## Enjeux métier

Sur un véhicule qui parcourt 30 000 km/an à 8 L/100 km, 2400 litres sont consommés : 10 centimes d'écart par litre représentent 240 EUR par véhicule, soit environ 29 000 EUR par an sur la flotte, uniquement en arbitrage de station. L'enjeu monte encore si l'on intègre la prévision des prix : le plein différé de deux jours quand le prix redescend. Ce sont exactement les problèmes que traitent les outils dits de fuel management.

## Utilisateurs finaux

- Responsables de flotte (transport, logistique, BTP).
- Chauffeurs : instruction de ravitaillement sur l'application d'entreprise.
- Particuliers : version grand public du même service.

## Données

- Prix des carburants en France, flux quotidien (prix station par station à J-1, historique de plusieurs années) : https://www.data.gouv.fr/datasets/prix-des-carburants-en-france-flux-quotidien-1
- Portail officiel : https://www.prix-carburants.gouv.fr/rubrique/opendata/
- Historiques annuels sur le même portail pour l'entraînement sur plusieurs années.

## Formalisation ML

Deux briques. A) Prévision : prix du carburant demain pour une station ou une zone (régression de série temporelle, avec prix du pétrole Brent si accessible en exogène). B) Décision : étant donné un itinéraire et un niveau de réservoir, choisir la station qui minimise le coût total, en combinant écart de prix et détours. La brique B est une optimisation classique, la brique A est le cœur ML.

## Modèles et techniques

Baseline : valeur d'hier, moyenne mobile. Puis modèle par station ou par zone (pooling des stations proches), features calendaires et lags. Un modèle global par zone est plus robuste qu'un modèle par station (les stations isolées ont des trous d'histoire).

## Métriques et arbitrage métier

MAE en centimes par litre. Seuil métier : sous 1,5 centime de marge d'erreur moyenne, la prévision bat l'heuristique du prix d'hier et le gain est net. Quantifier le gain en euros par véhicule et par an sur un scénario simulé : c'est la seule métrique qui parle au décideur.

## Démo Hugging Face

Space Gradio : carte des stations autour d'un point (ou saisie d'une ville), prix du jour, prix prédit demain par station, indicateur plein maintenant ou attends, et simulation annuelle du gain sur une flotte paramétrable (taille, km/an).

## Pièges et points de vigilance

- La base est déclarative (les exploitants de stations sont tenus de transmettre) : il existe des retards et des oublis.
- Stations qui ferment : données survivantes biaisées.
- Ne pas promettre l'arbitrage temporel au-delà de J+2 : au-delà, la prévision est trop faible pour trancher.

## Extensions

Classification des stations en chères/bon marché par zone, effet des changements de taxe et des indémnités inflation, extension à l'électrique (réseau de bornes, bornes open data).
