---
name: nextjs-frontend
description: >
  Use this skill whenever building, editing, or reviewing frontend code in a Next.js project.
  Triggers on: creating or refactoring React components, working with Figma MCP to extract
  designs or tokens, writing PostCSS/BEM styles, setting up component file structure, writing
  accessible HTML, working with TypeScript interfaces and props, or any task involving
  Next.js App Router patterns. Use this skill even for partial tasks like "add a new component",
  "extract tokens from this Figma frame", or "fix the accessibility on this form" — not just
  for full page builds.
---

# Next.js Frontend Skill

This skill defines coding conventions, file structure, and workflow patterns for a Next.js
frontend codebase. Follow these rules consistently — even for small edits — to keep the
codebase coherent.

## Stack Overview

| Concern       | Choice                                  |
| ------------- | --------------------------------------- |
| Framework     | Next.js (App Router)                    |
| Language      | TypeScript — strict mode always         |
| Styling       | PostCSS + BEM + CSS Modules             |
| Design source | Figma via MCP (when available)          |
| Tokens        | Figma Variables → CSS custom properties |
| Components    | Bespoke, atomic/compound pattern        |
| Animation     | See `ux-motion` skill (separate)        |

---

## File & Folder Structure

```
src/
├── app/                        # Next.js App Router pages
│   └── [route]/
│       └── page.tsx            # Default export
├── components/
│   ├── atoms/                  # Smallest reusable units (Button, Icon, Label)
│   ├── molecules/              # Composed atoms (FormField, Card, NavItem)
│   └── organisms/              # Full sections (Header, ProductGrid, Footer)
├── styles/
│   ├── tokens.css              # CSS custom properties from Figma Variables
│   ├── global.css              # Resets, base type, body
│   └── utils.css               # Shared PostCSS utilities / mixins
└── types/
    └── *.types.ts              # Shared prop interfaces used by 2+ components
```

Components live alongside their own CSS Module:

```
components/atoms/Button/
├── Button.tsx
├── Button.module.css
└── index.ts                    # Re-exports named export
```

---

## TypeScript Rules

- **Always use strict mode.** No `any`. No non-null assertions unless unavoidable with a comment.
- **Props for a single component**: define a `interface` directly above the component in the same file.
- **Props shared by 2+ components**: define in `src/types/` as a named export.
- Use `type` for unions/aliases, `interface` for object shapes.

```tsx
// Single-component props — same file, above the component
interface ButtonProps {
  label: string;
  onClick: () => void;
  variant?: "primary" | "secondary" | "ghost";
  disabled?: boolean;
}

export function Button({ label, onClick, variant = "primary", disabled = false }: ButtonProps) {
  // ...
}
```

---

## Component Conventions

### Export pattern

- **Named exports** for all components.
- **Default exports** for Next.js pages only (`app/**/page.tsx`).
- Each component folder has an `index.ts` that re-exports the named export.

```ts
// components/atoms/Button/index.ts
export { Button } from "./Button";
```

### Server vs Client Components

- **Server Components by default.** Add `'use client'` only when genuinely needed:
  - Browser APIs (`window`, `document`, `localStorage`)
  - Event handlers that require state or refs
  - React hooks (`useState`, `useEffect`, etc.)
- Keep `'use client'` as deep in the tree as possible — don't bubble it up unnecessarily.
- If a component needs both server-fetched data and client interactivity, split it: fetch in a Server Component, pass data as props to a small Client Component leaf.

### Atomic pattern

- **Atoms**: single-purpose, no children of their own (Button, Icon, Tag, Avatar)
- **Molecules**: compose 2–4 atoms with local logic (SearchField = Input + Button + Icon)
- **Organisms**: full sections composing molecules (SiteHeader, ProductCard, FilterPanel)
- When unsure of level, default one level lower (simpler). Promote only when reuse demands it.

---

## HTML & Accessibility

- Use **semantic HTML** by default. Reach for `<article>`, `<section>`, `<nav>`, `<main>`,
  `<aside>`, `<header>`, `<footer>`, `<figure>`, `<time>`, `<address>` appropriately.
- No `<div>` or `<span>` where a semantic element fits.
- Use `<button>` for actions, `<a>` for navigation. Never use a `<div>` as a button.
- All interactive elements must be keyboard-focusable and have a visible focus state.
- **Images**: always provide `alt`. Decorative images use `alt=""` and `aria-hidden="true"`.
- **ARIA**: use only when native semantics aren't sufficient. Prefer native HTML first.
  Common correct uses: `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-expanded`,
  `aria-controls`, `aria-live` for dynamic regions, `role="status"` for polite updates.
- Heading hierarchy must be logical (`h1` → `h2` → `h3`), never skip levels.
- Forms: every `<input>` has an associated `<label>` (via `htmlFor` / `id`, not just proximity).
- Colour contrast: meet WCAG AA minimum (4.5:1 for body text, 3:1 for large/UI text).
- Don't suppress outlines with `outline: none` unless replacing with a custom focus style.

---

## CSS / PostCSS / BEM

Read `references/css-conventions.md` for the full guide. Summary:

- **BEM naming**: `.block__element--modifier`
- **CSS Modules**: one `.module.css` per component. Import as `styles` object.
- **Tokens**: reference design tokens as CSS custom properties (`var(--color-primary)`).
  Never hardcode hex values, spacing units, or font sizes in component CSS.
- **PostCSS**: use nesting (`&`) to keep related rules together. Avoid deep nesting (max 3 levels).
- **No global class leakage**: all component styles are scoped to their CSS Module.
  Only `global.css` and `tokens.css` write to `:root` or bare element selectors.

---

## Design Token Conventions

When Figma Variables are available (via MCP or synced export):

- Map Figma variable collections to CSS custom property groups:
  - `Color/*` → `--color-*`
  - `Spacing/*` → `--space-*`
  - `Typography/Size/*` → `--text-size-*`
  - `Typography/Weight/*` → `--font-weight-*`
  - `Radius/*` → `--radius-*`
  - `Shadow/*` → `--shadow-*`
- Define all tokens in `src/styles/tokens.css` on `:root`.
- When a Figma token uses modes (e.g. light/dark), map to `[data-theme="dark"]` selector.

When working on a **prototype without a token system**: use clearly named temporary custom
properties rather than hardcoded values. This makes it easy to wire up tokens later.

---

## Figma MCP Workflow

When Figma MCP is connected, use it actively. Don't guess at design values.

### Extracting tokens

1. Use `get_variable_defs` on the relevant Figma node to retrieve variables.
2. Map to CSS custom properties using the token naming conventions above.
3. Write to `src/styles/tokens.css`.

### Generating components from a design

1. Use `get_design_context` on the target frame or component node.
2. Use `get_screenshot` to visually verify the design before coding.
3. Extract spacing, colour, and type values — map to tokens, not raw values.
4. Structure the component following the atomic level appropriate to its complexity.
5. Write semantic HTML first, then layer CSS via BEM + CSS Modules.

### When Figma MCP is unavailable

Note it, then proceed with clearly named placeholder custom properties and a comment
flagging where real tokens should be wired in once available.

---

## Reference Files

- `references/css-conventions.md` — Full PostCSS/BEM/CSS Modules guide with examples
- `references/a11y-patterns.md` — Accessible component patterns (modals, menus, forms, etc.)
