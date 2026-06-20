# Tech Stack & Implementation

## Project Structure

```
src/              → Application source code
src/commands/     → CLI command handlers (add, complete, list)
src/storage/      → JSON file read/write logic
src/types.ts      → Shared TypeScript types
tests/            → Unit tests
sdd-specs/        → Specification documents
```

## Code Style

### Naming Conventions

- Files: `camelCase.ts` for sources, `name.test.ts` for tests
- Functions: `camelCase`
- Constants: `UPPER_SNAKE_CASE`
- Types/interfaces: `PascalCase`

### Formatting Rules

- Indentation: 2 spaces
- Line length: 100

### Example

```typescript
// Good
async function addTask(text: string): Promise<Task> {
  const task: Task = { id: crypto.randomUUID(), text, done: false };
  await storage.append(task);
  return task;
}

// Bad
async function add(t) {
  return storage.append({ id: uuid(), text: t, done: false });
}
```

## Testing Strategy

### Framework & Tools
- Test Runner: Vitest
- Coverage Target: 80%

### Test Organization

Tests live in `tests/` mirroring `src/` structure.

### Test Levels

- **Unit**: Test individual functions — storage, task manipulation
- **Integration**: Test command handlers end-to-end
- **E2E**: Not required for this project
