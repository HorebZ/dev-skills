---
name: tailwind-v4
description: Tailwind CSS v4 essentials — CSS-first configuration with `@theme`, single-line `@import "tailwindcss"` setup, first-party Vite plugin, automatic content detection, dynamic utility values, container queries, 3D transforms, expanded gradients (`bg-linear-*`, `bg-radial-*`, `bg-conic-*`), `@starting-style`, `not-*` variant, and migration away from v3 patterns (`tailwind.config.js`, `@tailwind` directives, `content` array, `bg-gradient-*`, `@tailwindcss/container-queries`). Use when installing, configuring, upgrading, or reviewing Tailwind CSS v4 projects.
license: MIT
metadata:
  author: Horebz
  source: https://github.com/HorebZ/dev-skills
---

# Tailwind CSS v4

Source: [tailwindcss.com/blog/tailwindcss-v4](https://tailwindcss.com/blog/tailwindcss-v4)

Apply these rules when authoring, upgrading, or reviewing a Tailwind v4 project. v4 is a ground-up rewrite — most v3 habits must go.

## Installation

Three steps, no more:

```bash
npm i tailwindcss @tailwindcss/postcss
```

```js
// postcss.config.js
export default {
	plugins: ['@tailwindcss/postcss'],
};
```

```css
/* app.css */
@import 'tailwindcss';
```

- No more `@tailwind base;` / `@tailwind components;` / `@tailwind utilities;`.
- No more `postcss-import` — `@import` is handled natively.
- On Vite, prefer the first-party plugin for better performance:

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
	plugins: [tailwindcss()],
});
```

## CSS-first configuration

`tailwind.config.js` is gone. Configure directly in CSS with `@theme`:

```css
@import 'tailwindcss';

@theme {
	--font-display: 'Satoshi', 'sans-serif';
	--breakpoint-3xl: 1920px;
	--color-brand-500: oklch(0.84 0.18 117.33);
	--ease-snappy: cubic-bezier(0.2, 0, 0, 1);
}
```

- Every token in `@theme` is emitted as a real CSS custom property on `:root`, so you can use `var(--color-brand-500)` in inline styles, JS, or animation libraries.
- Define custom utilities/variants in CSS — no JS plugin layer needed for most cases.

## Content detection

- Do NOT configure a `content` array. Tailwind scans the project automatically, respecting `.gitignore` and skipping binaries.
- If you need to include an otherwise-ignored path (e.g. a monorepo UI lib in `node_modules`), add `@source`:

```css
@import 'tailwindcss';
@source '../node_modules/@my-company/ui-lib';
```

## Dynamic utilities

Many utilities accept arbitrary numeric values without arbitrary-value syntax or theme extension:

- Grids of any size: `grid-cols-15`.
- Spacing utilities (`p-*`, `m-*`, `w-*`, `h-*`, …) derive from `--spacing` and accept any integer: `mt-8`, `w-17`, `pr-29`.
- Custom boolean `data-*` attributes work as variants with no config: `data-current:opacity-100`.

Prefer `w-17` over `w-[4.25rem]`. Only drop to arbitrary values for non-scale values.

## Container queries

Built in — do NOT install `@tailwindcss/container-queries`.

```html
<div class="@container">
	<div class="grid grid-cols-1 @sm:grid-cols-3 @lg:grid-cols-4">…</div>
</div>
```

- Max-width variant: `@max-md:grid-cols-1`.
- Stack for ranges: `@min-md:@max-xl:hidden`.

## Gradients

`bg-gradient-*` is renamed to `bg-linear-*`. New APIs:

- Angles: `bg-linear-45`.
- Interpolation modifier: `bg-linear-to-r/oklch`, `bg-linear-to-r/srgb` (default is OKLAB).
- Radial: `bg-radial-[at_25%_25%] from-white to-zinc-900 to-75%`.
- Conic: `bg-conic/[in_hsl_longer_hue] from-red-600 to-red-600`.

## 3D transforms

Use native utilities — no custom CSS:

```html
<div class="perspective-distant">
	<article class="rotate-x-51 rotate-z-43 transform-3d">…</article>
</div>
```

Also: `rotate-y-*`, `scale-z-*`, `translate-z-*`, `perspective-origin-*`.

## New variants worth knowing

- `starting:` — hooks into CSS `@starting-style` for enter transitions, no JS:

  ```html
  <div popover class="transition-discrete starting:open:opacity-0">…</div>
  ```

- `not-*` — negates any variant, media query, or `@supports`: `not-hover:opacity-75`, `not-supports-hanging-punctuation:px-4`.
- `in-*` — like `group-*` but without needing a `group` class on the ancestor.
- `nth-*`, `inert`, `open` (now also matches `:popover-open`), `descendant`.

## New utilities worth knowing

- `inset-shadow-*` and `inset-ring-*` (stack up to four shadow layers).
- `field-sizing-content` — auto-resize textareas without JS.
- `color-scheme-*` — fixes light scrollbars in dark mode.
- `font-stretch-*` for variable fonts.

## Colors

Default palette is now OKLCH/P3. Keep balance ~identical to v3 — existing designs look the same on sRGB displays but pop on wide-gamut screens. Use `oklch()` when defining custom colors in `@theme`.

## Migrating from v3

Use the official upgrade tool first: `npx @tailwindcss/upgrade`. Then verify:

| v3                                        | v4 replacement                                              |
| ----------------------------------------- | ----------------------------------------------------------- |
| `@tailwind base/components/utilities;`    | `@import 'tailwindcss';`                                    |
| `tailwind.config.js` (theme, screens, …)  | `@theme { … }` in CSS                                       |
| `content: [...]` array                    | Automatic detection + `@source` for exceptions              |
| `postcss-import`                          | Built-in, remove from `postcss.config.js`                   |
| `@tailwindcss/container-queries` plugin   | Built-in `@container`, `@sm:`, `@max-md:`                   |
| `bg-gradient-to-r`                        | `bg-linear-to-r` (and new `bg-radial-*`, `bg-conic-*`)      |
| Arbitrary spacing `w-[4.25rem]` on-scale  | Dynamic value `w-17` derived from `--spacing`               |
| JS plugins for simple utilities           | `@utility` / `@variant` in CSS                              |
| Hardcoded theme tokens in JS              | `var(--color-brand-500)` — tokens are real CSS variables    |

## Review checklist

When reviewing a Tailwind v4 setup, verify:

- [ ] Single `@import 'tailwindcss';` — no `@tailwind` directives
- [ ] No `tailwind.config.js` (or it is empty / only JS-plugin glue); theme lives in `@theme`
- [ ] No `content` array; `@source` only used for intentional extras
- [ ] Vite projects use `@tailwindcss/vite`, not PostCSS
- [ ] `postcss-import` and `@tailwindcss/container-queries` are removed
- [ ] Gradients use `bg-linear-*` / `bg-radial-*` / `bg-conic-*`, not `bg-gradient-*`
- [ ] On-scale spacing uses dynamic values (`w-17`) instead of arbitrary (`w-[4.25rem]`)
- [ ] Theme tokens are consumed via `var(--…)` where JS/inline styles need them
- [ ] New variants (`not-*`, `starting:`, `in-*`, `@container`) preferred over custom CSS
