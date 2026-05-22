# Démarrage rapide

Mettez en place ProofRails sur votre projet en moins de 10 minutes.

## Étape 1 : Vérifier les prérequis

```bash
git --version          # >= 2.0
echo $SHELL            # bash, zsh ou dash
```

## Étape 2 : Installer ProofRails

**Option A : Cloner le dépôt**

```bash
git clone https://github.com/proofrails/proofrails.git /tmp/proofrails
cp -r /tmp/proofrails/{skill,scripts,templates,docs,examples} ./
```

**Option B : Copier uniquement la compétence**

```bash
# Claude Code
mkdir -p ~/.claude/skills/proofrails
cp proofrails/skill/SKILL.md ~/.claude/skills/proofrails/SKILL.md

# Compétences Markdown de style OpenClaw / Hermes
mkdir -p ~/.openclaw/workspace/skills/proofrails
cp proofrails/skill/SKILL.md ~/.openclaw/workspace/skills/proofrails/SKILL.md
```

Pour les autres agents à compétences Markdown, copiez `skill/SKILL.md` dans le dossier de compétences de l'outil.

**Option C : Utiliser le script bootstrap de ce dépôt**

Si vous avez déjà cloné ce dépôt, exécutez directement le script bootstrap :

```bash
./scripts/proofrails-bootstrap
```

## Étape 3 : Bootstrapper votre projet

```bash
# Essai à blanc d'abord — voir ce qui sera créé sans toucher aux fichiers
./scripts/proofrails-bootstrap --dry-run

# Appliquer
./scripts/proofrails-bootstrap
```

Sortie attendue :

```
[proofrails] Target: /chemin/vers/votre-projet
[proofrails] Créé .agentic/changes/
[proofrails] Créé .agentic/runs/
[proofrails] Créé planwithfile/
[proofrails] Créé .evidence/
[proofrails] Écrit AGENTS.md (depuis le modèle)
[proofrails] Écrit CLAUDE.md (depuis le modèle)
[proofrails] Terminé. Exécutez ./scripts/proofrails-check pour vérifier.
```

## Étape 4 : Valider

```bash
./scripts/proofrails-check
```

Sortie attendue :

```
[proofrails-check] Vérification de ProofRails installé...
[proofrails-check] Cible: /chemin/vers/votre-projet

[proofrails-check]   .agentic/changes/: OK
[proofrails-check]   .agentic/runs/: OK
[proofrails-check]   planwithfile/: OK
[proofrails-check]   .evidence/: OK
[proofrails-check]   CLAUDE.md: OK
[proofrails-check]   AGENTS.md: OK

[proofrails-check] Toutes les vérifications cibles sont passées.
```

## Étape 5 : Utiliser la compétence

Invoquez la compétence dans votre outil de codage IA :

```
/proofrails dans ./ corriger le bug de timeout de connexion. Commencer par la découverte brownfield. Attendre l'approbation avant d'éditer le code. Critères de succès : test de régression passe.
```

## Étape 6 : Lint régulièrement

```bash
./scripts/proofrails-lint
```

Analyse les problèmes courants : fichiers requis manquants et termes interdits.

## Prochaines étapes

- Lisez [Concepts](./concepts.fr.md) pour comprendre l'architecture.
- Lisez [Recettes](./recipes.fr.md) pour les flux de travail courants.
- Consultez [Dépannage](./troubleshooting.fr.md) en cas de problème.
