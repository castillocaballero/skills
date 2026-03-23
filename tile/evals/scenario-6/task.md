# Deep-Readonly Type Utility

A TypeScript type utility library that provides a `DeepReadonly` mapped type and related conditional helpers for working with deeply immutable data structures.

## Capabilities

### DeepReadonly mapped type

- `DeepReadonly<{ a: { b: number } }>` produces a type where both the outer and inner objects are readonly [@test](./tests/deep-readonly.test.ts)
- `DeepReadonly<{ items: string[] }>` produces a type where `items` is `readonly string[]` [@test](./tests/deep-readonly-array.test.ts)
- Primitive types (`string`, `number`, `boolean`) pass through unchanged [@test](./tests/deep-readonly-primitive.test.ts)

### Conditional type: extract nullable inner type

- `UnwrapNullable<string | null>` resolves to `string` [@test](./tests/unwrap-nullable.test.ts)
- `UnwrapNullable<number | undefined>` resolves to `number` [@test](./tests/unwrap-nullable-undefined.test.ts)
- `UnwrapNullable<boolean>` resolves to `boolean` (already non-nullable) [@test](./tests/unwrap-nullable-noop.test.ts)

### Conditional type: extract Promise value

- `Awaited<Promise<string>>` resolves to `string` [@test](./tests/awaited.test.ts)
- `Awaited<Promise<Promise<number>>>` resolves to `number` (nested unwrapping) [@test](./tests/awaited-nested.test.ts)

## Implementation

[@generates](./src/types.ts)

## API

```typescript { #api }
export type DeepReadonly<T> = T extends object
  ? { readonly [K in keyof T]: DeepReadonly<T[K]> }
  : T

export type UnwrapNullable<T> = T extends null | undefined ? never : T

export type Awaited<T> = T extends Promise<infer U> ? Awaited<U> : T
```

## Dependencies { .dependencies }

### @matteo.collina/skills 1.0.0 { .dependency }

Provides TypeScript best-practice skill definitions including advanced type system patterns.

[@satisfied-by](@matteo.collina/skills)
