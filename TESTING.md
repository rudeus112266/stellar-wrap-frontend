# Testing

This document describes the testing strategy for the project, including unit,
integration, end-to-end, and accessibility testing.

## Accessibility testing

We use two accessibility tools, each with a distinct responsibility:

- **`@storybook/addon-a11y`** runs axe against individual components inside the
  Storybook UI. It is a **development aid only**: it surfaces violations while
  you are building or reviewing a component, but it does not run in CI and does
  not fail any build. Use it to catch problems early, at the component level.
- **`@axe-core/playwright`** runs axe against fully rendered pages in the
  Playwright end-to-end suite. This is the **CI gate**: it runs against the real
  application (routing, data, layout) and fails the build on regressions.

In short: Storybook catches component-level issues during development;
Playwright catches page-level regressions in CI. Both are needed because a
component can be accessible in isolation yet broken once composed into a page.

### Baseline and regression gating

Fixing every existing accessibility violation up front would block this work
indefinitely, so the CI check is **regression-based** rather than absolute:

1. The current set of violations is recorded as a baseline.
2. CI runs the axe checks and compares the results against that baseline.
3. CI **fails only when a new violation is introduced** (a regression).
4. Existing baseline violations are tracked separately and are expected to be
   removed over time as the related issues are fixed.

When a baseline violation is fixed, update the recorded baseline so the fix is
locked in and cannot silently regress.

### Related issues

The automated check verifies the fixes for the following open accessibility
issues, so they can be closed once the check passes against the updated
baseline:

- #421
- #426
- #500

### Running the checks locally

Run the Playwright suite (which includes the axe assertions) the same way you
run the rest of the end-to-end tests:

```bash
npx playwright test
```

To inspect component-level results interactively, start Storybook and open the
Accessibility panel:

```bash
npm run storybook
```
