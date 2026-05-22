# Démarrage rapide

Mettez en place le harness sur votre projet en moins de 10 minutes.

## Étape 1 : Vérifier les prérequis

```bash
git --version          # >= 2.0
echo $SHELL            # bash, zsh ou dash
```

## Étape 2 : Installer le harness

**Option A : Cloner le dépôt**

```bash
git clone https://github.com/agentic-dev-harness/harness.git /tmp/harness
cp -r /tmp/harness/{skill,scripts,templates,docs,examples} ./
```

**Option B : Copier uniquement la compétence**

```bash
# Claude Code
mkdir -p ~/.claude/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.claude/skills/agentic-dev-harness/SKILL.md

# Compétences Markdown de style OpenClaw / Hermes
mkdir -p ~/.openclaw/workspace/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.openclaw/workspace/skills/agentic-dev-harness/SKILL.md
```

Pour les autres agents à compétences Markdown, copiez `skill/SKILL.md` dans le dossier de compétences de l'outil.

**Option C : Utiliser le script bootstrap de ce dépôt**

Si vous avez déjà cloné ce dépôt, exécutez directement le script bootstrap :

```bash
./scripts/bootstrap-harness
```

## Étape 3 : Bootstrapper votre projet

```bash
# Essai à blanc d'abord — voir ce qui sera créé sans toucher aux fichiers
./scripts/bootstrap-harness --dry-run

# Appliquer
./scripts/bootstrap-harness
```

Sortie attendue :

```
[harness] Target: /chemin/vers/votre-projet
[harness] Créé .agentic/changes/
[harness] Créé .agentic/runs/
[harness] Créé planwithfile/
[harness] Créé .evidence/
[harness] Écrit AGENTS.md (depuis le modèle)
[harness] Écrit CLAUDE.md (depuis le modèle)
[harness] Terminé. Exécutez ./scripts/harness-check pour vérifier.
```

## Étape 4 : Valider

```bash
./scripts/harness-check
```

Sortie attendue :

```
[harness-check] Vérification du harness installé...
[harness-check] Cible: /chemin/vers/votre-projet

[harness-check]   .agentic/changes/: OK
[harness-check]   .agentic/runs/: OK
[harness-check]   planwithfile/: OK
[harness-check]   .evidence/: OK
[harness-check]   CLAUDE.md: OK
[harness-check]   AGENTS.md: OK

[harness-check] Toutes les vérifications cibles sont passées.
```

## Étape 5 : Utiliser la compétence

Invoquez la compétence dans votre outil de codage IA :

```
/agentic-dev-harness dans ./ corriger le bug de timeout de connexion. Commencer par la découverte brownfield. Attendre l'approbation avant d'éditer le code. Critères de succès : test de régression passe.
```

## Étape 6 : Lint régulièrement

```bash
./scripts/harness-lint
```

Analyse les problèmes courants : fichiers requis manquants et termes interdits.

## Prochaines étapes

- Lisez [Concepts](./concepts.fr.md) pour comprendre l'architecture.
- Lisez [Recettes](./recipes.fr.md) pour les flux de travail courants.
- Consultez [Dépannage](./troubleshooting.fr.md) en cas de problème.
