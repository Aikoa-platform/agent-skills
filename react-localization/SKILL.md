---
name: react-localization
description: Find and localize untranslated user-facing strings in React components. Use when asked to localize,
internationalize, add i18n, find missing translations, or add translations.
---

# React Localization

Localize hardcoded or otherwise unlocalized user-facing strings in the codebase. The main focus is to move hardcoded
user-facing strings to the appropriate localization files.

If the user asks to only work on a specific part of the app, focus on that part.

## Quick Reference

- **Source language:** English (`en`)
- **Translation files:** `src/locales/{{namespace}}/{{language}}.json`
- **Import hook from:** `react-i18next`
- **Source files:** `src/**/*.{ts,tsx}`

## Workflow Summary

1. **Find hardcoded strings** - Run the i18next-cli lint script
2. **Process each file** - Add keys to JSON, replace with t() calls
3. **Verify changes** - Run lint again to check for issues
4. **Regenerate i18n types** - Update TypeScript definitions for translations
5. **Check project health** - Run typecheck

For detailed instructions, see:

- [WORKFLOW.md](references/WORKFLOW.md) - Step-by-step localization process
- [T-FUNCTION.md](references/T-FUNCTION.md) - How to use the t() function
- [LOCALIZATION-FILES.md](references/LOCALIZATION-FILES.md) - Namespace organization

## Finding Untranslated Strings

Run the i18next-cli lint script from this skill's scripts folder:

```bash
npx tsx scripts/i18next-lint.mjs lint
```

This uses i18next-cli to detect hardcoded strings. The script is self-contained and doesn't depend on the project's
configuration.

**Alternative:** If `tsx` is not available:

```bash
node --experimental-strip-types scripts/i18next-lint.mjs lint
```

Use your judgment to identify user-facing strings. The linter may flag technical strings (console logs, IDs, keys, etc.)
that should not be translated.

## Processing Strings

For each hardcoded string:

1. **Determine namespace** - Based on feature domain (not component name)
2. **Add to JSON** - In `en.json` for that namespace
3. **Replace with t()** - See format below
4. **Import hook** - Add `useTranslation` if needed

### Translation Hook

```tsx
import { useTranslation } from "react-i18next";

const { t } = useTranslation("namespace");
// or multiple:
const { t } = useTranslation(["namespace1", "namespace2"]);
```

**Important:** Call `useTranslation` in every component that needs translations. A file can have multiple components.

### t() Function Format

**String format:**

```tsx
// Single namespace
const { t } = useTranslation("common");
t("button.save");

// Multiple namespaces
const { t } = useTranslation(["user", "common"]);
t("profileTitle"); // from "user" (first)
t("common:button.save"); // from "common" (prefixed)
```

**Critical:** First namespace is NOT prefixed, others use `namespace:` prefix.

See [T-FUNCTION.md](references/T-FUNCTION.md) for complete details on interpolation, pluralization, and passing t() to
functions.

## Namespace Organization

Organize by **feature domain**, not component location:

- `common` - Reusable UI text (buttons, labels)
- `user` - User-related features
- `orders` - Order management
- etc.

Example: "User profile" in `ProfileCard.tsx` belongs in `user` namespace, not `profile`.

See [LOCALIZATION-FILES.md](references/LOCALIZATION-FILES.md) for detailed guidance.

## Creating New Namespaces

When needed:

1. Add keys to `en.json` in new namespace
2. Create empty `{}` files for all other languages

## Regenerating i18n Types

After adding translation keys, regenerate TypeScript types for the translations:

```bash
npx tsx scripts/i18next-lint.mjs types
```

This updates the i18next type definitions so the `t()` function has proper autocomplete and type safety for the new
keys.

Ignore type errors from new translation keys until types are regenerated.

## Verifying Changes

Run the project's linting:

```bash
npm run lint
```

Run type checking:

```bash
npm run typecheck
```

Fix all issues until no errors remain.

## Example Transformation

**Before:**

```tsx
function UserCard() {
  return <Button>Save Changes</Button>;
}
```

**After:**

```tsx
import { useTranslation } from "react-i18next";

function UserCard() {
  const { t } = useTranslation("common");
  return <Button>{t("button.save")}</Button>;
}
```

**In `common/en.json`:**

```json
{
  "button": {
    "save": "Save Changes"
  }
}
```

## Optional: i18next Configuration

If the user requests it, you can create an `i18next.config.ts` file in the project root. See
[I18NEXT-CONFIG.md](references/I18NEXT-CONFIG.md) for the template.

This is NOT required for the localization workflow but enables additional i18next-cli features.

## Strings to Ignore

Do not localize:

- Console logs, debug messages
- Technical IDs, keys, routes
- Component props (className, testID, etc.)
- Already translated strings (using t() or Trans)
