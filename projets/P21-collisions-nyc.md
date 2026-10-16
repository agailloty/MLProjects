# P21 — Prédire la gravité des collisions routières à New York

Tâche ML : classification | Difficulté : ★★☆ | Données : NYC Open Data (Motor Vehicle Collisions)

## Scénario

Le programme Vision Zero de la ville de New York vise zéro tué sur la voie publique. Avec environ 250 000 collisions enregistrées sur moins de trois ans, les équipes ne peuvent analyser chaque rapport manuellement. L'objectif : un modèle qui, dès qu'une collision est signalée, estime la probabilité qu'elle fasse au moins un blessé, pour prioriser l'envoi des secours et cibler les interventions d'ingénierie de la voirie sur les configurations qui produisent les blessures.

## Enjeux métier

43,5 % des collisions enregistrées font au moins un blessé. La gravité varie fortement par facteur contributif : les erreurs de piétons et cyclistes atteignent 83,5 % de blessés, l'inattention du conducteur est le premier facteur quantitatif (60 641 cas). La ville dispose de budgets d'aménagement (carrefours protégés,Limitation de vitesse) : les orienter vers les carrefours et créneaux horaires à plus fort risque de blessure maximise le rendement de chaque dollar investi.

## Utilisateurs finaux

- Département des transports de NYC (DOT) : priorisation des aménagements de voirie.
- Services de secours : hiérarchisation des appels en temps réel.
- Assureurs : tarification et prévention (offres de conduite assistée).

## Données

- Collisions de véhicules à moteur, NYC Open Data (Socrata, dataset h9gi-nx95), 250 000 lignes les plus récentes (août 2023 - juin 2026), 33 colonnes : https://data.cityofnewyork.us/resource/h9gi-nx95.csv?$order=crash_date%20DESC&$limit=250000
- Interface de découverte : https://data.cityofnewyork.us/Public-Safety/Motor-Vehicle-Collisions-Crashes/h9gi-nx95
- Enrichissement possible : volume de trafic par artère (NYC DOT), géométrie des carrefours, météo (NOAA).

## Formalisation ML

Cible binaire : au moins un blessé (43,5 %, classes équilibrées). Régresseurs : heure (18 h-19 h : 48-49 % de blessés, nuit : 40 %), jour de semaine, arrondissement, nombre de véhicules impliqués, types de véhicules, facteurs contributifs du premier véhicule. Découpage temporel : le CSV est trié par date décroissante — re-trier avant toute découpe, entraîner sur les mois anciens, tester sur les plus récents. Baseline : taux de blessés par arrondissement et par tranche horaire.

## Modèles et techniques

Baseline : régression logistique sur heures et facteurs contributifs encodés. Puis gradient boosting avec interactions heure × facteur × arrondissement. Extensions : agrégation par carrefour (géocodage) pour un modèle de risque de site, pondération par gravité (tué vs blessé léger).

## Métriques et arbitrage métier

AUC et F1 (classes équilibrées), mais surtout la courbe précision-rappel côté dispatch : si le modèle sert à prioriser les secours, le rappel des collisions graves prime. Comparer par segment : la nuit le taux de blessés baisse (40 %) mais la gravité par victime monte. Signaler la limite des données : nombre de victimes = colonne cible directe, à ne jamais utiliser comme feature.

## Démo Hugging Face

Space Gradio : formulaire (date, heure, arrondissement, facteur contributif, types de véhicules) → probabilité de blessé, comparaison au taux de base de l'arrondissement, carte des carrefours les plus dangereux.

## Pièges et points de vigilance

- Le facteur contributif « Unspecified » domine (63 650 cas) : c'est une absence d'information, pas une cause — le traiter explicitement.
- Les colonnes véhicules 3 à 5 et facteurs associés sont vides à plus de 90 % : peu exploitables.
- 45,7 % des noms de rues transversales manquants : la localisation fine est incomplète.
- Découpe temporelle obligatoire : le tri décroissant du CSV crée une fuite si on découpe naïvement par position.

## Extensions

Modèle de risque par carrefour avec géocodage, prise en compte des piétons et cyclistes comme classe de gravité séparée, comparaison avec les interventions d'ingénierie déjà réalisées.
