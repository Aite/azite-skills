# User Journey Audit Skill (Claude Code)

## Overview
This guide explains how to design, implement, validate, and publish a reusable Claude Code skill that audits a codebase against product specifications and detects gaps in the user journey.

---

## 1. Define the Problem Clearly

### Goal
Detect mismatches between:
- Product specifications (expected behavior)
- Actual implementation (codebase)

### Output
The skill must produce:
- Coverage report per user journey
- Missing steps
- Partial implementations
- UX gaps
- Concrete file-level fixes

---

## 2. Define the Skill Contract

### Inputs
- `specs_file`: markdown/json product specs
- `codebase_root`: project root

### Outputs
- Structured audit report
- Actionable fixes

---

## 3. Design the Skill Logic

### Core Pipeline

1. Parse specs
2. Extract journeys
3. Scan codebase
4. Map implementation to journeys
5. Detect gaps
6. Generate report

---

## 4. Create the Skill File

Create a file:

```
skills/user-journey-audit/SKILL.md
```

### Example

```yaml
name: user-journey-audit
version: 1.0

description: |
  Audits codebase against product specs and detects missing user journey steps.

triggers:
  - audit user journey
  - compare with specs
  - what's missing

inputs:
  - specs_file
  - codebase_root

steps:
  - Parse specs into journeys
  - Identify actors and steps
  - Scan routes and pages
  - Scan forms and fields
  - Scan API endpoints
  - Map journey steps to implementation
  - Detect missing or partial steps
  - Detect UX issues (loading, errors, empty states)
  - Generate structured report

output:
  - coverage_summary
  - missing_features
  - ux_issues
  - backend_gaps
  - suggested_fixes
```

---

## 5. Implement Spec Parsing Strategy

### Expected Spec Format (recommended)

```md
## Journey: Post Listing

1. User signs in
2. Clicks Add Listing
3. Fills form:
   - title
   - price
4. Submits
5. Sees confirmation
```

### Extraction Rules
- Each numbered item = step
- Nested bullets = required fields
- Titles = journey names

---

## 6. Codebase Scanning Strategy

### Detect:

#### Frontend
- Routes (`/app`, `/pages`)
- Forms and inputs
- Buttons and actions

#### Backend
- API endpoints
- Validation logic
- DB interactions

#### UX
- Loading states
- Error handling
- Redirects

---

## 7. Mapping Logic

For each step:

| Step | Expected | Found | Status |
|------|---------|------|--------|
| Fill form | title, price | title only | Partial |

### Status Rules
- Missing → no implementation found
- Partial → incomplete fields or logic
- Complete → fully implemented

---

## 8. Gap Detection Rules

### Functional Gaps
- Missing fields
- Missing endpoints
- Missing navigation steps

### UX Gaps
- No loading state
- No error feedback
- No success confirmation

### Backend Gaps
- No validation
- Weak schema enforcement

---

## 9. Output Format

### Example

```md
## Journey: Post Listing
Coverage: 60%

### Missing
- location field
- image upload

### Partial
- description has no validation

### UX Issues
- no loading state
- no error handling

### Suggested Fixes
- add input in /app/listings/new/page.tsx
- add validation in API route
```

---

## 10. Local Testing

### Test Cases

1. Complete implementation → expect 100%
2. Missing features → detect gaps
3. Broken UX → flag issues

### Command

```
claude code
> audit user journey using specs.md
```

---

## 11. Improve Accuracy

Add heuristics:

- Match routes to actions ("Add", "Create")
- Detect forms via `<form>` and handlers
- Detect validation via schema libs (Zod, Yup)

---

## 12. Package the Skill

Directory structure:

```
/skills/user-journey-audit/
  SKILL.md
  README.md
  examples/
```

---

## 13. Write README

Include:
- What it does
- Example input/output
- Supported frameworks (Next.js, Node.js)
- Limitations

---

## 14. Publish as Public Skill

### Steps

1. Push to GitHub
2. Add clear README
3. Tag version (v1.0)
4. Submit to skill directories:
   - Claude skill marketplaces
   - Community repos

---

## 15. Optional Enhancements

- Generate E2E tests automatically
- Detect dead-end flows
- Validate i18n coverage
- Integrate with CI/CD

---

## 16. Positioning (Important)

This is not just a skill.

It is:

**Spec-driven QA automation for codebases**

High-value use cases:
- startups validating MVP completeness
- teams enforcing product consistency
- preventing silent UX regressions

---

## 17. Next Step

Integrate into your workflow:

- run after feature development
- run before deployment
- use as QA gate

---

End of guide

