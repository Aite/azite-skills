# User Journey Audit Skill

> **Spec-driven QA for real codebases** — Detect gaps between what your product should do and what your code actually implements.

Modern teams ship features fast, but:
- user flows break silently
- specs drift from implementation
- UX gaps go unnoticed

This skill audits your codebase against product expectations and shows exactly what’s missing.

---

## Quick Start

```bash
# run audit using specs
claude code "audit user journey using docs/specs.md"

# or let specs guide journey generation
claude code "audit user journey using docs/product-spec.md"
```

---

## What It Does

The skill analyzes your codebase and detects:

- Missing pages or screens
- Broken or missing navigation flows
- Incomplete forms (missing required fields)
- UX gaps (loading, errors, confirmations)
- Backend validation issues
- Data model inconsistencies

It produces a **concrete, file-level report with fixes**.

---

## Example Output

```md
## Journey: Post Listing
Coverage: 55%

### Missing
- page `/listings/new` not implemented
- confirmation step after submit

### Partial
- location field missing from form

### UX Issues
- no loading state during submit
- no error feedback

### Backend Issues
- location optional in data model but required in spec

### Fixes
- create page for `/listings/new`
- add location field to form
- enforce validation in backend
```

---

## Features

| Feature | Description |
|--------|-------------|
| Page Detection | Scans project structure to identify screens/pages |
| Navigation Analysis | Detects missing or broken flows between steps |
| Form Analysis | Finds missing required inputs |
| API Detection | Identifies missing or incomplete endpoints |
| Validation Detection | Detects schema and manual validation |
| Data Consistency Check | Compares UI inputs with backend/data layer |
| UX Detection | Flags missing loading, error, and success states |
| Coverage Report | Measures completeness of implementation |

---

## Installation

### Project-level (recommended)

```bash
mkdir -p .claude/skills
cp -r skills/user-journey-audit .claude/skills/
```

### Global

```bash
cp -r skills/user-journey-audit ~/.claude/skills/
```

---

## Usage

### 1. With Explicit Journey Specs

```bash
claude code "audit user journey using docs/specs.md"
```

Your specs should follow this format:
```markdown
## Journey: Create Listing

1. User navigates to create listing page
2. Fills listing form:
   - title
   - price
   - description
3. Submits form
4. Sees confirmation message
```

### 2. With Product/Business Requirements (PRD)

If you don't have explicit journeys, provide product or business requirements:

If you don’t have explicit journeys, provide product/business specs:

```bash
claude code "audit user journey using docs/product-spec.md"
```

The skill will:
- Interpret product/business requirements
- Synthesize expected user journeys
- Document assumptions made during synthesis
- Audit implementation against generated journeys

**Example PRD format:**
```markdown
## Features

### Listing Management
- Users can create listings with title, price, description
- System must validate all required fields
- Users receive confirmation after successful submission
```

⚠️ Journey synthesis is heuristic-based and includes documented assumptions.

### 3. Alternative Triggers

```bash
claude code "check journey coverage compared to the docs/specs.md"
claude code "what's missing from my implementation compared to the docs/specs.md"
claude code "compare with specs docs/specs.md"
claude code "validate against product specs docs/product-spec.md"
```

---

## Spec Format

The skill accepts two formats:

### Format 1: Explicit User Journeys

Write your user journeys directly as markdown:

```markdown
## Journey: Post Listing

1. User signs in
2. Clicks "Add Listing"
3. Fills listing form:
   - title
   - price
   - description
   - location
4. Submits form
5. Sees confirmation message

## Journey: Browse Listings

1. User navigates to home page
2. Views list of listings
3. Each listing shows:
   - title
   - price
   - thumbnail
```

### Spec Format Rules

- **Journey headers**: Use `## Journey: [Name]`
- **Steps**: Numbered lists `1. Step description`
- **Required fields**: Nested bullet points under steps
- **Action types**: Automatically inferred from keywords (sign, click, fill, submit, etc.)

### Format 2: Product/Business Requirements (PRD)

Provide your product or business requirements in any structured markdown format:

```markdown
## Product Requirements

### Core Features
- Users can create and manage listings
- Listings require title, price, and description
- System validates all inputs
- Confirmation shown after submission

### User Stories
- As a user, I want to create listings to sell items
- As a user, I want to see confirmation when my listing is posted
```

The skill will synthesize journeys from these requirements automatically.

---

## Output Example

The skill generates a comprehensive markdown report:

```markdown
# User Journey Audit Report

## Journey Generation Notes

The following journeys were synthesized from the provided product requirements.

**Assumptions:**
- "Create listing" flow assumes a dedicated form page at /listings/new
- Validation assumed to happen on both client and server
- Confirmation assumed to be an in-app message

## Summary

**Overall Coverage:** 65% (C)

### Quick Stats

| Metric | Count |
|--------|-------|
| Total Journeys | 3 |
| Missing Steps | 2 |
| Partial Implementations | 3 |
| UX Gaps | 4 |
| Backend Gaps | 1 |

## Journey: Post Listing
Coverage: 60%

### Missing
- location field (form input not found)
- confirmation page (/listings/success not found)

### Partial
- description has no validation

### UX Issues
- no loading state during submit
- no error handling shown to user

### Suggested Fixes
- Add `<input name="location">` in /app/listings/new/page.tsx
- Add validation schema in /app/api/listings/route.ts
```

---

## Example Specs

See the `examples/specs/` directory for example specifications:

- **basic-journey.md** - Simple listing management
- **ecommerce-journey.md** - Complete e-commerce flow

## Example Implementations

See the `examples/nextjs-prisma/` directory:

- **complete/** - Full implementation with all features
- **incomplete/** - Intentionally incomplete for testing

---

## Coverage Model
The skill calculates weighted coverage:

| Component | Weight | Description |
|-----------|--------|-------------|
| Steps | 40% | Page/navigation detection |
| Fields | 40% | Form field completeness |
| UX | 20% | Loading, errors, confirmations |

**Overall Grade Scale:**
- **A+ (95%+)**: Excellent
- **A (85%+)**: Very Good
- **B (75%+)**: Good
- **C (60%+)**: Fair
- **D (40%+)**: Poor
- **F (<40%)**: Critical

---

## Detection Rules (Simplified)

### Pages / Screens
- scans project files to identify user-facing views
- infers intent from naming patterns

### Navigation
- detects links, redirects, and transitions

### Forms
- detects forms, inputs, and handlers

### Backend
- detects endpoints and request handling
- matches POST endpoints to actions

### Validation
- detects schema validation and manual checks

### UX Pattern

| Pattern | Detection Indicators |
|---------|---------------------|
| Loading | `loading.tsx`, `isLoading`, `<Suspense>` |
| Error Handling | `try/catch`, `error.tsx`, error state variables |
| Success Confirmation | `router.push` + success, toast notifications |
| Empty State | `.length === 0`, `EmptyState` component |

---

## CI/CD Integration

Run the skill automatically in your CI pipeline:

### GitHub Actions

```yaml
name: User Journey Audit
on: [push, pull_request]

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Journey Audit
        run: |
          claude code "audit user journey using docs/specs.md"
      - name: Check Coverage
        run: |
          # Fail if coverage < 90%
          if [ $COVERAGE -lt 90 ]; then
            echo "Coverage too low: $COVERAGE%"
            exit 1
          fi
```

Note: parsing coverage from output requires custom scripting.

---

## Limitations

- Heuristic-based (not 100% accurate)
- Journey synthesis from PRDs involves assumptions (documented in output)
- Static analysis only (does not run the app)
- Complex or highly dynamic apps may reduce accuracy

---

## When Not to Use

- very small prototypes
- apps without defined flows
- as a replacement for real QA/testing

---

## Roadmap

- framework-specific optimizations
- E2E test generation
- dead-end flow detection
- i18n validation

---

## Positioning

This is not just a code review tool.

It is:

**Spec-driven QA automation for user journeys**

Use it to:
- validate MVP completeness
- enforce product consistency
- catch UX regressions early

---

## Contributing

Contributions welcome! Areas of contribution:

1. **New framework scanners** - Add support for your favorite framework
2. **Improved heuristics** - Better detection patterns
3. **Additional validators** - New validation libraries
4. **Test cases** - More example specs and implementations

---

## License

MIT License - feel free to use in your projects!

---

## Acknowledgments

Built for Claude Code - the AI-powered development environment.

**Use Cases:**
- ✅ Startups validating MVP completeness
- ✅ Teams enforcing product consistency
- ✅ Preventing silent UX regressions
- ✅ QA automation for spec-driven development
