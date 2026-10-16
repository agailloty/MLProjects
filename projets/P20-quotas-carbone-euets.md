# P20 — Anticiper les installations en dépassement de quotas carbone (EU ETS)

Tâche ML : classification | Difficulté : ★★★ | Données : EU ETS data viewer (EEA)

## Scénario

Le marché carbone européen fonctionne par allocations de quotas (EUA) et vérification annuelle des émissions. Un analyste de marché (trader de quotas, consultant énergie) veut anticiper, avant la publication officielle, quels secteurs et quels pays émettront plus que leur allocation — c'est-à-dire qui devra acheter des quotas, un signal direct sur la demande du marché. Aujourd'hui, cette analyse se fait à la main pays par pays. L'objectif : un modèle qui détecte les dépassements à partir de l'historique du dispositif.

## Enjeux métier

48,8 % des paires pays-secteur-année observées (9 175) ont des émissions vérifiées supérieures à l'allocation totale. Le ratio émissions/allocation médian est de 0,99, avec une corrélation log-log de 0,96 : le marché est globalement équilibré, ce sont les écarts qui comptent. Pour un trader, anticiper la demande nette de quotas de quelques grands secteurs (électricité, sidérurgie, ciment) sur un marché de plusieurs dizaines de milliards d'euros, c'est un avantage immédiat.

## Utilisateurs finaux

- Traders et analystes marché carbone : signaux d'achat/vente long terme.
- Consultants en stratégie carbone : conseil aux industriels sur leur exposition.
- ONG et journalistes : surveillance des secteurs en dérive.

## Données

- Base EU ETS de l'EEA (installations couvertes, allocations et émissions vérifiées, 2005-2025, 90 325 lignes) : https://sdi.eea.europa.eu/datashare/s/aZ7Ti5HkRGEbF62/download (archive zip contenant `ETS_Database_September_2026.xlsx`)
- Page du jeu : https://www.eea.europa.eu/en/datahub/datahubitem-view/df56ae22-e1b3-4d4a-93cd-0e849cd67126
- Enrichissement possible : prix du quota EUA (données de marché), production industrielle Eurostat, prix des énergies.

## Formalisation ML

Cible binaire : émissions vérifiées > allocation totale pour une paire pays × secteur × année (48,8 % de positifs). Régresseurs : année, pays, secteur d'activité, allocation de l'année (corr log 0,96 — le prédicteur dominant), historique du pays-secteur (ratio des années passées, tendance). Découpage temporel strict : entraîner sur 2005-2023, tester sur 2024-2025. Baseline : ratio émissions/allocation de l'année précédente supérieur à 1.

## Modèles et techniques

Baseline : régression logistique sur log-allocation et ratio N-1. Puis gradient boosting avec features d'historique par secteur et par pays. Extensions : régression du ratio directement (cible continue, translation simple en classification), calibration des probabilités, modèles par secteur.

## Métriques et arbitrage métier

Aire sous la courbe PR et F1 (classes à peu près équilibrées, 48,8 %). Coût asymétrique côté trader : un faux positif coûte une position ratée, un faux négatif coûte une position perdue. Rapporter les performances par secteur : l'électricité décarbone vite (les émissions totales passent de 4 574 Mt en 2005 à 2 568 Mt en 2024), les secteurs industriels plus lentement.

## Démo Hugging Face

Space Gradio : sélection pays + secteur + année → probabilité de dépassement, ratio prédit, historique en barres (émissions vs allocation), classement des secteurs à risque pour l'année.

## Pièges et points de vigilance

- Le format long impose un pivot pays × secteur × année : la colonne année contient aussi des lignes de totaux texte, à écarter avant conversion numérique.
- Ne pas confondre les catégories CITL : « 2. Verified emissions » et non « 2.1 EU-ETS Verified Emission » ou la variante gap-filled « 2.1b » de la dernière année.
- Les allocations incluent des sous-catégories (allocation gratuite, enchères, réserve nouveaux entrants) : choisir explicitement la catégorie total.
- L'année 2025 partiellement estimée (gap-filling) : la traiter à part ou l'exclure.

## Extensions

Prédiction du montant net de quotas à acheter (régression), effet du prix du quota sur les émissions, extension au CBAM pour l'analyse import/export.
