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
/skills/user-journey-audit/SKILL.md
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

---

## 18. Next.js + Prisma: Concrete Implementation Rules

### Project Assumptions
- Next.js App Router (`/app`)
- API routes under `/app/api/*/route.ts`
- Forms in client components
- Prisma schema in `/prisma/schema.prisma`

---

### A) Route Detection

Scan:
```
/app/**/page.tsx
/app/**/layout.tsx
```

Map routes:
- `/app/listings/new/page.tsx` → "Add Listing"
- `/app/listings/[id]/page.tsx` → "View Listing"

Heuristics:
- folder name verbs → intent (new/create/edit)
- presence of `useRouter().push()` → navigation step

---

### B) Form & Field Detection

Detect forms via:
- `<form` tags
- `useForm` (react-hook-form)
- `onSubmit` handlers

Extract fields:
- `<input name="..." />`
- `<textarea name="..." />`
- controlled inputs (`value` + `onChange`)

Map to spec fields:
- normalize names: `price`, `listingPrice`, `amount` → "price"

---

### C) API Detection

Scan:
```
/app/api/**/route.ts
```

Detect:
- `POST` → create actions
- `GET` → read

Match to journeys:
- "Submit listing" → POST `/api/listings`

---

### D) Validation Detection

Look for:
- Zod (`z.object({...})`)
- Yup schemas
- manual checks (`if (!title) throw`)

Rules:
- missing required fields in schema → backend gap
- mismatch with spec fields → inconsistency

---

### E) Prisma Schema Cross-Check

Scan:
```
/prisma/schema.prisma
```

Validate:
- required fields exist in DB model
- optional vs required matches spec

Example:
```
model Listing {
  title String
  price Int
  location String?
}
```

→ if spec requires `location`, flag as mismatch

---

### F) UX Detection Rules

#### Loading
- look for `loading.tsx` or `isLoading`

#### Errors
- `try/catch` in submit
- error state rendering

#### Success
- redirect after submit (`router.push`)
- confirmation page exists

Flag if missing.

---

### G) Auth Flow Detection

Check:
- middleware (`/middleware.ts`)
- auth guards in pages

If journey requires auth but route is public → gap

---

## 19. Example: Real Audit Output (Next.js)

```md
## Journey: Post Listing
Coverage: 55%

### Missing
- image upload component (no file input detected)
- confirmation page (/listings/success not found)

### Partial
- location field exists in UI but not saved in API

### Backend Gaps
- Prisma model has optional location
- API does not validate price > 0

### UX Issues
- no loading indicator during submit
- errors not shown to user

### Files to Update
- /app/listings/new/page.tsx
- /app/api/listings/route.ts
- /prisma/schema.prisma
```

---

## 20. CI/CD Integration (High Impact)

Run the skill automatically in CI:

### Example (GitHub Actions)

```
- name: User Journey Audit
  run: |
    claude code "audit user journey using docs/specs.md"
```

Fail condition:
- coverage < 90%
- critical missing steps

---

## 21. Versioning Strategy

- v1: basic detection (routes + forms + API)
- v2: schema + validation
- v3: UX + i18n + performance

---

## 22. Publishing Positioning

Title:
"Spec-Driven User Journey Audit for Next.js + Prisma"

Tags:
- code-review
- qa-automation
- nextjs
- prisma

Audience:
- startups
- solo founders
- product engineers

---

## 23. Example README Snippet

```md
This skill audits your Next.js + Prisma app against product specs and detects missing user journey steps.

Supports:
- App Router
- API routes
- Prisma schema

Outputs:
- coverage report
- missing features
- exact file fixes
```

---

End of guide

