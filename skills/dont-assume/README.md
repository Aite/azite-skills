# Don't Assume Skill (`dont-assume`)

A developer skill directive designed to prevent unverified assumptions, silent interpretations, and premature code execution when dealing with ambiguous or underspecified tasks.

## 📌 Overview

The **`dont-assume`** skill enforces an explicit **"Stop and Ask"** protocol for AI models and automated workflows. Whenever a user request lacks key specifications, contains multiple valid interpretations, or involves critical architectural choices, this skill requires the system to pause immediately, lay out the assumptions/tradeoffs clearly, and seek user confirmation before proceeding.

## 📑 Metadata

* **Name:** `dont-assume`

* **Version:** `1.0.0`

* **Description:** ALWAYS invoke when handling ambiguous requests, underspecified tasks, or architectural choices. Do not guess or pick interpretations silently.

## 🎯 Triggers

This skill is automatically or manually triggered by the following phrases:

* `Don't assume`

* `Ask me before proceeding`

* `resolve ambiguity by asking`

## 📜 Core Guidelines

When triggered, the system adheres to the following principles:

1. **Stop Immediately:** Do not proceed with code implementation or final output generation based on guesses.

2. **Expose Assumptions:** Explicitly state any underlying assumptions or ambiguities detected in the prompt.

3. **Present Options & Tradeoffs:** Highlight alternative interpretations and their respective consequences or tradeoffs.

4. **Ask Targeted Questions:** Formulate concise, focused questions to clarify the user's intent.

## 💻 Example Usage

### ❌ Without `dont-assume` Skill

> **User:** "Add authentication to my project."
>
> **AI:** *Silently decides to build a full JWT token implementation with MongoDB without asking.*

### ✅ With `dont-assume` Skill

> **User:** "Add authentication to my project."
>
> **AI:**
>
> "Before I proceed, I noticed a few different ways we can implement authentication depending on your tech stack and requirements:
>
> 1. **Session-based Auth** (Great for traditional server-rendered apps)
>
> 2. **JWT / OAuth2** (Ideal for decoupled APIs / React / Mobile)
>
> 3. **Managed Service** (e.g., Firebase Auth, Supabase, Auth0)
>
> Could you clarify:
>
> * What tech stack/framework are you currently using?
>
> * Do you prefer an in-house implementation or an external provider?"

## 📂 Installation & Setup

1. Copy the `SKILL.md` file into your project's skills directory (e.g., `.claude/skills/`, `.cursor/skills/`, `.github/skills/`, or your custom agent framework folder).

2. Ensure your AI tool or framework recognizes the triggers and description metadata.