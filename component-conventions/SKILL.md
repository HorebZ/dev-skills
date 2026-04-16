---
name: component-conventions
description: Framework-agnostic strongly-recommended rules for naming, organizing, and writing frontend components (Svelte, Vue, React). Use when creating or reviewing components, naming a `.svelte` / `.vue` / `.tsx` file, structuring props, deciding folder layout, extracting logic out of templates, or refactoring oversized components. Triggers on base/presentational components, coupled parent-child components, prop casing, multi-attribute elements, complex template expressions.
license: MIT
metadata:
  author: Horebz
  source: https://github.com/HorebZ/dev-skills
---

# Frontend Component Style

Source: adapted from [vuejs.org/style-guide/rules-strongly-recommended](https://vuejs.org/style-guide/rules-strongly-recommended.html), made framework-agnostic.

Notation: `.ext` = the framework extension in use (`.svelte`, `.vue`, `.tsx`).

Examples default to **Vue 3** (the source of these rules). For Svelte 5 / React snippets on the framework-divergent rules (9, 13, 14), see [references/frameworks.md](references/frameworks.md).

## Naming & file layout

1. **One component per file.** Never declare multiple components in a single file.
2. **Filename casing must be consistent project-wide.** Pick PascalCase (`UserProfile.ext`) OR kebab-case (`user-profile.ext`). Never mix. No other casing.
3. **Base/presentational components use the `Base` prefix** (`BaseButton`, `BaseIcon`, `BaseTable`). They may contain ONLY HTML, other `Base` components, and 3rd-party UI. They must NEVER touch global state (stores, app-wide context).
4. **Tightly coupled children are prefixed with the parent name**, not nested in sub-directories.

   ```
   Bad:  TodoList.ext, TodoItem.ext, TodoButton.ext
   Good: TodoList.ext, TodoListItem.ext, TodoListItemButton.ext
   ```

5. **Word order: most general → most specific.** Groups related files alphabetically.

   ```
   Bad:  ClearSearchButton.ext, RunSearchButton.ext, SearchInput.ext
   Good: SearchButtonClear.ext,  SearchButtonRun.ext, SearchInputQuery.ext
   ```

6. **No abbreviations.** Prefer full words: `UserProfileOptions.ext`, not `UProfOpts.ext`.

## Casing in code

7. **Components in markup: PascalCase.** `<MyComponent />`, never `<my-component />` or `<myComponent />`.
8. **Components in JS/TS: PascalCase.** Imports, exports, declarations.

   ```ts
   import MyComponent from './MyComponent.ext'
   ```

9. **Props:** `camelCase` in declaration. Usage in markup: `kebab-case` (Vue/Svelte) or `camelCase` (React — JSX does not accept kebab). Do not mix kebab and camel in usage within the same project.

   ```vue
   <script setup lang="ts">
   defineProps<{ greetingText: string }>();
   </script>

   <template>
   	<WelcomeMessage greeting-text="hi" />
   </template>
   ```

## Markup hygiene

10. **Self-close components with no content.** `<MyComponent />`, not `<MyComponent></MyComponent>`. Valid in Svelte/Vue SFCs and JSX.
11. **One attribute per line** when an element has multiple attributes.

    ```html
    <MyComponent foo="a" bar="b" baz="c" />
    ```

12. **Quote non-empty attribute values.** `<input type="text">`, not `<input type=text>`.

## Template logic

13. **Templates contain only simple expressions.** Extract anything complex into a `computed`. See [references/frameworks.md](references/frameworks.md) for Svelte 5 / React equivalents.

    ```vue
    <script setup lang="ts">
    const props = defineProps<{ fullName: string }>();
    const normalized = computed(() =>
    	props.fullName
    		.split(' ')
    		.map((w) => w[0].toUpperCase() + w.slice(1))
    		.join(' ')
    );
    </script>

    <template>
    	<span>{{ normalized }}</span>
    </template>
    ```

14. **Split complex derived values into several smaller, well-named ones.** Easier to test, read, and adapt.

    ```ts
    // Bad
    const price = computed(() => {
      const base = cost.value / (1 - margin.value);
      return base - base * (discount.value || 0);
    });

    // Good
    const basePrice  = computed(() => cost.value / (1 - margin.value));
    const discount_  = computed(() => basePrice.value * (discount.value || 0));
    const finalPrice = computed(() => basePrice.value - discount_.value);
    ```

## Review checklist

When creating or reviewing a component, verify:

- [ ] Exactly one component per file
- [ ] Filename casing matches the rest of the project (PascalCase OR kebab-case — no mixing)
- [ ] Presentational components use the `Base` prefix and contain no global state
- [ ] Children coupled to a single parent are prefixed with the parent's name
- [ ] Names read general → specific; no abbreviations
- [ ] Components are PascalCase in both markup and JS/TS
- [ ] Props: camelCase declaration; usage casing is consistent across the project
- [ ] Empty components self-close; multi-attribute elements are one-per-line
- [ ] All non-empty attribute values are quoted
- [ ] No complex expressions in markup — extracted to `$derived` / `computed` / `useMemo`
- [ ] Large derived values are split into smaller named ones
