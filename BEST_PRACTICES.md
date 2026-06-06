# Web Development Best Practices

This guide outlines coding standards, conventions, and best practices for the Web Development Internship Program.

## Table of Contents

1. [Code Style & Formatting](#code-style--formatting)
2. [Naming Conventions](#naming-conventions)
3. [Component Patterns](#component-patterns)
4. [State Management](#state-management)
5. [API Design](#api-design)
6. [Testing Standards](#testing-standards)
7. [Git Practices](#git-practices)
8. [Performance](#performance)
9. [Security](#security)
10. [Documentation](#documentation)

---

## Code Style & Formatting

### General Rules

- **Use Prettier** for automatic formatting
- **Use ESLint** for code quality
- **Configure both tools** before starting

### Configuration Files

`.prettierrc.json`:
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "arrowParens": "always"
}
```

`.eslintrc.json`:
```json
{
  "extends": ["next/core-web-vitals"],
  "rules": {
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn",
    "no-console": "warn"
  }
}
```

### Before Committing

```bash
npm run format     # Format with Prettier
npm run lint       # Check with ESLint
npm run lint:fix   # Auto-fix ESLint issues
```

---

## Naming Conventions

### Files & Directories

```
✅ Good              ❌ Bad
components/         COMPONENTS/
Button.tsx         button.tsx (lowercase)
UserProfile.tsx    UserProfilePage.tsx (too specific)
api/auth/route.ts  api/auth.ts

lib/                utils/ (too vague)
utils/              helpers/ (vague)
types/              types_folder/ (underscore)
hooks/              custom-hooks/ (hyphen)
```

### Variables & Constants

```typescript
✅ Good              ❌ Bad
const userName     const UserName
const userId       const user_id
const MAX_RETRIES  const max_retries
const isLoading    const loading
const fetchUsers   const getUsers (not always descriptive)

let userList = []  let data = []
const apiUrl       const API_url
```

### React Components

```typescript
// ✅ Good - PascalCase, descriptive name
function UserProfileCard({ user }) {
  return <div>...</div>
}

// ❌ Bad - lowercase
function userProfileCard({ user }) {
  return <div>...</div>
}

// ❌ Bad - too generic
function Card({ user }) {
  return <div>...</div>
}
```

### Boolean Variables

```typescript
// ✅ Good - prefix with is/has/should/can
const isLoading = true
const hasError = false
const canEdit = user.role === 'admin'
const shouldShowModal = true

// ❌ Bad - ambiguous
const loading = true
const error = false
const edit = true
```

---

## Component Patterns

### Functional Components with TypeScript

```typescript
// ✅ Good
interface UserCardProps {
  user: User
  onDelete?: (id: string) => void
  className?: string
}

export function UserCard({ user, onDelete, className }: UserCardProps) {
  const handleDelete = () => {
    onDelete?.(user.id)
  }

  return (
    <div className={className}>
      <h2>{user.name}</h2>
      {onDelete && <button onClick={handleDelete}>Delete</button>}
    </div>
  )
}

// ❌ Bad - prop drilling, no types
export function UserCard(props) {
  return <div>{props.user.name}</div>
}
```

### Custom Hooks

```typescript
// ✅ Good - clear purpose, typed
interface UseFetchResult<T> {
  data: T | null
  loading: boolean
  error: Error | null
}

export function useFetch<T>(url: string): UseFetchResult<T> {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url)
        if (!response.ok) throw new Error('Failed to fetch')
        setData(await response.json())
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Unknown error'))
      } finally {
        setLoading(false)
      }
    }

    fetchData()
  }, [url])

  return { data, loading, error }
}
```

### Client vs Server Components

```typescript
// ✅ Good - Server component for data fetching
export default async function UsersList() {
  const users = await fetchUsers()
  return <UserListClient users={users} />
}

// Client component for interactivity
'use client'

export function UserListClient({ users }: { users: User[] }) {
  const [selected, setSelected] = useState<string | null>(null)

  return (
    <ul>
      {users.map((user) => (
        <li
          key={user.id}
          onClick={() => setSelected(user.id)}
          className={selected === user.id ? 'active' : ''}
        >
          {user.name}
        </li>
      ))}
    </ul>
  )
}
```

---

## State Management

### useState Best Practices

```typescript
// ✅ Good - separate concerns
const [user, setUser] = useState<User | null>(null)
const [isLoading, setIsLoading] = useState(false)
const [error, setError] = useState<Error | null>(null)

// ❌ Bad - combine unrelated state
const [data, setData] = useState({
  user: null,
  loading: false,
  error: null,
  theme: 'dark',
})
```

### useCallback for Handlers

```typescript
// ✅ Good - memoized callback
const handleDelete = useCallback(async (id: string) => {
  try {
    await api.deleteUser(id)
    // Update state
  } catch (error) {
    console.error('Delete failed', error)
  }
}, [])

// ❌ Bad - function recreated on every render
const handleDelete = async (id: string) => {
  // ...
}
```

### useEffect Dependencies

```typescript
// ✅ Good - explicit dependencies
useEffect(() => {
  fetchUser(userId)
}, [userId])

// ❌ Bad - missing dependency
useEffect(() => {
  fetchUser(userId) // userId not in dependency array
}, [])

// ✅ Good - empty dependency = run once
useEffect(() => {
  initializeApp()
}, [])
```

---

## API Design

### Route Handlers

```typescript
// ✅ Good - proper error handling
import { NextRequest, NextResponse } from 'next/server'

export async function GET(request: NextRequest) {
  try {
    const users = await db.user.findMany()
    return NextResponse.json(users)
  } catch (error) {
    console.error('Failed to fetch users:', error)
    return NextResponse.json(
      { error: 'Failed to fetch users' },
      { status: 500 }
    )
  }
}

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()

    // Validate input
    if (!body.email || !body.name) {
      return NextResponse.json(
        { error: 'Missing required fields' },
        { status: 400 }
      )
    }

    const user = await db.user.create({
      data: {
        email: body.email,
        name: body.name,
      },
    })

    return NextResponse.json(user, { status: 201 })
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to create user' },
      { status: 500 }
    )
  }
}
```

### Error Handling

```typescript
// ✅ Good - consistent error responses
interface ApiError {
  error: string
  code: string
  statusCode: number
}

// ❌ Bad - inconsistent responses
{
  "message": "Error",
  "status": 500
}
```

### Versioning

```
/api/v1/users     ✅ Good - explicit versioning
/api/users        ❌ Bad - no versioning
```

---

## Testing Standards

### Unit Tests

```typescript
// ✅ Good - clear test structure
describe('Button Component', () => {
  it('should render with correct text', () => {
    const { getByText } = render(<Button>Click me</Button>)
    expect(getByText('Click me')).toBeInTheDocument()
  })

  it('should call onClick when clicked', () => {
    const onClick = jest.fn()
    const { getByRole } = render(<Button onClick={onClick}>Click</Button>)
    
    fireEvent.click(getByRole('button'))
    expect(onClick).toHaveBeenCalled()
  })
})
```

### Test Coverage

```bash
# Aim for 80%+ coverage
npm run test:coverage

# Coverage targets:
# - Statements: 80%
# - Branches: 75%
# - Functions: 80%
# - Lines: 80%
```

### E2E Tests

```typescript
// ✅ Good - realistic user flows
describe('User Login Flow', () => {
  it('should login successfully with valid credentials', () => {
    cy.visit('/')
    cy.get('input[type="email"]').type('user@example.com')
    cy.get('input[type="password"]').type('password123')
    cy.get('button[type="submit"]').click()
    cy.url().should('include', '/dashboard')
  })
})
```

---

## Git Practices

### Commit Messages

```bash
# ✅ Good - descriptive and concise
git commit -m "feat: add user authentication with NextAuth"
git commit -m "fix: resolve issue with form validation"
git commit -m "docs: update API documentation"
git commit -m "refactor: simplify user service"
git commit -m "test: add unit tests for Button component"

# ❌ Bad
git commit -m "update"
git commit -m "fix bug"
git commit -m "changes"
```

### Commit Format

Use conventional commits:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (formatting, missing semicolons, etc.)
- `refactor`: Code refactoring
- `perf`: Performance improvement
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Example:**

```
feat(auth): implement JWT token refresh

Add automatic token refresh logic to prevent unauthorized
access when tokens expire. Implements refresh token rotation
for enhanced security.

Closes #123
```

### Pull Requests

```markdown
## Description
Brief description of the changes

## Related Issue
Fixes #123

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added
- [ ] Integration tests passed
- [ ] Manual testing completed

## Checklist
- [ ] Code follows style guidelines
- [ ] No console.log statements
- [ ] Documentation updated
- [ ] No breaking changes
```

### Branch Naming

```bash
# ✅ Good
feature/user-authentication
fix/form-validation-issue
docs/api-guide
refactor/database-queries

# ❌ Bad
feature_user_auth
fix-form
update
my-branch
```

---

## Performance

### Image Optimization

```typescript
// ✅ Good - using Next.js Image
import Image from 'next/image'

export function Avatar({ src, alt }: { src: string; alt: string }) {
  return (
    <Image
      src={src}
      alt={alt}
      width={100}
      height={100}
      priority={false}
    />
  )
}

// ❌ Bad - using HTML img tag
function Avatar({ src, alt }: { src: string; alt: string }) {
  return <img src={src} alt={alt} />
}
```

### Code Splitting

```typescript
// ✅ Good - lazy load heavy components
import dynamic from 'next/dynamic'

const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <div>Loading...</div>,
})

export default function Page() {
  return <HeavyComponent />
}
```

### Monitoring

```bash
# Analyze bundle size
npm run build
npm run analyze

# Check Core Web Vitals
npm run lighthouse
```

---

## Security

### Environment Variables

```bash
# ✅ Good - sensitive data in .env.local
NEXTAUTH_SECRET=your-secret-key
DATABASE_URL=postgres://...

# ✅ Good - public variables prefixed
NEXT_PUBLIC_API_URL=https://api.example.com

# ❌ Bad - secrets in code
const API_KEY = "sk_live_1234567890"
```

### Input Validation

```typescript
// ✅ Good - validate and sanitize
import { z } from 'zod'

const userSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().min(0).max(150).optional(),
})

export async function POST(request: NextRequest) {
  const body = await request.json()
  const result = userSchema.safeParse(body)

  if (!result.success) {
    return NextResponse.json(
      { error: 'Invalid input' },
      { status: 400 }
    )
  }

  // Use result.data
}
```

### SQL Injection Prevention

```typescript
// ✅ Good - use parameterized queries
const user = await db.user.findUnique({
  where: { id: userId },
})

// ❌ Bad - string concatenation
const query = `SELECT * FROM users WHERE id = '${userId}'`
```

### CORS

```typescript
// ✅ Good - restrict CORS
const allowedOrigins = ['https://example.com', 'https://app.example.com']

export function middleware(request: NextRequest) {
  const origin = request.headers.get('origin')

  if (origin && allowedOrigins.includes(origin)) {
    return NextResponse.next({
      headers: {
        'Access-Control-Allow-Origin': origin,
      },
    })
  }

  return NextResponse.next()
}
```

---

## Documentation

### README Requirements

```markdown
# Project Name

## Description
Brief description of the project

## Technologies
- Next.js 14
- React 18
- TypeScript
- Tailwind CSS

## Getting Started

### Prerequisites
- Node.js v16+
- npm or yarn

### Installation
```bash
git clone ...
cd project
npm install
npm run dev
```

### Configuration
Document environment variables needed

## Project Structure
Explain directory structure

## API Documentation
Document endpoints

## Testing
```bash
npm run test
```

## Deployment
How to deploy

## Contributing
How others can contribute

## License
Project license
```

### Code Comments

```typescript
// ✅ Good - explain why, not what
// Cache user data for 5 minutes to reduce database queries
const cachedUser = await redis.get(`user:${id}`)

// ✅ Good - complex logic
// Check if user has permission and rate limit hasn't been exceeded
if (user.role === 'admin' && !isRateLimited(user.id)) {
  // ...
}

// ❌ Bad - obvious comment
// Get user by id
const user = await db.user.findUnique({ where: { id } })

// ❌ Bad - commented code
// const oldFunction = () => {}
```

### TypeScript Types Documentation

```typescript
/**
 * Represents a user in the system
 */
interface User {
  /** Unique identifier */
  id: string

  /** User's email address (validated) */
  email: string

  /** User's full name */
  name: string

  /** User creation timestamp */
  createdAt: Date

  /** User's role in the system */
  role: 'user' | 'admin' | 'moderator'
}

/**
 * Fetches a user by ID
 * @param id - The user ID to fetch
 * @returns Promise resolving to User or null if not found
 * @throws {ApiError} If the API request fails
 */
async function fetchUser(id: string): Promise<User | null> {
  // ...
}
```

---

## Quick Checklist

Before submitting a PR:

- [ ] Code formatted with Prettier
- [ ] ESLint passes with no errors
- [ ] Tests written and passing
- [ ] No console.log statements
- [ ] No hardcoded secrets or sensitive data
- [ ] Meaningful commit messages
- [ ] Clear PR description
- [ ] Updated documentation
- [ ] No breaking changes (unless necessary)
- [ ] Performance impact assessed

---

## Resources

- [Google Style Guides](https://google.github.io/styleguide/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [TypeScript Best Practices](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html)
- [Next.js Best Practices](https://nextjs.org/docs/app/building-your-application/best-practices)
- [React Best Practices](https://react.dev/learn)

---

**Created for:** Hela-Nexus Web Development Internship  
**Last updated:** 2026-06-06
