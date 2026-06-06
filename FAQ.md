# FAQ & Troubleshooting

Common questions and solutions for the Web Development Internship Program.

## Installation & Setup

### Q1: Node.js is not recognized after installation

**Solution:**
1. Restart your terminal/IDE after installing Node.js
2. Verify installation: `node --version`
3. If still not working, restart your computer
4. On Windows, restart PowerShell or Command Prompt

### Q2: npm command not found

**Solution:**
```bash
# Windows - Reinstall Node.js
# Make sure "Add to PATH" is checked during installation

# macOS/Linux - Install via Homebrew
brew install node

# Verify installation
which node
which npm
```

### Q3: Port 3000 is already in use

**Solution:**
```bash
# Run on different port
npm run dev -- -p 3001

# Or kill process using port 3000
# macOS/Linux
lsof -ti:3000 | xargs kill -9

# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

---

## Next.js Setup

### Q4: "Module not found" errors

**Solution:**
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install

# Clear Next.js cache
rm -rf .next
npm run dev
```

### Q5: TypeScript errors in .tsx files

**Solution:**
1. Ensure TypeScript is installed:
```bash
npm install --save-dev typescript
```

2. Check `tsconfig.json` exists
3. Restart your IDE's TypeScript server (Cmd+Shift+P in VS Code)

### Q6: "Cannot find module 'react'"

**Solution:**
```bash
# Reinstall dependencies
npm install react react-dom
npm install --save-dev @types/react @types/react-dom
```

---

## Development

### Q7: Changes not reflecting in browser

**Solution:**
1. Check if dev server is running
2. Hard refresh browser: `Ctrl+Shift+R` (Windows/Linux) or `Cmd+Shift+R` (Mac)
3. Clear browser cache
4. Restart dev server: `npm run dev`

### Q8: Styling not working

**Solution:**
1. For Tailwind CSS, ensure it's imported in global CSS
2. Check `tailwind.config.js` has correct content paths:
```javascript
module.exports = {
  content: [
    './app/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
  ],
}
```

3. Restart dev server after changing config

### Q9: Can't import components correctly

**Solution:**
Check path aliases in `tsconfig.json`:
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"],
      "@components/*": ["./components/*"]
    }
  }
}
```

---

## Routing & Pages

### Q10: 404 error on custom routes

**Solution:**
1. Verify file is in correct location:
```
// ✅ Correct App Router
app/
├── page.tsx          // /
├── about/
│   └── page.tsx      // /about
└── api/
    └── hello/
        └── route.ts  // /api/hello
```

2. Page files must be named `page.tsx` (or `.ts`, `.jsx`, `.js`)
3. API files must be named `route.ts` (or `.js`)

### Q11: Dynamic routes not working

**Solution:**
```typescript
// ✅ Correct file structure
app/
└── blog/
    └── [slug]/
        └── page.tsx

// Page file content
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>Post: {params.slug}</h1>
}
```

---

## Data Fetching

### Q12: Data fetching on client component gives error

**Solution:**
Use server components for data fetching when possible:

```typescript
// ✅ Server component - fetches data
export default async function Posts() {
  const posts = await fetch('...').then(r => r.json())
  return <PostsList posts={posts} />
}

// ✅ Client component - handles interactivity
'use client'
export function PostsList({ posts }) {
  // Interactive logic
}
```

### Q13: Infinite loops in useEffect

**Solution:**
Add proper dependency array:

```typescript
// ✅ Run once on mount
useEffect(() => {
  fetchData()
}, [])

// ✅ Run when specific value changes
useEffect(() => {
  fetchData(id)
}, [id])

// ❌ Always runs (infinite loop)
useEffect(() => {
  fetchData() // Missing dependency
})
```

### Q14: CORS errors when fetching API

**Solution:**
1. Use relative URLs when possible:
```typescript
// ✅ Good - relative URL
const res = await fetch('/api/users')

// ❌ May cause CORS issues
const res = await fetch('http://localhost:3000/api/users')
```

2. Configure CORS in API route if needed:
```typescript
export async function GET(request: NextRequest) {
  return NextResponse.json(
    { data: 'value' },
    {
      headers: {
        'Access-Control-Allow-Origin': '*',
      },
    }
  )
}
```

---

## Forms & Validation

### Q15: Form data not submitting

**Solution:**
```typescript
// ✅ Correct form handling
export function MyForm() {
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault() // Prevent page reload
    const formData = new FormData(e.currentTarget)
    // Process form
  }

  return <form onSubmit={handleSubmit}>...</form>
}
```

### Q16: Validation not working

**Solution:**
Install and use validation library:
```bash
npm install zod
```

```typescript
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
})

const validation = schema.safeParse(data)
if (!validation.success) {
  console.log(validation.error.errors)
}
```

---

## Database & API

### Q17: "PrismaClientInitializationError"

**Solution:**
1. Check `DATABASE_URL` in `.env.local` is correct
2. Ensure database is running
3. Run migrations:
```bash
npx prisma migrate dev
```

4. Generate Prisma Client:
```bash
npx prisma generate
```

### Q18: API returning 500 error

**Solution:**
1. Check server logs in terminal
2. Add error logging:
```typescript
export async function POST(request: NextRequest) {
  try {
    // Your code
  } catch (error) {
    console.error('Detailed error:', error)
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

### Q19: "NEXT_PUBLIC" variables not available

**Solution:**
Environment variables must start with `NEXT_PUBLIC_` to be accessible on client:

```bash
# ✅ Available in browser
NEXT_PUBLIC_API_URL=https://api.example.com

# ❌ Server-only (not in browser)
API_KEY=secret
```

---

## Testing

### Q20: Tests not running

**Solution:**
```bash
# Install test dependencies
npm install --save-dev jest @testing-library/react

# Generate Jest config
npx jest --init

# Run tests
npm test
```

### Q21: "Unexpected token" in test files

**Solution:**
Add Jest configuration for JSX:

```javascript
// jest.config.js
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
}
```

---

## Git & GitHub

### Q22: Git command returns "not a git repository"

**Solution:**
```bash
# Initialize git if needed
git init

# Or clone the repository
git clone https://github.com/Hela-Nexus/web_development.git
cd web_development
```

### Q23: Commit history is messy

**Solution:**
Use conventional commits:
```bash
git commit -m "feat: add user profile page"
git commit -m "fix: resolve authentication bug"
```

### Q24: Can't push changes

**Solution:**
```bash
# Pull latest changes first
git pull origin main

# Resolve conflicts if any
git add .
git commit -m "Merge main into feature branch"
git push origin feature-branch
```

---

## Performance & Optimization

### Q25: Site is loading slowly

**Solution:**
1. Optimize images:
```typescript
import Image from 'next/image'

<Image src="/img.jpg" alt="desc" width={400} height={300} />
```

2. Enable caching:
```typescript
export const revalidate = 3600 // Revalidate every hour
```

3. Check bundle size:
```bash
npm run build
npm run analyze
```

### Q26: Build takes too long

**Solution:**
1. Enable SWC compiler (default in Next.js 13+)
2. Use dynamic imports for large components
3. Disable source maps in production
4. Check for large dependencies:
```bash
npm ls -all | grep duplicate
```

---

## Deployment

### Q27: Deployment fails on Vercel

**Solution:**
1. Check build logs in Vercel dashboard
2. Ensure environment variables are set
3. Verify build command runs locally:
```bash
npm run build
npm start
```

### Q28: Site works locally but not in production

**Solution:**
1. Check environment variables - use `NEXT_PUBLIC_` prefix for client vars
2. Look for console errors in browser DevTools
3. Check Vercel/deployment logs
4. Test production build locally:
```bash
npm run build
npm start
```

---

## General Development

### Q29: "React Hook X called conditionally"

**Solution:**
Hooks must be called at top level of component:

```typescript
// ✅ Correct
function MyComponent() {
  const [count, setCount] = useState(0)
  return <div>{count}</div>
}

// ❌ Wrong - hook inside condition
function MyComponent() {
  if (someCondition) {
    const [count, setCount] = useState(0) // ❌ Error
  }
}
```

### Q30: Prop drilling issues

**Solution:**
Use Context API:

```typescript
// Create context
const UserContext = createContext()

// Provider component
export function UserProvider({ children }) {
  const [user, setUser] = useState(null)
  return (
    <UserContext.Provider value={{ user, setUser }}>
      {children}
    </UserContext.Provider>
  )
}

// Use in component
function MyComponent() {
  const { user } = useContext(UserContext)
  return <div>{user?.name}</div>
}
```

---

## Getting Help

### Before Asking for Help

1. ✅ Search this FAQ
2. ✅ Check the error message carefully
3. ✅ Google the error
4. ✅ Check official documentation
5. ✅ Try the solution suggested in error message

### How to Ask for Help

When asking your mentor or team:

1. **Describe the problem clearly**
   ```
   ❌ "It doesn't work"
   ✅ "When I click the submit button, I get a 404 error"
   ```

2. **Share relevant code**
   ```
   Share only the problematic section, not the entire file
   ```

3. **Share error message**
   ```
   Copy the exact error from console
   ```

4. **Describe what you've tried**
   ```
   "I already tried restarting the dev server and clearing node_modules"
   ```

---

## Useful Debug Commands

```bash
# Check Node.js version
node --version

# Check npm version
npm --version

# List all installed packages
npm list

# Check for security vulnerabilities
npm audit

# Update all packages
npm update

# Check outdated packages
npm outdated

# Clear npm cache
npm cache clean --force

# Check git status
git status

# View git log
git log --oneline

# See changed files
git diff
```

---

## Resources

- [Next.js Docs](https://nextjs.org/docs)
- [React Docs](https://react.dev)
- [TypeScript Docs](https://www.typescriptlang.org/docs/)
- [MDN Web Docs](https://developer.mozilla.org/)
- [Stack Overflow](https://stackoverflow.com/)
- [GitHub Issues](https://github.com/vercel/next.js/issues)

---

## Still Stuck?

1. Check the other starter documents:
   - [GETTING_STARTED.md](./GETTING_STARTED.md)
   - [NEXTJS_SETUP.md](./NEXTJS_SETUP.md)
   - [BEST_PRACTICES.md](./BEST_PRACTICES.md)
   - [CURRICULUM.md](./CURRICULUM.md)

2. Ask your mentor

3. Post in team chat with:
   - Error message
   - Relevant code
   - Steps to reproduce
   - What you've tried

Happy debugging! 🐛

---

**Created for:** Hela-Nexus Web Development Internship  
**Last updated:** 2026-06-06
