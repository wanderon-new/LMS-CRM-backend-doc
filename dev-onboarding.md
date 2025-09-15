# Developer Onboarding Guide

## 🚀 Quick Start (5 minutes)

### Prerequisites
- **Node.js 20+** or **Nix** (recommended)
- **Docker & Docker Compose**
- **Git**
- **VS Code** (recommended IDE)

### Option 1: Using Nix (Recommended)
```bash
# Clone the repository
git clone <repository-url>
cd lead-processor

# Enter Nix development shell (auto-installs all dependencies)
nix develop .#default 

# Start local services
docker-compose up -d

# Install dependencies
npm install

# Start development server
npm run dev
```

### Option 2: Traditional Setup
```bash
# Install Node.js 20+ from nodejs.org
# Clone and enter project
git clone <repository-url>
cd lead-processor

# Start local services
docker-compose up -d

# Install dependencies
npm install

# Start development server
npm run dev
```

## 🏗️ Project Structure

```
lead-processor/
├── server/                          # Backend source code
│   ├── index.ts                    # Main application entry point
│   ├── adapters/                   # Express request/response adapters
│   ├── background-scripts/         # Cron jobs and scheduled tasks
│   ├── common/                     # Shared utilities and helpers
│   ├── config/                     # Database and service configurations
│   ├── controllers/                # HTTP request handlers
│   ├── entities/                   # Data models and repositories
│   ├── interfaces/                 # TypeScript interfaces
│   ├── middlewares/                # Express middleware
│   ├── routes/                     # API route definitions
│   ├── services/                   # COntains external infra services and Business in useCases folder
|   |   └──useCases/                # usecase core business logic that plays with entities
│   └── validators/                 # Request validation schemas
├── __tests__/                      # Backend test files
├── coverage/                       # Test coverage reports
├── scripts/                        # Utility scripts
├── docs/                          # Technical documentation
├── package.json                   # Backend dependencies
├── tsconfig.json                  # TypeScript configuration
├── jest.config.js                 # Test configuration
├── docker-compose.yml             # Local development services
└── flake.nix                      # Nix development environment

lead-processor-frontend/
├── app/                           # Next.js app directory
│   ├── (routes)/                  # Page components
│   ├── _components/               # Reusable components
│   ├── _context/                  # React contexts
│   ├── _redux/                    # Redux store and slices
│   ├── _styles/                   # Global styles
│   ├── _theme/                    # Material-UI theme
│   ├── _types/                    # TypeScript type definitions
│   ├── _utils/                    # Frontend utilities
│   ├── api/                       # API route handlers
│   ├── lib/                       # Shared libraries
│   └── layout.tsx                 # Root layout component
├── public/                        # Static assets
├── package.json                   # Frontend dependencies
├── next.config.mjs                # Next.js configuration
├── tsconfig.json                  # TypeScript configuration
└── biome.json                     # Code formatting configuration
```

## 🔧 Development Environment Setup

### Environment Variables

Create `.env.development` in the backend root:
```bash
# Database
MONGO_URI=mongodb://localhost:27017
MONGO_DB_NAME=leadProcessor-dev

# Redis
REDIS_URI=redis://localhost:6379

# Authentication
JWT_SECRET=your-super-secret-jwt-key
COOKIE_SECRET=your-cookie-secret

# Feature Flags
START_CDC=true
RUN_QUEUE_CONSUMERS=true
NODE_ENV=development

# External APIs (optional for development)
LSQ_ACCESS_KEY=your-leadsquared-access-key
LSQ_SECRET_KEY=your-leadsquared-secret-key

# Webhook URLs (optional)
PSV_JUNK_DOUBLE_TICK_WEBHOOK=
WEST_DOUBLE_TICK_ASSIGNMENT_WEBHOOK=
```

Create `.env.local` in the frontend root:
```bash
# Backend API URL
NEXT_PUBLIC_API_URL=http://localhost:3000

# Other frontend-specific variables
NEXT_PUBLIC_APP_NAME=Lead Processor
```

### Local Services
```bash
# Start MongoDB and Redis
docker-compose up -d

# Verify services are running
docker-compose ps

# View logs
docker-compose logs -f mongodb
docker-compose logs -f redis
```

### Database Setup
```bash
# The application will create collections automatically
# Optional: Seed initial data (if seed scripts exist)
npm run seed  # (if available)
```

## 🏃‍♂️ Running the Application

### Backend Development
```bash
# Development with hot reload
npm run dev

# Development with OpenTelemetry tracing
npm run dev:optl

# Build for production
npm run build

# Run tests
npm test
npm run test:watch
npm run test:coverage
```

### Frontend Development
```bash
cd lead-processor-frontend

# Install dependencies
npm install

# Development server
npm run dev

# Build for production
npm run build

# Lint code
npm run lint
npm run format
```

### Full Stack Development
```bash
# Terminal 1: Backend
cd lead-processor
npm run dev

# Terminal 2: Frontend
cd lead-processor-frontend
npm run dev

# Access:
# - Frontend: http://localhost:3000 (Next.js)
# - Backend API: http://localhost:3000/api (Express)
# - MongoDB: localhost:27017
# - Redis: localhost:6379
```

## 🧪 Testing

### Backend Testing
```bash
# Run all tests
npm test

# Watch mode for development
npm run test:watch

# Coverage report
npm run test:coverage

# Test specific file
npm test -- opportunityScoreConfiguration.test.ts

# Universal test runner (works with Nix)
npm run test:universal
```

### Frontend Testing
```bash
cd lead-processor-frontend

# Run linting
npm run lint

# Format code
npm run format

# Type checking
npx tsc --noEmit
```

### Database Testing
Tests use an in-memory MongoDB instance. No setup required.

## 🐛 Debugging

### VS Code Setup
Install recommended extensions:
- TypeScript and JavaScript Language Features
- ESLint
- Prettier
- Jest Runner
- Docker

### Debug Configuration
`.vscode/launch.json`:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Backend",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/server/index.ts",
      "env": {
        "NODE_ENV": "development"
      },
      "runtimeArgs": ["-r", "ts-node/register"],
      "envFile": "${workspaceFolder}/.env.development"
    }
  ]
}
```

### Performance Profiling
```bash
# Chrome DevTools profiling
npm run profile:chrome

# Clinic.js profiling
npm run profile:doctor
npm run profile:flame
npm run profile:bubbleprof
```

## 📊 Monitoring Development

### Health Checks
```bash
# Backend health
curl http://localhost:3000/health

# Check logs
npm run dev | pino-pretty

# MongoDB status
docker exec mongodb_container mongosh --eval "db.runCommand('ping')"

# Redis status
docker exec redis_container redis-cli ping
```

### Log Analysis
The application uses structured logging with Pino:
```bash
# Pretty print logs in development
npm run dev | npx pino-pretty

# Filter logs by level
npm run dev | npx pino-pretty --level warn
```

## 🛠️ Common Development Tasks

### Adding a New API Endpoint
1. **Define route** in `server/routes/`
2. **Create controller** in `server/controllers/`
3. **Add use case** in `server/services/useCases/`
4. **Add validation** in `server/validators/` or in `server/controllers/<controller name>/validator.ts`
5. **Write tests** in `__tests__/`

### Adding a New Entity
1. **Create interface** in `server/entities/<entity>/<entity>.interface.ts`
2. **Create model** in `server/entities/<entity>/<entity>.modal.ts`
3. **Create repository** in `server/entities/<entity>/<entity>.repo.ts`
4. **Add to main exports**

### Frontend Component Development
1. **Create component** in `app/_components/`
2. **Add types** in `app/_types/`
3. **Add to theme** if needed in `app/_theme/`
4. **Create page** in `app/(routes)/`

## 🔧 Troubleshooting

### Common Issues

**Port already in use:**
```bash
# Find and kill process using port 3000
lsof -ti:3000 | xargs kill -9
```

**Docker services won't start:**
```bash
# Reset Docker compose
docker-compose down
docker-compose up -d

# Check Docker disk space
docker system df
docker system prune
```

**Node modules issues:**
```bash
# Clear and reinstall
rm -rf node_modules package-lock.json
npm install
```

**MongoDB connection issues:**
```bash
# Check MongoDB logs
docker-compose logs mongodb

# Reset MongoDB data
docker-compose down
docker volume rm lead-processor_mongodb_data
docker-compose up -d
```

**Redis connection issues:**
```bash
# Check Redis logs
docker-compose logs redis

# Test Redis connection
docker exec redis_container redis-cli ping
```

### Getting Help
1. **Check logs** - Always start with application logs
2. **Review documentation** - Check relevant docs sections
3. **Check tests** - Tests often show usage examples
4. **Ask team** - Use appropriate communication channels

## 📚 Next Steps
1. **Explore codebase** - Start with `server/index.ts` and `app/layout.tsx`
2. **Read architecture docs** - Review `docs/architecture-overview.md`
3. **Understand data flow** - Study entity relationships and API patterns
4. **Run tests** - Get familiar with test patterns
5. **Make a small change** - Try adding a simple endpoint or UI component

## 🔗 Useful Links
- [Architecture Overview](./architecture-overview.md)
- [API Documentation](./backend/api-documentation.md)
- [Database Schema](./backend/database-schema.md)
- [Tech Stack Details](./tech-stack.md)
