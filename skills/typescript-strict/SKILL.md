---
name: typescript-strict
description: Strict TypeScript patterns — no any, unknown + narrowing, interface vs type, readonly immutability. Use when writing or reviewing TypeScript code.
---

# TypeScript Strict Patterns

Extends `rules/typescript/coding-style.md` with concrete templates.

## No `any`

Replace `any` with `unknown` + narrowing:

```ts
// BAD
function parse(x: any): unknown { return x }

// GOOD
function parse(x: unknown): unknown {
  if (typeof x === 'string') return JSON.parse(x)
  return x
}
```

Never use `as any`; use `as` only after a narrowing check.

## interface vs type

| Use | Construct |
| --- | --- |
| Object shapes | `interface` |
| Unions / intersections | `type` |

```ts
interface User { id: string; name: string }
type Result = User | { error: string }
```

## Immutability

Prefer `Readonly<T>` and `readonly` fields; create new objects instead of mutating.

```ts
// BAD
user.name = 'x'

// GOOD
const next: User = { ...user, name: 'x' }
```

For arrays, use spread/`map`/`filter`, never `push`/`splice` on shared state.

## Zod validation

Validate external input with Zod (project convention): parse once at the boundary, keep the inferred type.

```ts
import { z } from 'zod'
const UserSchema = z.object({ id: z.string(), name: z.string() })
type User = z.infer<typeof UserSchema>
```

## Rules

- Typecheck with `vue-tsc --noEmit` (`npm run typecheck`) — never plain `tsc` for `.vue` files.
- Follow `rules/typescript/` for the full standard.
