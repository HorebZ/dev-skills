# Framework-specific snippets

Snippets for the rules in [SKILL.md](../SKILL.md) that diverge across Vue 3 / Svelte 5 / React. The main skill uses Vue 3 as the default (source of the rules); this file covers Svelte 5 (the current repo) and React.

## Rule 9 — Prop casing

**Svelte 5**

```svelte
<script lang="ts">
	let { greetingText } = $props<{ greetingText: string }>();
</script>

<WelcomeMessage greeting-text="hi" />
```

**React / JSX** — kebab-case is not accepted, camelCase everywhere.

```tsx
function WelcomeMessage({ greetingText }: { greetingText: string }) { /* ... */ }

<WelcomeMessage greetingText="hi" />
```

## Rule 13 — Simple expressions in templates

**Svelte 5** — `$derived`

```svelte
<script lang="ts">
	let { fullName } = $props<{ fullName: string }>();
	const normalized = $derived(
		fullName
			.split(' ')
			.map((w) => w[0].toUpperCase() + w.slice(1))
			.join(' ')
	);
</script>

<span>{normalized}</span>
```

**React** — `useMemo` or plain derived variable

```tsx
const normalized = useMemo(
  () => fullName.split(' ').map((w) => w[0].toUpperCase() + w.slice(1)).join(' '),
  [fullName]
);
```

## Rule 14 — Split derived values

**Svelte 5**

```svelte
<script lang="ts">
	const basePrice = $derived(cost / (1 - margin));
	const discount_ = $derived(basePrice * (discount || 0));
	const finalPrice = $derived(basePrice - discount_);
</script>
```

**React**

```tsx
const basePrice  = useMemo(() => cost / (1 - margin), [cost, margin]);
const discount_  = useMemo(() => basePrice * (discount || 0), [basePrice, discount]);
const finalPrice = useMemo(() => basePrice - discount_, [basePrice, discount_]);
```
