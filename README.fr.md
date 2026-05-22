# Agentic Dev Harness

**Mettez en place des garde-fous pour le développement IA en 10 minutes.**

Une compétence à point d'entrée unique qui transforme le codage assisté par IA en un flux de travail d'ingénierie reproductible. Pointez-la vers n'importe quel projet — nouveau ou existant — et obtenez des spécifications, des plans, des limites de risque, des portes de vérification et un suivi des preuves sans avoir à assembler manuellement une douzaine d'outils.

[English](./README.md) | [中文](./README.zh-CN.md) | [Français](./README.fr.md)

---

## Pourquoi cela existe

Les agents de codage IA sont puissants mais imprévisibles. Ils sautent la découverte, éditent sans approbation, fusionnent sans tests et ne laissent aucune trace. Les équipes finissent par écrire des règles ad-hoc, copier des modèles CLAUDE.md et appliquer manuellement des contrôles — un travail qui n'a rien à voir avec leur produit réel.

Ce harness vous donne cette infrastructure en une seule commande. Ce n'est pas un framework, ni un SaaS, ni une plateforme propriétaire. C'est une **compétence** — un flux de travail structuré qui s'exécute dans votre outil de codage IA existant — plus un ensemble de scripts et de modèles qui maintiennent votre projet dans un état connu et sain.

## Ce que vous obtenez en 10 minutes

```text
your-project/
├── CLAUDE.md                    # Instructions pour l'agent IA
├── AGENTS.md                    # Règles de collaboration multi-agents
├── .agentic/
│   ├── changes/                 # Propositions de changement et documents de conception
│   └── runs/                    # Rapports de revue et preuves
├── planwithfile/                # Répertoire de travail pour les changements actifs
└── .evidence/                   # Enregistrements de preuves immuables
```

## Comment ça fonctionne

```
Réception -> Détection -> Mode -> Découverte -> Spéc -> Challenge -> Approbation -> Application -> Revue -> Vérification -> Archive
```

1. **Réception** — Collecter l'objectif, les contraintes, les critères de succès. Ne demander que ce qui manque.
2. **Détection** — Détecter les outils disponibles (git, OpenSpec, gstack, etc.). Cartographier les solutions de secours en mode dégradé.
3. **Mode** — Classer le travail : nouveau projet, existant, hybride ou bootstrap du harness.
4. **Découverte** — Exploration en lecture seule. Trouver les points d'entrée, les contrats, les risques. Pas d'édition.
5. **Spéc** — Rédiger la proposition, la conception, les tâches et les scénarios d'acceptation (compatible OpenSpec).
6. **Challenge** — Tester la spécification sous les angles métier, risque, implémentation et historique.
7. **Approbation** — Présenter la portée, les risques et le plan. Exiger une approbation explicite avant toute édition du code source.
8. **Application** — Implémenter par tranches petites et vérifiables, avec les tests d'abord.
9. **Revue** — Diff par rapport à la spécification. Signaler les dérives. Exiger une résolution.
10. **Vérification** — Build, tests, lint, smoke. Des preuves ou rien.
11. **Archive** — Mettre à jour la documentation durable. Enregistrer les leçons apprises. État terminal pour ce changement.

## Démarrage rapide

### Prérequis

- git (toute version récente)
- Un shell compatible POSIX (bash, zsh, dash)
- Un outil de codage IA prenant en charge les compétences (Claude Code, ou tout outil avec chargement de compétences Markdown)

### Installation

```bash
# Cloner dans votre projet ou répertoire de compétences
git clone https://github.com/agentic-dev-harness/harness.git

# Claude Code
mkdir -p ~/.claude/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.claude/skills/agentic-dev-harness/SKILL.md

# Compétences Markdown de style OpenClaw / Hermes
mkdir -p ~/.openclaw/workspace/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.openclaw/workspace/skills/agentic-dev-harness/SKILL.md

# Autres agents à compétences Markdown : copiez skill/SKILL.md dans le dossier de compétences de l'outil.
```

### Bootstrap d'un projet

```bash
# À la racine de votre projet :
./scripts/bootstrap-harness

# Ou essai à blanc pour voir ce qui sera créé :
./scripts/bootstrap-harness --dry-run

# Vérifier la santé du harness :
./scripts/harness-check

# Lint pour détecter les problèmes :
./scripts/harness-lint
```

### Utiliser la compétence

```
/agentic-dev-harness dans ./mon-projet corriger le bug de timeout de paiement.
Commencer par la découverte du code existant. Attendre l'approbation avant d'éditer.
Critères de succès : test de régression passe, tests du module verts.
```

## Modes

| Mode | Quand l'utiliser | Contrainte clé |
|---|---|---|
| **Greenfield** | Nouveau projet, nouveau dépôt, nouveau module | Exiger des documents produit/conception/tech ou un entretien |
| **Brownfield** | Correction de bug, refactoring, migration, perf | Découverte avant édition, tests de caractérisation |
| **Hybride** | Nouveau sous-système dans un projet existant | Combiner spéc greenfield + découverte brownfield |
| **Bootstrap** | Mettre en place le harness lui-même | Aucun code métier touché |

## Compatibilité

Ce harness fonctionne avec tout outil de codage IA capable de lire des fichiers de compétence Markdown. Intégrations optionnelles :

| Intégration | Rôle | Sans elle |
|---|---|---|
| [OpenSpec](https://github.com/anthropics/openspec) | CLI de cycle de vie des specs | Repli sur `.agentic/changes/` |
| [gstack](https://github.com/gstack) | Compétences de planification/review/QA | Les portes intégrées fonctionnent seules |
| Superpowers | Discipline TDD et exécution | Les règles de base sont émulées dans le harness |
| GitNexus | Graphe de code et analyse d'impact | Repli sur grep/find |
| gbrain | Mémoire persistante et recherche de code | Fonctionne sans |

**Ce harness n'a aucune dépendance obligatoire.** Installez ce qui aide ; ignorez le reste.

## Principes de sécurité

1. **Pas d'édition avant approbation.** La découverte et la rédaction de specs sont toujours autorisées. Les modifications du code source, des dépendances et du CI/CD nécessitent une approbation explicite.
2. **Des preuves, pas des affirmations.** Chaque porte de vérification nécessite une sortie de build, des résultats de tests ou des exceptions explicitement acceptées par l'utilisateur.
3. **Les contrats à haut risque nécessitent une confirmation.** Les APIs publiques, les schémas de données, les files de messages, l'authentification, le déploiement et la configuration de production nécessitent une approbation avant modification.
4. **L'archive est terminale.** Après l'archivage d'un changement, ne le modifiez pas sur place. Commencez un nouveau changement.
5. **Point d'entrée unique.** L'utilisateur invoque une seule compétence. Le harness route vers les sous-compétences en interne.

## Exemples

- [Configuration minimale du harness](./examples/minimal/README.md) — Bootstrap sur un dépôt vide
- [Correction de bug Brownfield](./examples/brownfield/README.md) — Découverte, spec, correction, vérification sur un projet existant
- [Service Greenfield](./examples/greenfield/README.md) — Nouveau service from scratch avec harness complet

## Documentation

| Document | EN | ZH | FR |
|---|---|---|---|
| Démarrage rapide | [en](./docs/quickstart.md) | [zh](./docs/quickstart.zh-CN.md) | [fr](./docs/quickstart.fr.md) |
| Concepts | [en](./docs/concepts.md) | [zh](./docs/concepts.zh-CN.md) | [fr](./docs/concepts.fr.md) |
| Recettes | [en](./docs/recipes.md) | [zh](./docs/recipes.zh-CN.md) | [fr](./docs/recipes.fr.md) |
| Dépannage | [en](./docs/troubleshooting.md) | [zh](./docs/troubleshooting.zh-CN.md) | [fr](./docs/troubleshooting.fr.md) |

## Contribuer

Les contributions sont les bienvenues. Avant de soumettre une PR :

1. Exécutez `./scripts/harness-lint` et corrigez tous les problèmes.
2. Exécutez `./scripts/harness-check` et vérifiez que tout passe.
3. Ajoutez des preuves dans `.evidence/` pour tout nouveau comportement.
4. Mettez à jour la documentation si vous modifiez le flux de travail.

## Licence

MIT — voir [LICENSE](./LICENSE) pour plus de détails.

---

**Conçu pour les équipes qui veulent la vitesse de l'IA avec la rigueur de l'ingénierie.**
