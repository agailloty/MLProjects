# P18 — Qualité des eaux de baignade en Europe

Tâche ML : classification (binaire ou multi-classes) | Difficulté : ★★☆ | Données : EEA Bathing Water Directive (statut 1990-2024)

## Scénario

Une application européenne de tourisme balnéaire (type guide de plages) affiche aujourd'hui un statut officiel qui date de la saison précédente. Un site qui glisse de « excellent » à « suffisant » passe inaperçu jusqu'à l'été suivant. L'objectif : prédire le statut qualité d'un site de baignade pour la saison à venir à partir de son historique, de son type et de sa localisation, et alerter les gestionnaires à l'intersaison.

## Enjeux métier

La directive européenne 2006/7/CE impose des normes microbiologiques ; le badge « excellent » conditionne l'éligibilité au pavillon bleu et, de fait, la fréquentation. 27 571 sites sont recensés en Europe, dont 87,2 % en excellent en 2024 — mais avec des écarts énormes : les eaux douces (rivières) atteignent 47 % de non-excellent, contre 10,4 % pour les eaux côtières. Pour une commune balnéaire, un statut dégradé a un impact direct sur la saison touristique ; pour un baigneur, un risque sanitaire.

## Utilisateurs finaux

- Offices de tourisme et gestionnaires de sites : anticipation des déclassements et plan de correction (assainissement).
- Applications de baignade : affichage d'un risque estimé par site.
- Agences environnementales nationales : ciblage des contrôles de laboratoire.

## Données

- Statut qualité des eaux de baignade européennes 1990-2024 (EEA), 672 629 lignes, 27 571 sites, 31 pays : https://sdi.eea.europa.eu/datashare/s/J86aarkSmCMXpkc/download (archive zip contenant le classeur `bw_assessment_eea_datahub_1990_2024.xlsx`)
- Page de présentation : https://www.eea.europa.eu/en/analysis/topics/bathing-water-quality
- Enrichissement possible : occupation du sol, pluviométrie, assainissement par zone.

## Formalisation ML

Cible : statut qualité de la saison (binaire non-excellent 12,8 % en 2024, ou multi-classes excellent / bon / suffisant / mauvais). Régresseurs : type d'eau (côtière 68,7 %, lac 26 %, rivière 4,1 %), pays, latitude, longitude, statuts des saisons passées du même site, nombre de saisons d'historique. Découpage temporel strict : entraîner jusqu'à 2023, tester sur 2024. Baseline naïve : le statut 2023 persiste dans 94,6 % des accords (rappel 80,6 % sur les non-excellents) — tout modèle doit battre cette persistance.

## Modèles et techniques

Baseline : régression logistique sur type + pays + statut précédent. Puis gradient boosting avec features d'historique glissant (nombre de saisons non-excellentes sur 5 ans, tendance). Extensions : modèles spatiaux (auto-corrélation entre sites voisins), calibration des probabilités par pays.

## Métriques et arbitrage métier

Rappel sur la classe non-excellent et F1 : rater un site qui va se dégrader coûte une saison touristique, prédire à tort une dégradation coûte une contre-mesure inutile. La persistance naïve sert de plancher : un modèle utile doit surtout améliorer le rappel des dégradations, pas la précision globale. Publier les performances par type d'eau (rivières vs côtes).

## Démo Hugging Face

Space Gradio : carte de l'Europe avec les sites, filtre pays/type, sélection d'un site → historique du statut, prédiction pour la prochaine saison avec probabilité, comparaison avec la persistance naïve.

## Pièges et points de vigilance

- Déséquilibre fort : 87,2 % d'excellent en 2024 ; l'accuracy globale est trompeuse.
- Les colonnes `monitoringCalendar` (77 % manquant), `management`, `bwProfile` sont des métadonnées souvent vides : peu exploitables.
- Deux conventions de qualité coexistent dans l'historique (directive 76/160 puis 2006/7) : les codes 0-4 changent de sens, ne pas mélanger les périodes sans vérification.
- Les sites apparaissent et disparaissent entre saisons : travailler sur les 21 544 sites communs 2023-2024 pour l'évaluation.

## Extensions

Prédiction multi-classes avec structure ordinale, effet des précipitations hivernales, détection de sites à dégradation rapide, comparaison des politiques de contrôle par pays.
