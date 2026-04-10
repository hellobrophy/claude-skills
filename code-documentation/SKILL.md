---
name: code-documentation
description: Generates comprehensive code documentation following JSDoc standards. Use when writing documentation, adding docstrings, or documenting APIs.
---

# Code Documentation Skill

## Instructions

When asked to document code:

1. Use JSDoc format for JavaScript/TypeScript
2. Include @param, @returns, and @example tags
3. Write descriptions that explain the "why," not just the "what"
4. Add @throws annotations for functions that can throw

### TypeScript projects

When the codebase uses TypeScript, omit JSDoc type annotations (`{number}`, `{string}`, etc.) — the types are already in the signature. Keep only the descriptions:

```typescript
/**
 * Calculates the discounted price based on a percentage reduction.
 * Useful for applying promotional discounts at checkout.
 *
 * @param price - Original price before discount
 * @param percentage - Discount percentage (0-100)
 * @returns The price after applying the discount
 * @example
 * calculateDiscount(100, 20) // Returns 80
 */
function calculateDiscount(price: number, percentage: number): number {
  return price * (1 - percentage / 100);
}
```

### JavaScript projects

Include full JSDoc type annotations when there is no TypeScript:

```javascript
/**
 * Calculates the discounted price based on a percentage reduction.
 * Useful for applying promotional discounts at checkout.
 *
 * @param {number} price - Original price before discount
 * @param {number} percentage - Discount percentage (0-100)
 * @returns {number} The price after applying the discount
 * @example
 * calculateDiscount(100, 20) // Returns 80
 */
function calculateDiscount(price, percentage) {
  return price * (1 - percentage / 100);
}
```
