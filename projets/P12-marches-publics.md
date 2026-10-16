# P12 — Détection des marchés publics à risque dans la commande publique

Tâche ML : classification / détection d'anomalies sur données transactionnelles | Difficulté : ★★★ | Données : DECP consolidés (data.gouv.fr)

## Scénario

Les données essentielles de la commande publique (DECP) recensent tous les marchés publics français, obligatoirement publiés par les acheteurs. Un journal d'investigation ou une association anticorruption (type Anticor) veut prioriser ses signalements : quels marchés, parmi des centaines de milliers, présentent des signaux de risque (montant anormal au regard des références sectorielles, procédure sans publicité là où la réglementation impose la mise en concurrence, attributions répétitives au même fournisseur, modification substantielle du montant après attribution) ?

## Enjeux métier

La commande publique représente environ 120 milliards d'euros par an. Les études menées sur les données consolidées (dont les travaux d'Anticor et de l'EPFL sur la complaisance dans les marchés) estiment à plusieurs pourcents la part soumise à des surcoûts ou des attributions douteuses : ce sont des centaines de millions à plusieurs milliards. L'enjeu d'un modèle de scoring est le ciblage : les capacités de contrôle (commissions régionales, HATVP, magistrats) sont limitées, il faut trier le flux.

## Utilisateurs finaux

- Instances de contrôle (CRTC, HATVP) : sélection des marchés à auditer.
- Journalistes et ONG : signalements.
- Les acheteurs eux-mêmes : autocontrôle de leurs pratiques, préparation des audits.
- Marchands de vigilance SaaS : produits réels de scoring de la commande publique existent.

## Données

- Données essentielles de la commande publique, fichiers consolidés (marchés de plus de 40 000 EUR HT, acheteurs et fournisseurs, montants, procédures, dates) : https://www.data.gouv.fr/datasets/donnees-essentielles-de-la-commande-publique-fichiers-consolides
- SIRENE (INSEE) pour les caractéristiques des entreprises fournisseurs (âge, effectifs).
- Sources de référence sur le contexte : travaux publics sur la détection de marchés anormaux (rapports de la Cour des comptes, travaux Anticor/EPFL sur la complaisance).

## Formalisation ML

Formulation A (anomalie) : prédire la probabilité qu'un marché soit atypique dans son contexte (département, catégorie d'achat, acheteur), en modélisant le montant log-normalisé conditionnellement, ou par isolation forest sur les features. Formulation B (répétitivité) : construire des features relationnelles (part du fournisseur dans les marchés d'un même acheteur, taux de gré à gré de l'acheteur) et classifier les couples acheteur-fournisseur à risque. La formulation B est la plus proche des méthodes réelles de détection (scoring de complaisance).

## Modèles et techniques

Gradient boosting sur features agrégés, isolation forest, analyse de réseaux (graphes acheteur-fournisseur). Attention : ce projet mobilise du feature engineering lourd sur des données administratives bruitées.

## Métriques et arbitrage métier

La vérité terrain est rare et c'est toute la difficulté : on ne peut pas mesurer un rappel sur une classe fraude quasiment absente du jeu. Approche pragmatique : construire des signaux composites vérifiables à la main (concordance procédure/montant avec les seuils réglementaires, écart au montant médian de référence), mesurer la précision des alertes sur un échantillon contrôlé manuellement, et assumer une évaluation qualitative. Ce point pédagogique (apprentissage avec faibles labels) est riche.

## Démo Hugging Face

Space Gradio : carte des départements, sélection d'un acheteur (commune, département), profil de risque de son portefeuille de marchés, classement des 20 marchés les plus atypiques de l'année avec explication des signaux (montant vs référence, procédure, fournisseur récurrent), et graphe de réseau acheteur-fournisseur.

## Pièges et points de vigilance

- Le champ montant est souvent une fourchette [min, max] : normalisation nécessaire.
- Les mêmes fournisseurs apparaissent sous des SIREN multiples (groupes).
- Biais de survie : les marchés non publiés (illégalement) sont précisément absents des données.
- Point éthique : le modèle désigne des suspicions, pas des preuves. Cadre de communication strict dans la démo.

## Extensions

Analyse de réseau de co-attribution, évolution temporelle des pratiques d'un acheteur, mise en relation avec les données de subventions publiques, produit réel : tableau de bord de vigilance d'un département.
