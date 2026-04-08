# CSS Conventions — PostCSS + BEM + CSS Modules

## Table of Contents

- [CSS Conventions — PostCSS + BEM + CSS Modules](#css-conventions--postcss--bem--css-modules)
  - [Table of Contents](#table-of-contents)
  - [BEM Naming](#bem-naming)
    - [Rules](#rules)
  - [CSS Modules Setup](#css-modules-setup)
    - [Combining classes cleanly](#combining-classes-cleanly)
    - [Accessing CSS Module classes](#accessing-css-module-classes)
  - [PostCSS Nesting](#postcss-nesting)
  - [Token Usage](#token-usage)
    - [Example tokens.css](#example-tokenscss)
  - [Responsive Patterns](#responsive-patterns)
  - [Common Pitfalls](#common-pitfalls)

---

## BEM Naming

**Block**: the standalone component (maps to the component name).  
**Element**: a part of the block, separated by `__`.  
**Modifier**: a variant or state, separated by `--`.

```
.card                  ← Block
.card__header          ← Element
.card__body            ← Element
.card__footer          ← Element
.card--featured        ← Modifier on block
.card__header--compact ← Modifier on element
```

### Rules

- Block name matches the component name (PascalCase component → kebab-case block).
  `ProductCard.tsx` → `.product-card`
- Elements are never standalone — they only exist inside their block.
- Modifiers never appear without their base class. Always apply both:

  ```tsx
  // ✅ correct
  className={`${styles['card']} ${styles['card--featured']}`}

  // ❌ wrong — modifier without base
  className={styles['card--featured']}
  ```

- Don't reflect HTML structure in BEM names. BEM describes meaning, not nesting.
  ```
  // ✅ .nav__item  (meaningful)
  // ❌ .nav__ul__li  (structural)
  ```

---

## CSS Modules Setup

Each component has exactly one `.module.css` file. Import as `styles`:

```tsx
import styles from "./Button.module.css";

export function Button({ label, variant = "primary" }: ButtonProps) {
  return <button className={`${styles["button"]} ${styles[`button--${variant}`]}`}>{label}</button>;
}
```

### Combining classes cleanly

For conditional classes, use a simple helper rather than long template literals:

```tsx
const cx = (...classes: (string | undefined | false)[]) =>
  classes.filter(Boolean).join(' ');

// Usage
<button className={cx(
  styles['button'],
  variant && styles[`button--${variant}`],
  disabled && styles['button--disabled']
)}>
```

Or use the `clsx` package if it's already in the project.

### Accessing CSS Module classes

Use bracket notation for BEM names with `__` or `--` since they're not valid identifiers:

```tsx
// ✅
styles["card__header"];
styles["card--featured"];

// ❌ (syntax error — double underscore breaks dot notation)
styles.card__header;
```

---

## PostCSS Nesting

Use `&` nesting to group related rules. Keep nesting **max 3 levels deep**.

```css
/* Button.module.css */

.button {
  display: inline-flex;
  align-items: center;
  gap: var(--space-2);
  padding: var(--space-2) var(--space-4);
  border-radius: var(--radius-md);
  font-size: var(--text-size-sm);
  font-weight: var(--font-weight-medium);
  cursor: pointer;
  transition:
    background-color 150ms ease,
    color 150ms ease;

  /* Modifier: primary (default) */
  &--primary {
    background-color: var(--color-primary);
    color: var(--color-on-primary);

    &:hover {
      background-color: var(--color-primary-hover);
    }
  }

  /* Modifier: ghost */
  &--ghost {
    background-color: transparent;
    color: var(--color-primary);
    border: 1px solid currentColor;

    &:hover {
      background-color: var(--color-primary-subtle);
    }
  }

  /* State: disabled — applies regardless of variant */
  &:disabled,
  &--disabled {
    opacity: 0.5;
    cursor: not-allowed;
    pointer-events: none;
  }

  /* Element: icon inside button */
  &__icon {
    width: 1em;
    height: 1em;
    flex-shrink: 0;
  }
}
```

---

## Token Usage

Never hardcode design values. Every colour, spacing unit, type size, radius, or shadow
must reference a CSS custom property from `tokens.css`.

```css
/* ❌ Hardcoded — never do this */
.card {
  padding: 16px;
  background: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.12);
}

/* ✅ Token-referenced */
.card {
  padding: var(--space-4);
  background: var(--color-surface);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-md);
}
```

### Example tokens.css

```css
/* src/styles/tokens.css */
:root {
  /* Color */
  --color-primary: #1a56db;
  --color-primary-hover: #1648c4;
  --color-primary-subtle: #eff4ff;
  --color-on-primary: #ffffff;
  --color-surface: #ffffff;
  --color-surface-raised: #f9fafb;
  --color-border: #e5e7eb;
  --color-text: #111827;
  --color-text-muted: #6b7280;

  /* Spacing (4px base) */
  --space-1: 0.25rem; /* 4px */
  --space-2: 0.5rem; /* 8px */
  --space-3: 0.75rem; /* 12px */
  --space-4: 1rem; /* 16px */
  --space-6: 1.5rem; /* 24px */
  --space-8: 2rem; /* 32px */
  --space-12: 3rem; /* 48px */
  --space-16: 4rem; /* 64px */

  /* Typography */
  --text-size-xs: 0.75rem;
  --text-size-sm: 0.875rem;
  --text-size-base: 1rem;
  --text-size-lg: 1.125rem;
  --text-size-xl: 1.25rem;
  --text-size-2xl: 1.5rem;
  --text-size-3xl: 1.875rem;
  --font-weight-regular: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;

  /* Radii */
  --radius-sm: 4px;
  --radius-md: 6px;
  --radius-lg: 12px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
}

/* Dark mode via data attribute */
[data-theme="dark"] {
  --color-primary: #3b82f6;
  --color-surface: #111827;
  --color-surface-raised: #1f2937;
  --color-border: #374151;
  --color-text: #f9fafb;
  --color-text-muted: #9ca3af;
}
```

---

## Responsive Patterns

Use mobile-first media queries. Define breakpoints as custom media queries in `utils.css`:

```css
/* src/styles/utils.css */
@custom-media --sm (min-width: 640px);
@custom-media --md (min-width: 768px);
@custom-media --lg (min-width: 1024px);
@custom-media --xl (min-width: 1280px);
```

Use in component CSS:

```css
.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--space-4);

  @media (--md) {
    grid-template-columns: repeat(2, 1fr);
  }

  @media (--lg) {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

---

## Common Pitfalls

| Pitfall                              | Fix                                                           |
| ------------------------------------ | ------------------------------------------------------------- |
| Using `any` in TypeScript            | Use `unknown`, narrow with guards, or model the type properly |
| Hardcoding `px` values in CSS        | Reference a `--space-*` or `--text-size-*` token              |
| Deep BEM nesting like `.a__b__c`     | Flatten — BEM depth doesn't follow DOM depth                  |
| Applying modifier without base class | Always include both `.block` and `.block--modifier`           |
| `'use client'` at layout level       | Push it down to the leaf component that needs it              |
| `<div onClick={...}>`                | Use `<button>` — it's focusable and fires on Enter/Space      |
| Missing `alt` on `<img>`             | Always provide `alt`; use `""` for decorative images          |
| Skipping heading levels              | Never jump from `h2` to `h4` — sequential hierarchy only      |
