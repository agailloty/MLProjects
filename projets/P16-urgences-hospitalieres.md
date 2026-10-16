# P16 — Prévision des affluences aux urgences hospitalières et planification des moyens

Tâche ML : séries temporelles de surveillance | Difficulté : ★★★ | Données : SurSaUD/OSCOUR (Santé publique France)

## Scénario

Les urgences d'un groupe hospitalier voient leurs passages fluctuer selon des dynamiques difficiles à prévoir : vagues de bronchiolite chez les nourrissons en hiver, épidémies de gastro-entérite, pics de polytraumatisés les week-ends de beau temps. Le directeur des soins veut anticiper à 7 jours le volume de passages attendus, globalement et par tranche d'âge, pour réguler les ouvertures de boxes, les renforts infirmiers et les campagnes de redirection des patients non urgents (vers la médecine de ville ou les maisons de santé).

## Enjeux métier

Les urgences françaises sont en tension structurelle : les plans blancs d'hiver se succèdent, et le déclenchement de renforts se fait souvent trop tard. La surveillance syndromique (SurSaUD) existe précisément parce que l'anticipation des vagues est un besoin de santé publique : chaque hiver, les vagues de bronchiolite saturent des services de pédiatrie entiers. Une prévision à J+7 fiable, même grossière (plus ou moins 15 %), suffit à décaler des congés et ouvrir des lits — c'est le cœur du métier de bed management.

## Utilisateurs finaux

- Directions des soins et cadres de santé (bed management, régulation des moyens).
- Observatoires régionaux de santé (ORS) et ARS : vigilance régionale.
- Santé publique France : surveillance nationale des vagues syndromiques.
- Médecine de ville : anticipation des besoins de garde.

## Données

- Données de surveillance syndromique SurSaUD (passages aux urgences, par classe d'âge et motif, croisées OSCOUR et SOS Médecins) : publiées par Santé publique France, portail https://www.santepubliquefrance.fr/surveillance-syndromique-sursaudr — jeux téléchargeables sur data.gouv.fr (chercher SurSaUD, OSCOUR, ou données urgentences).
- Données météo France open data (épidémies hivernales liées au froid, pics estivaux à la chaleur).
- Calendaires scolaires et jours fériés (effets nets sur les passages).

## Formalisation ML

Cible : nombre de passages quotidiens aux urgences à horizon 7 jours, globalement et par tranche d'âge (les nourrissons pour la bronchiolite, les personnes âgées pour la grippe et la canicule). Série temporelle avec effets calendaires, saisonnalité annuelle marquée et épisodes épidémiques ponctuels.

## Modèles et techniques

Baseline : moyenne des semaines analogues. Puis régression avec features calendaires et lags (les passages infectés sont auto-excitants : un jour de hausse en prédit d'autres), gradient boosting, modèles de comptage. La détection de vague (changement de régime) est un cas d'usage de détection de changement ponctuel.

## Métriques et arbitrage métier

MAPE à J+7 par tranche d'âge, et surtout le délai de détection de vague : combien de jours avant le pic maximal le modèle signale la croissance anormale. Un jour d'anticipation sur une vague de bronchiolite représente des dizaines de lits organisés en avance.

## Démo Hugging Face

Space Gradio : graphe de l'historique réel et de la prévision à 7 jours avec intervalle, alerte de vague (comparaison au seuil épidémique), onglet par tranche d'âge, et simulateur de moyens : si la vague dépasse X passages/jour, suggestion de renfort en personnel (règles simples paramétrables).

## Pièges et points de vigilance

- Couverture des données publiques SurSaUD à vérifier selon les périodes (les jeux ouverts connaissent des trous) ; si la maille fine manque, basculer sur une région.
- Confidentialité : les données agrégées sont publiques, les données individuelles (RPU) ne le sont pas : rester sur l'agrégé.
- Hétérogénéité entre établissements ; modéliser un établissement ou un ensemble régional, pas la France entière.

## Extensions

Détection des syndromes dominants (motifs de passages), croisement avec les ventes de médicaments ouvertes (données de ventes ouvertes de l'assurance maladie pour certaines pathologies), comparaison de la prévision avec les alertes officielles de Santé publique France.
