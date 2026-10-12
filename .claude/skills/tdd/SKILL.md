---
name: tdd
description: Test-driven development workflow for TypeScript and Vue 3. Use when writing tests first for a new feature or bug fix, with Vitest and @vue/test-utils examples.
---

# TDD (TypeScript + Vue 3)

Concrete RED → GREEN → REFACTOR practice. Extends `rules/common/testing.md` with runnable patterns.

## RED — write the failing test first

```ts
// counter.test.ts
import { describe, expect, it } from 'vitest'

describe('increment', () => {
  it('increments from zero', () => {
    expect(increment(0)).toBe(1)
  })
})
```

Run `npm run test` and confirm it fails **for the right reason** (missing function), not a typo.

For a Vue component, mount with `@vue/test-utils`:

```ts
import { mount } from '@vue/test-utils'
import { describe, expect, it } from 'vitest'
import Counter from './Counter.vue'

describe('Counter', () => {
  it('renders and increments', async () => {
    const wrapper = mount(Counter)
    expect(wrapper.text()).toContain('0')
    await wrapper.get('button').trigger('click')
    expect(wrapper.text()).toContain('1')
  })
})
```

## GREEN — minimal implementation

Write only enough to pass. No speculative features, no gold-plating.

```ts
export function increment(n: number): number {
  return n + 1
}
```

## REFACTOR — without changing behavior

- Improve names, extract helpers, reduce nesting (≤4 levels).
- Re-run tests after every refactor step.
- Keep functions <50 lines and files 200–400 lines.

## Rules

- Follow `rules/typescript/testing.md` for TypeScript specifics.
- Verify 80%+ coverage with `npm run test:coverage`.
- Never skip a test or add `// @ts-ignore` to make a suite pass.
