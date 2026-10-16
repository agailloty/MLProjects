# P03 — Détection des passoires thermiques et prédiction du DPE sans diagnostic

Tâche ML : classification multiclasse ordonnée + régression | Difficulté : ★★★ | Données : base DPE de l'ADEME

## Scénario

Une agence départementale d'accompagnement à la rénovation (type France Rénov') doit traiter des milliers de demandes de propriétaires. Chaque dossier commence aujourd'hui par un audit énergétique à 500 EUR, payé même quand la conclusion est évidente (maison des années 1970 non isolée). L'agence veut un outil de tri : estimer le DPE probable d'un logement à partir de ses caractéristiques déclarées, pour prioriser les audits utiles et dérouler un parcours de rénovation pertinent.

## Enjeux métier

La loi Climat et Résilience interdit progressivement la mise en location des passoires thermiques (logements classés G à partir de 2025, puis F, puis E). Des millions de logements sont concernés et les propriétaires doivent investir (une rénovation performante coûte 20 000 à 80 000 EUR). Un mauvais tri des audits gaspille l'argent public des accompagnements (MaPrimeRénov'). Un prédicteur de DPE fiable est un outil d'orientation à forte valeur.

## Utilisateurs finaux

- Espaces Conseil France Rénov' et accompagnateurs : tri des dossiers, parcours conseillé.
- Propriétaires bailleurs : connaître leur risque locatif probable avant de payer un diagnostic.
- Syndics et gestionnaires de patrimoine : cartographie du patrimoine à risque réglementaire.
- Collectivités : ciblage des campagnes d'aide.

## Données

- Base des DPE logements existants (plusieurs millions de diagnostics réels, depuis juillet 2021) : https://data.ademe.fr/datasets/dpe03existant
- Observatoire DPE de l'ADEME : https://observatoire-dpe-audit.ademe.fr/donnees-dpe-publiques
- Présentation de la base sur data.gouv : https://www.data.gouv.fr/posts/la-base-des-diagnostics-de-performance-energetique-dpe

## Formalisation ML

Cible A : classe DPE (A à G), classification ordinale. Cible B : consommation énergétique primaire (kWh/m²/an), régression. Variables : année de construction, surface, hauteur sous plafond, murs, isolation, type de chauffage, département, etc. La base contient à la fois les caractéristiques et le résultat du calcul réglementaire : le modèle apprend la physique du bâtiment à partir de dizaines de milliers de cas réels.

## Modèles et techniques

Baseline : régression linéaire sur variables encodées. Puis gradient boosting. Point pédagogique fort : traiter la variable cible ordinale (régression avec seuils de classe plutôt que classification brute), et quantifier l'incertitude (quantiles).

## Métriques et arbitrage métier

Exactitude de classe ± 1 (le DPE à la lettre près est bruité ; être adjacent est presque suffisant pour trier). Taux de faux passoires thermiques (prédire G sur un logement D) : c'est l'erreur qui nuit gravement à la confiance. Présenter le pourcentage de logements où le modèle se trompe d'au moins deux classes.

## Démo Hugging Face

Space Gradio : formulaire de caractéristiques du logement (avec listes déroulantes réalistes : période de construction, type de mur, type de fenêtre, chauffage). Sortie : classe DPE prédite avec jauge de couleur, consommation estimée, intervalle, et les trois travaux de rénovation les plus impactants à tester (simulation what-if : je passe le mur de non isolé à isolé, nouvelle prédiction).

## Pièges et points de vigilance

- La base contient des DPE d'anciennes normes, moins fiables : filtrer sur la version du DPE 2021.
- Déséquilibre : très peu de classes A/B, beaucoup de D/E.
- Attention à l'éthique : une prédiction affichée comme un diagnostic officiel serait un abus ; disclaimer obligatoire dans la démo.

## Extensions

Prédire le gain de rénovation (what-if par travaux), estimer le prix des travaux à partir de bases publiques, détecter les DPE frauduleux (anomalie entre caractéristiques et résultat).
