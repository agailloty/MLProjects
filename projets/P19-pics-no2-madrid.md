# P19 — Prévoir les jours de pollution NO2 à Madrid

Tâche ML : classification sur données temporelles | Difficulté : ★★★ | Données : portaildatos.madrid.es (qualité de l'air, horaire)

## Scénario

La ville de Madrid active des restrictions de circulation (abaissement des vitesses, interdiction de circulation dans le centre pour les véhicules non labellisés) quand la qualité de l'air se dégrade. Ces protocoles se déclenchent en réaction, souvent trop tard pour prévenir les populations sensibles. L'objectif : prévoir à J+1 si le seuil de 40 µg/m³ de NO2 en moyenne horaire sera dépassé sur au moins une station de mesure, pour préparer la restriction plutôt que de la subir.

## Enjeux métier

L'Organisation mondiale de la santé recommande de ne pas dépasser 40 µg/m³ de NO2 en moyenne annuelle ; Madrid est une des villes européennes les plus exposées au trafic. En 2024, 29,5 % des jours ont connu au moins une station au-dessus de 40 µg/m³ en moyenne horaire. Chaque jour de restriction a un coût économique (livraisons, trajets domicile-travail) et un bénéfice sanitaire : la décision doit être prise la veille au soir, d'où l'intérêt d'une prévision probabiliste.

## Utilisateurs finaux

- Municipalité de Madrid (protocole haute pollution) : activation anticipée des mesures de trafic.
- Applications de santé publique : alerte aux personnes asthmatiques.
- Médias et associations : communication du risque quotidien.

## Données

- Mesures horaires de la qualité de l'air à Madrid, année 2024 (28 stations, 13 polluants, format large avec colonnes h01-h24 et drapeaux de validité v01-v24) : https://datos.comunidad.madrid/dataset/f18aa765-7432-4b39-8b6e-c41799c1c3e0/resource/519afc1d-55e7-4e9a-91c4-af0d71e1fc14/download/2024.csv
- Historique des années précédentes disponible sur le même portail (même schéma).
- Enrichissement possible : météo AEMET (vent, température), calendrier scolaire, calendrier scolaire et événements.

## Formalisation ML

Cible binaire : jour dégradé (une station NO2 > 40 µg/m³ en moyenne horaire), 29,5 % des 366 jours de 2024. Régresseurs : NO2 moyen de la veille (corr lag-1 : 0,79), lag-7 (0,34), mois (forte saisonnalité : 23,4 µg/m³ en janvier contre 10,0 en juin), jour de semaine, valeurs horaires de la veille sur les stations, autres polluants mesurés. Découpage temporel strict : pas de validation croisée aléatoire sur une série chronologique. Baseline : persistance du jour précédent et moyenne glissante 7 jours.

## Modèles et techniques

Baseline : régression logistique sur lag-1 et lag-7. Puis gradient boosting avec features calendaires et retards multi-stations. Extensions : classification multi-seuils (niveaux du protocole municipal), agrégation par station (28 stations NO2) avec prédiction du maximum, ajout de la météo.

## Métriques et arbitrage métier

Rappel sur les jours dégradés et précision : une alerte intempestive mobilise des équipes et dégrade la confiance ; un jour manqué a un coût sanitaire. Comparer systématiquement à la persistance lag-1, qui est déjà un bon prédicteur. Signaler que 2024 ne contient que 366 jours : l'entraînement doit s'appuyer sur plusieurs années du portail.

## Démo Hugging Face

Space Gradio : date + station (ou moyenne ville) → probabilité de jour dégradé demain, niveau de confiance, comparaison avec la persistance, courbe du NO2 des 30 derniers jours.

## Pièges et points de vigilance

- Format large h01-h24 avec drapeaux de validité : ne pas moyenner les heures invalides (97,5 % des heures sont valides, mais les trous sont concentrés sur certaines stations).
- Les magnitudes numériques (8 = NO2, 7 = NO, 12 = NOx...) nécessitent un filtrage explicite.
- 366 jours d'entraînement suffisent à peine : concaténer 2021-2024 pour un modèle robuste.
- Ne pas utiliser les mesures du jour J comme features si la décision doit être prise la veille au soir.

## Extensions

Prévision par station avec hiérarchie (ville → station), injection de données météo externes, impact mesuré des restrictions sur les niveaux observés (analyse causale simple).
