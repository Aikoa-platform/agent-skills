# t() Function Format

This reference explains how to use the `t()` function for translations based on your project's configuration.

## Import and Hook Setup

Import from `react-i18next`:

```tsx
import { useTranslation } from "react-i18next";
```

Call at the top of your component with the namespace(s):

```tsx
const { t } = useTranslation("common");
// or multiple:
const { t } = useTranslation(["feedback", "common"]);
```

**Important:** Don't pass the `t()` function to child components. Instead, call `useTranslation` separately in each component that needs translations. A file can have multiple components, so make sure to add the hook to all components that need it.

## String Format

Your project uses the **string format** for the `t()` function:

### Single Namespace

```tsx
const { t } = useTranslation("common");
t("navigation.next"); // The namespace is NOT included in the key
```

### Multiple Namespaces

When using multiple namespaces, the first namespace is the default:

```tsx
const { t } = useTranslation(["feedback", "common"]);

// From the first namespace (feedback) - do NOT prefix
t("flow.title");

// From other namespaces - prefix with "namespace:"
t("common:navigation.next");
```

**Critical:** Getting the key correct is essential. When a single namespace is used, the namespace is not included in the key. When multiple namespaces are used, the first namespace is not included in the key, but all other namespaces must be prefixed with `namespace:`.

### With Interpolation

```tsx
t("greeting", { name: userName });
```

### With Pluralization

```tsx
t("room", { count: countValue });
```

### With Context

```tsx
t("friend", { context: "male" });
```


## Passing t() to Functions

For helper functions that need translations, accept a typed `t` parameter:

```tsx
import { useTranslation, type TFunction } from "react-i18next";

function formatLabel(t: TFunction<"common">) {
  return t("inputs.min");
}

// In component:
const { t } = useTranslation("common");
formatLabel(t);
```

**Important:** Always use the `TFunction` type for the `t` parameter. Do not try to infer the type from the `useTranslation` call.

For components that need multiple namespaces, assign multiple `t()` functions to separate variables rather than trying to pass a multi-namespace `t` to a function.

## Interpolation

Use double curly braces for dynamic values in your translation strings:

```json
{
  "greeting": "Hi, {{name}}!",
  "itemCount": "You have {{count}} items in your cart"
}
```

```tsx
t("greeting", { name: userName });
t("itemCount", { count: itemCount });
```

**Always use interpolation for dynamic values.** Never split strings into parts and combine them with concatenation in the UI.

## Pluralization

To use i18next pluralization, create multiple keys with suffixes:

```json
{
  "room_one": "room",
  "room_other": "rooms"
}
```

```tsx
t("room", { count: countValue });
```

**Do not use object values for pluralization.** Always use separate suffixed keys.

## Type Errors

After adding translation keys, you may see TypeScript errors. These are expected and will be resolved when the i18next types are regenerated.

Ignore type errors from newly added translation keys during the localization process.

## Dependencies

If the `t()` call happens inside a `useMemo`, `useEffect`, or `useCallback`, make sure to include `t` in the dependencies array:

```tsx
useEffect(() => {
  const message = t("welcomeMessage");
  showNotification(message);
}, [t]);
```
