# Cursor Rules

A curated collection of Cursor AI rules for consistent, production-grade code across projects.

## Quick Start

Copy the rules you need into your project's `.cursor/rules/` directory:

```bash
# Create the rules directory
mkdir -p .cursor/rules

# Copy desired rules (examples)
cp /path/to/cursor-rules/global/default.mdc .cursor/rules/
cp /path/to/cursor-rules/tech-stacks/typescript.mdc .cursor/rules/
cp /path/to/cursor-rules/tech-stacks/react.mdc .cursor/rules/
```

## Rule Loading Behavior

Rules are loaded based on their **frontmatter configuration**:

| Frontmatter | Behavior |
|-------------|----------|
| `alwaysApply: true` | Always loaded for every file |
| `globs: ["**/*.ts"]` | Loaded only when editing matching files |
| `alwaysApply: false` | Manual—must be explicitly referenced or attached |

## Recommended Structure

```
your-project/
└── .cursor/
    └── rules/
        ├── default.mdc          # Always-on (alwaysApply: true)
        ├── typescript.mdc       # Auto-loads for *.ts, *.tsx
        ├── go.mdc               # Auto-loads for *.go
        ├── terraform.mdc        # Auto-loads for *.tf, *.tfvars
        ├── react.mdc            # Auto-loads for *.tsx, *.jsx
        └── (optional concerns)
            ├── security.mdc     # Manual load
            ├── api-design.mdc   # Manual load
            ├── database.mdc     # Manual load
            └── ...
```

## Available Rules

### Global (Always Apply)

| File | Purpose | Context Cost |
|------|---------|--------------|
| `global/default.mdc` | Core coding standards, naming, testing, git conventions | ~450 tokens |

### Tech Stacks (Auto-Load by Glob)

| File | Globs | Purpose |
|------|-------|---------|
| `tech-stacks/typescript.mdc` | `**/*.ts`, `**/*.tsx`, `**/*.mts`, `**/*.cts` | TypeScript strict patterns, Zod, pino |
| `tech-stacks/go.mdc` | `**/*.go` | Effective Go, slog/zap, error handling |
| `tech-stacks/terraform.mdc` | `**/*.tf`, `**/*.tfvars`, `**/terragrunt.hcl` | IaC versioning, safety, Terragrunt |
| `tech-stacks/react.mdc` | `**/*.tsx`, `**/*.jsx` | React/Next.js patterns, accessibility |

### Project Level (Manual)

| File | Purpose | When to Use |
|------|---------|-------------|
| `project-level/monorepo.mdc` | pnpm + Turbo or multi-stack conventions | Monorepo projects |

### Concerns (Manual—Load When Needed)

These rules have `alwaysApply: false`. Load them only when working on related features to avoid context bloat.

| File | Purpose | When to Load |
|------|---------|--------------|
| `concerns/security.mdc` | Auth, input validation, secrets, headers | Auth features, user input, sensitive data |
| `concerns/api-design.mdc` | REST/GraphQL conventions, response shapes | Building APIs |
| `concerns/database.mdc` | Migrations, schema design, SQL style, N+1 | Database work |
| `concerns/observability.mdc` | Logging, metrics, tracing, alerting | Adding instrumentation |
| `concerns/cicd.mdc` | Pipeline structure, quality gates, deployment | CI/CD configuration |

### How to Load Manual Rules

**Option 1: Project-level attachment**
Add to your project's `.cursor/rules/` with modified frontmatter:

```yaml
---
alwaysApply: true  # Change from false
---
```

**Option 2: Reference in conversation**
Mention the rule file in chat: "Apply the security rules for this auth feature."

**Option 3: Selective globs**
Add globs to target specific directories:

```yaml
---
globs: ["**/api/**", "**/routes/**"]
alwaysApply: false
---
```

## Context Budget Strategy

To minimize token usage:

1. **Always include**: `default.mdc` (~450 tokens)
2. **Auto-load by file type**: tech-stack rules (~300 tokens each, only when relevant)
3. **Load on demand**: concern rules (~350 tokens each, only when needed)

**Typical session costs:**
- Go project: ~750 tokens (default + go)
- React/TS project: ~1050 tokens (default + typescript + react)
- Full-stack with API work: ~1400 tokens (above + api-design)

## Customization

### Override Rules Per-Project

Copy the rule and modify. Project-level rules in `.cursor/rules/` take precedence.

### Extend Rules

Create project-specific rules that complement these:

```yaml
---
description: Project-specific conventions
alwaysApply: true
---

# Project: MyApp

- Use `@/` path alias for imports
- Prefer Drizzle ORM over Prisma
- Auth via Clerk (never NextAuth)
```

## Rule Design Principles

1. **Concise**: Every token counts. No meta-commentary or redundant phrasing.
2. **Actionable**: Rules are directives, not suggestions.
3. **Composable**: Rules layer without conflict.
4. **Glob-targeted**: Tech rules auto-apply only to relevant files.
5. **Opt-in concerns**: Domain rules load manually to avoid bloat.

## File Format

All rules use `.mdc` (Markdown Cursor) format with YAML frontmatter:

```yaml
---
description: Brief description shown in Cursor UI
globs: ["**/*.ts"]      # Optional: file patterns to match
alwaysApply: false      # true = always load, false = manual/glob-only
---

# Section

- Rule 1
- Rule 2
```

## Contributing

When adding or modifying rules:

1. Keep rules atomic and focused
2. Prefer shorter phrasing without losing clarity
3. Test with real projects before committing
4. Update this README if adding new files
