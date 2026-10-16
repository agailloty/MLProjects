# MLProjects — Projets machine learning à objet métier, sur données publiques ouvertes

Catalogue de projets ML concrets, fondés sur des données publiques ouvertes : françaises (data.gouv.fr, INSEE, ADEME, opendata des administrations), européennes (Agence européenne pour l'environnement, opendata des villes et régions européennes) et américaines (NYC Open Data, Chicago Data Portal, EPA fueleconomy.gov). Chaque projet est documenté selon la même grille : scénario métier, enjeux chiffrés, utilisateurs finaux, données vérifiées, formalisation ML, métriques orientées décision, démo Hugging Face, pièges et extensions.

Objectif pédagogique : entraîner un modèle sur un vrai problème, puis le déployer dans une interface visuelle (Gradio sur Hugging Face Spaces), en justifiant chaque choix par l'usage métier, pas par la métrique brute.

## Catalogue

| # | Projet | Domaine | Tâche ML | Difficulté |
|---|--------|---------|----------|------------|
| [P01](projets/P01-immobilier-dvf.md) | Estimateur de valeur immobilière à la parcelle | Immobilier | Régression géolocalisée | ★★☆ |
| [P02](projets/P02-securite-routiere.md) | Gravité des accidents et ciblage des points noirs | Sécurité routière | Classification déséquilibrée | ★★☆ |
| [P03](projets/P03-renovation-energetique-dpe.md) | Détection des passoires thermiques | Énergie / bâtiment | Classification ordinale | ★★★ |
| [P04](projets/P04-prevision-electricite.md) | Prévision de la consommation électrique | Énergie | Séries temporelles | ★★★ |
| [P05](projets/P05-ponctualite-ferroviaire.md) | Retards de trains et information voyageurs | Transport | Régression / séries | ★★☆ |
| [P06](projets/P06-carburants-flotte.md) | Prix des carburants et ravitaillement de flotte | Transport / logistique | Séries temporelles + optimisation | ★★☆ |
| [P07](projets/P07-qualite-air-atmo.md) | Prévision des pics de pollution (indice ATMO) | Environnement / santé | Classification ordinale | ★★★ |
| [P08](projets/P08-eau-potable.md) | Performance des services d'eau et priorisation des investissements | Eau / collectivités | Classification / régression | ★★☆ |
| [P09](projets/P09-velib-disponibilite.md) | Disponibilité des vélos partagés et rééquilibrage | Mobilité | Séries multi-stations | ★★★ |
| [P10](projets/P10-cultures-satellite.md) | Classification satellitaire des cultures | Agriculture / assurance | Vision / séries temporelles | ★★★★ |
| [P11](projets/P11-nutriscore-reformulation.md) | Reformulation et Nutri-Score | Agroalimentaire | Classification ordinale | ★★☆ |
| [P12](projets/P12-marches-publics.md) | Détection de marchés publics à risque | Contrôle public / anticorruption | Détection d'anomalies | ★★★ |
| [P13](projets/P13-catastrophes-naturelles.md) | Risque Cat Nat par commune et tarification | Assurance | Classification spatio-temporelle | ★★★ |
| [P14](projets/P14-lycees-reussite.md) | Détection des lycées en dérive | Éducation | Régression / classification | ★★☆ |
| [P15](projets/P15-matching-emploi.md) | Matching compétences-offres d'emploi | Emploi / NLP | NLP, similarité sémantique | ★★★ |
| [P16](projets/P16-urgences-hospitalieres.md) | Prévision des affluences aux urgences | Santé | Séries temporelles | ★★★ |
| [P17](projets/P17-co2-voitures-eea.md) | Émissions CO2 des voitures neuves européennes | Mobilité / climat | Régression | ★★☆ |
| [P18](projets/P18-baignade-europeenne.md) | Qualité des eaux de baignade en Europe | Environnement / santé | Classification | ★★☆ |
| [P19](projets/P19-pics-no2-madrid.md) | Jours de pollution NO2 à Madrid | Environnement / santé | Classification temporelle | ★★★ |
| [P20](projets/P20-quotas-carbone-euets.md) | Dépassements de quotas carbone (EU ETS) | Climat / finance carbone | Classification | ★★★ |
| [P21](projets/P21-collisions-nyc.md) | Gravité des collisions routières à New York | Sécurité routière | Classification | ★★☆ |
| [P22](projets/P22-inspections-chicago.md) | Échecs d'inspection sanitaire à Chicago | Santé publique | Classification déséquilibrée | ★★☆ |
| [P23](projets/P23-tarif-taxi-nyc.md) | Tarif des courses de taxi vert à New York | Transport / mobilité | Régression | ★★☆ |
| [P24](projets/P24-consommation-epa.md) | Consommation des véhicules américains (EPA) | Mobilité / énergie | Régression | ★★☆ |

Toutes les sources de données citées dans les fiches ont été vérifiées en ligne (URLs de data.gouv.fr, opendata Enedis, SNCF, Airparif, Géorisques, ADEME, France Travail, opendata Paris, et pour les projets P17-P24 : Agence européenne pour l'environnement, datos.comunidad.madrid, NYC Open Data, Chicago Data Portal, fueleconomy.gov). Licences à la charge des étudiants : vérifier la mention licence de chaque jeu avant publication (Licence Ouverte ou Etalab dans la grande majorité des cas) et citer la source dans la démo.

## Notebooks d'exploration des données (EDA)

Chaque projet du catalogue dispose d'un notebook Jupyter exécutable dans `notebooks/` : cellule de téléchargement des données depuis la source publique (URL vérifiée), chargement, analyse (distributions, corrélations), résultats observés (exécution du 16/10/2026), puis identification de la variable cible et des régresseurs, avec les pièges à éviter (fuites de données, classes déséquilibrées, découpe temporelle). Index des notebooks et cible ML de chaque projet : `notebooks/README.md`.

## Contrat pédagogique

Chaque binôme ou trinôme rend :

1. Un dépôt Git contenant : notebook ou scripts d'entraînement reproductibles, README du projet rédigé à partir du gabarit ci-dessous, exigence de justifier la découpe train/test et les métriques au regard de l'usage métier décrit dans la fiche.
2. Un Space Hugging Face (Gradio ou Streamlit) utilisable par un non-data-scientiste, avec disclaimer si le cas d'usage le justifie (santé, éducation, accusation de fraude).
3. Une courte démo orale où l'écran est la démo, pas les slides : une décision métier simulée devant le jury.

Barème suggéré : qualité du pipeline de données (20 %), rigueur de l'évaluation (20 %), pertinence métier des métriques et du seuil de décision (20 %), démo Hugging Face (30 %), README et reproductibilité (10 %).

## Gabarit de fiche projet (à copier pour tout nouveau projet)

Voir _TEMPLATE.md.

## Comment ajouter un projet

1. Chercher le jeu de données public d'abord, l'histoire métier ensuite : un projet sans données vérifiables n'entre pas au catalogue.
2. Copier _TEMPLATE.md dans projets/PNN-slug.md et remplir chaque section.
3. Vérifier chaque URL citée et indiquer la licence du jeu.
4. Ajouter la ligne correspondante dans le tableau ci-dessus.
