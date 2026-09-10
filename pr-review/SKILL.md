---
name: pr-review
description: Review pull requests / diffs against this team's Angular + TypeScript engineering conventions and flag real issues (bugs, missing tests, anti-patterns) with clear blocking-vs-nitpick labels. Use this skill whenever the user pastes a diff or PR link and asks for review, asks "does this follow our style guide?", asks for feedback before merging, or asks whether a change is "ready to ship."
---

# PR Review Skill

## Purpose
Provides a consistent checklist, tone, and set of known anti-patterns for
reviewing pull requests against this team's Angular/TypeScript conventions.

## When to use
- User pastes a diff or PR link and asks for review
- User asks "does this follow our style guide?"
- User asks for feedback before merging
- User asks if a change is ready to ship

## Review checklist

1. **Correctness** — does the logic do what the PR description claims? Walk
   through the actual control flow rather than trusting the description.
2. **Tests** — are new code paths covered? Are edge cases (null/undefined,
   empty arrays, error responses) tested?
3. **Naming & style** — consistent with the codebase conventions below?
4. **Error handling** — are failures handled explicitly, not swallowed
   silently (e.g. an empty `catch`, or a subscribe with no error callback)?
5. **Security** — any injected input, secrets, or unvalidated data?
6. **Known anti-patterns** (see below) — flag these even if nothing else is
   wrong with the PR.

## Known anti-patterns to flag (blocking or nit, per severity below)

This team's codebase currently contains some of these patterns already, but
new code should not add more of them. Flag them even in an otherwise
clean-looking diff:

- **`any` types** — new parameters, properties, or return types typed as
  `any` (or effectively untyped) should be flagged. Suggest a concrete
  interface or a narrower type. **Severity: nit**, unless the untyped value
  flows into something security- or correctness-sensitive (e.g. unvalidated
  API input), in which case **blocking**.
- **`console.log` / `console.error` for debugging** — flag any left-in debug
  logging. **Severity: nit** for a stray log; **blocking** if it could leak
  sensitive data (tokens, user PII).
- **Leftover commented-out code** — blocks of commented-out logic (old
  implementations, disabled features) left in a diff should be flagged for
  removal. **Severity: nit**.
- **Long `if`/`else if` chains for simple mapping logic** — e.g. a chain of
  `if (x === 'Monday') ... if (x === 'Tuesday') ...` that could be a lookup
  object or `switch`. **Severity: nit**.
- **Unmanaged RxJS subscriptions** — a `.subscribe()` in a component without
  `takeUntil`, the async pipe, or manual unsubscription in `ngOnDestroy`
  (risk of memory leaks / stale callbacks). **Severity: blocking** if the
  component is long-lived (e.g. a page-level component); **nit** for
  short-lived one-off subscriptions.
- **Magic strings/numbers** for things like day names, timeouts, or feature
  flags, where a shared constant already exists elsewhere in the codebase.
  **Severity: nit**.

## Naming & style conventions (inferred from the codebase)

- Components: `PascalCase` class names ending in `Component`; selector
  `app-<kebab-case>`.
- Services injected via constructor parameter properties
  (`private userService: UserService`), not `inject()`, unless the
  surrounding file already uses `inject()`.
- Boolean flags prefixed `is`/`show`/`has` (`isMenuShowing`, `showAuthor`).
- Class properties and locals in `camelCase`; no Hungarian notation beyond
  the boolean prefixes above.
- Prefer `const`/`let` — flag any new `var`.
- Template/model imports grouped by origin (Angular/Material first, then
  app-internal `src/app/...`, then third-party libraries) — flag new imports
  that break this grouping.

## Tone guidelines
- Lead with what's good before critiques.
- Flag blocking issues separately from nitpicks (label them "blocking:" vs
  "nit:").
- Suggest concrete fixes, not just "this is wrong" — show the replacement
  code when it's short enough to include inline.
- Don't pile on: if the same anti-pattern (e.g. `any`) shows up five times,
  call it out once with all locations listed, not five separate comments.

## Output format

Respond with:
- A one-line summary verdict (**approve** / **request changes** / **comment**)
- Blocking issues (if any), each with file/line reference and a suggested fix
- Nitpicks (optional, non-blocking), each with file/line reference
- A short closing note if the PR does something well worth calling out
