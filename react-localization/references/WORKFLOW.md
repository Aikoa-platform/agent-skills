# Localization Workflow

This reference describes the step-by-step workflow for localizing hardcoded strings in your React application.

## Step 1: Find Untranslated Strings

Run the i18next-cli lint script from your skill's scripts folder:

```bash
npx tsx scripts/i18next-lint.mjs lint
```

This script uses i18next-cli to detect hardcoded strings in your source files.

**Alternative runtime:** If `tsx` is not available, you can use Node.js with experimental TypeScript support:

```bash
node --experimental-strip-types scripts/i18next-lint.mjs lint
```

**Focusing on specific parts:** If the user wants to focus on a specific part of the app, you can temporarily modify the `SOURCE_GLOB` in the script or create a custom version of the script with a narrower path pattern.

The linter may miss some strings or flag strings that should not be translated (technical strings, names, keys, IDs, console logs, etc). Use your best judgment to identify user-facing strings and process only those.

## Step 2: Process Each File with Issues

For each file reported by the linter:

1. Open the file and locate the hardcoded strings
2. Determine the appropriate namespace for each string (see [LOCALIZATION-FILES.md](LOCALIZATION-FILES.md))
3. Add the translation key and value to the appropriate JSON file
4. Replace the hardcoded string with a `t()` call (see [T-FUNCTION.md](T-FUNCTION.md))
5. Import and call `useTranslation` if not already present

**Important:** A file can have multiple components. Make sure to add the `useTranslation` hook to each component that needs translations.

## Step 3: Add Translation Keys to JSON Files

Location: `src/locales/{{namespace}}/{{language}}.json`

For each string:
- Determine the namespace based on the feature domain (not the component name)
- Add the key-value pair to the `en.json` file in that namespace folder
- Use nested keys with dot notation for organization
- Ensure no duplicate keys exist within a namespace

If you need to create a new namespace:
- Add the key to `en.json` in the new namespace folder
- Create empty placeholder files (`{}`) for all other languages in the same namespace folder

## Step 4: Verify No New Issues

After making changes, run the linter again to ensure you haven't introduced new hardcoded strings:

```bash
npx tsx scripts/i18next-lint.mjs lint
```

## Step 5: Regenerate i18n Types

After all translation keys are added, regenerate the TypeScript types for translations:

```bash
npx tsx scripts/i18next-lint.mjs types
```

This updates the i18next type definitions so the `t()` function has proper autocomplete and type safety for the new keys.

Ignore type errors from new translation keys until this step is completed.

## Step 6: Check for Remaining Issues


Run the project's full linting command to catch any other issues:

```bash
npm run lint
```



Run the type checker to ensure there are no type errors:

```bash
npm run typecheck
```



Fix all issues and run the commands again until no issues are found.


## Common Patterns to Handle

### Dynamic Values
Use interpolation for dynamic content:
```json
{ "greeting": "Hello, {{name}}!" }
```

### Pluralization
Use suffixed keys (_one, _other):
```json
{
  "room_one": "room",
  "room_other": "rooms"
}
```

### Context Variants
Use suffixed keys with context separator:
```json
{
  "friend_male": "He is my friend",
  "friend_female": "She is my friend"
}
```

## Avoiding Duplication

Before adding a string:
1. Search existing namespace files to see if it already exists
2. If it's a common term (like "Save", "Cancel"), consider moving it to the `common` namespace
3. Some duplication is acceptable for context-specific translations where the meaning differs
