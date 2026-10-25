# PayForge TOMSEN : stratégie de test, revue d'exigences et cahier de recette

Projet 11 de la formation Testeur logiciel d'OpenClassrooms. Mise en situation : consultant en test chez PayForge, une entreprise de finance, sur la première version (APP 1.0.0) de TOMSEN, une application de gestion financière pour les particuliers.

Ce dépôt contient les trois livrables demandés. Il n'y a pas de code applicatif : le produit n'existe que sous forme de spécifications, de maquettes et d'un backlog. Aucun test n'a été exécuté. Les documents préparent la campagne.

## Les trois fichiers

| Fichier | Contenu |
|---|---|
| [Strategie-de-test-TOMSEN.pdf](Strategie-de-test-TOMSEN.pdf) | Stratégie de test, 15 pages. Scénarios, méthodes, ressources, jalons, charge, préconisations, critères d'entrée et de sortie, anomalies, pilotage |
| [Revue-exigences-TOMSEN.xlsx](Revue-exigences-TOMSEN.xlsx) | Revue d'exigences : 29 questions, avec criticité, répondant et état |
| [Cahier-de-recette-TOMSEN.xlsx](Cahier-de-recette-TOMSEN.xlsx) | Cahier de recette : 60 cas de test, 11 scénarios, 5 non-régressions, jeux de données |

Ordre de lecture : la revue (ce qui est flou dans les exigences), la stratégie (ce qu'on teste, comment, et pour quelle charge), puis le cahier (les cas, étape par étape).

Le PDF s'ouvre avec un lecteur PDF. Les classeurs s'ouvrent avec Excel ou LibreOffice Calc. Les mots de passe de recette ne sont pas écrits dans le cahier : ils sont fournis par l'équipe de développement.

## Le contexte

L'équipe se compose d'Elsa (product owner), de trois développeurs dont Alice (responsable technique et scrum master) et de Quentin (bureau d'étude). Le consultant en test est intégré à cette équipe agile.

Contraintes du mail de cadrage :

- l'agrément de la Banque de France est obtenu, donc les contrôles d'accès et de données bancaires portent l'enjeu réglementaire ;
- le produit doit être livré dans 4 mois à compter du GO ;
- c'est une première version, il n'existe aucun patrimoine de tests ;
- la direction exige 100 % de validation sur les fonctionnalités critiques et accepte 85 % sur les majeures et mineures ;
- une campagne de non-régression est attendue en fin d'exécution, car un autre projet va ajouter une fonctionnalité à l'application ;
- la charge se chiffre avec l'abaque du cours, une fois les cas rédigés ;
- le cahier de recette est organisé par sprint.

Le backlog compte 10 exigences testables, TOM-6 à TOM-15. TOM-3 est un épique, il n'est pas testé comme une story.

| Exigence | Sujet | Criticité retenue |
|---|---|---|
| TOM-6 | Création de compte | Critique |
| TOM-7 | Téléversement des pièces justificatives | Critique |
| TOM-8 | Authentification double facteur | Critique |
| TOM-9 | Authentification biométrique | Critique |
| TOM-10 | Récupération des données bancaires (API de la Banque de France) | Critique |
| TOM-11 | Tableau de bord multi-banques | Critique |
| TOM-12 | Téléchargement des relevés de compte | Majeur |
| TOM-13 | Consultation des transactions | Critique |
| TOM-14 | Suivi de consommation | Majeur |
| TOM-15 | Conseiller virtuel | Mineur |

## La revue d'exigences

29 questions, toutes en attente de réponse : 22 critiques, 5 majeures, 2 mineures. Celles qui pèsent le plus sur la campagne :

- Découpage des sprints. Le mail en prévoit 6, le backlog en compte 5 (question 1). Hypothèse retenue : le cahier suit les 6 sprints du mail, les exigences viennent du backlog.
- Criticité du backlog. Presque tout est noté « Moyenne », alors que l'accès et les données bancaires sont critiques (question 2). La campagne reprend la criticité de la revue, à confirmer par la PO.
- Sécurité non spécifiée. Identifiants envoyés en GET, domaine d'authentification différent des autres services, appel en http, absence de chiffrement et de règles de second facteur, pas de gestion de session (questions 9 à 15).
- Pas de matrice d'appareils. La biométrie et les fonctions natives n'ont aucune couverture définissable (question 4).
- Type de dépense absent. Aucun service ne le restitue, le scénario 10 n'est pas exécutable en l'état (question 25).
- Livraison en recette et mise en production non décrites (question 29).

La criticité d'une question suit celle de l'exigence visée. Une question sans réponse bloque les cas qui en dépendent. Un cas non joué compte comme non réussi.

## La stratégie de test

Le document suit les cinq parties du template du cours : scénarios prévus, méthodes de test adaptées, ressources nécessaires, étapes clés de la stratégie, préconisations.

### Scénarios

11 scénarios de nouveautés, un parcours métier chacun, et 5 campagnes de non-régression qui réutilisent des cas déjà écrits.

| Scénario | Parcours | Exigences | Criticité | Cas |
|---|---|---|---|---|
| 1 | Création de compte et contrôles de saisie | TOM-6 | Critique | 8 |
| 2 | Téléversement et suivi des pièces justificatives | TOM-7 | Critique | 7 |
| 3 | Connexion nominale et bascule double facteur | TOM-8 | Critique | 8 |
| 4 | Connexion par biométrie et repli | TOM-9 | Critique | 4 |
| 5 | Récupération des données bancaires, cas nominal | TOM-10 | Critique | 5 |
| 6 | Récupération des données bancaires, erreur et quota | TOM-10, TOM-11 | Critique | 4 |
| 7 | Consultation du tableau de bord multi-banques | TOM-11 | Critique | 5 |
| 8 | Téléchargement des relevés de compte | TOM-12 | Majeur | 4 |
| 9 | Consultation des transactions par banque | TOM-13 | Critique | 5 |
| 10 | Suivi de consommation et paramétrage de période | TOM-14 | Majeur | 5 |
| 11 | Accès au conseiller virtuel et redirection | TOM-15 | Mineur | 4 |

Total : 59 cas de nouveautés, plus le cas 53 réservé à la non-régression 5, soit 60 cas.

La non-régression se joue une seule fois, au jalon J5, après la dernière livraison. Elle porte sur le critique et sur les points d'interface avec le projet de l'autre équipe : inscription jusqu'au tableau de bord, connexion avec cloisonnement des comptes, récupération des données bancaires, intégrité du tableau de bord après l'arrivée du projet tiers, cohérence des montants entre les écrans. Elle réutilise 20 cas, dont 17 distincts. Les non-régressions 1 à 3 sont automatisées et rejouées à chaque livraison dès le sprint 5.

### Risques et couverture

Le niveau de risque produit est la probabilité de défaut multipliée par l'impact métier. Il fixe la criticité, donc la priorité d'exécution et l'objectif de couverture : 100 % pour le critique, 85 % pour le majeur et le mineur. Le taux se calcule par niveau de criticité : cas réussis / cas prévus. Les risques résiduels hors application (chiffrement de la base, protocole réel de connexion, domaine finabank.com) sont traités par préconisation, pas par test.

### Méthodes

| Méthode | Où | Pourquoi |
|---|---|---|
| Test manuel fonctionnel | Scénarios 1 à 11, critiques d'abord | Sans historique et avec une application encore mouvante, scripter maintenant coûterait plus cher |
| Test exploratoire | Fin de chaque sprint, sur TOM-7, TOM-14 et TOM-15 | Découvrir le non-spécifié sans attendre la PO |
| Test d'interface de service | TOM-8, 10, 12, 13 | Délai de 30 s, quota de 5 min, Banque de France indisponible : à provoquer dans l'environnement de test |
| Automatisation | Non-régressions 1 à 3, à partir de la fin du sprint 4 | Rentable seulement si le cas est rejoué |
| Automates de tests avant recette | Chaque livraison | Build, tests unitaires, vérification que l'application démarre, arrêt au premier échec |
| Sécurité fonctionnelle | Scénarios 2 à 7 et 9, non-régressions 2 à 4 | Contrôles observables : https, repli, cloisonnement, URL |
| Audit de sécurité externe | Hors campagne, optionnel | Compétence et budget absents du projet |
| Compatibilité | Optionnelle | Conditionnée à une matrice d'appareils, qui n'existe pas |

Techniques de conception : partitions d'équivalence, valeurs limites (année de naissance 2007 et 2008, mot de passe de 7 et 8 caractères, quota d'un appel par 5 minutes, délai de 30 s, bornes de période), table de décision (identifiant, mot de passe et second facteur), transitions d'états, cas d'utilisation, test basé sur l'expérience.

Chaque étape d'un cas a un seul résultat attendu. Ce résultat vient de l'exigence ou de la maquette. S'il manque, la question est en revue et le cas est marqué non jouable.

### Jalons et charge

| Jalon | Activité | Quand |
|---|---|---|
| J1 | Analyse de test et revue d'exigences | Semaines 1 à 2, avant le sprint 1 |
| J2 | Stratégie de test et validation par la PO | Semaines 2 à 3, en parallèle du sprint 1 |
| J3 | Conception du cahier, pendant le développement du sprint | 16,5 h |
| J4 | Exécution des nouveautés et retests | Fin de sprint, semaines 3, 5, 8, 10, 13 et 15. 73,5 h |
| J5 | Non-régression, critique uniquement | Semaine 16, après la dernière campagne. 66 h |
| J6 | Clôture, rapport de fin de test, avis de mise en production | Semaine 17 |
| J7 | Mise en production | Hors processus de test. Une instance d'abord, vérification que l'application démarre, retour arrière prêt |

Les cas sont rédigés d'abord, comptés ensuite, puis classés avec l'abaque du cours. Un scénario Moyen (3 à 9 cas) vaut 6 h. Un scénario Simple (0 à 2 cas) vaut 2 h. On a 15 scénarios Moyen et 1 Simple (la non-régression 4, 2 cas), soit 92 h, ou 11,5 jours. L'abaque ne couvre pas quatre postes, ajoutés à part. Une journée de production vaut 8 h.

| Poste | Charge |
|---|---|
| Provision de retest sur le critique | 2 jours (16 h), au jalon J4 |
| Réserve pour le projet tiers | 2 jours (16 h), au jalon J5 |
| Exploratoire | 1 jour (8 h), au jalon J4 |
| Automatisation des non-régressions 1 à 3 | 3 jours (24 h), au jalon J5 |

Total : 156 h, soit 19,5 jours. 92 h viennent de l'abaque, 64 h sont hors formules. Le consultant est à environ un quart de temps sur 17 semaines : 15 de développement, 1 de non-régression, 1 de bilan. La revue et la stratégie (J1, J2) ne sont pas comptées dans ces 156 h.

Entrée en exécution (J4) : automates de tests au vert, version taguée, base remise à zéro, cas du sprint rédigés, environnement de test de la Banque de France et de la connexion réglé. Si le 100 % critique n'est pas atteint en J6, la PO décide : report de la mise en production, ou acceptation écrite des anomalies restantes.

### Préconisations

Huit alertes. Le consultant les pose, le projet décide.

| Constat | Décideur |
|---|---|
| Découpage des sprints contradictoire (mail, backlog, cahier) | PO |
| Projet tiers : périmètre et date inconnus | PO |
| Sécurité hors application : GET, finabank.com, chiffrement, second facteur, session | PO et direction |
| Pas de matrice des appareils et systèmes | PO et bureau d'étude |
| Développement et recette en parallèle, un seul testeur | Responsable technique et PO |
| Type de dépense manquant (question 25) | Bureau d'étude |
| Pas de patrimoine, et 100 % exigé sur le critique | PO |
| Livraison en recette et mise en production non décrites (question 29) | Responsable technique |

## Le cahier de recette

Le classeur suit le gabarit du cours, avec un onglet en plus pour les jeux de données.

| Onglet | Rôle |
|---|---|
| Exigences | Chaque exigence avec ses cas, son scénario et sa criticité. C'est la traçabilité exigence, scénario, cas |
| Espace cas de tests | Les 60 cas : préconditions, étapes, un résultat attendu par étape, criticité |
| Scénarios sprint 1 et 2, jusqu'au sprint 6 | Les scénarios et les non-régressions regroupés par sprint, cas dans l'ordre d'exécution |
| Jeux de données | Comptes A, B et C, soldes, transactions, pièces justificatives, réglages de l'environnement de test (Banque de France et connexion), valeurs limites |

Règles d'écriture, celles de l'étape 8 du cours : un cas clair, un langage simple, un résultat attendu par étape, des cas indépendants. Quand l'exigence est floue, l'hypothèse est écrite dans les préconditions et la question de revue est citée. Toutes les données sont fictives.

L'environnement de test sert à provoquer ce que l'application seule ne permet pas : délai de 30 secondes, quota de 5 minutes, Banque de France indisponible, connexion reconnue ou non, second facteur accepté ou refusé.

## Hypothèses encore ouvertes

- Sprints d'environ 2,5 semaines, 6 sprints selon le mail de cadrage (question 1).
- Criticités de la revue : TOM-6 à TOM-11 et TOM-13 critiques, TOM-12 et TOM-14 majeurs, TOM-15 mineur (question 2).
- Livraison en recette par les automates de tests de l'équipe d'Alice : build, tests unitaires, déploiement, vérification que l'application démarre, version taguée (question 29).
- Les 29 questions n'ont pas de réponse. Les cas qui en dépendent portent l'hypothèse dans leurs préconditions.
- La performance et l'accessibilité sont hors campagne : il n'y a pas d'exigence dessus.
- La compatibilité et l'audit de sécurité externe restent optionnels, faute de matrice et de budget.
