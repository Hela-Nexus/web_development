# Getting Started - Web Development Internship Program

Welcome to the Web Development Internship Program! This guide will help you set up your development environment and get started with Next.js.

## Prerequisites

Before you begin, ensure you have the following installed:
- **Node.js** (v16 or higher) - [Download](https://nodejs.org/)
- **npm** or **yarn** - Comes with Node.js
- **Git** - [Download](https://git-scm.com/)
- **Code Editor** - VSCode recommended - [Download](https://code.visualstudio.com/)

## Step 1: Environment Setup

### 1.1 Verify Installation
```bash
node --version
npm --version
git --version
```

### 1.2 Configure Git
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## Step 2: Clone the Repository

```bash
git clone https://github.com/Hela-Nexus/web_development.git
cd web_development
```

## Step 3: Install Dependencies

```bash
npm install
# or
yarn install
```

## Step 4: Create Your First Next.js Project

### Option A: Using Create Next App (Recommended)
```bash
npx create-next-app@latest my-app --typescript --eslint
cd my-app
npm run dev
```

### Option B: Manual Setup
See [NEXTJS_SETUP.md](./NEXTJS_SETUP.md) for detailed manual setup instructions.

## Step 5: Verify Your Setup

Open your browser and navigate to:
```
http://localhost:3000
```

You should see the Next.js welcome page.

## Project Structure

```
web_development/
├── README.md
├── GETTING_STARTED.md
├── NEXTJS_SETUP.md
├── CURRICULUM.md
├── BEST_PRACTICES.md
└── my-app/                 # Your Next.js project
    ├── app/
    ├── public/
    ├── package.json
    └── next.config.js
```

## Next Steps

1. Read [CURRICULUM.md](./CURRICULUM.md) for the internship learning path
2. Review [BEST_PRACTICES.md](./BEST_PRACTICES.md) for coding standards
3. Check [NEXTJS_SETUP.md](./NEXTJS_SETUP.md) for detailed Next.js documentation

## Common Commands

```bash
# Start development server
npm run dev

# Build for production
npm run build

# Start production server
npm start

# Run linter
npm run lint

# Format code
npm run format
```

## Troubleshooting

### Port 3000 Already in Use
```bash
npm run dev -- -p 3001
```

### Node Modules Issues
```bash
rm -rf node_modules
rm package-lock.json
npm install
```

### Git Issues
```bash
# Pull latest changes
git pull origin main

# Check status
git status
```

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [React Documentation](https://react.dev)
- [MDN Web Docs](https://developer.mozilla.org/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

## Need Help?

- Check the [FAQ.md](./FAQ.md)
- Review the [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)
- Ask your mentor or team lead

## Program Timeline

See [CURRICULUM.md](./CURRICULUM.md) for the complete program timeline and milestones.

---

Happy coding! 🚀
