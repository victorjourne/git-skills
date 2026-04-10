# git-skills

A collection of Claude Code skills for a Git/GitHub workflow — from issue creation to branch naming and commit conventions.

## Skills

### `/github-create-ticket <description>`

Creates a GitHub issue from a short description.

- Infers title, type, and labels automatically
- Shows a markdown preview with a confirm/modify/cancel UI (`AskUserQuestion`)
- Creates the issue via `gh` with no confirmation prompts

```
/github-create-ticket fix the login redirect bug
```

### `/git-branch <issue_number | description>`

Generates a branch name following the project convention: `<type>/<description-kebab>#<ticket>`.

- If given an issue number: fetches the title and labels from GitHub, infers the type, generates and creates the branch
- If given a free-form description: asks for the ticket number if missing

```
/git-branch 42
/git-branch "add worker logs" 
```

### `/git-commit`

Generates a conventional commit message with gitmojis.

Format: `type(scope): gitmoji short description`

Includes a "why" and "what" body, and a `closes #N` reference.

```
/git-commit
```

## Current workflow

```
/github-create-ticket <description>   → creates the issue
/git-branch <issue_number>            → creates the branch
... implement the fix ...
/git-commit                           → commits with conventions
```

> The resolve-issue step (reading the issue, implementing the fix, running tests) is not yet covered by a skill.

## End-to-end example

**Prompt:**
```
crée la branche pour résoudre l'issue 4 et implémente une solution, puis commit et push
```

**What happened:**

1. `/git-branch 4` was invoked — fetched issue #4 (`Create hello_world.py script`, label `enhancement`), inferred type `feat`, created branch `feat/hello-world-script#4`
2. `hello_world.py` was created with `print("Bonjour")`
3. `/git-commit` was invoked — staged the file and committed with the conventional format:
   ```
   feat: ✨ ajoute un script hello_world.py

   ## Pourquoi les changements ont été faits :
   - Besoin d'un script de démonstration affichant un message de bienvenue en français

   ## Quelles modifications ont été apportées :
   - Ajout de hello_world.py qui affiche 'Bonjour' via print()

   closes #4
   ```
4. Pushed to GitHub using `gh auth token` (HTTPS remote with token injection)

The entire flow — branch, implement, commit, push — was driven by a single natural language prompt, with skills handling the conventions automatically.

## TODO

- [ ] **Resolve-issue skill** — a skill that takes an issue number, reads the issue description, implements the fix, runs tests, and calls `/git-commit` automatically
- [ ] **Subagent composition** — explore mixing skills via subagents, e.g. a single `/fix 42` command that orchestrates `/git-branch`, the implementation, and `/git-commit` by spawning specialized subagents for each step
