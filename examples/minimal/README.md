# Minimal ProofRails Setup

This example shows the smallest possible ProofRails setup on an empty repository.

## Starting point

```bash
mkdir my-project && cd my-project && git init
```

## Steps

### 1. Copy ProofRails files

```bash
cp -r /path/to/proofrails/{skill,scripts,templates} ./
```

### 2. Bootstrap

```bash
./scripts/proofrails-bootstrap
```

### 3. Verify

```bash
./scripts/proofrails-check
./scripts/proofrails-lint
```

### 4. Customize

Edit `CLAUDE.md` to add your project's build and test commands:

```markdown
## Build and test

```bash
# Build
make build

# Test
make test

# Lint
make lint
```
```

## Result

```text
my-project/
├── CLAUDE.md
├── AGENTS.md
├── .proofrails/
│   ├── changes/
│   └── runs/
├── planwithfile/
├── .evidence/
├── skill/
│   └── SKILL.md
└── templates/
    ├── AGENTS.md
    ├── CLAUDE.md
    ├── openspec-change.md
    └── superpowers-execution.md
```

## Next steps

- Follow the [quickstart](../../docs/quickstart.md) to use the skill.
- Read [concepts](../../docs/concepts.md) to understand the workflow.
- Check [recipes](../../docs/recipes.md) for common patterns.
