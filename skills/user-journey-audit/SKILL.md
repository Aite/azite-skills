---
name: user-journey-audit
version: 1.0.0
description: |
  Audits codebases against product specifications and detects gaps in user journey implementation.

  **When to use this skill:**
  - User asks to "audit user journey", "check journey coverage", "validate against specs"
  - User mentions comparing implementation against product requirements, specs, or PRDs
  - User asks "what's missing from my implementation" or "compare with specs"
  - User wants to detect UX gaps, missing features, or incomplete flows
  - Any request to validate code completeness against documented requirements

  **What it does:**
  - Parses product specs (journey definitions or PRDs) to extract expected user flows
  - Scans codebase for pages, routes, forms, API endpoints, and UX patterns
  - Maps implementation to expected journey steps
  - Generates structured report with coverage percentage, missing steps, partial implementations, UX gaps, and concrete fixes

triggers:
  - audit user journey
  - check journey coverage
  - validate against product specs
  - compare with specs
  - what's missing from implementation
  - detect UX gaps
  - journey completeness check
  - spec-driven audit
  - product requirements validation

inputs:
  specs_file: Path to product specifications (markdown or JSON)
  codebase_root: Root directory of codebase to audit (default: current working directory)

outputs:
  - coverage_summary: Overall coverage percentage across all journeys
  - journey_reports: Per-journey breakdown with missing steps, partial implementations, UX issues
  - backend_gaps: Validation and schema inconsistencies
  - suggested_fixes: File-level, actionable fixes with specific paths
  - journey_generation_notes: When using PRDs, documents assumptions made during journey synthesis
---

# User Journey Audit Skill

## Overview

This skill performs spec-driven QA by auditing a codebase against product specifications. It detects mismatches between expected behavior (from specs) and actual implementation (in code).

**Core Pipeline:**
1. Parse specs → Extract user journeys (or synthesize from PRDs)
2. Scan codebase → Identify pages, forms, APIs, UX patterns
3. Map implementation → Match code to journey steps
4. Detect gaps → Find missing, partial, or broken implementations
5. Generate report → Produce structured, actionable audit output

---

## Step 1: Parse Specifications

First, read and parse the specs file provided by the user.

### Determine Spec Format

Check which format the user provided:

**Format A: Explicit User Journeys**
- Contains `## Journey: [Name]` headers
- Has numbered steps under each journey
- May include nested bullets for required fields

**Format B: Product/Business Requirements (PRD)**
- Contains feature descriptions, user stories, or requirement lists
- May use sections like `## Features`, `## Requirements`, `## User Stories`
- Does NOT have explicit `## Journey:` headers

### For Explicit Journeys

Extract each journey:

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
```

Extraction rules:
- Journey name: text after `## Journey:`
- Steps: numbered items under journey
- Required fields: nested bullet points under steps
- Actors: typically "User" (could be others if specified)

### For PRDs (Journey Synthesis)

When the user provides product/business requirements without explicit journeys:

1. **Interpret the requirements** to understand expected user flows
2. **Synthesize plausible user journeys** based on the requirements
3. **Document your assumptions** clearly in the final report under "Journey Generation Notes"

Example PRD interpretation:
```markdown
## Product Requirements

### Listing Management
- Users can create listings with title, price, description
- System must validate all required fields
- Users receive confirmation after successful submission
```

Synthesized journey:
1. User navigates to create listing page
2. User fills listing form (title, price, description)
3. User submits form
4. System validates inputs
5. User sees confirmation

**Important:** When synthesizing journeys, make reasonable assumptions but document them. For example:
- Assume standard REST patterns for API endpoints
- Assume form pages follow framework conventions
- Assume confirmation is inline rather than separate page (unless specified)

---

## Step 2: Scan Codebase

For each journey, scan the codebase to detect implementation.

### Detection Strategy

Use Glob and Grep tools to find relevant code. Scan for:

#### Frontend: Pages and Routes

- **Next.js App Router:** `glob("**/app/**/page.tsx")`, `glob("**/app/**/route.ts")`
- **Next.js Pages Router:** `glob("**/pages/**/index.tsx")`, `glob("**/pages/**.tsx")`
- **React Router:** Look for route configurations in `src/routes`, `src/App.tsx`
- **Vue:** `glob("**/views/**/*.vue")`, `glob("**/pages/**/*.vue")`
- **General:** Look for common patterns like `pages/`, `views/`, `screens/`

Match journey steps to pages:
- Step mentions "sign in" → Look for login, signin, auth pages
- Step mentions "create listing" → Look for `/listings/new`, `/listings/create`
- Step mentions "browse" → Look for index, list, browse pages

#### Frontend: Navigation
- Look for a starting point for each journey (e.g., homepage, dashboard)
- Look for links or buttons that match expected navigation steps
- Check for presence in nav bars, dropdowns, or side menus
- Look for redirects in page components that match expected flows
- Check for dynamic routing patterns (e.g., `[id].tsx` for listing details)

#### Frontend: Components in Pages
- Look for components that are not used in the implemented pages: Grep for component usage in page files
- Look for form components, buttons, links that match expected steps

#### Frontend: Forms and Fields

For each step involving form filling:

1. **Find the form component/page**
   - Grep for form keywords: `<form`, `useForm`, `react-hook-form`, `Formik`
   - Look for submit handlers: `onSubmit`, `handleSubmit`

2. **Extract form fields**
   - Grep for input elements: `<input`, `<textarea`, `<select`
   - Look for name attributes: `name="title"`, `name="price"`
   - Check for form validation: `zod.schema`, `yup.object`, `validation`

3. **Match expected fields**
   - For each required field in spec, verify implementation exists
   - Check field types (text, number, email, etc.)

#### Backend: API Endpoints

- **Next.js API Routes:** `glob("**/api/**/route.ts")`, `glob("**/pages/api/**/*.ts")`
- **Express:** Look for `app.post`, `app.get`, `router.*` in server files
- **General REST:** Grep for HTTP methods and endpoints

Match actions to endpoints:
- "create", "submit", "add" → POST endpoints
- "list", "browse", "get" → GET endpoints
- "update", "edit" → PUT/PATCH endpoints

#### Backend: Validation and Schemas

- **Schema validation:** Grep for `zod`, `yup`, `joi`, `class-validator`
- **TypeScript types:** Look for interface/type definitions matching entities
- **Database schemas:** Look for Prisma models (`schema.prisma`), Mongoose schemas, SQL migrations

#### UX Patterns

Detect these UX elements:

| Pattern | Detection Indicators |
|---------|---------------------|
| Loading State | `loading.tsx`, `isLoading` states, `<Suspense>`, spinners |
| Error Handling | `try/catch` blocks, `error.tsx`, error state variables, error boundaries |
| Success Confirmation | `toast()`, notifications, success messages, redirect after success |
| Empty State | `.length === 0` checks, `<EmptyState>` components, conditional rendering |

---

## Step 3: Map Implementation to Journey Steps

For each journey, create a mapping table:

| Step | Expected | Implementation Found | Status |
|------|----------|---------------------|--------|
| Sign in | Auth page | `/app/auth/signin/page.tsx` found | Complete |
| Click Add Listing | Navigation | Link/button found in nav | Complete |
| Fill form: title | title field | `<input name="title">` found | Complete |
| Fill form: price | price field | `<input name="price">` found | Complete |
| Fill form: location | location field | NOT FOUND | **Missing** |
| Submit form | Form submit | `handleSubmit` found, calls API | Complete |
| See confirmation | Confirmation message | No toast/confirmation found | **Missing** |

### Status Rules

- **Complete**: All expected elements found and properly implemented
- **Partial**: Some elements found but incomplete (e.g., field exists but no validation)
- **Missing**: No implementation found for expected element

---

## Step 4: Calculate Coverage

For each journey, calculate weighted coverage:

| Component | Weight |
|-----------|--------|
| Steps (pages/routes) | 40% |
| Fields (form completeness) | 40% |
| UX (loading, errors, confirmations) | 20% |

**Formula:**
```
Step Score = (Complete Steps / Total Steps) × 40
Field Score = (Complete Fields / Total Fields) × 40
UX Score = (UX Elements Present / UX Elements Expected) × 20

Total Coverage = Step Score + Field Score + UX Score
```

**Overall Grade Scale:**
- A+ (95%+): Excellent
- A (85%+): Very Good
- B (75%+): Good
- C (60%+): Fair
- D (40%+): Poor
- F (<40%): Critical

---

## Step 5: Generate Audit Report

**CRITICAL:** Always output the report in this exact markdown format:

```markdown
# User Journey Audit Report

## Journey Generation Notes

[Only include this section if you synthesized journeys from a PRD]

The following journeys were synthesized from the provided product requirements.

**Assumptions made:**
- [List each key assumption with rationale]
- [e.g., "Create listing flow assumes a dedicated form page at /listings/new"]
- [e.g., "Validation assumed to happen on both client and server"]
- [e.g., "Confirmation assumed to be an in-app toast message"]

---

## Summary

**Overall Coverage:** XX% (Grade)

### Quick Stats

| Metric | Count |
|--------|-------|
| Total Journeys | N |
| Journeys with 100% Coverage | N |
| Missing Steps | N |
| Partial Implementations | N |
| UX Gaps | N |
| Backend Gaps | N |

---

## Journey: [Journey Name]

**Coverage:** XX% (Grade)

### Implementation Mapping

| Step | Expected | Found | Status |
|------|----------|-------|--------|
| ... | ... | ... | ... |

### Missing Features

- [feature name] - [file/location where it should exist]
- [feature name] - [file/location where it should exist]

### Partial Implementations

- [feature name] - [what's missing or incomplete]
- [feature name] - [what's missing or incomplete]

### UX Issues

- [issue description] - [where it's missing]
- [issue description] - [where it's missing]

### Backend Issues

- [issue description] - [file/location]
- [issue description] - [file/location]

### Suggested Fixes

1. [File path]: [specific fix]
2. [File path]: [specific fix]

---

[Repeat for each journey]

---

## Data Consistency Issues

[If found, list inconsistencies between frontend forms and backend schemas]

---

## Critical Issues Summary

[List any issues that completely break user flows]

---

## Recommendations

[Prioritized list of improvements]
```

---

## Framework-Specific Guidance

### Next.js App Router

- Pages: `app/**/page.tsx`
- API Routes: `app/api/**/route.ts`
- Loading states: `loading.tsx`, `error.tsx`
- Forms: Server actions via `"use server"`

### Next.js Pages Router

- Pages: `pages/**/*.tsx`
- API Routes: `pages/api/**/*.ts`
- Loading: Custom components

### React + React Router

- Routes: Check `App.tsx` or router configuration
- Forms: Typically in `src/pages`, `src/components`
- API calls: Look for `fetch`, `axios` in services

### Vue + Vue Router

- Pages: `src/views/**/*.vue`, `src/pages/**/*.vue`
- Router: `src/router/index.ts`
- Forms: Check `v-model`, `<form>` elements

### Express Backend

- Routes: Look for `app.*` patterns
- Controllers: `src/controllers/**/*.ts`
- Middleware: Validation middleware
- Schemas: Often in separate `src/schemas` or `src/validators`

---

## Important Heuristics

When scanning codebases:

1. **Match intent, not just names** - A "create listing" step might be at `/listings/new`, `/listings/create`, or even `/post-ad`
2. **Check for redirects** - A step might be implemented as a redirect rather than a page
3. **Look for composed components** - Form might be split across multiple components
4. **Check both client and server** - Validation might happen in Zod schema, API handler, or database
5. **Consider conditional flows** - Some steps might only appear for certain user roles or states
6. **Be framework-aware** - Different frameworks have different patterns

---

## Output Delivery

After generating the report:

1. **Write to file:** Save the report as `journey-audit-report.md` in the codebase root
2. **Show summary:** Display the overall coverage and key findings to the user
3. **Offer next steps:** Ask if user wants help implementing any of the suggested fixes

---

## Error Handling

If issues occur:

- **Specs file not found:** Ask user to provide the correct path
- **No journeys found:** Explain format requirements and offer to help format specs
- **Codebase scan fails:** Check if codebase_root is correct, try current directory
- **Framework not recognized:** Use generic scanning patterns and note limitations

---

## Example Invocation

```bash
# User provides specs file
claude code "audit user journey using docs/product-specs.md"

# User specifies codebase root
claude code "audit user journey using docs/specs.md in src/"

# Alternative triggers
claude code "check journey coverage compared to docs/specs.md"
claude code "what's missing from my implementation compared to docs/specs.md"
```

---

## Notes

- This is a heuristic-based audit, not runtime testing
- Accuracy depends on code organization and naming conventions
- Complex or highly dynamic apps may reduce accuracy
- Results should be reviewed by a human familiar with the codebase
- Use as a QA gate, not a replacement for manual testing
