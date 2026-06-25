# AGENTS.md — expo-ts-dev-pattern

## Project Type

This development pattern (originally created with Crush) is for building Expo React Native applications. The convention applies to any agent or environment. It uses **Expo SDK + React Native + TypeScript** with component architecture strictly following **Atomic Design** methodology.

---

## Atomic Design Component Hierarchy

All UI components must be organized into five hierarchical levels. **Never flatten or skip levels.**

| Level | Description | Examples (React Native) |
|-------|-------------|------------------------|
| **Atoms** | Fundamental building blocks, cannot be broken down without losing meaning | `Text`, `Button`, `Input`, `Icon`, `Image`, `Badge`, `Spinner` |
| **Molecules** | Simple groups of atoms functioning as a unit | `SearchBar` (Input + Icon + Button), `FormField` (Label + Input + ErrorText), `AvatarWithName` (Image + Text) |
| **Organisms** | Complex components forming distinct interface sections | `Header`, `ProductCard`, `CommentThread`, `BottomNav`, `FeedItem` |
| **Templates** | Page-level layouts that place components into a layout, showing content structure without real data | `HomeTemplate`, `ProfileTemplate`, `ProductDetailTemplate` |
| **Pages** | Specific instances of templates with real representative content | `HomePage`, `ProfilePage`, `ProductDetailPage` |

### Key Rules

- **Atoms** do not import molecules, organisms, templates, or pages. They may import other atoms.
- **Molecules** import atoms only. They do not import organisms, templates, or pages.
- **Organisms** import atoms and molecules. They may import other organisms. They do not import templates or pages.
- **Templates** import organisms, molecules, and atoms. They do not import pages.
- **Pages** import templates and populate them with real data/content. They may also import organisms, molecules, or atoms directly for page-specific additions.

---

## Folder Structure Convention

Every Expo/React Native project using this skill must follow this directory layout:

```
src/
├── components/
│   ├── atoms/
│   │   ├── Button/
│   │   │   ├── index.tsx
│   │   │   ├── Button.tsx
│   │   │   ├── Button.styles.ts
│   │   │   ├── Button.test.tsx
│   │   │   └── Button.types.ts
│   │   ├── Input/
│   │   │   └── ...
│   │   └── index.ts           # Barrel export: export * from './Button' etc.
│   ├── molecules/
│   │   ├── SearchBar/
│   │   │   └── ...
│   │   └── index.ts
│   ├── organisms/
│   │   ├── Header/
│   │   │   └── ...
│   │   └── index.ts
│   ├── templates/
│   │   ├── HomeTemplate/
│   │   │   └── ...
│   │   └── index.ts
│   └── pages/
│       ├── HomePage/
│       │   └── ...
│       └── index.ts
├── hooks/
├── services/
├── stores/                     # If using Zustand/Context
├── types/
├── utils/
├── constants/
└── theme/                      # Colors, typography, spacing tokens
```

### Per-Component File Structure

Every component folder must contain these files:

| File | Purpose |
|------|---------|
| `index.tsx` | Barrel export: `export { default } from './ComponentName'` |
| `ComponentName.tsx` | The React component implementation |
| `ComponentName.styles.ts` | StyleSheet or styled-components definitions |
| `ComponentName.types.ts` | TypeScript interfaces/types for props |
| `ComponentName.test.tsx` | Unit tests with React Native Testing Library |

**Exception**: Atoms that are pure wrappers around React Native primitives (e.g., a themed `Text` atom) may combine `.tsx` and `.types.ts` if the types are trivial, but prefer separation for consistency.

---

## TypeScript Conventions

### Naming

- Components: `PascalCase` (e.g., `PrimaryButton`, `UserProfileCard`)
- Files/folders: Match component name exactly (e.g., `PrimaryButton/PrimaryButton.tsx`)
- Props interfaces: `{ComponentName}Props` (e.g., `ButtonProps`)
- Hooks: `camelCase` prefixed with `use` (e.g., `useAuth`, `useTheme`)
- Utilities: `camelCase` (e.g., `formatDate`, `truncateText`)
- Constants: `UPPER_SNAKE_CASE` for true constants, `PascalCase` for enum-like objects

### Type Patterns

```typescript
// ComponentName.types.ts
export interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  loading?: boolean;
  onPress: () => void;
  children: React.ReactNode;
}

// Always type the component props explicitly
export const Button: React.FC<ButtonProps> = ({ variant = 'primary', ... }) => {
  // ...
};
```

### Strict Rules

- Enable `strict: true` in `tsconfig.json`
- No `any` without explicit justification comment
- Prefer interface extension over intersection types for component props
- Use `React.ReactNode` for children, not `React.ReactChild` or `JSX.Element`

---

## Styling Conventions

- Use **React Native's `StyleSheet`** or **Styled Components for React Native**
- All style objects live in `ComponentName.styles.ts`
- Theme values (colors, spacing, font sizes) must come from `src/theme/` — no hardcoded values in components
- Atoms should be theme-aware (accept `variant` or `color` props that map to theme tokens)
- Responsive behavior: use `useWindowDimensions` hook at the template/page level, pass sizing props down — never access dimensions inside atoms/molecules

---

## Testing Patterns

- Test framework: **Jest** + **React Native Testing Library** (`@testing-library/react-native`)
- Every component must have a corresponding `.test.tsx` file
- Test files live alongside the component (co-location)

### Test Structure

```typescript
import { render, screen, fireEvent } from '@testing-library/react-native';
import { Button } from './Button';

describe('Button', () => {
  it('renders with default variant', () => {
    render(<Button onPress={jest.fn()}>Press me</Button>);
    expect(screen.getByText('Press me')).toBeTruthy();
  });

  it('calls onPress when pressed', () => {
    const onPress = jest.fn();
    render(<Button onPress={onPress}>Press me</Button>);
    fireEvent.press(screen.getByText('Press me'));
    expect(onPress).toHaveBeenCalledTimes(1);
  });
});
```

### Testing Rules

- Prefer `screen.getByRole` and `screen.getByLabelText` over `getByTestId`
- Only use `testID` as last resort for elements without accessible roles/labels
- **Accessibility testing is required**: verify `accessibilityRole`, `accessibilityLabel`, and touchable hit areas for every interactive component
- Mock native modules at `__mocks__/` level, not inside test files
- Template/page tests: mock child organisms/molecules to test layout structure in isolation

---

## State and Data Flow

- **Lift state to organisms or templates**, not molecules or atoms
- Atoms and molecules should be purely presentational (props in, events out)
- Use **Zustand** for client/global state when needed; keep stores in `src/stores/`
- Data fetching: use `react-query` (TanStack Query) or similar; co-locate fetching logic in custom hooks under `src/hooks/`
- **Custom hooks must be cohesive and feature-scoped** — one hook per feature (e.g., `useAuth`, `useProducts`). Never create generic hooks like `useApi` that abstract too much; prefer explicit, purpose-built hooks that encapsulate a single domain concern
- Never import stores directly in atoms or molecules — pass data and callbacks via props

---

## Navigation Conventions

- Use **Expo Router** (file-based routing) or **React Navigation**
- Route components are **Pages** in atomic design terms
- Navigation logic stays in pages, not in organisms/templates
- Deep-link configuration lives in app root, not in components

---

## Asset Management

- Images: `src/assets/images/` — use `@2x`, `@3x` suffixes for React Native
- Icons: Prefer vector icons (`@expo/vector-icons` or `react-native-svg`) over raster images
- Fonts: Load via `expo-font` in app entry point; define font family tokens in theme

---

## Gotchas

### Atomic Design Specific

- **Do not create "molecule" that is really an organism**: if a component has 5+ atoms, multiple data sources, or internal state, it is likely an organism
- **Templates are not "screens"**: templates show layout structure with placeholder/empty data; pages populate templates with real content
- **Barrel exports are mandatory**: every `index.ts` must re-export its folder's public API; consumers import from the level (`components/atoms`) not deep paths

### React Native Specific

- `View` vs `ScrollView`: templates/pages decide scrollability, not organisms
- Keyboard handling: use `KeyboardAvoidingView` at template level, never in atoms/molecules
- Platform differences (`Platform.OS`): handle at organism/template level, not in atoms
- Safe area: wrap pages with `SafeAreaView` or use `react-native-safe-area-context`; never hardcode padding for notches

### Expo Specific

- Environment variables: use `EXPO_PUBLIC_*` prefix for client-side env vars in Expo SDK 48+
- **Never commit secrets**: `process.env.EXPO_PUBLIC_*` is for non-sensitive config only. Sensitive secrets (API keys, tokens) must be stored in **EAS Secrets**, never in source code or `.env` files
- Native modules: if adding a module with native code, run `npx expo prebuild` or use EAS Build — Metro bundler alone is insufficient
- Assets in `app.json` must be referenced from project root, not `src/`

---

## Essential Commands

Commands should be inferred from the target Expo project's `package.json`. Typical patterns:

| Command | Typical Script |
|---------|---------------|
| Start dev server | `npx expo start` or `npm run start` |
| iOS simulator | `npx expo run:ios` or press `i` in Expo CLI |
| Android emulator | `npx expo run:android` or press `a` in Expo CLI |
| Run tests | `npm test` or `jest` |
| Type check | `npx tsc --noEmit` |
| Lint | `npx eslint .` |
| Build (EAS) | `eas build --platform ios` / `eas build --platform android` |
| Update (OTA) | `eas update --branch preview` |

**Always check the actual `package.json` scripts section before running commands** — the project may use `yarn`, `pnpm`, or custom script names.

---

## When Modifying This Pattern

- `SKILL.md` is the main pattern definition
- `agents/openai.yaml` defines the OpenAI agent interface metadata
- `references/` is for documentation, design specs, or API schemas
- `scripts/` is for automation scripts
- Keep this documentation in sync with the conventions above
