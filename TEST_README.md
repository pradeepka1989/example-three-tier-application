# Testing Guide

This document provides guidance on testing the example-three-tier-application.

## Overview

The application consists of three tiers that need to be tested:
- **Frontend** (Next.js) — UI and client-side logic
- **API** (Express) — REST endpoints and business logic
- **Database** (PostgreSQL) — schema and migrations

## Running Tests Locally

### Prerequisites

- Docker Desktop or Docker Engine with Compose plugin
- Node.js 22+ (for running tests outside Docker)
- npm or yarn

### Start the test environment

```bash
docker compose up --build
```

This starts all services including the database with migrations applied.

### Frontend Tests

The Next.js frontend can be tested with:

```bash
cd src/web
npm install
npm run test
```

### API Tests

The Express API can be tested with:

```bash
cd src/api
npm install
npm run test
```

### Database Tests

Database migrations can be validated by checking the schema:

```bash
cd src/db
npm install
DATABASE_URL=postgres://app:app@localhost:5432/app npx node-pg-migrate list
```

## Manual Testing

### Test the application flow

1. Open [http://localhost:3000](http://localhost:3000) in your browser
2. Create a new task by entering a title and clicking "Add"
3. Mark tasks as complete by clicking the checkbox
4. Verify tasks persist after page refresh

### Test API endpoints directly

```bash
# Health check
curl http://localhost:3001/health

# List tasks
curl http://localhost:3001/tasks

# Create a task
curl -X POST http://localhost:3001/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Test task"}'

# Update a task
curl -X PATCH http://localhost:3001/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'
```

## Continuous Integration

Tests should be run as part of the CI/CD pipeline before deployment:

1. Lint code (ESLint, Prettier)
2. Type check (TypeScript)
3. Run unit tests
4. Run integration tests
5. Build Docker images
6. Deploy to staging environment

## Troubleshooting

### Database connection issues

If the API cannot connect to the database:

```bash
# Check database logs
docker compose logs postgres

# Verify the DATABASE_URL is correct
echo $DATABASE_URL
```

### Port conflicts

If ports 3000 or 3001 are already in use:

```bash
# Find and stop the process using the port
lsof -i :3000
kill -9 <PID>

# Or use docker compose with custom ports
docker compose up -p custom_port_prefix
```

### Stale data

To reset the database and start fresh:

```bash
docker compose down -v
docker compose up --build
```

## Best Practices

- Write tests for new features before implementation (TDD)
- Keep tests focused and independent
- Use descriptive test names that explain the expected behavior
- Mock external dependencies
- Test both happy paths and error cases
- Maintain >80% code coverage for critical paths
- Run tests locally before pushing to GitHub

## Resources

- [Next.js Testing](https://nextjs.org/docs/testing)
- [Express Testing](https://expressjs.com/en/guide/testing.html)
- [node-pg-migrate Documentation](https://salsita.github.io/node-pg-migrate/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
