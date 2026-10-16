# P05 — Prédiction des retards de trains et information voyageurs

Tâche ML : régression / classification sur séries | Difficulté : ★★☆ | Données : SNCF Open Data

## Scénario

La SNCF verse à l'État des pénalités lorsque la ponctualité de ses trains (calculée au retard au terminus, seuil de 5 min selon la durée du parcours) franchit certains seuils. En interne, les gestionnaires de la circulation veulent anticiper les mois de crise (hiver, grèves, travaux) et préparer des plans de renfort (materiel, conducteurs, reroutage). Un étudiant reproduit l'exercice de prévision de la régularité mensuelle par liaison à partir de l'historique.

## Enjeux métier

La régularité est l'indicateur qualité central du rail : elle pilote les pénalités contractuelles, la compensation des voyageurs (programme fidélité : garantie 30 min de retard = remboursement) et la confiance. Anticiper un mois dégradé permet de mobiliser les moyens avant, pas après. Le retard a aussi un coût direct pour les voyageurs (rendez-vous manqués, correspondances), que les opérateurs cherchent à arbitrer par re-plannification.

## Utilisateurs finaux

- Gestion de l'exploitation (retour à la normale, gestion de crise).
- Information voyageurs : alertes sur les liaisons à risque.
- Contrôle de gestion : provision des pénalités.

## Données

- Régularité mensuelle TGV par liaison : https://ressources.data.sncf.com/explore/dataset/regularite-mensuelle-tgv-aqst/
- Régularité mensuelle TGV globale : https://ressources.data.sncf.com/explore/dataset/reglarite-mensuelle-tgv-nationale/
- Régularité mensuelle TGV par axe : httpsressources.data.sncf.com/explore/dataset/regularite-mensuelle-tgv-axes/
- Catalogue complet (trains complets, retard à l'arrivée par train, TER, Transilien) : https://data.sncf.com

## Formalisation ML

Cible : taux de régularité du mois suivant pour une liaison (régression), ou probabilité d'un mois dégradé (inférieur à 80 % de trains à l'heure, classification). Variables : historique de la liaison, saisonnalité, nombre de trains programmés, grèves et vacances via le calendrier. Variante riche : le dataset des trains (une ligne par train) permet de prédire le retard à l'arrivée d'un train donné, ce qui est plus proche de l'usage opérationnel.

## Modèles et techniques

Baseline : valeur du même mois de l'année précédente. Puis modèles avec features calendaires et lags (LightGBM), puis approche séries temporelles multi-séries (une série par liaison, modèles globaux type DeepAR). Attention aux événements rares et ruptures de série (COVID).

## Métriques et arbitrage métier

MAE en points de régularité. Seuil métier : prédire à ±2 points le taux de régularité d'une liaison est déjà exploitable pour la provision des pénalités. Pour la classification, la matrice de coût est asymétrique : ne pas anticiper un mois dégradé coûte plus que la fausse alerte.

## Démo Hugging Face

Space Gradio : sélection d'une liaison et d'un mois, prédiction du taux de régularité avec intervalle, classement des liaisons à risque pour le mois prochain, et graphe historique réel vs prédit. Variante train : formulaire (gare de départ, gare d'arrivée, jour, heure, mois) et probabilité de retard supérieur à 15 min.

## Pièges et points de vigilance

- COVID crée une rupture structurelle : soit on l'exclut, soit on l'apprend.
- La régularité mensuelle est une moyenne : le modèle des trains individuels est plus difficile mais plus utile.
- Décalage de définitions entre datasets (retard au terminus vs à l'arrivée) : lire la documentation de chaque jeu.

## Extensions

Croiser avec la météo (gel = retards), analyser l'effet d'un incident (dataset des incidents si disponible), prévoir la compensation due aux voyageurs.
