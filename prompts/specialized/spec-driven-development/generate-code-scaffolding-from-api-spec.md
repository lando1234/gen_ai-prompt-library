---
type: specialized
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, scaffolding, code-generation, api, boilerplate]
---

# Generate Code Scaffolding from API Spec

## Purpose
Automatically generate boilerplate code structure from OpenAPI specifications including routes, controllers, models, and validation schemas.

## Context
Use this prompt when:
- Starting implementation from approved API spec
- Need consistent project structure
- Want to avoid manual boilerplate writing
- Coordinating work across multiple developers

---

## Prompt

You are a code generator specializing in API scaffolding. Generate complete project structure and boilerplate code from OpenAPI specifications.

**INPUT**:
- OpenAPI 3.0 specification (YAML/JSON)
- Technology stack (Node.js/Express, Python/FastAPI, Go/Gin, etc.)
- Optional: Project structure preferences

**GENERATE**:

1. **Project Structure**:
   - Organized directory layout
   - Separation of concerns (routes, controllers, models, services)
   - Configuration files
   - Entry point (main/index file)

2. **Routes & Controllers**:
   - Route definitions from API paths
   - Controller methods for each endpoint
   - Parameter extraction (path, query, body)
   - Response formatting

3. **Data Models**:
   - TypeScript interfaces / Python dataclasses from schemas
   - Validation logic
   - Type annotations
   - Documentation comments

4. **Validation Schemas**:
   - Request validation (Zod, Joi, Pydantic)
   - Response validation
   - Error messages

5. **Error Handling**:
   - Custom error classes
   - HTTP status code mapping
   - Standard error response format

6. **Configuration**:
   - Environment variables
   - TypeScript config / Python setup
   - Linting and formatting
   - Package dependencies

**OUTPUT FORMAT**:
```
project-root/
├── src/
│   ├── api/
│   │   ├── routes/
│   │   │   └── [resource].routes.ts
│   │   ├── controllers/
│   │   │   └── [Resource]Controller.ts
│   │   └── middleware/
│   │       ├── errorHandler.ts
│   │       └── validator.ts
│   ├── models/
│   │   └── [Resource].ts
│   ├── schemas/
│   │   └── [resource].schema.ts
│   ├── services/
│   │   └── [Resource]Service.ts
│   ├── types/
│   │   └── api.types.ts
│   ├── config/
│   │   └── index.ts
│   └── app.ts
├── tests/
│   └── [resource].test.ts
├── package.json
├── tsconfig.json
├── .env.example
└── README.md

# For each file, generate:
- Complete code with TODO comments for business logic
- Type definitions
- Import statements
- Export statements
- Documentation comments
```

**CONSTRAINTS**:
- Follow language/framework conventions
- Use strongly typed languages (TypeScript, not JavaScript)
- Include input validation
- Follow RESTful principles
- Generate runnable code (not pseudocode)
- Include helpful TODO comments

---

## Example

**Input**: OpenAPI spec for User Management API

```yaml
openapi: 3.0.3
paths:
  /users:
    get:
      operationId: listUsers
      parameters:
        - name: page
          in: query
          schema:
            type: integer
      responses:
        '200':
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'
    post:
      operationId: createUser
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserCreate'
      responses:
        '201':
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      required: [id, email, name]
      properties:
        id:
          type: string
          format: uuid
        email:
          type: string
          format: email
        name:
          type: string
    UserCreate:
      type: object
      required: [email, name]
      properties:
        email:
          type: string
          format: email
        name:
          type: string
          minLength: 2
    UserList:
      type: object
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/User'
```

**Output**:

```
user-api/
├── src/
│   ├── api/
│   │   ├── routes/
│   │   │   └── user.routes.ts
│   │   ├── controllers/
│   │   │   └── UserController.ts
│   │   └── middleware/
│   │       ├── errorHandler.ts
│   │       └── validator.ts
│   ├── models/
│   │   └── User.ts
│   ├── schemas/
│   │   └── user.schema.ts
│   ├── services/
│   │   └── UserService.ts
│   ├── types/
│   │   └── api.types.ts
│   ├── utils/
│   │   └── errors.ts
│   ├── config/
│   │   └── index.ts
│   └── app.ts
├── tests/
│   └── user.test.ts
├── package.json
├── tsconfig.json
├── .env.example
└── README.md
```

```typescript
// src/models/User.ts
/**
 * User domain model
 * Generated from OpenAPI schema: #/components/schemas/User
 */
export interface User {
  /** Unique user identifier (UUID v4) */
  id: string;

  /** User email address */
  email: string;

  /** User full name */
  name: string;
}

/**
 * Data required to create a new user
 * Generated from OpenAPI schema: #/components/schemas/UserCreate
 */
export interface UserCreate {
  /** User email address (must be unique) */
  email: string;

  /** User full name (min 2 characters) */
  name: string;
}

/**
 * Paginated user list response
 * Generated from OpenAPI schema: #/components/schemas/UserList
 */
export interface UserList {
  data: User[];
}
```

```typescript
// src/schemas/user.schema.ts
import { z } from 'zod';

/**
 * Validation schema for creating a user
 * Generated from OpenAPI requestBody: POST /users
 */
export const createUserSchema = z.object({
  email: z
    .string({ required_error: 'Email is required' })
    .email('Invalid email format'),

  name: z
    .string({ required_error: 'Name is required' })
    .min(2, 'Name must be at least 2 characters')
});

/**
 * Validation schema for listing users (query parameters)
 * Generated from OpenAPI parameters: GET /users
 */
export const listUsersQuerySchema = z.object({
  page: z
    .string()
    .optional()
    .transform(val => (val ? parseInt(val, 10) : 1))
    .refine(val => val > 0, 'Page must be positive integer')
});

// Type inference
export type CreateUserInput = z.infer<typeof createUserSchema>;
export type ListUsersQuery = z.infer<typeof listUsersQuerySchema>;
```

```typescript
// src/services/UserService.ts
import { User, UserCreate } from '../models/User';

/**
 * User service - Business logic layer
 * TODO: Implement actual business logic and database operations
 */
export class UserService {
  /**
   * List all users with pagination
   * TODO: Implement database query with pagination
   * @param page - Page number (1-indexed)
   * @returns Paginated list of users
   */
  async listUsers(page: number = 1): Promise<User[]> {
    // TODO: Implement pagination logic
    // Example: const users = await db.users.findMany({ skip: (page - 1) * 20, take: 20 });
    throw new Error('Not implemented');
  }

  /**
   * Create a new user
   * TODO: Implement user creation with database persistence
   * @param data - User creation data
   * @returns Created user with generated ID
   */
  async createUser(data: UserCreate): Promise<User> {
    // TODO: Generate UUID
    // TODO: Check for duplicate email
    // TODO: Save to database
    // TODO: Return created user
    throw new Error('Not implemented');
  }
}
```

```typescript
// src/api/controllers/UserController.ts
import { Request, Response, NextFunction } from 'express';
import { UserService } from '../../services/UserService';
import { CreateUserInput, ListUsersQuery } from '../../schemas/user.schema';

/**
 * User controller - Handles HTTP requests
 * Generated from OpenAPI paths: /users
 */
export class UserController {
  private userService: UserService;

  constructor() {
    this.userService = new UserService();
  }

  /**
   * GET /users
   * Operation ID: listUsers
   * Returns paginated list of users
   */
  async listUsers(
    req: Request<{}, {}, {}, ListUsersQuery>,
    res: Response,
    next: NextFunction
  ): Promise<void> {
    try {
      const { page } = req.query;

      // Call service layer
      const users = await this.userService.listUsers(page);

      // Return success response (200)
      res.status(200).json({
        data: users
      });
    } catch (error) {
      next(error);
    }
  }

  /**
   * POST /users
   * Operation ID: createUser
   * Creates a new user
   */
  async createUser(
    req: Request<{}, {}, CreateUserInput>,
    res: Response,
    next: NextFunction
  ): Promise<void> {
    try {
      const userData = req.body;

      // Call service layer
      const user = await this.userService.createUser(userData);

      // Return created response (201)
      res.status(201).json(user);
    } catch (error) {
      next(error);
    }
  }
}
```

```typescript
// src/api/routes/user.routes.ts
import { Router } from 'express';
import { UserController } from '../controllers/UserController';
import { validate } from '../middleware/validator';
import { createUserSchema, listUsersQuerySchema } from '../../schemas/user.schema';

/**
 * User routes
 * Generated from OpenAPI paths: /users
 */
const router = Router();
const controller = new UserController();

/**
 * GET /api/v1/users
 * List all users with pagination
 */
router.get(
  '/',
  validate({ query: listUsersQuerySchema }),
  controller.listUsers.bind(controller)
);

/**
 * POST /api/v1/users
 * Create a new user
 */
router.post(
  '/',
  validate({ body: createUserSchema }),
  controller.createUser.bind(controller)
);

export default router;
```

```typescript
// src/api/middleware/validator.ts
import { Request, Response, NextFunction } from 'express';
import { ZodSchema, ZodError } from 'zod';

/**
 * Validation middleware factory
 * Validates request body, query, or params against Zod schema
 */
export function validate(schemas: {
  body?: ZodSchema;
  query?: ZodSchema;
  params?: ZodSchema;
}) {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      if (schemas.body) {
        req.body = await schemas.body.parseAsync(req.body);
      }
      if (schemas.query) {
        req.query = await schemas.query.parseAsync(req.query);
      }
      if (schemas.params) {
        req.params = await schemas.params.parseAsync(req.params);
      }
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        res.status(400).json({
          success: false,
          error: {
            code: 'VALIDATION_ERROR',
            message: 'Validation failed',
            details: error.errors.map(err => ({
              field: err.path.join('.'),
              message: err.message
            }))
          }
        });
      } else {
        next(error);
      }
    }
  };
}
```

```typescript
// src/utils/errors.ts
/**
 * Base application error class
 */
export class AppError extends Error {
  constructor(
    message: string,
    public statusCode: number = 500,
    public code: string = 'INTERNAL_ERROR'
  ) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}

/**
 * 404 Not Found error
 */
export class NotFoundError extends AppError {
  constructor(message: string = 'Resource not found') {
    super(message, 404, 'NOT_FOUND');
  }
}

/**
 * 400 Bad Request error
 */
export class BadRequestError extends AppError {
  constructor(message: string = 'Bad request') {
    super(message, 400, 'BAD_REQUEST');
  }
}
```

```typescript
// src/api/middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../../utils/errors';

/**
 * Global error handling middleware
 * Catches all errors and formats response
 */
export function errorHandler(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
): void {
  console.error('Error:', err);

  if (err instanceof AppError) {
    res.status(err.statusCode).json({
      success: false,
      error: {
        code: err.code,
        message: err.message
      }
    });
  } else {
    res.status(500).json({
      success: false,
      error: {
        code: 'INTERNAL_SERVER_ERROR',
        message: 'An unexpected error occurred'
      }
    });
  }
}
```

```typescript
// src/app.ts
import express, { Application } from 'express';
import userRoutes from './api/routes/user.routes';
import { errorHandler } from './api/middleware/errorHandler';

/**
 * Create and configure Express application
 */
export function createApp(): Application {
  const app = express();

  // Middleware
  app.use(express.json());
  app.use(express.urlencoded({ extended: true }));

  // Routes
  app.use('/api/v1/users', userRoutes);

  // Health check endpoint
  app.get('/health', (req, res) => {
    res.json({ status: 'ok', timestamp: new Date().toISOString() });
  });

  // Error handling (must be last)
  app.use(errorHandler);

  return app;
}

// Start server if running directly
if (require.main === module) {
  const PORT = process.env.PORT || 3000;
  const app = createApp();

  app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
  });
}
```

```json
// package.json
{
  "name": "user-api",
  "version": "1.0.0",
  "description": "User Management API generated from OpenAPI spec",
  "main": "dist/app.js",
  "scripts": {
    "dev": "ts-node-dev --respawn src/app.ts",
    "build": "tsc",
    "start": "node dist/app.js",
    "test": "jest",
    "lint": "eslint src/**/*.ts",
    "format": "prettier --write src/**/*.ts"
  },
  "dependencies": {
    "express": "^4.18.0",
    "zod": "^3.22.0"
  },
  "devDependencies": {
    "@types/express": "^4.17.0",
    "@types/node": "^20.0.0",
    "ts-node-dev": "^2.0.0",
    "typescript": "^5.0.0",
    "jest": "^29.0.0",
    "@types/jest": "^29.0.0",
    "eslint": "^8.50.0",
    "prettier": "^3.0.0"
  }
}
```

```json
// tsconfig.json
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

```bash
# .env.example
# Application
PORT=3000
NODE_ENV=development

# Database (TODO: Configure based on your needs)
DATABASE_URL=postgresql://user:password@localhost:5432/userdb

# TODO: Add other environment variables as needed
```

```markdown
# User Management API

API generated from OpenAPI specification.

## Setup

1. Install dependencies:
```bash
npm install
```

2. Configure environment:
```bash
cp .env.example .env
# Edit .env with your settings
```

3. Run development server:
```bash
npm run dev
```

## Project Structure

- `src/api/` - API layer (routes, controllers, middleware)
- `src/models/` - Data models and types
- `src/schemas/` - Validation schemas (Zod)
- `src/services/` - Business logic layer
- `src/utils/` - Utility functions
- `tests/` - Test files

## API Endpoints

### GET /api/v1/users
List all users with pagination.

**Query Parameters**:
- `page` (optional): Page number (default: 1)

**Response**: 200 OK
```json
{
  "data": [
    {
      "id": "uuid",
      "email": "user@example.com",
      "name": "John Doe"
    }
  ]
}
```

### POST /api/v1/users
Create a new user.

**Request Body**:
```json
{
  "email": "user@example.com",
  "name": "John Doe"
}
```

**Response**: 201 Created
```json
{
  "id": "uuid",
  "email": "user@example.com",
  "name": "John Doe"
}
```

## TODO

- [ ] Implement database layer (choose ORM: Prisma, TypeORM, etc.)
- [ ] Add authentication middleware
- [ ] Implement pagination logic
- [ ] Add comprehensive error handling
- [ ] Write tests
- [ ] Add logging (Winston, Pino)
- [ ] Configure Docker
- [ ] Set up CI/CD

## Development

```bash
npm run dev       # Start dev server with auto-reload
npm run build     # Build for production
npm start         # Start production server
npm test          # Run tests
npm run lint      # Lint code
npm run format    # Format code with Prettier
```
```

---

## Usage Notes

- **When to use**: After API spec finalized, before manual coding
- **When NOT to use**: For complex business logic (only generates structure)
- **Best practice**: Review generated code, add business logic incrementally
- **Iteration**: Regenerate if API spec changes significantly

---

## Quality Checklist

- [ ] All API endpoints have routes
- [ ] All request/response schemas have models
- [ ] Validation schemas generated from OpenAPI
- [ ] Controllers call service layer (separation of concerns)
- [ ] Error handling middleware included
- [ ] TypeScript strict mode enabled
- [ ] Package.json with all dependencies
- [ ] README with setup instructions
- [ ] TODO comments for business logic
- [ ] Code is runnable (passes TypeScript compilation)
