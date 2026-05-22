# Dépannage

Problèmes courants et comment les résoudre.

## `proofrails-check` signale des fichiers manquants

**Symptôme :**

```
[proofrails-check]   CLAUDE.md : MANQUANT
[proofrails-check]   AGENTS.md : MANQUANT
```

**Solution :**

Exécutez le bootstrap pour créer les fichiers manquants :

```bash
./scripts/proofrails-bootstrap
```

Les fichiers existants ne seront pas écrasés, sauf si vous passez `--force`.

## `proofrails-lint` signale des termes interdits

**Symptôme :**

```
[proofrails-lint] INTERDIT : terme interdit trouvé dans docs/concepts.fr.md:42
```

**Solution :**

Supprimez le terme interdit du fichier signalé. Le script proofrails-lint vérifie une liste intégrée de références internes de plateforme qui ne doivent pas apparaître dans le dépôt open source.

## Le script bootstrap écrase mes fichiers

**Symptôme :**

Vous avez exécuté `./scripts/proofrails-bootstrap --force` et votre CLAUDE.md personnalisé a été remplacé.

**Solution :**

Par défaut, le bootstrap n'écrase **pas** les fichiers existants. Seul `--force` le fait. Pour des mises à jour sélectives :

1. Exécutez d'abord `--dry-run` pour voir ce qui changerait.
2. Sauvegardez vos fichiers personnalisés.
3. Exécutez avec `--force` puis réappliquez vos personnalisations.
4. Envisagez de maintenir vos personnalisations dans un fichier séparé, référencé par CLAUDE.md.

## ProofRails crée des répertoires dont je ne veux pas

**Symptôme :**

ProofRails a créé `.proofrails/` mais votre équipe utilise OpenSpec.

**Solution :**

ProofRails détecte `.openspec/` et l'utilise s'il est présent. Si vous préférez OpenSpec :

```bash
openspec init
```

ProofRails peut utiliser `.openspec/changes/` pour les propositions de changement tout en conservant `.proofrails/runs/` pour les rapports de revue et les preuves d'exécution. Gardez `.proofrails/runs/` en place afin que `proofrails-check` puisse valider ProofRails installé.

Si votre équipe standardise OpenSpec, configurez votre outil IA pour écrire les brouillons de changement dans `.openspec/changes/`, mais ne supprimez pas tout le répertoire `.proofrails/`. Il reste l'espace local d'exécution et de preuves de ce ProofRails.

## La compétence n'est pas reconnue par mon outil IA

**Symptôme :**

Taper `/proofrails` ne fait rien.

**Solution :**

1. Vérifiez que le fichier de compétence existe au bon chemin pour votre outil :
   ```bash
   # Claude Code
   ls ~/.claude/skills/proofrails/SKILL.md

   # Compétences Markdown de style OpenClaw / Hermes
   ls ~/.openclaw/workspace/skills/proofrails/SKILL.md
   ```

2. Pour Claude Code, vérifiez que les compétences sont activées dans les paramètres.

3. Pour les autres outils IA, consultez leur documentation sur le chargement des compétences. La compétence ProofRails est un fichier Markdown standard avec un frontmatter YAML — tout outil prenant en charge les compétences Markdown devrait la reconnaître.

4. En solution de repli, vous pouvez copier le contenu de `skill/SKILL.md` directement dans les instructions de votre outil IA.

## La découverte prend trop de temps sur une grande codebase

**Symptôme :**

ProofRails lit des centaines de fichiers pendant la découverte brownfield.

**Solution :**

ProofRails essaie d'être efficace mais les grandes codebases ont besoin de guidage :

1. Fournissez des points d'entrée plus précis dans votre invocation :
   ```
   /proofrails dans ./mon-projet corriger le timeout de paiement.
   Point d'entrée : src/payment/processor.ts. Tests existants : src/payment/__tests__/.
   ```

2. Si votre projet a un CLAUDE.md ou AGENTS.md, ProofRails le lit d'abord pour comprendre la structure du projet.

3. Pour les très grands monorepos, exécutez ProofRails limité à un module ou service spécifique.

## La porte d'approbation est ignorée

**Symptôme :**

L'IA a modifié le code source sans demander l'approbation.

**Solution :**

C'est un problème de comportement de l'IA, pas de ProofRails. Le SKILL.md de ProofRails exige explicitement l'approbation avant toute modification du code source. Si l'IA l'ignore :

1. Rappelez-lui : « Vous devez présenter la porte d'approbation avant de modifier le code source. »
2. Ajoutez une règle plus stricte dans CLAUDE.md :
   ```markdown
   # Règle absolue
   Ne JAMAIS modifier le code source sans approbation explicite de l'utilisateur.
   Présenter d'abord la portée, les risques et le plan.
   ```
3. Utilisez le système de permissions de votre outil IA pour exiger l'approbation des écritures de fichiers.

## Le répertoire de preuves se remplit

**Symptôme :**

`.evidence/` contient de nombreux fichiers volumineux.

**Solution :**

Les fichiers de preuves sont immuables mais vous pouvez archiver les anciens :

```bash
# Archiver les preuves de plus de 90 jours
find .evidence/ -type f -mtime +90 -exec mv {} .evidence/archive/ \;

# Ou utiliser git pour ne suivre que les preuves récentes
echo ".evidence/archive/" >> .gitignore
```

Le répertoire `.evidence/` est destiné aux preuves actuelles et récentes. Les anciennes preuves doivent toujours exister dans l'historique git et dans les changements archivés.
