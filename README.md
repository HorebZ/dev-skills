<img src="https://cdn.prod.website-files.com/68a44d4040f98a4adf2207b6/6903d2307f9555d7c1bc46cb_77dd9077412abc790bf2bc6fa3383b37724d6305-1000x1000.svg" alt="dev-skills" width="100" style="display: block; margin: 0 auto;">

# dev-skills

Personal collection of [Agent Skills](https://docs.claude.com/en/docs/claude-code/skills) I use across my own side projects.

## Why

Every personal project ends up with the same small coding conventions I want to respect. Encoding them as lint rules or formatter config in each repo is overkill, and relying on personal discipline alone is unreliable.

These skills offload that effort to the AI agent: narrow, practical guardrails I can drop into any project without configuring tooling.

## Scope

- **Personal projects only.** Not meant as general-purpose standards.
- **Convenience over optimization.** The goal is ergonomics, not exhaustive correctness.
- **Deliberately limited scope.** Each skill covers a small, well-defined area.

## Skills

| Name | Description | Source |
|-------|-------------|--------|
| component-conventions | Framework-agnostic rules for naming, organizing, and writing frontend components (Svelte / Vue / React). | [VueJS.org Style Guide](https://vuejs.org/style-guide/rules-strongly-recommended.html) |
| svelte-api | Svelte 5 best practices (runes, events, snippets, context, legacy-feature migration). | [Svelte.dev Best Practices](https://svelte.dev/docs/svelte/best-practices) |

## License

[MIT](LICENSE)
