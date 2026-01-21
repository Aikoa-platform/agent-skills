# i18next Configuration

This reference provides a template for creating an optional `i18next.config.ts` file in your project.

## When to Create This File

Create `i18next.config.ts` when:
- The user explicitly asks you to set up i18next configuration
- The project needs to use i18next-cli's extract or sync features
- The project wants centralized i18next tooling configuration

This is **optional** and not required for the localization workflow described in this skill.

## Configuration Template

Create a file named `i18next.config.ts` in the root of the project:

```typescript
// i18next.config.ts
import { defineConfig } from "i18next-cli";

export default defineConfig({
  locales: ["en"],
  extract: {
    input: ["src/**/*.{ts,tsx}"],
    output: "src/locales/{{namespace}}/{{language}}.json",
  },
});
```

## What This Enables

With this configuration file, you can use i18next-cli commands directly:

```bash
# Extract translation keys from source files
npx i18next-cli extract

# Check translation status
npx i18next-cli status

# Sync translation files
npx i18next-cli sync

# Lint for hardcoded strings
npx i18next-cli lint

# Generate TypeScript types
npx i18next-cli types
```

## Advanced Configuration Options

You can customize the configuration further based on project needs:

```typescript
import { defineConfig } from "i18next-cli";

export default defineConfig({
  locales: ["en"],
  
  extract: {
    input: ["src/**/*.{ts,tsx}"],
    output: "src/locales/{{namespace}}/{{language}}.json",
    
    // Translation functions to detect
    functions: ['t', '*.t'],
    
    // React components to analyze
    transComponents: ['Trans', 'Translation'],
    
    // Namespace configuration
    defaultNS: 'translation',
    nsSeparator: ':',
    keySeparator: '.',
    
    // Primary language settings
    primaryLanguage: 'en',
    
    // Remove unused keys
    removeUnusedKeys: true,
    
    // Output formatting
    sort: true,
    indentation: 2,
  },
  
  // TypeScript type generation
  types: {
    output: 'src/types/i18next.d.ts',
  },
});
```

## Reference

For complete configuration options, see the [i18next-cli documentation](https://github.com/i18next/i18next-cli).
