# TypeScript Magician Skill

The `typescript-magician` skill designs complex generic types, refactors `any` types to strict alternatives, creates type guards and utility types, and resolves TypeScript compiler errors.

## Skill Reference

```text { .api }
Skill name:  typescript-magician
Tile name:   mcollina/typescript-magician
Version:     0.1.0
Path:        skills/typescript-magician/SKILL.md
Tile:        skills/typescript-magician/tile.json
Tags:        typescript, types, generics, type-safety, advanced-typescript
```

## When to Use

- TypeScript errors and type challenges
- Eliminating `any` types from codebases
- Complex generics and type inference issues
- Strict typing, type guards, type narrowing
- Advanced TypeScript patterns: `infer`, `extends`, conditional types, mapped types, template literal types, branded/opaque types
- Utility types: `Partial`, `Record`, `ReturnType`, `Awaited`, etc.

**Trigger terms**: TypeScript errors, generics, type inference, type guards, removing any types, strict typing, `infer`, `extends`, conditional types, mapped types, template literal types, branded types, opaque types, utility types

## Workflow

```text { .api }
When invoked:
1. Run `tsc --noEmit` to capture the full error output before making changes
2. Identify the root cause (unsound inference, missing constraints, implicit any, etc.)
3. Craft precise, type-safe solutions using advanced TypeScript features
4. Eliminate all `any` types — validate each replacement still satisfies call sites
5. Confirm the fix compiles with a second `tsc --noEmit` pass
```

## Rule Sub-Documents

```text { .api }
skills/typescript-magician/rules/
  array-index-access.md       - Safe array index access (noUncheckedIndexedAccess)
  as-const-typeof.md          - as const assertions and typeof operator patterns
  builder-pattern.md          - Builder pattern with type-tracking generics
  conditional-types.md        - Conditional type expressions (T extends U ? X : Y)
  deep-inference.md           - Deep type inference, type propagation patterns
  error-diagnosis.md          - Diagnosing TypeScript compiler errors systematically
  function-overloads.md       - Function overload signatures
  generics-basics.md          - Generic type parameters, constraints, defaults
  infer-keyword.md            - infer keyword in conditional types
  mapped-types.md             - Mapped type transformations (keyof, in, as)
  opaque-types.md             - Branded/nominal/opaque typing patterns
  template-literal-types.md   - Template literal type expressions
  type-narrowing.md           - Type narrowing: typeof, instanceof, discriminated unions
  utility-types.md            - Built-in TypeScript utility types
```

## Key Capabilities

### Eliminating `any` with Generics

```typescript { .api }
// Before
function getProperty(obj: any, key: string): any {
  return obj[key]
}

// After — fully type-safe with keyof constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]
}
```

### Conditional Types

```typescript { .api }
// Extract the element type of an array
type ElementOf<T> = T extends (infer E)[] ? E : never

// Unwrap Promise
type Awaited<T> = T extends Promise<infer U> ? Awaited<U> : T

// Conditional with constraint
type NonNullable<T> = T extends null | undefined ? never : T
```

### Mapped Types

```typescript { .api }
// Make all properties optional
type Partial<T> = { [K in keyof T]?: T[K] }

// Make all properties required
type Required<T> = { [K in keyof T]-?: T[K] }

// Make all properties readonly
type Readonly<T> = { readonly [K in keyof T]: T[K] }

// Pick a subset of properties
type Pick<T, K extends keyof T> = { [P in K]: T[P] }

// Remap keys with `as`
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
}
```

### Template Literal Types

```typescript { .api }
type EventName<T extends string> = `on${Capitalize<T>}`
type ClickEvent = EventName<'click'>  // 'onClick'

type CssProperty = 'margin' | 'padding'
type CssWithUnit = `${CssProperty}-${'top' | 'bottom' | 'left' | 'right'}`
// 'margin-top' | 'margin-bottom' | ... | 'padding-right'
```

### Discriminated Unions and Type Narrowing

```typescript { .api }
type Shape =
  | { kind: 'circle'; radius: number }
  | { kind: 'square'; side: number }
  | { kind: 'rectangle'; width: number; height: number }

function area(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':    return Math.PI * shape.radius ** 2
    case 'square':    return shape.side ** 2
    case 'rectangle': return shape.width * shape.height
  }
}
```

### Branded / Opaque Types

```typescript { .api }
// Create a nominal type distinct from string
declare const __brand: unique symbol
type Brand<T, B> = T & { [__brand]: B }

type UserId = Brand<string, 'UserId'>
type OrderId = Brand<string, 'OrderId'>

function createUserId(id: string): UserId {
  return id as UserId
}

// UserId and OrderId are not assignable to each other even though both are strings
```

### `infer` Keyword

```typescript { .api }
// Extract function return type
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never

// Extract function parameter types
type Parameters<T> = T extends (...args: infer P) => any ? P : never

// Extract first argument type
type FirstArg<T> = T extends (first: infer F, ...rest: any[]) => any ? F : never

// Extract Promise value
type PromiseValue<T> = T extends Promise<infer V> ? V : T
```

### Built-in Utility Types

```typescript { .api }
Partial<T>           // All properties optional
Required<T>          // All properties required
Readonly<T>          // All properties readonly
Record<K, V>         // Object type with keys K and values V
Pick<T, K>           // Select subset of properties
Omit<T, K>           // Remove subset of properties
Exclude<T, U>        // Remove U from union T
Extract<T, U>        // Keep only U from union T
NonNullable<T>       // Remove null and undefined
ReturnType<T>        // Return type of function T
Parameters<T>        // Parameter types of function T as tuple
ConstructorParameters<T>  // Constructor parameter types
InstanceType<T>      // Instance type of constructor T
Awaited<T>           // Unwrap Promise (recursive)
```

### Function Overloads

```typescript { .api }
// Overload signatures (must come before implementation)
function process(input: string): string
function process(input: number): number
function process(input: string[]): string[]

// Implementation signature (not visible to callers)
function process(input: string | number | string[]): string | number | string[] {
  if (typeof input === 'string') return input.toUpperCase()
  if (typeof input === 'number') return input * 2
  return input.map(s => s.toUpperCase())
}
```

### `as const` and `typeof`

```typescript { .api }
// as const: prevents widening, creates readonly literal types
const config = {
  host: 'localhost',
  port: 3000,
} as const
// config.host: 'localhost' (not string)
// config.port: 3000 (not number)

// typeof: extract type from value
type Config = typeof config
// { readonly host: 'localhost'; readonly port: 3000 }

// typeof for enum-like patterns
const Direction = { Up: 'UP', Down: 'DOWN' } as const
type Direction = typeof Direction[keyof typeof Direction]  // 'UP' | 'DOWN'
```

## Tile Manifest

```json { .api }
// skills/typescript-magician/tile.json
{
  "name": "mcollina/typescript-magician",
  "version": "0.1.0",
  "private": false,
  "summary": "Designs complex generic types, refactors any types to strict alternatives, creates type guards and utility types, and resolves TypeScript compiler errors.",
  "skills": {
    "typescript-magician": {
      "path": "SKILL.md"
    }
  }
}
```
