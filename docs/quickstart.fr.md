# Démarrage rapide

Mettez en place ProofRails sur votre projet en moins de 10 minutes.

## Étape 1 : Vérifier les prérequis

```bash
git --version          # >= 2.0
echo $SHELL            # bash, zsh ou dash
```

## Étape 2 : Installer ProofRails

**Option A : Cloner le dépôt et utiliser ses scripts (recommandé)**

```bash
git clone https://github.com/proudCrisa/proofrails.git /tmp/proofrails
```

Conservez ce checkout comme source des scripts ProofRails. Le source lint valide le checkout ProofRails complet ; le target check valide votre projet installé.

**Option B : Copier seulement la compétence**

```bash
# Claude Code
mkdir -p ~/.claude/skills/proofrails
cp /tmp/proofrails/skill/SKILL.md ~/.claude/skills/proofrails/SKILL.md

# Compétences Markdown de style OpenClaw / Hermes
mkdir -p ~/.openclaw/workspace/skills/proofrails
cp /tmp/proofrails/skill/SKILL.md ~/.openclaw/workspace/skills/proofrails/SKILL.md
```

Pour les autres agents à compétences Markdown, copiez `/tmp/proofrails/skill/SKILL.md` dans le dossier de compétences de l'outil. Copier seulement la compétence n'installe pas les scripts du projet ; utilisez `/tmp/proofrails/scripts/...` pour le bootstrap, les checks et le source lint.

## Étape 3 : Bootstrapper votre projet

```bash
# Essai à blanc — voir les fichiers de workflow créés sans toucher au code applicatif/source
/tmp/proofrails/scripts/proofrails-bootstrap --dry-run /path/to/your-project

# Appliquer ; bootstrap crée uniquement des fichiers de workflow ProofRails, pas de changements applicatifs/source
/tmp/proofrails/scripts/proofrails-bootstrap /path/to/your-project
```

Sortie attendue :

```
[proofrails] Target: /chemin/vers/votre-projet
[proofrails] Créé .proofrails/changes/
[proofrails] Créé .proofrails/runs/
[proofrails] Créé planwithfile/
[proofrails] Créé .evidence/
[proofrails] Écrit AGENTS.md (depuis le modèle)
[proofrails] Écrit CLAUDE.md (depuis le modèle)
[proofrails] Terminé. Exécutez /tmp/proofrails/scripts/proofrails-check --target /path/to/your-project pour vérifier.
```

## Étape 4 : Valider

```bash
/tmp/proofrails/scripts/proofrails-check --target /path/to/your-project
```

Sortie attendue :

```
[proofrails-check] Vérification de ProofRails installé...
[proofrails-check] Cible: /chemin/vers/votre-projet

[proofrails-check]   .proofrails/changes/: OK
[proofrails-check]   .proofrails/runs/: OK
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

## Étape 6 : Linter le checkout ProofRails

```bash
/tmp/proofrails/scripts/proofrails-lint
```

Ce source lint analyse le checkout ProofRails complet pour les fichiers requis et les termes interdits. Utilisez `proofrails-check --target /path/to/your-project` pour les projets installés.

## Prochaines étapes

- Lisez [Concepts](./concepts.fr.md) pour comprendre l'architecture.
- Lisez [Recettes](./recipes.fr.md) pour les flux de travail courants.
- Consultez [Dépannage](./troubleshooting.fr.md) en cas de problème.
