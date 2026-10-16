# P13 — Risque de catastrophe naturelle par commune et tarification assurantielle

Tâche ML : classification / régression spatio-temporelle | Difficulté : ★★★ | Données : GASPAR (Géorisques), INSEE, RPG

## Scénario

Le régime français des catastrophes naturelles garantit les particuliers et les entreprises contre des périls non assurables classiquement (inondations, sécheresse-gonflement des argiles, séismes). Les assureurs tarifent cette garantie, provisionnent leurs engagements et cotisent au fonds de réassurance de l'État (CCR). Après les sinistres records de sécheresse (retrait-gonflement des argiles), un assureur veut modéliser, à la maille communale, la probabilité qu'un arrêté de reconnaissance de catastrophe naturelle soit pris, et le nombre d'arrêtés attendu par décennie.

## Enjeux métier

Les épisodes de sécheresse récents ont généré plusieurs milliards d'euros de sinistres provisionnés par les assureurs français, avec des débats publics sur le maintien de la solidarité du régime. La modélisation du risque à maille fine détermine la tarification, la provision et les décisions de réassurance. Les communes, de leur côté, préparent des demandes de reconnaissance (accès à l'indemnisation des sinistrés) et argumentent mieux avec des données. C'est un métier actuariel complet sur données publiques.

## Utilisateurs finaux

- Actaires et assureurs : tarification Cat Nat, provisions.
- Caisse Centrale de Réassurance : mutualisation de l'État.
- Mairies et EPCI : préparation des demandes d'arrêtés, information des habitants.
- Services de l'État (préfectures, BRGM pour l'aléa argiles) : instruction.

## Données

- Base GASPAR : procédures administratives relatives aux risques, dont tous les arrêtés de catastrophe naturelle historiques (catégorie de péril, commune, date) : https://www.georisques.gouv.fr/donnees/bases-de-donnees/procedures-administratives-relatives-aux-risques et https://www.data.gouv.fr/datasets/base-nationale-de-gestion-assistee-des-procedures-administratives-relatives-aux-risques-gaspar
- Recensement des arrêtés Cat Nat : https://www.data.gouv.fr/datasets/arretes-de-catastrophe-naturelle-en-france-metropolitaine-2/
- Aléa retrait-gonflement des argiles (BRGM, cartes d'aléa) et aléas inondation (georisques).
- INSEE pour les variables communales (population, logement, bâti).

## Formalisation ML

Cible : probabilité qu'une commune connaisse au moins un arrêté Cat Nat de péril donné (inondation, RGA) dans les N prochaines années. Variables : historique d'arrêtés, exposition (zone d'aléa, pourcentage de surfaces argileuses, présence de cours d'eau), urbanisation (part de sols artificialisés), climat local. Formulation alternative : comptage d'arrêtés par commune et par décennie (régression de Poisson).

## Modèles et techniques

Gradient boosting sur features communaux, régression de Poisson/négatif binomial pour les comptages, pondération par l'exposition. L'agrégation temporelle (le nombre d'arrêtés par décennie) protège du bruit administratif. Extension naturelle : croiser avec des indicateurs climatiques (séries de précipitations).

## Métriques et arbitrage métier

AUC par péril, calibration des probabilités (un assureur tarife à partir de probabilités calibrées, pas de scores). Pour le comptage : MAE par commune-décennie. Seuil métier : la calibration à 5 points de pourcentage près sur les communes à fort historique est exploitable pour provisionner.

## Démo Hugging Face

Space Gradio : carte de France des scores de risque par commune pour un péril sélectionné, fiche communale (historique des arrêtés, aléas, score), simulateur d'assurance habitation (probabilité de sinistre et prime indicative de la garantie Cat Nat) et visualisation de la montée du risque RGA sur les décennies.

## Pièges et points de vigilance

- Les arrêtés sont des décisions administratives, pas des événements physiques : le signal mélange aléa, exposition et pratique administrative. À discuter explicitement.
- Le RGA a explosé récemment : le passé récent surestime le long terme (ou l'inverse pour l'inondation) : validation temporelle soignée.
- Effet de mode des demandes communales (dynamique de filing après un arrêté voisin).

## Extensions

Modèle par péril, prise en compte du changement climatique (scénarios simples de tendance), couplage avec les données DVF (valeur des biens exposés), prime moyenne par commune.
