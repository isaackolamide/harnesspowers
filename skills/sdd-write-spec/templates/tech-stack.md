# Tech Stack & Implementation

## Project Structure

```
src/              → Application source code
src/components    → [Component description]
src/lib           → Shared utilities and helpers
tests/            → Unit and integration tests
e2e/              → End-to-end tests
sdd-docs/             → Documentation
sdd-specs/            → Specification documents
```

## Code Style

### Naming Conventions

- Files: `camelCase.ts` for sources, `name.test.ts` for tests
- Functions: `camelCase`
- Constants: `UPPER_SNAKE_CASE`
- Classes: `PascalCase`

### Formatting Rules

- Indentation: [spaces/tabs]
- Line length: [max length]

### Example

```typescript
// Good
function fetchUserData(userId: string): Promise<User> {
  // [implementation]
}

// Bad
function getuserdata(id) {
  // [avoid this]
}
```

## Testing Strategy

### Framework & Tools
- Test Runner: [framework]
- Assertion Library: [library]
- Coverage Target: [%]

### Test Organization

Tests live in: `tests/` and `src/[feature].test.ts`

### Test Levels

- **Unit**: Test individual functions, pure logic
- **Integration**: Test feature workflows, database interactions
- **E2E**: Test user flows, critical paths

Coverage expectations by level:
- Unit: 80%+
- Integration: [%]
- E2E: Critical paths only
