# Expo TS Dev Pattern

`expo-ts-dev-pattern` is a Codex skill for building, reviewing, and refactoring Expo React Native applications with TypeScript and Atomic Design.

## Skill name

Invoke the skill with:

```text
$expo-ts-dev-pattern
```

## What it enforces

- Expo SDK, React Native, and TypeScript conventions.
- Atomic Design component hierarchy: atoms, molecules, organisms, templates, and pages.
- Strict TypeScript props and naming patterns.
- Theme-based React Native styling.
- Co-located React Native Testing Library tests.
- Safe Expo practices for navigation, assets, environment variables, and native modules.

## Repository layout

```text
.
|-- SKILL.md
|-- AGENTS.md
|-- README.md
|-- agents/
|   `-- openai.yaml
|-- references/
|   `-- expo-ts-dev-pattern.md
`-- scripts/
```

## Source of truth

For skill behavior, `SKILL.md` is the entry point Codex loads when the skill is selected. The full pattern lives in `references/expo-ts-dev-pattern.md`, which `SKILL.md` tells agents to read before working in a target app.

`AGENTS.md` is still useful, but it is repository guidance for agents working inside this repo. It is not automatically loaded as the payload of an installed skill unless the current Codex session is actually opened from this repository path.

## Validation notes

Current Codex documentation describes skills as directories with a required `SKILL.md` plus optional scripts and references. Codex initially sees the skill name, description, and path; when the skill is chosen, it loads the full `SKILL.md`.

Codex also reads `AGENTS.md` files before work, but that discovery is based on the active Codex home and the current repository path. That means this repo's `AGENTS.md` helps when editing this skill repository, while installed skill execution should be guided through `SKILL.md` and files it references.

## Maintenance checklist

- Keep the skill name as `expo-ts-dev-pattern`.
- Keep `agents/openai.yaml` aligned with `SKILL.md`.
- Update `references/expo-ts-dev-pattern.md` when the Expo/TypeScript pattern changes.
- Keep `SKILL.md` concise and use `references/` for longer guidance.
- Run the skill validation script after metadata changes.
