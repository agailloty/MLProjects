# P17 — Prédire les émissions CO2 d'une voiture neuve européenne

Tâche ML : régression sur données tabulaires | Difficulté : ★★☆ | Données : EEA CO2 cars (Agence européenne pour l'environnement)

## Scénario

Un courtier en leasing opérant dans plusieurs pays d'Europe doit chiffrer le coût réel d'un véhicule avant même sa commercialisation. Le malus CO2 (jusqu'à plusieurs dizaines de milliers d'euros en France) et la taxe annuelle de circulation dépendent directement de la valeur WLTP en g/km. Aujourd'hui, ses équipes attendent l'homologation officielle pour connaître la valeur exacte. L'objectif : un modèle qui estime le CO2 WLTP à partir des caractéristiques techniques, pour anticiper le positionnement tarifaire de chaque configuration.

## Enjeux métier

Le règlement européen impose aux constructeurs une cible moyenne de 93,6 g/km WLTP sur les ventes neuves, sous peine d'amende de 95 EUR par g/km excédentaire et par véhicule vendu. Côté acheteur, chaque gramme compte : en France, le malus bascule fortement autour de 118 g/km WLTP. Sur une flotte de 5 000 véhicules par an, une anticipation erronée de 5 g/km représente un enjeu fiscal de l'ordre du million d'euros. Pour un constructeur, prédire finement le CO2 par configuration permet de piloter le mix de ventes (electrified pour abaisser la moyenne).

## Utilisateurs finaux

- Courtiers et loueurs longue durée : chiffrage anticipé du malus et de la taxe par configuration.
- Constructeurs et importateurs : simulation de la conformité de la moyenne de flotte.
- Comparateurs et assureurs : affichage du CO2 estimé sur des fiches techniques incomplètes.

## Données

- Base CO2 des voitures neuves enregistrées en Europe (EEA), table `co2cars_2025Pv31`, 10,8 millions de lignes de records individuels d'immatriculation : https://discodata.eea.europa.eu/sql?query=SELECT%20... (API SQL, échantillon 0,5 % utilisé dans le notebook, 54 169 lignes)
- Page de présentation du jeu : https://www.eea.europa.eu/en/datahub/datahubitem-view/938cbd0e-b4d4-43a1-af14-ef40794484d2
- Enrichissement possible : barèmes de malus par pays (fiscaclaw), taux d'hybridation par marque.

## Formalisation ML

Cible : `ewltp_gkm` (g CO2/km cycle WLTP), restreinte aux motorisations thermiques non hybrides (les électriques n'ont pas de valeur WLTP comparable). Régresseurs : marque, catégorie (Ct), type de carburant (Ft), cylindrée (cm3), puissance (kW), masse à vide (kg), masse en ordre de marche (kg), pays d'enregistrement. Corrélations observées : puissance 0,71, masse en marche 0,59, masse à vide 0,57, cylindrée 0,44. Découpage train/test aléatoire acceptable (données individuelles), mais valider aussi par marque pour éviter l'excès d'optimisme sur les marques bien représentées.

## Modèles et techniques

Baseline : régression linéaire sur log-masse et puissance. Puis gradient boosting avec encodage cible par marque. Extensions : modèles par segment de marché, features d'interaction masse-puissance-ratio, comparaison des médianes par carburant (diesel 143 g/km vs petrol 124 g/km observés).

## Métriques et arbitrage métier

MAE en g/km, avec un tableau de conversion en EUR de malus (France) : l'erreur métier est une erreur d'impôt, pas une erreur symétrique. Une MAE de 5 g/km est acceptable pour prioriser, pas pour établir une facture. Rapporter la MAE par marque et par carburant : le diesel (dispersion plus grande, corr masse 0,82) demande un traitement distinct de l'essence (0,44).

## Démo Hugging Face

Space Gradio : sélection marque, catégorie, carburant, curseurs cylindrée / puissance / masse, sortie CO2 prédit + intervalle + malus français estimé + comparaison aux 10 modèles réels les plus proches de la base.

## Pièges et points de vigilance

- La colonne `innov_tech` (innovations créditées en g/km) décale la valeur WLTP : l'utiliser comme feature est une fuite si l'on prédit la valeur homologuée finale.
- Les hybrides (9,9 %) et électriques (18,8 %) ont des conventions différentes (`z_whkm` en Wh/km, manquant à 72,9 %) : à modéliser séparément.
- La masse en ordre de marche et la masse à vide sont quasi redondantes : en garder une seule.
- Médiane par pays très variable (Norvège 170 g/km, Islande 139) : le pays reflète le mix de ventes, pas la technologie.

## Extensions

Classification du malus (tranches fiscales), prédiction de la conformité moyenne d'une flotte par constructeur, clustering des configurations homogènes pour les comparateurs.
