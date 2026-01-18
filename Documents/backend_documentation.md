# Syntheza API

A modern RESTful API built with TypeScript, Node.js, Express, and Prisma ORM. Features JWT authentication, role-based access control, Docker support for both development and production environments, and comprehensive API documentation with Swagger.

## 📋 Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Database Schema](#-database-schema)
- [API Endpoints](#-api-endpoints)
- [Getting Started](#-getting-started)
- [Environment Variables](#-environment-variables)
- [Development](#-development)
- [Production Deployment](#-production-deployment)
- [Testing](#-testing)
- [API Documentation](#-api-documentation)
- [Useful Commands](#-useful-commands)
- [Troubleshooting](#-troubleshooting)

## ✨ Features

- **RESTful API** - Clean, well-documented API design
- **Type-Safe** - Built with TypeScript for type safety
- **Authentication** - JWT-based authentication with Bearer tokens and cookie support
- **Authorization** - Role-based access control (USER, MODO, ADMIN)
- **Database** - PostgreSQL with Prisma ORM
- **API Documentation** - Swagger/OpenAPI 3.0 integration
- **Hot Reload** - Development with instant code reload
- **Docker Support** - Containerized development and production environments
- **SSL/HTTPS** - Production-ready with Traefik and Let's Encrypt
- **Security** - Helmet.js, CORS, hashed passwords with bcrypt

## 🛠 Tech Stack

| Component | Technology |
|-----------|-----------|
| **Runtime** | Node.js 20.x |
| **Language** | TypeScript 5.x |
| **Framework** | Express 4.x |
| **ORM** | Prisma 7.x |
| **Database** | PostgreSQL 15 |
| **Authentication** | JWT (jsonwebtoken) |
| **Password Hashing** | bcrypt |
| **API Documentation** | Swagger UI / swagger-jsdoc |
| **Containerization** | Docker & Docker Compose |
| **Reverse Proxy** | Traefik (production) |
| **SSL/TLS** | Let's Encrypt (production) |
| **Testing** | Jasmine + Supertest |
| **Linting** | ESLint |
| **Hot Reload** | Nodemon |

## 🏗 Architecture

The project follows the **Model-View-Controller (MVC)** pattern:

```
backend/
├── src/
│   ├── controllers/      # Request handlers
│   │   ├── adminController.ts
│   │   └── userController.ts
│   ├── routes/          # API route definitions
│   │   ├── adminRoutes.ts
│   │   └── userRoutes.ts
│   ├── services/        # Business logic
│   │   └── userService.ts
│   ├── middlewares/     # Express middlewares
│   │   ├── authMiddleware.ts    # JWT authentication
│   │   ├── errorHandler.ts      # Error handling
│   │   ├── notFound.ts          # 404 handler
│   │   └── requestLogger.ts     # Request logging
│   ├── utils/          # Utility functions
│   │   ├── bcryptHandler.ts     # Password hashing
│   │   ├── jwtHandler.ts        # JWT token handling
│   │   ├── responseHandler.ts   # Response formatting
│   │   ├── HttpStatusCode.ts    # HTTP status codes
│   │   └── swagger.ts           # Swagger config
│   ├── types/          # TypeScript type definitions
│   │   ├── apiTypes.ts
│   │   ├── errorTypes.ts
│   │   ├── userTypes.ts
│   │   └── zod.ts
│   ├── app.ts          # Express app configuration
│   ├── server.ts       # Server entry point
│   └── prisma.ts       # Prisma client
├── prisma/
│   └── schema.prisma   # Database schema
├── spec/               # Test files
├── scripts/
│   └── build.ts        # Build script
├── config.ts           # Environment configuration
├── docker-compose.yml          # Development Docker
├── docker-compose.prod.yml     # Production Docker
├── Dockerfile          # Production Docker image
├── Dockerfile.dev      # Development Docker image
└── supervisord.conf    # Process manager for dev
```

### Request Flow

```
Client Request → Middleware Stack → Route → Controller → Service → Prisma → Database
                ↓
            - CORS
            - Helmet
            - Morgan (logging)
            - Request Logger
            - Auth Middleware (protected routes)
            - Response Handler
```

## 🗄 Database Schema

### User Model

```prisma
model User {
  id            Int       @id @default(autoincrement())
  name          String    @unique
  email         String    @unique
  hashedPassword String
  role          UserRole  @default(USER)
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

enum UserRole {
  USER    // Regular user
  MODO    // Moderator
  ADMIN   # Administrator
}
```

### User Roles

| Role | Description |
|------|-------------|
| `USER` | Default role, can manage own account |
| `MODO` | Moderator, extended permissions |
| `ADMIN` | Administrator, full access including user management |

## 🔌 API Endpoints

### Base URL
- **Development**: `http://localhost:3001`
- **Production**: `https://api.syntheza.ovh`

### Authentication

Most endpoints require authentication via JWT token. Two methods supported:
1. **Bearer Token**: Include in Authorization header: `Authorization: Bearer <token>`
2. **Cookie**: Token stored in `jwt` cookie (automatic on login)

### User Routes (`/api/user`)

| Method | Endpoint | Auth Required | Description |
|--------|----------|---------------|-------------|
| `POST` | `/api/user/signup` | No | Register a new user |
| `POST` | `/api/user/login` | No | Login and receive JWT token |
| `DELETE` | `/api/user/logout` | Yes | Logout (clears cookies) |
| `GET` | `/api/user/email/:email` | Yes | Get user by email |
| `GET` | `/api/user/:id` | Yes | Get user by ID |
| `PUT` | `/api/user/:id` | Yes | Update user by ID |

### Admin Routes (`/api/admin`)

| Method | Endpoint | Auth Required | Role | Description |
|--------|----------|---------------|------|-------------|
| `GET` | `/api/admin/users` | Yes | ADMIN | Get all users |
| `DELETE` | `/api/admin/:id` | Yes | ADMIN | Delete user by ID |

### Response Format

All API responses follow a consistent format:

**Success Response:**
```json
{
  "status": "success",
  "data": { ... }
}
```

**Error Response:**
```json
{
  "status": "error",
  "message": "Error description"
}
```

## 🚀 Getting Started

### Prerequisites

- **Docker** & **Docker Compose**
- **Node.js** 20.x (for local development without Docker)
- **Yarn** or **npm**
- **Git**

### Quick Start (Development with Docker)

#### 1. Clone the Repository

```bash
git clone git@github.com:Syntheza/backend.git
cd backend
```

#### 2. Create Environment Configuration

Create a `.env.local` file in the `config` folder:

```bash
# config/.env.local

NODE_ENV=development
DB_USER=postgres
DB_PASSWORD=postgres
DB_NAME=syntheza_db
DATABASE_URL=postgresql://${DB_USER}:${DB_PASSWORD}@db:5432/${DB_NAME}?schema=public
JWT_SECRET=your_secure_random_secret_key_here
API_URL=http://localhost:3001
PORT=3001
```

**Important:** Replace `your_secure_random_secret_key_here` with a strong, random string.

#### 3. Start Services

```bash
docker-compose --env-file config/.env.local up -d
```

This will:
- Start PostgreSQL database
- Build and start the API container
- Initialize the database
- Enable hot reload for development
- Expose API on port `3001`
- Expose Prisma Studio on port `5555`

#### 4. Access Services

- **API**: [http://localhost:3001](http://localhost:3001)
- **API Documentation**: [http://localhost:3001/api-docs/](http://localhost:3001/api-docs/)
- **Prisma Studio**: [http://localhost:5555](http://localhost:5555)

## ⚙️ Environment Variables

| Variable | Description | Required | Default | Notes |
|----------|-------------|----------|---------|-------|
| `NODE_ENV` | Environment mode | Yes | `development` | `development` or `production` |
| `DATABASE_URL` | PostgreSQL connection string | Yes | - | Format: `postgresql://user:pass@host:port/db?schema=public` |
| `JWT_SECRET` | Secret key for JWT signing | Yes | - | Must be a strong, random string |
| `API_URL` | Base API URL | Yes | `http://localhost:3001` | Used in Swagger documentation |
| `PORT` | Server port | Yes | `3000` | Development: `3001`, Production: `3002` |
| `DB_USER` | Database username | Dev only | `postgres` | Docker Compose only |
| `DB_PASSWORD` | Database password | Dev only | `postgres` | Docker Compose only |
| `DB_NAME` | Database name | Dev only | `syntheza_db` | Docker Compose only |

### Production Environment Variables

For production, set these in your CI/CD pipeline or hosting environment:

```bash
NODE_ENV=production
DATABASE_URL=postgresql://prod_user:prod_pass@prod_host:5432/prod_db?schema=public
JWT_SECRET=your_production_secret_key
API_URL=https://api.syntheza.ovh
PORT=3002
```

## 💻 Development

### Local Development (without Docker)

#### Install Dependencies

```bash
yarn install
```

#### Set Up Environment

```bash
cp config/.env.example config/.env.local
# Edit config/.env.local with your settings
```

#### Generate Prisma Client

```bash
yarn prisma generate
```

#### Run Migrations

```bash
yarn prisma migrate dev
```

#### Start Development Server

```bash
yarn dev:hot  # Hot reload with nodemon
# OR
yarn dev       # Regular start with ts-node
```

### Development with Docker (Recommended)

#### Hot Reload

The development Docker setup includes:
- **Volume mounts**: `.` → `/app` (source code)
- **Nodemon**: Automatically restarts on file changes
- **Supervisor**: Keeps the process running

Any changes to `src/` files trigger an automatic restart.

#### View Logs

```bash
# Follow API logs
docker-compose logs -f api

# View all logs
docker-compose logs -f
```

#### Restart Services

```bash
docker-compose restart api
```

#### Stop Services

```bash
docker-compose down
```

#### Reset Database

```bash
docker-compose down -v  # Remove volumes
docker-compose --env-file config/.env.local up -d
```

### Database Management

#### Prisma Studio (GUI)

```bash
yarn prisma studio
# Or via Docker: http://localhost:5555
```

#### Create Migration

```bash
yarn prisma migrate dev --name migration_name
```

#### Reset Database (Development)

```bash
yarn prisma migrate reset
```

#### Seed Database

```bash
yarn prisma db seed
```

## 🚢 Production Deployment

### Production Docker Setup

#### 1. Build Production Image

```bash
docker build -t syntheza-api:latest -f Dockerfile .
```

#### 2. Deploy with Docker Compose

Set production environment variables:

```bash
export IMAGE_NAME=syntheza-api:latest
export PROD_PORT=3002
export API_URL=https://api.syntheza.ovh
export JWT_SECRET=your_production_secret
export DATABASE_URL=postgresql://user:pass@host:5432/db?schema=public
export DB_USER=your_db_user
export DB_PASSWORD=your_db_password
export DB_NAME=your_db_name
```

Deploy:

```bash
docker-compose -f docker-compose.prod.yml up -d
```

#### 3. Production Services

- **API**: `https://api.syntheza.ovh`
- **API Docs**: `https://api.syntheza.ovh/api-docs/`

### Infrastructure Stack (Production)

The production setup uses:

- **Traefik**: Reverse proxy with automatic SSL
- **Let's Encrypt**: Automatic HTTPS certificates
- **Docker Swarm/Kubernetes**: Container orchestration (if configured)

#### Traefik Configuration

Traefik handles:
- SSL termination (HTTPS)
- Load balancing
- Automatic certificate renewal
- Routing based on domain name

## 🧪 Testing

### Run Tests

```bash
yarn test          # Run all tests once
yarn test:hot      # Run tests with hot reload
```

### Test with Hot Reload

```bash
yarn test:hot  # Runs tests and restarts on file changes
```

### Test Structure

Tests are located in the `spec/` directory and follow Jasmine framework conventions.

## 📚 API Documentation

### Swagger/OpenAPI Documentation

The API includes comprehensive Swagger documentation available at:

- **Development**: http://localhost:3001/api-docs/
- **Production**: https://api.syntheza.ovh/api-docs/

### Using the API Documentation

1. Visit the Swagger UI URL
2. Explore available endpoints
3. Try endpoints directly from the UI:
   - Login to get a JWT token
   - Authorize by clicking "Authorize" button and entering your token
   - Execute requests and view responses

### Authentication in Swagger

To test authenticated endpoints:

1. Call `POST /api/user/login` with your credentials
2. Copy the returned token
3. Click "Authorize" 🔒 button in Swagger
4. Enter `Bearer <your_token>` in the dialog
5. Click "Authorize"

## 🛠 Useful Commands

### Development

```bash
# Start dev server with hot reload
yarn dev:hot

# Start dev server (no hot reload)
yarn dev

# Run Prisma migrations
yarn prisma migrate dev

# Open Prisma Studio
yarn prisma studio

# Run tests
yarn test

# Run tests with hot reload
yarn test:hot

# Lint code
yarn lint

# Type check
yarn type-check

# Build for production
yarn build
```

### Docker

```bash
# Development
docker-compose --env-file config/.env.local up -d    # Start
docker-compose logs -f api                           # View logs
docker-compose restart api                           # Restart API
docker-compose down                                  # Stop

# Production
docker-compose -f docker-compose.prod.yml up -d     # Deploy
docker-compose -f docker-compose.prod.yml logs -f   # View logs
docker-compose -f docker-compose.prod.yml down      # Stop

# Clean everything
docker-compose down -v  # Remove volumes
```

### Database

```bash
# Generate Prisma client
yarn prisma generate

# Create migration
yarn prisma migrate dev --name migration_name

# Apply migrations in production
yarn prisma migrate deploy

# Reset database (dev only)
yarn prisma migrate reset

# Open database GUI
yarn prisma studio

# View database status
yarn prisma db pull
```

## 🔧 Troubleshooting

### Hot Reload Not Working

**Problem**: Changes not reflected in running container

**Solution**:
1. Check docker-compose.yml has volume mounts:
   ```yaml
   volumes:
     - .:/app
     - /app/node_modules
   ```
2. Restart container: `docker-compose restart api`
3. Check nodemon is running: `docker-compose logs api`

### Database Connection Failed

**Problem**: Cannot connect to PostgreSQL

**Solutions**:
1. Check database is running: `docker-compose ps`
2. Check logs: `docker-compose logs db`
3. Verify DATABASE_URL in `.env.local`
4. Restart services: `docker-compose down && docker-compose up -d`

### Swagger Shows "No Operations Defined"

**Problem**: API documentation empty in production

**Solution**:
Ensure `tsconfig.prod.json` has:
```json
"removeComments": false
```

Then rebuild and redeploy.

### JWT Authentication Failed

**Problem**: Getting "Unauthorized" errors

**Solutions**:
1. Check JWT_SECRET is set in environment
2. Verify token format: `Bearer <token>`
3. Check token hasn't expired
4. Verify token payload includes valid user ID

### Prisma Generation Failed

**Problem**: `npx prisma generate` fails

**Solutions**:
1. Install dependencies: `yarn install`
2. Check DATABASE_URL is set
3. Verify schema.prisma is valid
4. Clear Prisma cache: `rm -rf node_modules/.prisma`

### Build Failed in Production

**Problem**: Docker build fails

**Solutions**:
1. Check Dockerfile.prod syntax
2. Verify all dependencies in package.json
3. Check TypeScript compilation errors
4. Check tsconfig.prod.json settings
5. Build locally: `yarn build` to see errors

### Container Won't Start

**Problem**: API container exits immediately

**Solutions**:
1. Check logs: `docker-compose logs api`
2. Verify environment variables are loaded
3. Check port conflicts: `lsof -i :3001`
4. Verify database is healthy: `docker-compose ps db`
5. Check supervisord.conf if using dev image

### Cannot Access API on Port

**Problem**: `localhost:3001` not accessible

**Solutions**:
1. Check container is running: `docker-compose ps`
2. Check port mapping in docker-compose.yml
3. Check firewall settings
4. Verify API is listening: `docker-compose exec api netstat -tlnp`

---

**Support**: For issues or questions, please open an issue on GitHub.

**License**: See LICENSE file in the repository.
