---
name: user-journey
version: 1.0.0
description: |
  Generates comprehensive user journey documentation from business specifications or PRD files.

  **When to use this skill:**
  - User asks to "create user journeys", "generate journey documentation", "extract journeys from specs"
  - User mentions converting PRDs, business requirements, or product specs into user journeys
  - User asks to "document user flows", "map user experiences", "create journey maps"
  - User wants to transform raw requirements into structured, actionable journey documentation
  - User asks to "clarify and document user paths" or "create UX journey documents"

  **What it does:**
  - Parses business specifications or PRD files to extract product requirements
  - Synthesizes comprehensive, multi-layered user journeys from requirements
  - Interactively validates ambiguous requirements with the user
  - Generates visual Mermaid flow diagrams for each journey
  - Creates state diagrams showing system state transitions
  - Produces journey relationship maps showing flow connections
  - Generates structured journey documentation with steps, decision points, edge cases, and alternative paths
  - Creates production-ready journey files that teams can use for design, development, and QA

triggers:
  - create user journeys
  - generate journey documentation
  - extract journeys from specs
  - document user flows
  - map user experiences
  - create journey maps
  - convert PRD to journeys
  - document user paths
  - create UX journey documents
  - synthesize user journeys

parameters:
  validation_threshold: 80%  # Require user validation if confidence falls below this
  include_alternative_paths: true  # Always document alternative flows and edge cases

inputs:
  specs_file: Path to business specifications, PRD, or requirements document (markdown, JSON, or text)
  output_file: Path where journey documentation will be saved (default: user-journeys.md in current directory)
  journey_depth: Level of detail - "basic", "detailed", or "comprehensive" (default: "detailed")

outputs:
  - journey_document: Structured markdown file with all user journeys
  - journey_summary: High-level overview of journeys discovered
  - validation_notes: Documented decisions and assumptions made during interactive validation
  - edge_cases: Documented alternative paths and error scenarios
  - actors: List of user roles/personas identified
  - prerequisites: Conditions required for journeys to execute
---

# User Journey Generator Skill

## Overview

This skill transforms raw business specifications, PRDs, or requirement documents into comprehensive, production-ready user journey documentation. It bridges the gap between business requirements and implementation-ready documentation.

**Core Pipeline:**
1. Parse specifications → Extract features, user stories, and requirements
2. Identify actors → Determine user roles and personas
3. Synthesize journeys → Map complete user flows with decision points
4. Interactive validation → Clarify ambiguous requirements with user
5. Document alternatives → Capture edge cases, error paths, and alternative flows
6. Generate diagrams → Create Mermaid flow charts, state diagrams, and relationship maps
7. Generate output → Produce structured journey documentation

---

## Step 1: Parse Specifications

First, read and analyze the specs file provided by the user.

### Identify Specification Type

Determine which format the user provided:

**Type A: Structured PRD**
- Contains sections like `## Features`, `## Requirements`, `## User Stories`
- May include `## Actors`, `## Personas`, or `## User Roles`
- Uses structured lists for requirements
- May have success criteria or acceptance criteria

**Type B: Business Requirements Document**
- Focuses on business goals and objectives
- Describes problems to be solved
- May include stakeholder requirements
- Often less structured from a technical perspective

**Type C: Feature Specifications**
- Detailed descriptions of specific features
- May include technical constraints
- Often has UI/UX descriptions or mockup references

**Type D: Raw Notes or Loose Requirements**
- Unstructured or semi-structured text
- Bullet points of desired capabilities
- May need significant interpretation

### Extract Key Information

For each specification type, extract:

1. **Actors/Personas**: Who is performing the actions?
   - Look for: "User", "Admin", "Customer", "Manager", roles
   - Note any permissions or privilege levels mentioned

2. **Features/Functions**: What can users do?
   - Action verbs: create, read, update, delete, manage, view
   - Entities being acted upon: listings, orders, profiles, etc.

3. **Business Rules**: What constraints exist?
   - Validation rules, permissions, conditions
   - "Must", "should", "required", "optional" keywords

4. **Success Criteria**: What does "done" look like?
   - Explicit acceptance criteria
   - Desired outcomes and deliverables

---

## Step 2: Identify User Journey Candidates

From the extracted information, identify distinct user journeys.

### Journey Detection Heuristics

A journey should be created when:

1. **Primary User Goal**: A user wants to accomplish something specific
   - "Create a listing" → Journey: Create Listing
   - "Reset password" → Journey: Password Reset

2. **Multi-Step Process**: The goal requires multiple actions
   - Not a single click, but a flow
   - Has a beginning, middle, and end

3. **Value-Producing**: The journey delivers value to user or business
   - Not just internal system processes
   - Results in a measurable outcome

### Journey Categorization

Organize journeys into categories:

| Category | Description | Examples |
|----------|-------------|----------|
| Core | Primary value-creating flows | Purchase, Sign Up, Create Content |
| Supporting | Enables core flows | Profile Management, Settings |
| Administrative | Management and oversight | Admin Panel, Moderation |
| Edge Cases | Exception handling | Error Recovery, Cancel Flows |

---

## Step 3: Synthesize Journey Structure

For each journey, create a comprehensive structure.

### Journey Template

```markdown
## Journey: [Journey Name]

**Actor:** [Who performs this journey]
**Goal:** [What the user wants to accomplish]
**Preconditions:** [What must be true before starting]
**Success Criteria:** [What defines completion]

### Main Path (Happy Path)

1. [Step 1]: [Action description]
   - **System Action:** [What the system does]
   - **User Action:** [What the user does]
   - **UI Element:** [What the user interacts with]
   - **Data Required:** [Information needed from user]

2. [Step 2]: [Action description]
   - [...]

### Alternative Paths

**Alt Path 1: [Scenario]**
1. [Where path diverges]
2. [Steps specific to this path]
3. [Where path rejoins main path]

### Edge Cases & Error Scenarios

**Error 1: [Error condition]**
- **Trigger:** [What causes this error]
- **System Response:** [What the system shows]
- **User Recovery:** [How user resolves]

### Decision Points

**Decision 1: [Decision name]**
- **Condition:** [What determines the branch]
- **Option A:** [Path A description]
- **Option B:** [Path B description]
```

### Detailed Journey Synthesis Rules

1. **Start at entry point**: Where does the user begin?
   - Homepage, dashboard, email link, etc.

2. **Follow logical flow**: What happens next?
   - User actions → System responses → Next choices

3. **Identify decision points**: Where can the flow branch?
   - User choices, system conditions, errors

4. **Define completion**: When is the journey "done"?
   - Success state, abandonment, deferral

5. **Consider states**: What state changes occur?
   - Data changes, status updates, notifications

---

## Step 4: Interactive Validation

CRITICAL: Before finalizing journeys, validate with the user for ambiguous requirements.

### When to Trigger Interactive Validation

Trigger validation when:

1. **Ambiguous Actor**: Not clear who performs an action
2. **Unclear Flow**: Multiple possible paths exist
3. **Missing Detail**: Required information not specified
4. **Conflicting Requirements**: Specs contain contradictions
5. **Assumption Needed**: Reasonable interpretation required

### Validation Questions Structure

For each ambiguous point, ask the user:

#### 1. Actor Clarification
> *"For the [Journey Name] journey, who is the primary actor? Should I document this for 'regular users', 'administrators', or both?"*

#### 2. Flow Clarification
> *"In the [Step Name] step, the specs don't specify whether users should [Option A] or [Option B]. Which is the correct flow?"*

#### 3. Data/Field Clarification
> *"For the [Form/Action] step, I'm assuming these fields are required: [list]. Are there additional or different requirements?"*

#### 4. Edge Case Handling
> *"When [error condition] occurs, should the system [response A] or [response B]? The specs don't specify error handling."*

#### 5. Success Criteria Confirmation
> *"I'm interpreting the success of [Journey Name] as [criteria]. Is this accurate, or should success be defined differently?"*

### Documenting User Responses

Record all user clarifications in a validation notes section:

```markdown
## Validation Notes

### [Date/Time] - Actor Clarification
**Question:** Who performs the "Delete Account" journey?
**Answer:** Only users themselves; admins cannot delete user accounts
**Impact:** Updated to single-actor journey

### [Date/Time] - Flow Decision
**Question:** Should password reset use email or SMS?
**Answer:** Email only for now
**Impact:** Documented email-only reset flow
```

---

## Step 5: Document Alternative Paths

Every journey must include alternative paths and edge cases.

### Types of Alternative Paths

1. **User-Initiated Alternatives**
   - User cancels midway
   - User chooses different options
   - User saves for later

2. **System-Initiated Alternatives**
   - System errors (network, database)
   - Validation failures
   - Rate limiting / throttling

3. **Conditional Paths**
   - Different flows for different user roles
   - Location-based variations
   - A/B test variations

4. **Recovery Paths**
   - User recovers from errors
   - User retries failed actions
   - System auto-recovery

### Edge Case Documentation Template

```markdown
### Edge Cases

| Case | Trigger | System Response | User Recovery |
|------|---------|-----------------|---------------|
| Network timeout | No response for 30s | Show retry option | User clicks retry |
| Invalid email format | Email validation fails | Inline error message | User corrects input |
| Duplicate submission | Form submitted twice | Show success once | User navigates away |
```

---

## Step 6: Generate Mermaid Diagrams

Generate visual diagrams using Mermaid syntax for each journey and overall system.

### Diagram Types to Generate

#### 1. Journey Flow Diagram (Flowchart)

Create a flowchart showing the complete user journey including main path, alternatives, and error handling.

```mermaid
flowchart TD
    Start([User Starts Journey]) --> Step1[Step 1: Action Name]
    Step1 --> Decision1{Decision Point?}
    Decision1 -->|Yes| Step2A[Alternative Path A]
    Decision1 -->|No| Step2B[Main Path]
    Step2A --> Step3[Step 3: Next Action]
    Step2B --> Step3
    Step3 --> Decision2{Error Possible?}
    Decision2 -->|Yes| ErrorHandle[Error Handler]
    Decision2 -->|No| Success([Success State])
    ErrorHandle --> Retry{User Retries?}
    Retry -->|Yes| Step3
    Retry -->|No| Exit([Journey Abandoned])
```

**Flowchart Syntax Rules:**
- Use `TD` (top-down) for most journey flows
- Use `LR` (left-right) for linear/sequential flows
- Terminate with `([Name])` for start/end points
- Use `[Name]` for process steps
- Use `{Name}` for decision points
- Use `-->|Label|` for labeled edges

#### 2. State Diagram

Generate a state diagram showing system state transitions throughout the journey.

```mermaid
stateDiagram-v2
    [*] --> Initial: Journey Start
    Initial --> Processing: User Submits
    Processing --> Validating: Validation Check
    Validating --> Success: Valid Data
    Validating --> Error: Invalid Data
    Error --> Processing: User Corrects
    Success --> [*]: Journey Complete
```

**State Diagram Syntax Rules:**
- Use `[*]` for start/end states
- Use `-->` for transitions with optional labels
- Group related states together
- Show all possible state transitions

#### 3. Journey Relationship Diagram

Create a high-level diagram showing how journeys connect to each other.

```mermaid
flowchart LR
    SignUp[Sign Up Journey] --> Login[Login Journey]
    Login --> Dashboard[Dashboard Journey]
    Dashboard --> CreateListing[Create Listing Journey]
    Dashboard --> BrowseListings[Browse Listings Journey]
    BrowseListings --> ViewListing[View Listing Details]
    ViewListing --> Purchase[Purchase Journey]
    Purchase --> OrderTracking[Order Tracking Journey]

    subgraph Core Flows
        CreateListing
        BrowseListings
        Purchase
    end

    subgraph Supporting Flows
        Login
        Dashboard
        OrderTracking
    end
```

#### 4. Swimlane Diagram

For multi-actor journeys, create a swimlane diagram showing responsibilities.

```mermaid
flowchart TD
    subgraph User["User Actions"]
        U1[Clicks Button]
        U2[Fills Form]
        U3[Confirms]
    end

    subgraph System["System Actions"]
        S1[Validates Input]
        S2[Processes Request]
        S3[Saves Data]
    end

    subgraph External["External Services"]
        E1[Send Email]
        E2[Process Payment]
    end

    U1 --> S1
    U2 --> S2
    S1 --> S3
    S2 --> E1
    U3 --> E2
```

#### 5. Sequence Diagram

For complex interactions, show the sequence of messages between actors and system.

```mermaid
sequenceDiagram
    actor User
    participant UI as Frontend
    participant API as Backend
    participant DB as Database

    User->>UI: Click Submit
    UI->>API: POST /api/resource
    API->>DB: Validate & Save
    DB-->>API: Success
    API-->>UI: 200 OK
    UI-->>User: Show Success
```

### Diagram Generation Strategy

For each journey, generate diagrams based on complexity:

| Journey Complexity | Required Diagrams |
|-------------------|-------------------|
| Simple (1-3 steps) | Flow diagram only |
| Medium (4-7 steps) | Flow + State diagrams |
| Complex (8+ steps) | Flow + State + Sequence diagrams |
| Multi-actor | Swimlane diagram required |

### Diagram Naming Convention

```markdown
### Journey Flow Diagram

```mermaid
[Journey Name] Flow Diagram
```

### State Transition Diagram

```mermaid
[Journey Name] State Diagram
```

### Journey Relationships

```mermaid
All Journeys Relationship Map
```
```

### Diagram Best Practices

1. **Keep diagrams readable**: Limit nodes to <20 for clarity
2. **Use clear labels**: Label all edges and decision points
3. **Show alternatives**: Include error paths and edge cases
4. **Color code** (if supported): Use different colors for actors, states, or paths
5. **Group related elements**: Use subgraphs for related components
6. **Maintain consistency**: Use same style across all diagrams

---

## Step 7: Generate Journey Documentation

Output the complete journey documentation in the specified format.

### Output File Structure

```markdown
# User Journey Documentation

**Generated from:** [source file]
**Generated on:** [timestamp]
**Version:** [version]

---

## Table of Contents

- [Actors & Personas](#actors--personas)
- [Journey Index](#journey-index)
- [Journeys](#journeys)
- [Cross-Journey Flows](#cross-journey-flows)
- [Validation Notes](#validation-notes)
- [Assumptions & Limitations](#assumptions--limitations)

---

## Actors & Personas

### [Actor Name]
- **Description:** [Who they are]
- **Goals:** [What they want to accomplish]
- **Permissions:** [What they can/cannot do]
- **Typical Journeys:** [List of journeys they perform]

---

## Journey Index

| Journey | Actor | Complexity | Priority | Dependencies |
|---------|-------|------------|----------|--------------|
| [Journey 1] | [Actor] | [Low/Med/High] | [P0/P1/P2] | [Other journeys] |

---

## Journey: [Journey Name]

**[Complete journey structure from Step 3]**

### Journey Flow Diagram

```mermaid
[Mermaid flowchart for this journey]
```

### State Transition Diagram

```mermaid
[Mermaid state diagram for this journey]
```

---

## Cross-Journey Flow Diagram

```mermaid
[Mermaid diagram showing all journey relationships]
```

### [Flow Name]
**Description:** [How journeys connect]
**Entry Journey:** [Which journey starts this]
**Exit Journey:** [Which journey this leads to]
**Handoff Points:** [Where control transfers]

---

## Validation Notes

**[All validation interactions from Step 4]**

---

## Assumptions & Limitations

### Assumptions
- [List of assumptions made during synthesis]

### Limitations
- [Known limitations or scope exclusions]

### Out of Scope
- [Things explicitly not covered]
```

---

## Framework-Specific Patterns

### E-commerce
- Core: Browse, Search, Add to Cart, Checkout
- Supporting: Account Management, Order Tracking
- Edge: Payment failures, out of stock items

### SaaS Applications
- Core: Onboarding, Core Feature Usage
- Supporting: Settings, Team Management
- Edge: Subscription issues, overages

### Marketplaces
- Core: Create Listing, Browse, Purchase
- Supporting: Messaging, Reviews
- Edge: Disputes, cancellations

### Content Platforms
- Core: Create Content, Discover, Engage
- Supporting: Profile Management, Following
- Edge: Content moderation, copyright

---

## Journey Quality Checklist

Before finalizing, ensure each journey:

- [ ] Has a clear, specific name
- [ ] Identifies the primary actor
- [ ] States the user's goal
- [ ] Lists all preconditions
- [ ] Defines success criteria
- [ ] Documents the happy path completely
- [ ] Includes all major alternative paths
- [ ] Covers error scenarios
- [ ] Identifies decision points
- [ ] Specifies required data/fields
- [ ] Notes relevant UI elements
- [ ] Considers edge cases
- [ ] Includes visual flow diagram
- [ ] Has state transition diagram (for multi-state journeys)

---

## Output Delivery

After generating the documentation:

1. **Write to file:** Save as specified (or default `user-journeys.md`)
2. **Show summary:** Display journey count, actors, and any critical ambiguities resolved
3. **Offer next steps:** Ask if user wants to refine specific journeys, add more detail, or export to different format

---

## Error Handling

If issues occur:

- **Specs file not found:** Ask user to verify path and provide correct file
- **No journeys found:** Explain requirements structure and offer examples
- **Too many journeys:** Offer to merge related journeys or filter by priority
- **Ambiguous specs persist:** Flag unresolved ambiguities and recommend user review

---

## Example Invocation

```bash
# Generate journeys from PRD
claude code "create user journeys from docs/product-prd.md"

# Specify output location
claude code "generate journey documentation from specs.md output to docs/journeys.md"

# Request comprehensive detail
claude code "create comprehensive user journeys from requirements.txt"
```

---

## Tips for Best Results

1. **Start with clear specs**: Well-structured PRDs produce better journeys
2. **Be specific about actors**: Clearly define user roles beforehand
3. **Think in flows**: Consider how journeys connect to each other
4. **Embrace edge cases**: The best journeys handle errors gracefully
5. **Iterate with validation**: Use the interactive validation to refine
6. **Keep journeys focused**: One primary goal per journey

---

## Notes

- Generated journeys are heuristics based on requirements
- Review and refinement by product/UX teams recommended
- Journeys should evolve as products evolve
- Use as foundation for user stories, test cases, and design mockups
