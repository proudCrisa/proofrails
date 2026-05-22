# Recettes

Flux de travail courants et comment les exécuter avec le harness.

## Corriger un bug (Brownfield)

### 1. Invoquer le harness

```
/agentic-dev-harness dans ./mon-projet corriger les commandes avec quantité zéro qui font planter la synchro d'inventaire.
Commencer par la découverte brownfield. Ne pas toucher la config de déploiement.
Critères de succès : test de régression passe, tests du module order verts.
```

### 2. Ce que fait le harness

1. Détecte l'environnement (git, outils disponibles).
2. Classifie comme brownfield.
3. Exécute la découverte en lecture seule :
   - Trouve le point d'entrée du traitement des commandes.
   - Trace la chaîne d'appel jusqu'à la synchro d'inventaire.
   - Trouve les tests existants dans le module `order/`.
   - Identifie les limites de risque (schéma order, file d'inventaire).
4. Rédige une spec : proposal, design, tasks, scénarios d'acceptation.
5. Challenge la spec (et si la quantité est négative ? et les remboursements partiels ?).
6. Présente le plan et demande l'approbation.
7. Après approbation : écrit un test de caractérisation qui reproduit le crash quantité zéro.
8. Implémente la correction (clause de garde pour quantité zéro).
9. Exécute la suite de tests, vérifie la correction.
10. Revise le diff par rapport à la spec.
11. Enregistre les preuves.
12. Archive le changement.

### 3. Ce que vous faites

- Répondre aux questions de découverte que le harness ne peut pas résoudre seul.
- Approuver ou réviser la spec à la porte d'approbation.
- Fournir tout contexte spécifique à l'environnement (BD de staging, credentials de test).

## Démarrer un nouveau service (Greenfield)

### 1. Invoquer le harness

```
/agentic-dev-harness dans /chemin/vers/nouveau-service démarrer un service de notification utilisateur.
Pas de code existant. Préférence stack technique : TypeScript, PostgreSQL, Redis.
Critères de succès : proposal, design, tasks, plan de première tranche verticale.
Ne pas encore écrire l'implémentation.
```

### 2. Ce que fait le harness

1. Détecte l'environnement.
2. Classifie comme greenfield.
3. Conduit un entretien structuré (produit, conception, ingénierie, opérations).
4. Rédige proposal.md, design.md, tasks.md.
5. Identifie la plus petite tranche verticale (ex. « envoyer un email à l'inscription utilisateur »).
6. Challenge le design.
7. Présente le plan et demande l'approbation.
8. Après approbation : génère le squelette du projet, les tests de la première tranche.
9. Implémente la tranche.
10. Revue, vérification, archive.

### 3. Ce que vous faites

- Répondre aux questions de l'entretien.
- Fournir les exigences produit si disponibles.
- Approuver les décisions d'architecture à la porte de challenge.

## Refactorer un module (Brownfield)

### 1. Invoquer le harness

```
/agentic-dev-harness dans ./mon-projet refactorer le module de paiement pour utiliser la nouvelle API de passerelle.
Garder l'interface publique existante. Tous les tests existants doivent passer.
Critères de succès : même taux de réussite des tests, aucun changement de contrat d'API, performance à moins de 5%.
```

### 2. Comportements clés du harness

- La découverte identifie tous les appelants du module de paiement.
- Les tests de caractérisation capturent le comportement actuel avant le refactoring.
- La spec définit la stratégie seam/adapter.
- L'implémentation utilise le branch-by-abstraction.
- La vérification exécute la suite complète de tests et compare les performances.

## Bootstrapper un harness sur un projet existant

### 1. Invoquer le harness

```
/agentic-dev-harness dans ./mon-projet mettre en place le harness de développement IA.
Ne pas modifier le code métier. Créer uniquement les fichiers du harness.
Critères de succès : harness-check passe, harness-lint propre.
```

### 2. Ce que fait le harness

1. Analyse le projet : langage, système de build, commandes de test, CLAUDE.md existant.
2. Classifie comme harness-bootstrap.
3. Découvre les commandes build/test/lint existantes depuis package.json, Makefile, etc.
4. Crée ou met à jour CLAUDE.md avec des instructions spécifiques au projet.
5. Crée AGENTS.md pour les règles multi-agents.
6. Crée les répertoires .agentic/ et planwithfile/.
7. Exécute harness-check et harness-lint pour valider.
8. Enregistre les preuves.

### 3. Ce que vous faites

- Confirmer la compréhension par le harness de vos commandes build/test.
- Réviser et personnaliser CLAUDE.md selon les conventions de votre équipe.

## Exécuter le harness en mode dégradé

Quand les outils optionnels (gstack, OpenSpec CLI, Superpowers, GitNexus, gbrain) ne sont pas installés, le harness utilise des solutions de secours intégrées :

| Outil manquant | Comportement dégradé |
|---|---|
| gstack | Ignorer les compétences de challenge spécifiques à gstack. Utiliser le challenge intégré à quatre perspectives. |
| OpenSpec CLI | Écrire les specs dans `.agentic/changes/` au lieu de `.openspec/`. Même format. |
| Superpowers | Émuler manuellement TDD, débogage systématique, petites tâches. |
| GitNexus | Utiliser `grep -r` et `find` pour la recherche de code. |
| gbrain | Ignorer la synchro mémoire. Les artefacts sur disque sont la mémoire. |

Le harness fonctionne toujours en mode dégradé. Il demande une fois si vous voulez installer les outils manquants, puis continue avec ce qui est disponible.

## Personnaliser les modèles

Tous les modèles dans `templates/` sont des points de départ. Personnalisez-les pour votre équipe :

### CLAUDE.md

Ajoutez les conventions de votre équipe, la stack préférée et les règles spécifiques au projet :

```markdown
# Règles supplémentaires du projet

## Stack
- Backend : Go 1.22, chi router, pgx
- Frontend : React 18, TypeScript, Vite
- Base de données : PostgreSQL 16

## Conventions
- Les erreurs sont wrappées avec fmt.Errorf("%w", err)
- Les handlers HTTP retournent une erreur, jamais un statut direct
- Les tests utilisent testify/assert
```

### AGENTS.md

Définissez comment plusieurs agents IA doivent collaborer :

```markdown
# Rôles des agents
- lead : coordonne, exécute le harness, demande l'approbation
- reviewer : revue de diff indépendante, pas d'implémentation
- tester : écrit et exécute les tests, ne modifie jamais le code source
```
