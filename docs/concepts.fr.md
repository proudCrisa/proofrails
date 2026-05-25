# Concepts

Comment ProofRails est structuré et pourquoi chaque partie existe.

## Architecture

```text
proofrails/
├── skill/             # Le SKILL.md principal qui orchestre tout
├── scripts/           # Scripts shell POSIX pour la configuration et la validation
├── templates/         # Modèles de référence personnalisables
├── docs/              # Documentation en EN/ZH/FR
├── examples/          # Exemples concrets pour les scénarios courants
└── .evidence/         # Preuves d'implémentation (propres à ce projet)
```

### Dans votre projet

```text
your-project/
├── CLAUDE.md          # Instructions pour l'agent IA (source de vérité)
├── AGENTS.md          # Règles de collaboration multi-agents
├── .proofrails/          # Répertoire de travail de ProofRails
│   ├── changes/       # Propositions de changement : proposal, design, tasks, specs
│   └── runs/          # Rapports de revue et preuves de vérification
├── planwithfile/      # Répertoire de travail pour les changements actifs
├── .evidence/         # Enregistrements de preuves immuables
└── .openspec/         # Optionnel : cycle de vie des specs géré par OpenSpec
```

## La machine d'état

ProofRails modélise le développement assisté par IA comme une machine d'état à 11 étapes :

```
Réception des documents -> Sonde -> Détection du mode -> Découverte -> Spéc -> Challenge
  -> Approbation -> Application -> Revue -> Vérification -> Archive
```

Chaque étape produit des artefacts sur le disque. Aucune étape n'est sautée. Les étapes peuvent reboucler (par exemple, la revue trouve une dérive de spec -> retour à Spéc).

### Artefacts par étape

| Étape | Artefact(s) | Réversible ? |
|---|---|---|
| Réception | `planwithfile/<id>/brief.md` | Oui, modifiable à tout moment |
| Détection | (sortie terminal, tableau des outils classés) | N/A |
| Mode | `planwithfile/<id>/mode.md` | Oui, avec confirmation |
| Découverte | `planwithfile/<id>/findings.md` | Oui, ajout seulement recommandé |
| Spéc | `proposal.md`, `design.md`, `tasks.md`, `specs/` | Oui, avant approbation |
| Challenge | `planwithfile/<id>/decisions.md` | Oui, avec justification |
| Approbation | (confirmation utilisateur, pas de fichier) | Non — porte franchie |
| Application | `planwithfile/<id>/progress.md`, `evidence.md` | Oui, via git revert |
| Revue | `.proofrails/runs/<id>/review-report.md` | Oui, re-revue |
| Vérification | `evidence.md` (mis à jour) | Non — les preuves sont immuables |
| Archive | `.openspec/` ou `.proofrails/changes/<id>/` | Non — état terminal |

## Modes

### Greenfield

Nouveau projet, nouveau dépôt, nouveau module, nouvelle capacité. ProofRails impose :

- Documents produit/conception/techniques ou entretien structuré avant la spéc.
- Décisions d'architecture documentées dans design.md.
- Planification par tranche verticale — quelle est la plus petite chose complète à construire d'abord ?

### Brownfield

Correction de bug, refactoring, performance, migration, modernisation. ProofRails impose :

- Découverte en lecture seule avant toute édition.
- Tests de caractérisation ou preuves dorées avant les changements.
- Analyse du rayon d'impact — qu'est-ce qui pourrait casser d'autre ?
- Surface de changement minimale — ne toucher que ce qui doit changer.

### Hybride

Nouveau sous-système dans un projet existant. Combine :

- Spéc Greenfield pour le nouveau sous-système.
- Découverte Brownfield pour les points d'intégration existants.
- Contrats d'interface explicites entre l'ancien et le nouveau.

### Bootstrap de ProofRails

Mise en place de ProofRails lui-même. Crée :

- Fichiers d'instructions du projet (CLAUDE.md, AGENTS.md).
- Structure de répertoires (.proofrails, planwithfile, .evidence).
- Aucun code métier n'est touché.

## Outils et solutions de secours

ProofRails détecte les outils disponibles et s'adapte :

| Outil détecté | Rôle | Solution de secours si absent |
|---|---|---|
| git | Contrôle de version, diff, log | Requis — pas de secours |
| gstack | Compétences de planification/review/QA | Portes de workflow intégrées |
| OpenSpec CLI | Gestion du cycle de vie des specs | Répertoire `.proofrails/changes/` |
| Superpowers | Discipline TDD et exécution | Règles émulées dans ProofRails |
| GitNexus | Requêtes sur le graphe de code | `grep -r` / `find` |
| gbrain | Mémoire persistante | Fonctionne très bien sans |

## Limites de risque

Les limites de risque définissent les contrats qui nécessitent une approbation explicite avant modification. Elles sont documentées dans la section **Risk boundaries** de `CLAUDE.md` et incluent :

```markdown
# Limites de risque

## APIs publiques
- `POST /api/v1/users` — contrat d'appelant externe
- `GET /api/v1/orders` — dépendance de l'application mobile

## Schémas de données
- Table `users` — partagée avec le pipeline d'analytique
- Table `orders` — politique de rétention de conformité applicable

## Files de messages
- Topic `order.events` — consommé par le service de facturation
- Topic `user.actions` — consommé par le moteur de recommandation

## Authentification
- Validation de token OAuth2 — doit maintenir la compatibilité ascendante
- Format de cookie de session — partagé avec le frontend legacy

## Déploiement
- `docker-compose.prod.yml` — topologie de production
- `terraform/` — infrastructure as code
```

## Preuves

Les preuves sont la réponse de ProofRails au principe « faire confiance mais vérifier ». Chaque porte de vérification produit ou référence des preuves :

- **Sortie de build** — stdout/stderr des commandes de build.
- **Résultats de tests** — comptes de succès/échec, rapports de couverture.
- **Sortie lint/typecheck** — zéro avertissement ou exceptions documentées.
- **Smoke test runtime** — l'app démarre, l'API retourne 200, le flux clé fonctionne.
- **Preuve manuelle** — capture d'écran, extrait de log, confirmation utilisateur (quand l'automatisation est impossible).

Les preuves sont écrites dans `.evidence/` (immuable, ajout seulement) ou `planwithfile/<id>/evidence.md` (changement actif).

## Archive

L'archive est un **état terminal**. Après l'archivage d'un changement :

- Sa spec et ses preuves sont gelées.
- Ne pas modifier le même changement sur place.
- Un changement de suivi est un **nouveau** changement avec son propre change-id.
- Cela empêche la dérive du périmètre et garde la chaîne de preuves de chaque changement intacte.
