---
name: dont-assume
version: 1.0.0
description: ALWAYS invoke when handling ambiguous requests, underspecified tasks, or architectural choices. Do not guess or pick interpretations silently.
triggers:
  - Don't assume
  - Ask me before proceeding
  - resolve ambiguity by asking
---

# Directive: Stop and Ask

When a user request is ambiguous, underspecified, or could be interpreted in multiple ways:
- **Do not** write code or implement solutions based on unverified guesses.
- **Do not** hide confusion or pick an interpretation silently.
- **Stop immediately.**
- State your assumptions explicitly.
- Surface the ambiguity and present the clear tradeoffs or alternative interpretations to the user.
- Ask targeted questions to get explicit confirmation before proceeding.
