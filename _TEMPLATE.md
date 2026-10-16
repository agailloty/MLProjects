# PNN — Titre du projet

Tâche ML : ... | Difficulté : ★☆☆ à ★★★★ | Données : source principale

## Scénario

Raconter l'histoire : qui est le client (fictif mais réaliste), quel problème opérationnel concret le bloque aujourd'hui, ce qu'il fait actuellement et pourquoi c'est insuffisant. Une à deux phrases par acteur. Le scénario doit permettre de répondre à la question suivante en une phrase : qui prend quelle décision grâce au modèle ?

## Enjeux métier

Chiffrer autant que possible : coût de l'erreur, coût du statu quo, valeur du gain. Citer les ordres de grandeur vérifiables (rapports publics, presse économique, prix de marché). Un projet dont on ne peut pas dire s'il vaut 10 kEUR ou 10 MEUR n'est pas un projet métier.

## Utilisateurs finaux

Lister les utilisateurs réels avec leur usage précis. Un utilisateur sans action concrète n'est pas un utilisateur, c'est un lecteur.

## Données

- Jeu principal : nom, producteur, URL vérifiée, licence.
- Jeux d'enrichissement éventuels.
- Contraintes d'accès (API à clé, quotas, trous historiques).

## Formalisation ML

Cible, variables, granularité, découpage train/test (temporel si série, spatial si géographique), traitement du déséquilibre éventuel. Justifier chaque choix par la décision d'usage, pas par la convention.

## Modèles et techniques

Baseline explicite (toujours), puis montée en gamme. Indiquer les techniques attendues et la difficulté technique de bout en bout (collecte comprise).

## Métriques et arbitrage métier

Métrique(s) principale(s) reliées à l'usage, seuil de déploiabilité chiffré, matrice de coût si asymétrie. Préciser ce qu'on refuse d'optimiser (accuracy globale sur classes déséquilibrées, par exemple).

## Démo Hugging Face

Description de l'interface Gradio : entrées, sorties, visualisations, disclaimer éventuel. L'utilisateur final doit pouvoir tester la décision métier simulée en moins d'une minute.

## Pièges et points de vigilance

Fuites de données, biais de déclaration, ruptures de série, problèmes éthiques ou réglementaires, limites honnêtes du jeu de données.

## Extensions

Variantes, enrichissements, suites possibles pour les étudiants les plus avancés.
