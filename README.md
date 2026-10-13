# PayForge TOMSEN : stratégie de test, revue d'exigences et cahier de recette

Projet 11 de la formation Testeur Logiciel d'OpenClassrooms. Mise en situation : consultant en test chez PayForge, une entreprise de finance, sur la première version (APP 1.0.0) de TOMSEN, une application de gestion financière pour les particuliers.

Ce dépôt contient les trois livrables demandés et ce README, qui explique le contexte, la démarche et les chiffres. Il n'y a pas de code applicatif : le produit n'existe que sous forme de spécifications, de maquettes et d'un backlog.

## Livrables

| Fichier | Contenu |
|---|---|
| [`livrables/Strategie-de-test-TOMSEN.pdf`](livrables/Strategie-de-test-TOMSEN.pdf) | Stratégie de test, 14 pages : scénarios, méthodes, ressources, jalons et charge, préconisations, critères d'entrée et de sortie, anomalies, pilotage |
| [`livrables/Revue-exigences-TOMSEN.xlsx`](livrables/Revue-exigences-TOMSEN.xlsx) | Revue d'exigences : 29 questions avec criticité, répondant et état |
| [`livrables/Cahier-de-recette-TOMSEN.xlsx`](livrables/Cahier-de-recette-TOMSEN.xlsx) | Cahier de recette : 60 cas de test, 11 scénarios, 5 non-régressions, jeux de données |

Ordre de lecture conseillé : la revue (ce qui est flou dans les exigences), la stratégie (ce qu'on teste et comment), puis le cahier (les cas détaillés).

## Le contexte

L'équipe se compose d'Elsa (product owner), de trois développeurs dont Alice (responsable technique et scrum master) et de Quentin (bureau d'étude). Le consultant en test est intégré à cette équipe agile.

Contraintes de départ, tirées du mail de cadrage :

- l'agrément de la Banque de France est obtenu, donc les contrôles d'accès et de données bancaires portent l'enjeu réglementaire ;
- le produit doit être livré dans 4 mois à compter du GO ;
- c'est une première version, il n'existe aucun patrimoine de tests ;
- la direction exige 100 % de validation sur les fonctionnalités critiques et accepte 85 % sur les majeures et mineures ;
- une campagne de non-régression est attendue en fin d'exécution, car un autre projet va ajouter une fonctionnalité à l'application ;
- la charge se chiffre avec un fichier d'abaque, et le cahier de recette est organisé par sprint.

Le backlog compte 10 exigences testables, TOM-6 à TOM-15 :

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

29 questions, toutes en attente de réponse : 22 critiques, 5 majeures, 2 mineures. Les sujets qui pèsent le plus :

- **Découpage des sprints** : le mail en prévoit 6, le backlog en compte 5 (question 1).
- **Criticité du backlog** : presque tout est en « Moyenne », alors que l'accès et les données bancaires relèvent du critique (question 2).
- **Sécurité non spécifiée** : identifiants envoyés en GET, domaine d'authentification différent des autres services, appel en http, absence de chiffrement et de règles de second facteur, pas de gestion de session (questions 9 à 15).
- **Pas de matrice d'appareils** : la biométrie et les fonctions natives n'ont aucune couverture définissable (question 4).
- **Type de dépense absent** : aucun service ne le restitue, le scénario 10 n'est pas exécutable en l'état (question 25).
- **Livraison en recette et mise en production non décrites** (question 29).

La criticité d'une question suit celle de l'exigence visée. Une question sans réponse bloque les cas qui en dépendent, et un cas non joué compte comme non réussi.

## La stratégie de test

### Scénarios

11 scénarios de nouveautés, un parcours métier complet chacun, et 5 non-régressions qui réutilisent des cas existants.

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

La non-régression se joue une seule fois, au jalon J5, après la dernière livraison. Elle est bornée au critique et aux points d'interface avec le projet de l'autre équipe : inscription jusqu'au tableau de bord, connexion avec cloisonnement des comptes, récupération des données bancaires, intégrité du tableau de bord après l'arrivée du projet tiers, cohérence des montants entre les écrans. Elle réutilise 20 cas, dont 17 distincts. Les non-régressions 1 à 3 sont automatisées.

### Approche par les risques

Le niveau de risque produit est la probabilité de défaut multipliée par l'impact métier. Il fixe la criticité, donc la priorité d'exécution et l'objectif de couverture : 100 % pour le critique, 85 % pour le majeur et le mineur. Les risques résiduels hors de l'interface (chiffrement de la base, protocole réel de connexion) sont traités par préconisation, pas par test.

### Méthodes

| Méthode | Où | Pourquoi |
|---|---|---|
| Test manuel fonctionnel | Scénarios 1 à 11, critiques d'abord | Sans historique et avec une interface qui bouge, scripter maintenant coûterait plus cher |
| Test exploratoire | Fin de chaque sprint, sur TOM-7, TOM-14, TOM-15 | Découvrir le non-spécifié sans attendre la PO |
| Test d'interface de service | TOM-8, 10, 12, 13 | Délai de 30 s, quota de 5 min, indisponibilité : à provoquer avec des simulateurs |
| Automatisation | Non-régressions 1 à 3 | Rentable seulement si rejoué |
| Pipeline avant recette | Chaque livraison | Build, tests unitaires, test de fumée, arrêt au premier échec |
| Sécurité fonctionnelle | Scénarios 2 à 7 et 9, non-régressions 2 à 4 | Contrôles observables : https, repli, cloisonnement, adresse de la page |
| Audit de sécurité externe | Hors campagne, optionnel | Compétence et budget absents du projet |
| Compatibilité | Optionnelle | Conditionnée à une matrice d'appareils, qui n'existe pas |

Techniques de conception utilisées : partitions d'équivalence, valeurs limites (année de naissance 2007 et 2008, mot de passe de 7 et 8 caractères, quota d'un appel par 5 minutes, délai de 30 s, bornes de période), table de décision (identifiant, mot de passe et second facteur), transitions d'états, cas d'utilisation et test basé sur l'expérience.

### Jalons et charge

| Jalon | Activité | Quand |
|---|---|---|
| J1 | Analyse de test et revue d'exigences | Semaines 1 à 2, avant le sprint 1 |
| J2 | Stratégie de test et validation PO | Semaines 2 à 3 |
| J3 | Conception du cahier, sprint par sprint | En parallèle du développement |
| J4 | Exécution des nouveautés et retests | Fin de chaque sprint |
| J5 | Non-régression | Semaine 16 |
| J6 | Clôture et avis de mise en production | Semaine 17 |
| J7 | Mise en production | Hors processus de test |

Les cas sont rédigés d'abord, comptés ensuite, puis classés par l'abaque : 15 scénarios Moyen (6 h chacun) et 1 Simple (2 h), soit 92 h ou 11,5 jours. L'abaque ne couvre pas quatre postes, ajoutés à part :

| Poste | Charge |
|---|---|
| Provision de retest sur le critique | 2 jours |
| Réserve pour le projet tiers | 2 jours |
| Exploratoire | 1 jour |
| Automatisation des non-régressions 1 à 3 | 3 jours |

Total : 156 h, soit 19,5 jours, pour un consultant à environ un quart de temps sur 17 semaines (15 de développement, 1 de non-régression, 1 de bilan).

### Préconisations

Huit alertes, chacune avec son décideur : découpage des sprints contradictoire, projet tiers de périmètre inconnu, sécurité hors interface (audit externe avant la mise en production), absence de matrice d'appareils, un seul testeur en parallèle du développement, type de dépense manquant, absence de patrimoine face au 100 % exigé, livraison en recette et mise en production non décrites.

## Le cahier de recette

Le classeur suit le gabarit fourni, avec un onglet en plus :

| Onglet | Rôle |
|---|---|
| Exigences | Chaque exigence avec ses cas, son scénario et sa criticité (traçabilité exigence, scénario, cas) |
| Espace cas de tests | Les 60 cas : étapes, résultat attendu, criticité |
| Scénarios sprint 1 et 2 à sprint 6 | Les scénarios et non-régressions regroupés par sprint, cas dans l'ordre d'exécution |
| Jeux de données | Comptes A, B et C, soldes, transactions, pièces justificatives, simulateurs de la Banque de France et du service de connexion, valeurs limites |

Règles d'écriture des cas : un cas est indépendant des autres, chaque étape a un seul résultat attendu, le langage reste simple, et une hypothèse est écrite dans les préconditions quand l'exigence est floue. Toutes les données sont fictives et les mots de passe de recette ne figurent pas dans le cahier.

## Hypothèses et limites

- Sprints d'environ 2,5 semaines, 6 sprints selon le mail de cadrage.
- Criticités reprises de la revue : TOM-6 à TOM-11 et TOM-13 critiques, TOM-12 et TOM-14 majeurs, TOM-15 mineur.
- Livraison en recette par le pipeline de l'équipe de développement, avec version taguée et test de fumée.
- Les 29 questions de la revue n'ont pas de réponse : les cas qui en dépendent portent une hypothèse dans leurs préconditions.
- Aucun test n'a été exécuté, l'application n'existant pas. Les livrables couvrent la préparation de la campagne.
- La performance et l'accessibilité sont hors campagne faute d'exigences.
