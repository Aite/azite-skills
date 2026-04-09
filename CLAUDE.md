# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a collection of reusable Claude Code skills. Skills are specialized prompts that extend Claude Code's capabilities for specific tasks.

## Repository Structure

```
aite-skills/
└── skills/
    └── user-journey-audit/        # Spec-driven QA skill
        ├── README.md              # Skill documentation
        └── specs/                 # Build and publish guides
```

## Skill: user-journey-audit

**Purpose:** Audits a codebase against product specifications to detect gaps in user journey implementation.

**Installation:**
- Project-level: `mkdir -p .claude/skills && cp -r skills/user-journey-audit .claude/skills/`
- Global: `cp -r skills/user-journey-audit ~/.claude/skills/`

**Usage:**
```bash
# Audit using defined journey specs
claude code "audit user journey using docs/specs.md"

# Alternative triggers
claude code "check journey coverage compared to docs/specs.md"
claude code "validate against product specs docs/product-spec.md"
```

**Spec Format:**
```markdown
## Journey: [Journey Name]

1. Step description
   - required field 1
   - required field 2
2. Next step description
```

**Output:** Structured markdown report with coverage percentage, missing steps, partial implementations, UX issues, and suggested fixes.

## Adding New Skills

Create new skills under `/skills/<skill-name>/` with:
- `README.md` - Documentation (what it does, installation, usage, examples)
- `SKILL.md` (optional) - Skill definition file if needed
- Supporting documentation in subdirectories as appropriate

Follow the user-journey-audit skill as a template for documentation structure.
