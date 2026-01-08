---
name: project-setup
description: Comprehensive templates and guidelines for initializing new projects with proper structure, configuration, and best practices from the start.
---

# Project Setup Guide

## Overview

This skill provides templates and best practices for setting up new projects across different languages and frameworks. Follow these guidelines to ensure consistent project structure and configuration from day one.

## When to Use

- When starting a new project or repository
- When setting up microservices
- When creating libraries or packages
- When establishing team standards

## General Project Structure

### Universal Components

Every project should have:

```
project-name/
├── .github/              # GitHub-specific files
│   ├── workflows/        # CI/CD workflows
│   ├── ISSUE_TEMPLATE/   # Issue templates
│   └── PULL_REQUEST_TEMPLATE.md
├── docs/                 # Documentation
├── src/                  # Source code
├── tests/                # Tests
├── .gitignore           # Git ignore rules
├── .env.example         # Environment variables template
├── README.md            # Project documentation
├── LICENSE              # License file
├── CONTRIBUTING.md      # Contribution guidelines
└── CHANGELOG.md         # Version history
```

## Language-Specific Setups

### Node.js / TypeScript

```bash
# Initialize project
npm init -y

# Install TypeScript
npm install -D typescript @types/node

# Initialize TypeScript
npx tsc --init

# Install dev dependencies
npm install -D \
  eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin \
  prettier eslint-config-prettier \
  jest @types/jest ts-jest \
  nodemon ts-node
```

**Directory structure:**
```
nodejs-project/
├── src/
│   ├── index.ts
│   ├── config/
│   ├── controllers/
│   ├── models/
│   ├── services/
│   ├── utils/
│   └── types/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── dist/                 # Build output
├── .env.example
├── .eslintrc.json
├── .prettierrc
├── jest.config.js
├── tsconfig.json
├── package.json
└── README.md
```

**package.json scripts:**
```json
{
  "scripts": {
    "dev": "nodemon src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "lint": "eslint src --ext .ts",
    "lint:fix": "eslint src --ext .ts --fix",
    "format": "prettier --write \"src/**/*.ts\"",
    "type-check": "tsc --noEmit"
  }
}
```

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "tests"]
}
```

### Python

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install package manager
pip install poetry  # or use pip + requirements.txt

# Initialize project with poetry
poetry init

# Install dev dependencies
poetry add --group dev \
  pytest pytest-cov \
  black flake8 mypy \
  pylint bandit
```

**Directory structure:**
```
python-project/
├── src/
│   └── project_name/
│       ├── __init__.py
│       ├── main.py
│       ├── config.py
│       ├── models/
│       ├── services/
│       └── utils/
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   ├── unit/
│   └── integration/
├── docs/
├── .env.example
├── .gitignore
├── pyproject.toml       # Poetry config
├── setup.py             # Package setup
├── requirements.txt     # Or use poetry
├── requirements-dev.txt
├── pytest.ini
├── .flake8
├── mypy.ini
└── README.md
```

**pyproject.toml:**
```toml
[tool.poetry]
name = "project-name"
version = "0.1.0"
description = "Project description"
authors = ["Your Name <you@example.com>"]

[tool.poetry.dependencies]
python = "^3.9"

[tool.poetry.group.dev.dependencies]
pytest = "^7.0"
pytest-cov = "^4.0"
black = "^23.0"
flake8 = "^6.0"
mypy = "^1.0"

[tool.black]
line-length = 88
target-version = ['py39']

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]

[tool.mypy]
python_version = "3.9"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
```

### Java / Spring Boot

```bash
# Using Spring Initializr
curl https://start.spring.io/starter.zip \
  -d dependencies=web,data-jpa,postgresql,lombok \
  -d type=maven-project \
  -d language=java \
  -d bootVersion=3.2.0 \
  -d groupId=com.example \
  -d artifactId=myapp \
  -d name=myapp \
  -d packageName=com.example.myapp \
  -d javaVersion=17 \
  -o myapp.zip

unzip myapp.zip
```

**Directory structure:**
```
java-project/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/myapp/
│   │   │       ├── MyAppApplication.java
│   │   │       ├── config/
│   │   │       ├── controller/
│   │   │       ├── service/
│   │   │       ├── repository/
│   │   │       ├── model/
│   │   │       └── dto/
│   │   └── resources/
│   │       ├── application.yml
│   │       └── application-dev.yml
│   └── test/
│       └── java/
│           └── com/example/myapp/
├── .gitignore
├── pom.xml              # Maven
└── README.md
```

**pom.xml (excerpt):**
```xml
<properties>
    <java.version>17</java.version>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
</properties>

<dependencies>
    <!-- Spring Boot -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Testing -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### React / Next.js

```bash
# Create Next.js app
npx create-next-app@latest my-app --typescript --tailwind --app

# Or React with Vite
npm create vite@latest my-app -- --template react-ts

cd my-app
npm install

# Install common dependencies
npm install \
  axios \
  zustand \
  react-query

# Dev dependencies
npm install -D \
  @testing-library/react \
  @testing-library/jest-dom \
  vitest \
  eslint-plugin-react-hooks
```

**Directory structure:**
```
react-project/
├── src/
│   ├── app/              # Next.js app router
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── api/
│   ├── components/
│   │   ├── ui/           # Reusable UI components
│   │   ├── forms/
│   │   └── layouts/
│   ├── hooks/            # Custom hooks
│   ├── lib/              # Utilities
│   ├── services/         # API services
│   ├── store/            # State management
│   ├── types/            # TypeScript types
│   └── styles/
├── public/               # Static assets
├── tests/
├── .env.local.example
├── next.config.js
├── tailwind.config.js
├── tsconfig.json
└── README.md
```

## Configuration Files

### .gitignore

```gitignore
# Dependencies
node_modules/
venv/
__pycache__/
*.pyc

# Build outputs
dist/
build/
*.jar
*.war
target/

# Environment variables
.env
.env.local
.env.*.local

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Testing
coverage/
.nyc_output/

# Temporary files
*.tmp
.cache/
```

### .env.example

```bash
# Application
NODE_ENV=development
PORT=3000
APP_URL=http://localhost:3000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
DB_HOST=localhost
DB_PORT=5432
DB_NAME=myapp
DB_USER=user
DB_PASSWORD=password

# Redis
REDIS_URL=redis://localhost:6379

# Authentication
JWT_SECRET=your-secret-key-here
JWT_EXPIRES_IN=7d

# External Services
API_KEY=your-api-key
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_REGION=us-east-1

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=
SMTP_PASSWORD=

# Monitoring
SENTRY_DSN=
```

### README.md Template

```markdown
# Project Name

Brief description of what this project does.

## Features

- Feature 1
- Feature 2
- Feature 3

## Prerequisites

- Node.js 18+
- PostgreSQL 14+
- Redis (optional)

## Installation

```bash
# Clone the repository
git clone https://github.com/username/project.git
cd project

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env
# Edit .env with your values

# Run database migrations
npm run migrate

# Start development server
npm run dev
```

## Usage

```javascript
// Example usage
import { Something } from 'project';

const result = Something.doThing();
```

## API Documentation

See [API.md](./docs/API.md) for detailed API documentation.

## Testing

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage
```

## Deployment

See [DEPLOYMENT.md](./docs/DEPLOYMENT.md) for deployment instructions.

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for contribution guidelines.

## License

[MIT](./LICENSE)

## Support

- Documentation: https://docs.example.com
- Issues: https://github.com/username/project/issues
```

### CONTRIBUTING.md Template

```markdown
# Contributing to Project Name

Thank you for your interest in contributing!

## Getting Started

1. Fork the repository
2. Clone your fork
3. Create a new branch: `git checkout -b feature/my-feature`
4. Make your changes
5. Write tests
6. Run tests: `npm test`
7. Commit: `git commit -m "Add my feature"`
8. Push: `git push origin feature/my-feature`
9. Create a Pull Request

## Development Guidelines

### Code Style

- Follow ESLint rules
- Use Prettier for formatting
- Write meaningful commit messages

### Testing

- Write tests for new features
- Maintain code coverage above 80%
- Test edge cases

### Pull Requests

- Keep PRs focused and small
- Update documentation
- Add tests
- Follow PR template

## Code of Conduct

Be respectful, inclusive, and collaborative.
```

## CI/CD Setup

### GitHub Actions

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [18.x, 20.x]
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Type check
        run: npm run type-check
      
      - name: Test
        run: npm run test:coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
```

### Pre-commit Hooks

```bash
# Install husky
npm install -D husky lint-staged

# Initialize husky
npx husky install

# Add pre-commit hook
npx husky add .husky/pre-commit "npx lint-staged"
```

**package.json:**
```json
{
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,md,yml}": [
      "prettier --write"
    ]
  }
}
```

## Database Setup

### PostgreSQL with Prisma

```bash
# Install Prisma
npm install -D prisma
npm install @prisma/client

# Initialize Prisma
npx prisma init
```

**schema.prisma:**
```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

```bash
# Create migration
npx prisma migrate dev --name init

# Generate client
npx prisma generate
```

## Docker Setup

**Dockerfile:**
```dockerfile
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY --from=builder /app/dist ./dist

EXPOSE 3000
CMD ["node", "dist/index.js"]
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://user:password@db:5432/myapp
    depends_on:
      - db
      - redis
  
  db:
    image: postgres:14-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
  
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

## Security Setup

### Dependencies

```bash
# Check for vulnerabilities
npm audit

# Fix automatically
npm audit fix

# Install security tools
npm install -D snyk
npx snyk test
```

### Environment Variables

- Never commit `.env` files
- Use secrets management (AWS Secrets Manager, HashiCorp Vault)
- Rotate secrets regularly
- Use different secrets per environment

## Monitoring & Logging

### Logging Setup

```typescript
// src/config/logger.ts
import winston from 'winston';

export const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' })
  ]
});
```

## Best Practices Checklist

- [ ] Version control initialized
- [ ] README with setup instructions
- [ ] .gitignore configured
- [ ] Environment variables template
- [ ] Linting configured
- [ ] Code formatting configured
- [ ] Testing framework set up
- [ ] CI/CD pipeline configured
- [ ] Pre-commit hooks set up
- [ ] Database configured (if needed)
- [ ] Logging configured
- [ ] Error handling implemented
- [ ] Docker setup (optional)
- [ ] Documentation structure created
- [ ] License file added
- [ ] Contributing guidelines added

## Resources

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Python Best Practices](https://docs.python-guide.org/)
- [The Twelve-Factor App](https://12factor.net/)
- [Spring Boot Best Practices](https://spring.io/guides)
- [React Best Practices](https://react.dev/)
