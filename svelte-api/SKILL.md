---
name: svelte-api
description: Official Svelte 5 best practices covering runes ($state, $derived, $effect, $props, $inspect.trace), events, snippets, keyed each blocks, CSS variables from JS, scoped/child component styling, context with createContext, async Svelte, and avoiding legacy features. Use when writing, reviewing, or refactoring Svelte 5 components, choosing between $derived and $effect, deciding on $state vs $state.raw, styling child components, sharing reactive state, or migrating from Svelte 4 patterns (stores, $:, on:, slots, use:action, class: directive).
license: MIT
metadata:
  author: Horebz
  source: https://github.com/HorebZ/dev-skills
---

# Svelte Core Best Practices

Source: [svelte.dev/docs/svelte/best-practices](https://svelte.dev/docs/svelte/best-practices)

Apply these rules whenever you author or review Svelte 5 code. They are the canonical guidance from the Svelte team.

## `$state`

- Use `$state` ONLY for variables that drive reactive updates (`$effect`, `$derived`, or template expressions). Everything else should be a normal `let`.
- `$state({...})` / `$state([...])` is **deeply reactive** via Proxy — convenient but has overhead.
- For large objects that are **only ever reassigned** (e.g. API responses), use `$state.raw` to skip proxying.

## `$derived`

- Compute values from state with `$derived`, NEVER with `$effect`.

```svelte
<script>
	let count = $state(0);
	let square = $derived(count * count);
</script>
```

- `$derived(...)` takes an **expression**, not a function. For complex logic, use `$derived.by(() => {...})`.
- Deriveds are writable (assignment is allowed), but they re-evaluate when their dependencies change.
- If a derived returns an object/array, it is NOT made deeply reactive. Wrap with `$state` inside `$derived.by` if you really need that.

## `$effect`

Effects are an escape hatch — avoid them, especially avoid updating state inside them.

- Sync to an external library (D3, etc.) → use `{@attach ...}`
- Respond to user interaction → use an event handler or function binding
- Log/debug values → use `$inspect`
- Observe something external to Svelte → use `createSubscriber`
- NEVER wrap effect contents in `if (browser) {...}` — effects don't run on the server.

## `$props`

Treat props as if they will change. Derive dependent values:

```svelte
<script>
	let { type } = $props();
	let color = $derived(type === 'danger' ? 'red' : 'green');
</script>
```

Do NOT do `let color = type === 'danger' ? 'red' : 'green'` — it won't update.

## `$inspect.trace`

Debug reactivity. Drop `$inspect.trace(label)` as the first line of an `$effect`, `$derived.by`, or any function they call to discover which dependency triggered an update.

## Events

- Any attribute starting with `on` is an event listener: `onclick={...}`, `{onclick}`, `{...props}`.
- For `window` / `document`, use `<svelte:window>` and `<svelte:document>`. Do NOT use `onMount` or `$effect` for these.

```svelte
<svelte:window onkeydown={handleKey} />
<svelte:document onvisibilitychange={handleVis} />
```

## Snippets

Reusable markup, declared in the template, rendered with `{@render ...}`:

```svelte
{#snippet greeting(name)}
	<p>hello {name}!</p>
{/snippet}

{@render greeting('world')}
```

- Top-level snippets can be referenced from `<script>`.
- Snippets that don't reference component state can live in `<script module>` and be exported for use by other components.

## Each blocks

- Always use a **keyed** each block. The key MUST uniquely identify the item — never use the index.
- Avoid destructuring when you need to mutate the item (e.g. `bind:value={item.count}`).

```svelte
{#each items as item (item.id)}
	<Row {item} />
{/each}
```

## JS variables in CSS

Pass JS values to CSS through a custom property with `style:`:

```svelte
<div style:--columns={columns}>...</div>

<style>
	div {
		grid-template-columns: repeat(var(--columns), 1fr);
	}
</style>
```

## Styling child components

Component `<style>` is scoped. Preferred way to let a parent style a child is via CSS custom properties:

```svelte
<!-- Parent -->
<Child --color="red" />

<!-- Child -->
<style>
	h1 {
		color: var(--color);
	}
</style>
```

If impossible (third-party child), fall back to `:global`:

```svelte
<style>
	div :global {
		h1 {
			color: red;
		}
	}
</style>
```

## Context

- Prefer **context** over module-level shared state — it scopes state to a subtree and prevents leaking between users during SSR.
- Use `createContext` (type-safe) instead of `setContext` / `getContext`.

## Async Svelte

On Svelte ≥ 5.36 with `experimental.async` enabled in `svelte.config.js`, you can use `await` expressions and `<hydratable>` directly inside components. Still experimental — opt in deliberately.

## Avoid legacy features

Always work in runes mode. Replace legacy patterns:

| Legacy                                            | Modern replacement                                        |
| ------------------------------------------------- | --------------------------------------------------------- |
| Implicit reactivity (`let count = 0; count += 1`) | `$state`                                                  |
| `$:` assignments / statements                     | `$derived` (and `$effect` only as a last resort)          |
| `export let`, `$$props`, `$$restProps`            | `$props`                                                  |
| `on:click={...}`                                  | `onclick={...}`                                           |
| `<slot>`, `$$slots`, `<svelte:fragment>`          | `{#snippet ...}` + `{@render ...}`                        |
| `<svelte:component this={X}>`                     | `<DynamicComponent>` (dynamic component variable)         |
| `<svelte:self>`                                   | `import Self from './ThisComponent.svelte'` then `<Self>` |
| Stores for cross-component reactivity             | Classes with `$state` fields                              |
| `use:action`                                      | `{@attach ...}`                                           |
| `class:` directive                                | clsx-style arrays/objects in the `class` attribute        |

## Review checklist

When reviewing Svelte code, verify:

- [ ] No `$state` on non-reactive variables; `$state.raw` used for large reassign-only objects
- [ ] Computations use `$derived` / `$derived.by`, not `$effect`
- [ ] `$effect` is a last resort; no state mutation inside effects
- [ ] Props that influence derived values flow through `$derived`
- [ ] Event listeners use `onfoo={...}`; window/document via `<svelte:window>` / `<svelte:document>`
- [ ] `{#each}` blocks are keyed with a unique identifier
- [ ] Cross-component state uses context (`createContext`) or classes with `$state`, not stores or module globals
- [ ] No legacy syntax (`$:`, `export let`, `on:`, `<slot>`, `use:`, `class:`, `<svelte:component>`, `<svelte:self>`)
