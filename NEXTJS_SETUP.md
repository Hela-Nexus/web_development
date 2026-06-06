# Next.js Setup Guide - Detailed Instructions

This document provides step-by-step instructions for setting up a Next.js project from scratch.

## Table of Contents

1. [What is Next.js?](#what-is-nextjs)
2. [Installation](#installation)
3. [Project Structure](#project-structure)
4. [Configuration](#configuration)
5. [First Application](#first-application)
6. [Development Workflow](#development-workflow)

## What is Next.js?

Next.js is a React framework that enables you to build full-stack web applications with:
- **Server-side rendering (SSR)**
- **Static site generation (SSG)**
- **API routes**
- **Automatic code splitting**
- **Built-in optimization**

## Installation

### Quick Start with Create Next App

```bash
# Create a new Next.js project
npx create-next-app@latest my-project

# When prompted, answer:
# ✔ Would you like to use TypeScript? › Yes
# ✔ Would you like to use ESLint? › Yes
# ✔ Would you like to use Tailwind CSS? › Yes
# ✔ Would you like to use `src/` directory? › Yes
# ✔ Would you like to use App Router? › Yes

cd my-project
npm run dev
```

### Manual Setup

If you prefer to set up Next.js manually:

```bash
mkdir my-project
cd my-project
npm init -y
npm install next react react-dom
```

Update `package.json` scripts:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

Create `next.config.js`:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
}

module.exports = nextConfig
```

## Project Structure

### App Router Structure (Recommended)

```
my-project/
├── app/
│   ├── layout.tsx          # Root layout
│   ├── page.tsx            # Home page
│   ├── api/
│   │   └── hello/
│   │       └── route.ts    # API route
│   └── dashboard/
│       └── page.tsx        # Nested route
├── public/                 # Static files
│   ├── favicon.ico
│   └── images/
├── components/             # Reusable React components
│   ├── Header.tsx
│   ├── Navigation.tsx
│   └── Footer.tsx
├── lib/                    # Utility functions
│   ├── utils.ts
│   └── constants.ts
├── styles/                 # Global styles
│   └── globals.css
├── package.json
├── next.config.js
├── tsconfig.json
├── .env.local             # Environment variables (local)
└── .gitignore
```

### Pages Router Structure (Legacy)

```
my-project/
├── pages/
│   ├── _app.tsx           # Custom App wrapper
│   ├── _document.tsx      # Custom Document
│   ├── index.tsx          # Home page
│   ├── about.tsx          # /about route
│   └── api/
│       └── hello.ts       # /api/hello endpoint
├── public/
├── styles/
├── components/
└── package.json
```

## Configuration

### TypeScript Setup

Create `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "resolveJsonModule": true,
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./components/*"],
      "@lib/*": ["./lib/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

### Environment Variables

Create `.env.local`:

```env
# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:3000

# Database
DATABASE_URL=******localhost:5432/mydb

# Authentication
NEXTAUTH_SECRET=your-secret-key
NEXTAUTH_URL=http://localhost:3000
```

### ESLint Configuration

```bash
npx next lint
```

This creates `.eslintrc.json` automatically.

## First Application

### 1. Create a Simple Page

`app/page.tsx`:

```typescript
export default function Home() {
  return (
    <main>
      <h1>Welcome to Next.js!</h1>
      <p>Your web development internship starts here.</p>
    </main>
  )
}
```

### 2. Create a Component

`components/Button.tsx`:

```typescript
interface ButtonProps {
  text: string;
  onClick?: () => void;
}

export default function Button({ text, onClick }: ButtonProps) {
  return (
    <button 
      onClick={onClick}
      className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
    >
      {text}
    </button>
  )
}
```

### 3. Create an API Route

`app/api/hello/route.ts`:

```typescript
import { NextRequest, NextResponse } from 'next/server'

export async function GET(request: NextRequest) {
  return NextResponse.json({ message: 'Hello from Next.js!' })
}

export async function POST(request: NextRequest) {
  const data = await request.json()
  return NextResponse.json({ received: data })
}
```

### 4. Use Fetching

`app/dashboard/page.tsx`:

```typescript
async function getData() {
  const res = await fetch('http://localhost:3000/api/hello', {
    cache: 'no-store'
  })
  
  if (!res.ok) throw new Error('Failed to fetch data')
  return res.json()
}

export default async function Dashboard() {
  const data = await getData()
  
  return (
    <div>
      <h1>Dashboard</h1>
      <p>{data.message}</p>
    </div>
  )
}
```

## Development Workflow

### 1. Run Development Server

```bash
npm run dev
```

The server will start at `http://localhost:3000` with hot module replacement enabled.

### 2. Build for Production

```bash
npm run build
npm start
```

### 3. Linting and Formatting

```bash
# Check for issues
npm run lint

# Format code with Prettier (if configured)
npm run format
```

### 4. Debugging

#### Using VS Code

Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Next.js",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/node_modules/.bin/next",
      "args": ["dev"],
      "cwd": "${workspaceFolder}",
      "runtimeArgs": ["--inspect"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen"
    }
  ]
}
```

## Common Tasks

### Adding Dependencies

```bash
npm install package-name
npm install --save-dev dev-package-name
```

### Creating Dynamic Routes

`app/blog/[slug]/page.tsx`:

```typescript
interface Params {
  slug: string
}

export default function BlogPost({ params }: { params: Params }) {
  return <h1>Blog Post: {params.slug}</h1>
}
```

### Middleware

`middleware.ts`:

```typescript
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // Add custom logic here
  return NextResponse.next()
}

export const config = {
  matcher: ['/admin/:path*'],
}
```

## Deployment

### Vercel (Recommended)

```bash
npm install -g vercel
vercel
```

### Other Platforms

- **Netlify**: Connect your GitHub repository
- **AWS**: Use EC2, Lambda, or Amplify
- **DigitalOcean**: Deploy with Docker
- **Docker**: Create a Dockerfile for containerization

## Next Steps

1. Complete the [CURRICULUM.md](./CURRICULUM.md) exercises
2. Review [BEST_PRACTICES.md](./BEST_PRACTICES.md)
3. Start building your first project!

---

For more information, visit the [official Next.js documentation](https://nextjs.org/docs).
