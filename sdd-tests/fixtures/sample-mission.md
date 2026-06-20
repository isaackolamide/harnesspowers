# Project Mission

## Objective

What are we building?
- A CLI tool for managing personal task lists

Why?
- Existing tools are too complex for the use case

Who is the user?
- Individual developer managing their own tasks

What does success look like?
- User can add, complete, and list tasks from the terminal in under 3 commands

## Boundaries

### Always Do
- Keep the interface minimal (3 commands max)
- Write tests for all task manipulation logic
- Use TypeScript strict mode

### Ask First
- Adding a sync feature (requires infrastructure decisions)
- Switching storage backend (currently JSON file)

### Never Do
- Persist data in memory only (data must survive process restarts)
- Add a GUI — this is a CLI tool
- Use a database (storage is a plain JSON file)

## Quick Commands

```
Build: npm run build
Test:  npm test
Lint:  npm run lint
Dev:   npm run dev
```
