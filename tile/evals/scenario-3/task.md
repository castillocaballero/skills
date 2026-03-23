# CSV File Transformation Pipeline

A streaming pipeline that reads a CSV file, transforms each row, and writes the result to an output file — all without loading the entire file into memory.

## Capabilities

### Stream and transform CSV rows

Given an input CSV file with a header row followed by data rows:

- Each row is parsed and emitted as an object with typed fields [@test](./tests/transform.test.ts)
- The pipeline processes rows one at a time, not loading the whole file into memory [@test](./tests/streaming.test.ts)

### Write output to a file

- The transformed rows are written to an output file as newline-delimited JSON (NDJSON), one JSON object per line [@test](./tests/output-format.test.ts)

### Error propagation

- If reading the input file fails, the error propagates and the output file is not created [@test](./tests/read-error.test.ts)
- If a transform throws, the pipeline terminates and the error is reported to the caller [@test](./tests/transform-error.test.ts)

## Implementation

[@generates](./src/pipeline.ts)

## API

```typescript { #api }
export async function transformCsv(inputPath: string, outputPath: string): Promise<void>
```

## Dependencies { .dependencies }

### @matteo.collina/skills 1.0.0 { .dependency }

Provides Node.js best-practice skill definitions including stream and pipeline patterns.

[@satisfied-by](@matteo.collina/skills)
