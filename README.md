# Project Overview

This is a collection of reusable Claude Code skills. Skills are specialized prompts that extend Claude Code's capabilities for specific tasks.

# Repository Structure

```
aite-skills/
└── skills/
    ├── user-journey/              # Spec-driven user journey synthesising
    |   ├── README.md              # Skill documentation
    |   └── SKILL.md               # Skill file
    └── user-journey-audit/        # Spec-driven QA skill
        ├── README.md              # Skill documentation
        ├── SKILL.md               # Skill file
        └── specs/                 # Build and publish guides
```

# Adding New Skills

Create new skills under `/skills/<skill-name>/` with:
- `README.md` - Documentation (what it does, installation, usage, examples)
- `SKILL.md` (optional) - Skill definition file if needed
- Supporting documentation and scripts in subdirectories as appropriate

Follow the user-journey-audit skill as a template for documentation structure.
