# P15 — Matching compétences-offres d'emploi pour l'orientation et la reconversion

Tâche ML : NLP (classification, similarité sémantique) | Difficulté : ★★★ | Données : API France Travail, ROME/PSC

## Scénario

Un conseiller France Travail ou une plateforme d'orientation reçoit un profil : une personne issue de la logistique, cinq ans d'expérience en magasinage, souhaite se réorienter vers le transport de personnes. Deux questions : quelles compétences du profil sont transférables vers quel métier cible, et quels métiers proches embauchent réellement dans le bassin d'emploi ? Un modèle de matching compétences-métiers, entraîné sur les offres d'emploi collectées par France Travail, fournit une lecture objective de la proximité entre métiers.

## Enjeux métier

Plusieurs millions de personnes sont inscrites à France Travail, et la durée moyenne de retour à l'emploi dépend fortement de la qualité du ciblage des offres. Un matching approximatif (envoyer des offres sans rapport avec le profil) dégrade la confiance des demandeurs et noie les conseillers sous les sollicitations. Le référentiel ROME (compétences-métiers) et les taxonomies de compétences sont utilisés en pratique par les outils de placement automatisés. C'est un problème d'ingénierie de la connaissance autant que de ML.

## Utilisateurs finaux

- Conseillers France Travail et opérateurs du placement (demande d'emploi accompagnement).
- Plateformes d'orientation et de reconversion (AFP, organismes de formation).
- Candidats : visualisation des métiers accessibles et des compétences manquantes.

## Données

- API Offres d'emploi de France Travail (offres actives en temps réel, intitulés, descriptions, code ROME, compétences requises) : https://www.data.gouv.fr/dataservices/api-offres-demploi
- Référentiel ROME et référentiel des compétences : disponibles sur data.gouv.fr (chercher ROME).
- open data des formations (Onisep, Parcoursup) pour la brique reconversion.

## Formalisation ML

Formulation A : classification multilabel — prédire les compétences requises par une offre à partir de sa description (utile pour normaliser les offres). Formulation B : similarité sémantique — apprendre un espace de plongement où métiers et compétences sont proches (SBERT/sentence-transformers sur paires métier-compétence observées dans les offres), puis recommander les métiers accessibles depuis un profil, avec la liste des compétences manquantes. La formulation B correspond au besoin utilisateur.

## Modèles et techniques

Encodage SBERT + similarité cosinus, classification multilabel (linear probe, fine-tuning). Attention : l'API impose un enregistrement et des quotas : prévoir une collecte locale et un jeu figé pour l'entraînement.

## Métriques et arbitrage métier

Pour la recommandation : précision@k des compétences manquantes (validation experte sur échantillon), taux de recommandations jugées plausibles par des conseillers (évaluation qualitative sur 30 cas). Pour la classification multilabel : F1 macro sur les compétences fréquentes, assumer la chute sur les rares.

## Démo Hugging Face

Space Gradio : formulaire de profil (métier actuel ou description libre des compétences), sélection du bassin d'emploi, sortie : top 10 des métiers recommandés avec score de proximité, compétences déjà couvertes vs manquantes, nombre d'offres actives du bassin correspondant à chaque métier recommandé.

## Pièges et points de vigilance

- L'API nécessite une clé et respecte des quotas : planifier la collecte en amont du projet.
- Les offres sont bruitées (intitulés marketing, compétences énumérées sans ordre).
- Biais de marché : le matching reflète l'existant (femmes/hommes, secteurs) ; le modèle sans garde-fous reproduit les ségrégations. Point d'éthique à documenter.

## Extensions
Détection des compétences émergentes du bassin (évolution du vocabulaire des offres), prédiction de la tension par métier (durée de publication), recommandation de formations (croisement Onisep).
