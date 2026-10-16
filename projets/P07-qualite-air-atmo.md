# P07 — Prévision des pics de pollution atmosphérique et déclenchement des mesures

Tâche ML : classification + séries temporelles | Difficulté : ★★★ | Données : Atmo France / Airparif

## Scénario

Une préfecture de région dispose d'un protocole strict : quand l'indice de qualité de l'air ATMO atteint le mauvais niveau, elle peut déclencher la circulation différenciée (vignettes Crit'Air), la gratuité des transports ou l'abaissement des seuils industriels. Problème : la décision doit être annoncée la veille au soir, avant que les mesures de la journée ne soient connues. Un outil de prévision de l'indice du lendemain fiabilise cette décision, aujourd'hui fondée sur l'expertise humaine des ingénieurs Atmo.

## Enjeux métier

La pollution de l'air est responsable en France de plusieurs dizaines de milliers de décès prématurés par an selon Santé publique France, et les épisodes de pollution déclenchent des mesures économiquement coûteuses (restriction de circulation, arrêt de la production pour les ICPE concernées). Se tromper dans les deux sens coûte cher : un pic non anticipé nuit à la santé publique, une fausse alerte discrédite le protocole et coûte à l'économie locale.

## Utilisateurs finaux

- Préfectures et services de l'État : déclenchement des mesures du protocole.
- Ingénieurs des associations agréées de surveillance (AASQA type Airparif) : appui à la prévision.
- Établissements sensibles (crèches, hôpitaux), sportifs, asthmatiques : alerte personnelle.
- Collectivités : communication.

## Données

- Indice ATMO quotidien par zone, portail national de données agrégées : portails open data des AASQA ; exemple Paris : https://opendata.paris.fr/explore/dataset/qualite-de-l-air-indice-atmo/
- Airparif open data et API (séries de mesures horaires des polluants) : https://www.airparif.fr et https://www.airparif.fr/actualite/2021/atmo-data-un-acces-unique-aux-donnees-nationales
- Météo France open data pour les variables météo (vent, température, inversion).
- Trafic routier open data des grandes métropoles en exogène.

## Formalisation ML

Cible : indice ATMO du lendemain (sous-indice max des polluants : PM10, PM2.5, O3, NO2, SO2), en classification ordinale ou en régression du sous-indice par polluant puis agrégation. C'est un cas où la modélisation du sous-indice (prédire les PM10 de demain, l'ozone de demain) est plus fine que la cible agrégée.

## Modèles et techniques

Régression avec lags polluants et météo, gradient boosting. L'ozone est estival et photochimique, les particules hivernales et liées au chauffage et aux épandages : deux régimes distincts à modéliser (features de saison, ou deux modèles).

## Métriques et arbitrage métier

Précision de détection des épisodes (rappel sur les jours mauvais/moyens), et taux de fausses alertes. Étant donné l'asymétrie des coûts (pic manqué = santé publique, fausse alerte = économie), construire une matrice de coût et régler le seuil de décision en conséquence.

## Démo Hugging Face

Space Gradio : carte ou sélecteur de zone, prévision de l'indice ATMO de demain par polluant avec code couleur réglementaire, probabilité de franchissement du seuil de déclenchement préfectoral, et recommandation formatée pour le décideur (déclencher / surveiller).

## Pièges et points de vigilance

- Historique hétérogène entre AASQA : choisir une zone principale (Paris, Lyon, Marseille) et l'assumer.
- Changement de l'indice ATMO en 2021 : les séries anciennes ne sont pas directement comparables, la documentation du changement d'indice est obligatoire.
- La météo est le facteur dominant : sans elle, le modèle ne battra pas la saisonnalité.

## Extensions

Prédiction spatialisée par capteur (station de mesure), analyse de l'effet des mesures de circulation sur les jours suivants (inférence causale simple), comparaison des villes.
