# P04 — Prévision de la consommation électrique nationale et pilotage des achats d'énergie

Tâche ML : séries temporelles | Difficulté : ★★★ | Données : éCO2mix (RTE), météo

## Scénario

Un fournisseur d'électricité achète l'énergie pour ses clients en aval. Chaque quart d'heure, il doit annoncer l'équilibre de son portefeuille : s'il annonce 100 MWh et que ses clients en consomment 105, il paie l'écart au prix spot avec une pénalité. Les équipes de pilotage veulent une prévision à J+1 de la consommation agrégée de leur portefeuille, avec intervalles, pour optimiser les achats sur les marchés spot et forward.

## Enjeux métier

Le système électrique français n'a pas de stockage de masse : l'équilibre production-consommation est physique. Les erreurs de prévision se paient au mécanisme d'ajustement, et les années de prix volatifs ont montré que quelques pourcents d'erreur se traduisent par des millions d'euros sur un portefeuille de taille moyenne. La prévision de consommation est littéralement un métier (load forecasting) dans toutes les entreprises du secteur.

## Utilisateurs finaux

- Fournisseurs d'électricité : pilotage des achats, nomination.
- RTE/Enedis et industriels : gestion de la flexibilité et de l'effacement.
- Gestionnaires de réseaux : anticiper les contraintes locales.

## Données

- éCO2mix national temps réel (consommation, production par filière, échanges, raffraîchi toutes les 15 minutes) : https://www.data.gouv.fr/datasets/donnees-eco2mix-nationales-temps-reel-1 et https://www.rte-france.com/donnees-publications/eco2mix-donnees-temps-reel
- Version consolidée sur ODRé : https://odre.opendatasoft.com/explore/dataset/eco2mix-national-tr/
- Consommation annuelle par commune et par secteur (Enedis) pour une variante territoriale : https://data.enedis.fr/explore/dataset/consommation-electrique-par-secteur-dactivite-commune/
- Météo : données publiques Météo-France (open data) ou archives de stations.

## Formalisation ML

Série à prédire : consommation nationale (ou fictive de portefeuille) horaire, horizon 24 à 48 h. Variables exogènes : température (thermosensibilité !), jour de semaine, jours fériés, vacances scolaires, éclairage. C'est le grand classique de la prévision de charge : la consommation française varie de 35 GW à 90+ GW selon la saison et l'heure.

## Modèles et techniques

Baseline saisonnière naïve, puis régression avec effets calendaires, puis gradient boosting avec lag features. Approches avancées : Prophet, DeepAR, TFT ou simple LSTM. Point pédagogique clé : validation par backtesting temporel strict, jamais de découpage aléatoire.

## Métriques et arbitrage métier

MAPE horaire et MAE en MW. Seuil métier : en France, les bons modèles opérationnels descendent sous 2 % d'erreur à J+1 sur le national. Afficher l'erreur en euros à partir d'un prix spot moyen pour matérialiser l'enjeu.

## Démo Hugging Face

Space Gradio : sélecteur de date et de scénario météo (froid +, normal), graphe plotly des 48 h à venir avec intervalle de confiance, comparaison aux valeurs réelles quand elles sont disponibles, et compteur d'écart monétisé.

## Pièges et points de vigilance

- Le temps réel éCO2mix est corrigé a posteriori (consolidation) : utiliser les versions consolidées pour l'entraînement.
- Fuites temporelles : les lags doivent respecter l'horizon.
- La thermosensibilité est asymétrique (plus forte au froid) : un modèle linéaire simple la sous-estime.

## Extensions

Prédire aussi le mix de production et le taux de CO2 g/kWh associé, prévision à maille communale pour anticiper les contraintes de réseau, détection des jours atypiques (grèves, jours fériés décalés).
