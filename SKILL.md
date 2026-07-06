---
name: expo-ts-dev-pattern
description: Use this skill when building, reviewing, or refactoring Expo React Native TypeScript apps that must follow Atomic Design, strict TypeScript, theme-based styling, co-located tests, Expo Router or React Navigation conventions, and Expo project safety practices.
---

# Expo TS Dev Pattern

Use this skill for Expo React Native applications built with TypeScript and organized with Atomic Design.

Before making project changes in a target app, read and follow `references/expo-ts-dev-pattern.md`. It is the authoritative pattern definition for component hierarchy, folder structure, TypeScript rules, styling, testing, state/data flow, navigation, assets, Expo-specific gotchas, and command selection.

Do not rely on this skill's `AGENTS.md` file as the skill payload. `AGENTS.md` is repository guidance that Codex reads when working inside this skill repository; the reusable skill workflow is driven by `SKILL.md` and its referenced files.

When working in a target app:

1. Inspect the app's `package.json` before running commands.
2. Keep UI components in the required Atomic Design hierarchy: atoms, molecules, organisms, templates, and pages.
3. Preserve one-way component imports: lower levels never import higher levels.
4. Keep component styles in `ComponentName.styles.ts` and source design values from `src/theme/`.
5. Keep atoms and molecules presentational; lift state to organisms, templates, or pages.
6. Add or update colocated React Native Testing Library tests for each component change.
7. Keep navigation in pages and route files, not reusable components.
8. Never commit secrets; use `EXPO_PUBLIC_*` only for non-sensitive client config.
