# P01 — Estimateur de valeur immobilière à la parcelle

Tâche ML : régression sur données tabulaires géolocalisées | Difficulté : ★★☆ | Données : DVF (data.gouv.fr)

## Scénario

Une agence immobilière de moyenne importance, présente sur trois départements, constate que ses estimations de prix reposent sur l'intuition des négociateurs. Résultat : des biens surestimés qui stagnent des mois en vitrine, des biens sous-estimés vendus trop vite et laissant de la valeur sur la table. La directrice veut outiller ses équipes avec un estimateur objectif, entraîné sur toutes les mutations réellement enregistrées en France.

## Enjeux métier

L'erreur d'estimation moyenne constatée en pratique dépasse souvent 10 % alors que les portails (MeilleursAgents, etc.) affichent des erreurs médianes autour de 5-6 % sur l'appartement. Sur un parc de 200 mandats par an à 300 000 EUR en moyenne, chaque point d'erreur évité représente 600 000 EUR de valeur mieux négociée. L'enjeu est aussi commercial : une estimation instantanée et argumentée est un argument de prise de mandat face aux portails.

## Utilisateurs finaux

- Négociateurs immobiliers : estimation en rendez-vous, argumentaire chiffré face au vendeur.
- Particuliers vendeurs et acquéreurs : contre-estimation avant de signer un mandat.
- Promoteurs et investisseurs : screening d'opportunités sur des volumes importants.

## Données

- Demandes de valeurs foncières (DVF), 5 dernières années de mutations, open data : https://www.data.gouv.fr/datasets/demandes-de-valeurs-foncieres et version géolocalisée : https://www.data.gouv.fr/datasets/demandes-de-valeurs-foncieres-geolocalisees
- Exploration DVF : https://cadastre.data.gouv.fr/dvf
- Enrichissement possible : données cadastrales (surfaces), API Découpage Administratif de data.gouv (communes), indicateurs INSEE par commune (revenus, population).

## Formalisation ML

Cible : prix de vente (ou prix au m²) d'une mutation. Variables : type local, surface réelle bâtie, nombre de pièces, surface terrain, commune, section cadastrale, date de mutation. Découpage temporel strict : entraînement sur les années passées, test sur l'année la plus récente. Attention à l'agrégation : une mutation peut contenir plusieurs lignes (lots), il faut regrouper proprement.

## Modèles et techniques

Baseline : régression linéaire avec features transformés (log-prix, encodage communal par target encoding). Puis gradient boosting (LightGBM/XGBoost). Extensions : ajout de features spatiaux (distance aux commodités), modèles à effet mixte par commune.

## Métriques et arbitrage métier

MAPE et médiane du pourcentage d'erreur, calculées par segment (appartement Paris vs maison rurale) : une erreur moyenne globale masque des disparités énormes. Seuil métier : une estimation est utile si l'erreur médiane sur le segment visé est inférieure à 10 %. Discuter l'intervalle de confiance : le négociateur veut un intervalle, pas un chiffre unique.

## Démo Hugging Face

Space Gradio : formulaire (type de bien, commune, surface, pièces, étage, année) qui retourne estimation + intervalle + les 5 ventes comparables les plus proches utilisées par le modèle. Option : carte folium des prix au m² estimés par maille.

## Pièges et points de vigilance

- Le DVF contient des mutations atypiques (ventes de lots en bloc, dépendances isolées, prix 1 EUR symboliques) : nettoyage obligatoire.
- Fuite de données possible si l'on utilise des features postérieures à la vente.
- Le DVF géolocalisé se limite à certaines années : vérifier la couverture temporelle.

## Extensions

Classification du marché (vendre vite ou pas), détection de sous-évaluation automatique sur flux de petites annonces, modèle par quartier avec embeddings spatiaux.
