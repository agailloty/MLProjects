# P10 — Classification satellitaire des cultures et assurance récolte

Tâche ML : classification d'images (vision par satellite) | Difficulté : ★★★★ | Données : RPG (IGN/data.gouv) + Sentinel-2

## Scénario

Un assureur récolte doit estimer, avant la moisson, le rendement probable par parcelle et détecter les aléas (sécheresse, grêle). Sa source de vérité : les images satellitaires Sentinel-2 (gratuites, toutes les 5 jours, 10 m de résolution) et le registre parcellaire graphique (RPG), la déclaration cartographique des cultures des exploitants. Entraîner un modèle de classification de cultures par parcelle, c'est reproduire un outil réellement utilisé par les coopératives, les assureurs et les services de contrôle de la PAC.

## Enjeux métier

L'assurance récolte mobilise chaque année des centaines de millions d'euros d'indemnisation, et la sécheresse de ces dernières années a fait exploser les sinistres agricoles. Connaître la culture réelle et l'état végétatif par parcelle, sans déplacement physique, permet d'estimer les rendements, de tarifer et d'indemniser plus vite et plus justement. Même usage chez les coopératives : organisation des collectes. Et côté État : contrôle des déclarations PAC (fraude au couvert végétal).

## Utilisateurs finaux

- Assureurs récolte et courtiers : tarif et sinistralité par parcelle.
- Coopératives et négociants : planification de collecte.
- Offices agricoles (FranceAgriMer) : statistiques de production.
- Services instructeurs de la PAC : contrôle.

## Données

- Registre parcellaire graphique : îlots et parcelles déclarés à la PAC. Le RPG est diffusé en open data sur data.gouv.fr (historique 2015-2020 en Geopackage pour l'apprentissage automatique, restrictions ensuite : chercher RPG sur data.gouv.fr et lire les conditions). Fiche explicative : https://artificialisation.developpement-durable.gouv.fr/bases-donnees/registre-parcellaire-graphique
- Statistique agricole annuelle Agreste (superficies, rendements, cheptel) : https://www.data.gouv.fr/datasets/agreste-statistique-agricole-annuelle-saa et https://agreste.agriculture.gouv.fr
- Images Sentinel-2 : Copernicus (Sentinel Hub, terrascope ou Google Earth Engine), accès gratuit.

## Formalisation ML

Classification du type de culture (blé tendre, maïs, colza, vignes, prairies...) d'une parcelle à partir de sa série temporelle Sentinel-2 (une image = un cube multi-bandes + NDVI). Le RPG fournit les labels géographiques. Formulation tabulaire : agrégats de séries NDVI (moyennes par mois, amplitude, dates de sommets). Formulation vision : cube de raster par parcelle.

## Modèles et techniques

Baseline : forêt aléatoire sur descripteurs de série NDVI (12-20 features). Puis gradient boosting, puis réseaux récurrents ou temporels (LSTM, Transformer temporel) ou convolution 3D sur cubes. C'est le projet le plus lourd techniquement du catalogue : manipulation de rasters, échantillonnage spatial, jointures géographiques.

## Métriques et arbitrage métier

Accuracy équilibrée par classe (les cultures ne sont pas uniformément réparties), F1 par classe. Seuil métier réaliste : les outils opérationnels dépassent 85 % sur les grandes cultures ; à l'échelle d'une coopérative, cela suffit à prioriser les contrôles terrain mais pas à les supprimer. Le coût des erreurs est asymétrique selon la culture (indemnisation plus forte pour les cultures de valeur).

## Démo Hugging Face

Space Gradio : upload d'une série NDVI (ou sélection d'une parcelle d'exemple), prédiction de la culture avec probabilités par classe, courbe NDVI annotée, et estimation du rendement plausible via Agreste (rendement départemental moyen de la culture prédite).

## Pièges et points de vigilance

- Volume de données : une région administrative dépasse vite le million de parcelles ; sous-échantillonner proprement.
- Nuages : les séries Sentinel-2 sont trouées, il faut du masking et de l'interpolation.
- Le RPG récent est soumis à des conditions de réutilisation : bien citer la licence et choisir les millésimes ouverts.
- Fuite possible : les coordonnées brutes encodées naïvement permettent au modèle de mémoriser la parcelle (et donc sa culture). L'objectif est de généraliser à des parcelles non vues.

## Extensions
Détection des parcelles en dérive (culture déclarée différente de la culture observée), estimation du stress hydrique via bandes SWIR, prédiction du rendement à partir de la fin d'hiver.
