# P11 — Reformulation produits et prédiction du Nutri-Score

Tâche ML : classification ordinale / régression multi-cibles sur données nutritionnelles | Difficulté : ★★☆ | Données : Open Food Facts

## Scénario

Un industriel agroalimentaire veut reformuler une gamme (biscuiterie) pour améliorer ses Nutri-Scores : depuis 2025, l'apposition du Nutri-Score est en déploiement en France, et les distributeurs rejettent de plus en plus les produits D/E de leurs linéaires. L'équipe R&D veut un simulateur : modifier les paramètres nutritionnels (sel, sucres, fibres, matières grasses) et voir instantanément la classe Nutri-Score bouger, pour arbitrer les recettes sans attendre des calculs réglementaires.

## Enjeux métier

Le Nutri-Score détermine l'accès aux linéaires (certains distributeurs refusent les produits mal notés), la publicité autorisée (loi relative à la protection des enfants?) et les choix des consommateurs (des études montrent des effets mesurables sur les achats). Une erreur d'une lettre de classe a donc des conséquences commerciales directes. La reformulation coûte (recettes, procédés, études de durée de vie) : le tri des recettes candidates par simulation est rentable.

## Utilisateurs finaux

- Équipes R&D et qualité des industriels.
- Distributeurs : tri référencement.
- Consommateurs et applications de scan.
- Santé publique France : contrôle de cohérence des scores affichés.

## Données

- Open Food Facts : base collaborative mondiale, des millions de produits, valeurs nutritionnelles pour 100 g, champ Nutri-Score : https://fr.openfoodfacts.org/data
- Le Nutri-Score est un calcul réglementaire public (algorithme : Santé publique France) : les étudiants doivent d'abord le réimplémenter, puis apprendre à le prédire ou l'imputer.
- https://fr.openfoodfacts.org/nutriscore

## Formalisation ML

Formulation A (imputation) : prédire le Nutri-Score des produits qui ne l'affichent pas, à partir de la composition : utile pour les applications de scan (des produits n'affichent pas leur score). Formulation B (simulation) : réimplémenter l'algorithme exact et construire l'interface what-if, où le modèle sert surtout à analyser la distribution des produits par catégorie et à détecter les produits incohérents (score affiché impossible au vu de la composition : data quality ou fraude d'étiquetage).

## Modèles et techniques

Classification ordinale sur gradient boosting pour la formulation A. Comparaison systématique avec la réimplémentation exacte de l'algorithme : le modèle appris doit approcher la règle, et l'écart est analytiquement instructif. Filtrage par catégorie de produit (pnns_groups) : le Nutri-Score se joue sur des seuils différents selon les boissons.

## Métriques et arbitrage métier

Exactitude à ±0 lettre et à ±1 lettre. Le taux de produits où la prédiction contredit la règle exacte de plus d'une lettre doit être quasi nul : sinon l'outil n'est pas déployable. Pour la détection d'incohérence : précision de la liste remise au contrôle qualité, pas de rappel total (on triera des dossiers, pas un flux temps réel).

## Démo Hugging Face

Space Gradio : curseurs nutritionnels pour 100 g (énergie, sucres, sel/sodium, fibres, protéines, fruits/légumes/noix, matières grasses saturées), catégorie de produit, sortie : classe Nutri-Score prédite avec l'échelle A-E officielle en couleur, comparaison avec le score calculé par la règle exacte, et histogramme du score de la catégorie dans Open Food Facts (positionner son produit).

## Pièges et points de vigilance

- Open Food Facts est collaboratif : erreurs de saisie, doublons, données manquantes en masse. Le nettoyage est le vrai travail.
- Confondre sodium et sel (facteur 2,54) : erreur classique qui ruine la prédiction.
- Seuils différents pour les boissons : documenter la règle avant de coder.

## Extensions

Prédire le NOVA (degré de transformation), Eco-Score, détection de produits dont l'étiquette semble erronée, analyse par marque (qui a reformulé entre 2020 et aujourd'hui).
