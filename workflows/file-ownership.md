# File Ownership Schema

File ownership prevents merge conflicts in team implementations. Each teammate ONLY modifies files in their domain.

## Domain Table

| Pattern | Owner | Notes |
|---------|-------|-------|
| `src/app/**`, `src/components/**`, `src/features/**/components/**`, `src/styles/**`, `public/**` | Frontend Lead | Pages, UI components, styles, assets |
| `src/lib/**`, `src/services/**`, `src/api/**`, `supabase/**`, `src/features/**/services/**`, `src/features/**/actions/**` | Backend Lead | Services, API, DB, migrations |
| `__tests__/**`, `*.test.*`, `*.spec.*`, `e2e/**`, `cypress/**`, `playwright/**`, `test-utils/**` | Quality Lead | All test files |
| `src/types/**`, `package.json`, `tsconfig.json`, `next.config.*`, `.env*`, `tailwind.config.*`, `.sdd/**` | Lead Only | Shared — request changes via message |

### Grey Areas

| Pattern | Default Owner | Rationale |
|---------|--------------|-----------|
| `src/features/**/index.ts` | Backend Lead | Usually re-exports services |
| `src/features/**/types.ts` | Lead (shared) | Types cross domain boundaries |
| `src/hooks/**` | Frontend Lead | React hooks are UI concerns |
| `src/middleware.*` | Backend Lead | Server-side middleware |
| `*.config.*` (root) | Lead (shared) | Configuration affects everyone |

## Enforcement Checklist

1. Verify all modified files are in your domain before marking task complete (`git diff --name-only`)
2. If file is outside domain → revert + message correct owner
3. Lead verifies via TaskCompleted hook

## Conflict Resolution

| Scenario | Resolution |
|----------|-----------|
| Both need same file | Lead designates primary owner |
| Type definition needed | Backend proposes → frontend confirms → lead creates |
| Package.json change | Message lead with justification |
| New shared utility | Lead decides placement |
