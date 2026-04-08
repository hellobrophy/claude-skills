---
name: code-review
description: Comprehensive code review following standards. Use when reviewing pull requests, auditing code quality, or preparing code for production deployment.
---

# NovaTech Code Review

This skill guides comprehensive code reviews covering security, performance, and style standards.

## When to Use This Skill

- Reviewing pull requests before merge
- Auditing existing code for quality issues
- Preparing features for production deployment
- Onboarding reviews for new team members' code

## Review Workflow

1. **Security Review** — Check for vulnerabilities first
2. **Performance Review** — Identify bottlenecks and inefficiencies
3. **Style Review** — Ensure consistency with team standards
4. **Validation** — Run automated linters for final check

## Security Review

For detailed security checks, see [SECURITY.md](./SECURITY.md).

Key areas to examine:

- Input validation and sanitization
- Authentication and authorization
- Data exposure risks
- Dependency vulnerabilities

## Performance Optimization

Reference [PERFORMANCE.md](./PERFORMANCE.md) for performance patterns.

Focus on:

- Unnecessary re-renders and computations
- Network request optimization
- Memory leaks and resource cleanup
- Asset loading strategies

## Review Checklist Summary

Before approving any code:

- [ ] No security vulnerabilities identified
- [ ] Performance patterns followed
- [ ] Accessibility requirements satisfied
- [ ] Documentation is adequate
