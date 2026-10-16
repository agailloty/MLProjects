# P09 — Prévision de la disponibilité des vélos partagés et rééquilibrage

Tâche ML : séries temporelles multi-stations | Difficulté : ★★★ | Données : Vélib Métropole (GBFS), Paris Data

## Scénario

L'exploitant du service de vélos en libre-service d'une métropole fait tourner tous les jours des camions qui remontent les vélos des stations pleines (bornes saturées : impossible de rendre son vélo) vers les stations vides (impossible d'en prendre un). Le rééquilibrage se fait aujourd'hui en grande partie en réaction, sur alertes. L'exploitant veut une prévision à 1-2 heures de l'état des stations pour planifier les tournées de camions en début de journée et en soirée.

## Enjeux métier

Une station pleine ou vide est un service dégradé : l'usager ne trouve pas de vélo, l'abonnement paraît moins utile, le churn augmente. En interne, une tournée de camion coûte du carburant et des heures d'équipe : planifier les tournées sur prévision, au lieu d'empiler des alertes, réduit le nombre de tournées pour le même niveau de service. C'est un vrai sujet d'optimisation opérationnelle dans tous les grands systèmes de vélos partagés.

## Utilisateurs finaux

- Équipes d'exploitation (régulateurs, planificateurs de tournées).
- Usagers : fiabilité du service d'attaché.
- Collectivités délégantes : suivi de la qualité de service contractuelle.

## Données

- Vélib : disponibilité en temps réel (stations, vélos mécaniques/électriques, bornes libres), mise à jour minute par minute : https://opendata.paris.fr/explore/dataset/velib-disponibilite-en-temps-reel/
- Flux GBFS complet de Vélib Métropole : https://www.velib-metropole.fr/donnees-open-data-gbfs-du-service-velib-metropole
- Le GBFS temps réel est une API : l'étudiant doit construire son propre historique (échantillonnage) ou utiliser des archives tierces. Variante plus simple : les jeux ouverts de Lyon ou Marseille.

## Formalisation ML

Cible : nombre de vélos disponibles dans N minutes (régression) ou probabilité que la station soit vide/sature dans N minutes (classification). C'est un problème multi-séries (une série par station) avec forte structure spatiale et temporelle (heure de pointe du matin : flux résidence-travail).

## Modèles et techniques

Baseline : valeur actuelle. Puis gradient boosting avec lags et features calendaires par station. Puis modèle global avec embeddings de station (DeepAR, LSTM multi-série). La spatialisation (distance entre stations, clustering de profils) est une extension naturelle.

## Métriques et arbitrage métier

MAE en nombre de vélos, mais surtout le taux de stations critiques non anticipées : parmi les stations vides à 8h, combien ont été signalées vides par la prévision de 7h. C'est la métrique d'exploitation, pas la MAE brute. Simuler un gain de tournées (nombre de camions-heures économisés) sur une journée type.

## Démo Hugging Face

Space Gradio : carte des stations avec état actuel, curseur d'horizon (dans 30/60/120 min), stations à risque en surbrillance, et graphe du profil type d'une station sur 24 h (double pointe, effet météo). Option : mini planificateur de tournée sur 5 stations.

## Pièges et points de vigilance

- Construire un historique propre à partir d'une API temps réel est le premier travail : un script d'échantillonnage doit tourner plusieurs semaines. Prévoir ce délai ou fournir un extrait préconstruit.
- Le dataset temps réel ne donne que l'instant présent : pas de cible sans historique.
- Les stations en maintenance polluent les données (marquer les capacity anomalies).

## Extensions

Effet de la pluie (croiser avec la météo), classification fonctionnelle des stations (résidentielle, gare, loisir) par clustering, prédiction du retour de vélo (durée de trajet).
