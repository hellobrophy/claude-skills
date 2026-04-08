# Accessible Component Patterns

Patterns for common interactive components. Use native HTML semantics first;
reach for ARIA only when native elements fall short.

## Table of Contents

- [Accessible Component Patterns](#accessible-component-patterns)
  - [Table of Contents](#table-of-contents)
  - [Buttons \& Links](#buttons--links)
  - [Forms](#forms)
    - [Required fields](#required-fields)
    - [Grouping related inputs](#grouping-related-inputs)
  - [Navigation](#navigation)
  - [Modal / Dialog](#modal--dialog)
  - [Disclosure / Accordion](#disclosure--accordion)
  - [Dropdown Menu](#dropdown-menu)
  - [Tabs](#tabs)
  - [Live Regions](#live-regions)
  - [Skip Links](#skip-links)
  - [Icon-Only Controls](#icon-only-controls)

---

## Buttons & Links

```tsx
// Action → <button>
<button type="button" onClick={handleDelete}>Delete item</button>

// Navigation → <a>
<a href="/dashboard">Go to dashboard</a>

// Disabled button — keep in tab order so screen readers can announce it
<button type="button" disabled aria-disabled="true">Save</button>
```

Never use `<div>`, `<span>`, or `<a href="#">` for actions.

---

## Forms

Every input needs a label. Use `htmlFor`/`id` — never rely on visual proximity alone.

```tsx
<div className={styles["field"]}>
  <label htmlFor="email" className={styles["field__label"]}>
    Email address
  </label>
  <input
    id="email"
    type="email"
    name="email"
    autoComplete="email"
    aria-describedby={error ? "email-error" : undefined}
    aria-invalid={error ? "true" : undefined}
    className={styles["field__input"]}
  />
  {error && (
    <p id="email-error" role="alert" className={styles["field__error"]}>
      {error}
    </p>
  )}
</div>
```

### Required fields

```tsx
<label htmlFor="name">
  Full name <span aria-hidden="true">*</span>
</label>
<input id="name" type="text" required aria-required="true" />
<p className={styles['form__required-note']}>
  Fields marked <span aria-hidden="true">*</span> are required
</p>
```

### Grouping related inputs

```tsx
<fieldset>
  <legend>Delivery address</legend>
  {/* inputs */}
</fieldset>
```

---

## Navigation

```tsx
<nav aria-label="Main navigation">
  <ul role="list">
    <li>
      <a href="/" aria-current={isCurrentPage("/") ? "page" : undefined}>
        Home
      </a>
    </li>
    <li>
      <a href="/about" aria-current={isCurrentPage("/about") ? "page" : undefined}>
        About
      </a>
    </li>
  </ul>
</nav>
```

Use distinct `aria-label` values when multiple `<nav>` elements appear on the same page
(e.g. "Main navigation", "Breadcrumb", "Footer navigation").

---

## Modal / Dialog

Use the native `<dialog>` element. It handles focus trap, Escape key, and `aria-modal`
behaviour natively in modern browsers.

```tsx
"use client";

import { useEffect, useRef } from "react";
import styles from "./Modal.module.css";

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: React.ReactNode;
}

export function Modal({ isOpen, onClose, title, children }: ModalProps) {
  const dialogRef = useRef<HTMLDialogElement>(null);

  useEffect(() => {
    const dialog = dialogRef.current;
    if (!dialog) return;
    if (isOpen) {
      dialog.showModal();
    } else {
      dialog.close();
    }
  }, [isOpen]);

  return (
    <dialog
      ref={dialogRef}
      className={styles["modal"]}
      aria-labelledby="modal-title"
      onClose={onClose}
    >
      <div className={styles["modal__inner"]}>
        <header className={styles["modal__header"]}>
          <h2 id="modal-title" className={styles["modal__title"]}>
            {title}
          </h2>
          <button
            type="button"
            className={styles["modal__close"]}
            aria-label="Close dialog"
            onClick={onClose}
          >
            {/* Icon */}
          </button>
        </header>
        <div className={styles["modal__body"]}>{children}</div>
      </div>
    </dialog>
  );
}
```

---

## Disclosure / Accordion

```tsx
"use client";

import { useState } from "react";
import styles from "./Disclosure.module.css";

interface DisclosureProps {
  summary: string;
  children: React.ReactNode;
}

export function Disclosure({ summary, children }: DisclosureProps) {
  const [isOpen, setIsOpen] = useState(false);
  const contentId = `disclosure-${summary.toLowerCase().replace(/\s+/g, "-")}`;

  return (
    <div className={styles["disclosure"]}>
      <button
        type="button"
        aria-expanded={isOpen}
        aria-controls={contentId}
        className={styles["disclosure__trigger"]}
        onClick={() => setIsOpen((prev) => !prev)}
      >
        {summary}
        <span className={styles["disclosure__icon"]} aria-hidden="true">
          {isOpen ? "▲" : "▼"}
        </span>
      </button>
      <div id={contentId} className={styles["disclosure__content"]} hidden={!isOpen}>
        {children}
      </div>
    </div>
  );
}
```

---

## Dropdown Menu

```tsx
"use client";

import { useState, useRef, useEffect } from "react";
import styles from "./DropdownMenu.module.css";

interface DropdownMenuProps {
  trigger: string;
  items: Array<{ label: string; onClick: () => void }>;
}

export function DropdownMenu({ trigger, items }: DropdownMenuProps) {
  const [isOpen, setIsOpen] = useState(false);
  const menuRef = useRef<HTMLDivElement>(null);
  const triggerId = "dropdown-trigger";
  const menuId = "dropdown-menu";

  // Close on outside click
  useEffect(() => {
    const handleClickOutside = (e: MouseEvent) => {
      if (menuRef.current && !menuRef.current.contains(e.target as Node)) {
        setIsOpen(false);
      }
    };
    document.addEventListener("mousedown", handleClickOutside);
    return () => document.removeEventListener("mousedown", handleClickOutside);
  }, []);

  return (
    <div ref={menuRef} className={styles["dropdown"]}>
      <button
        id={triggerId}
        type="button"
        aria-haspopup="menu"
        aria-expanded={isOpen}
        aria-controls={menuId}
        className={styles["dropdown__trigger"]}
        onClick={() => setIsOpen((prev) => !prev)}
      >
        {trigger}
      </button>
      {isOpen && (
        <ul
          id={menuId}
          role="menu"
          aria-labelledby={triggerId}
          className={styles["dropdown__menu"]}
        >
          {items.map((item) => (
            <li key={item.label} role="none">
              <button
                type="button"
                role="menuitem"
                className={styles["dropdown__item"]}
                onClick={() => {
                  item.onClick();
                  setIsOpen(false);
                }}
              >
                {item.label}
              </button>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

---

## Tabs

```tsx
"use client";

import { useState } from "react";
import styles from "./Tabs.module.css";

interface Tab {
  id: string;
  label: string;
  content: React.ReactNode;
}

interface TabsProps {
  tabs: Tab[];
  label: string; // describes what this tab group is for
}

export function Tabs({ tabs, label }: TabsProps) {
  const [activeTab, setActiveTab] = useState(tabs[0].id);

  return (
    <div className={styles["tabs"]}>
      <div role="tablist" aria-label={label} className={styles["tabs__list"]}>
        {tabs.map((tab) => (
          <button
            key={tab.id}
            role="tab"
            id={`tab-${tab.id}`}
            aria-selected={activeTab === tab.id}
            aria-controls={`panel-${tab.id}`}
            tabIndex={activeTab === tab.id ? 0 : -1}
            className={styles["tabs__tab"]}
            onClick={() => setActiveTab(tab.id)}
          >
            {tab.label}
          </button>
        ))}
      </div>
      {tabs.map((tab) => (
        <div
          key={tab.id}
          role="tabpanel"
          id={`panel-${tab.id}`}
          aria-labelledby={`tab-${tab.id}`}
          hidden={activeTab !== tab.id}
          className={styles["tabs__panel"]}
          tabIndex={0}
        >
          {tab.content}
        </div>
      ))}
    </div>
  );
}
```

---

## Live Regions

For dynamic content updates that screen readers should announce:

```tsx
// Polite: announce after current speech finishes (status messages, search results)
<div role="status" aria-live="polite" aria-atomic="true">
  {statusMessage}
</div>

// Assertive: interrupt immediately (errors, critical alerts)
<div role="alert" aria-live="assertive" aria-atomic="true">
  {errorMessage}
</div>
```

Keep live regions in the DOM from page load — adding them dynamically after the update
is unreliable. Conditionally update their content instead.

---

## Skip Links

Add a skip link as the very first focusable element on every page:

```tsx
// In layout.tsx or a shared Header component
<a href="#main-content" className={styles['skip-link']}>
  Skip to main content
</a>

// ...

<main id="main-content" tabIndex={-1}>
  {children}
</main>
```

```css
/* Visually hidden until focused */
.skip-link {
  position: absolute;
  top: var(--space-2);
  left: var(--space-2);
  z-index: 9999;
  padding: var(--space-2) var(--space-4);
  background: var(--color-primary);
  color: var(--color-on-primary);
  border-radius: var(--radius-md);
  transform: translateY(-200%);
  transition: transform 150ms ease;

  &:focus {
    transform: translateY(0);
  }
}
```

---

## Icon-Only Controls

When a button or link contains only an icon, provide a text alternative:

```tsx
// Option 1: aria-label on the button
<button type="button" aria-label="Close menu">
  <CloseIcon aria-hidden="true" />
</button>

// Option 2: visually hidden text (preferred when icon + label appear elsewhere)
<button type="button">
  <CloseIcon aria-hidden="true" />
  <span className="sr-only">Close menu</span>
</button>
```

```css
/* global.css — visually hidden utility */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```
