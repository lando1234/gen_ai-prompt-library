---
type: specialized
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, integration-testing, api-testing, openapi, test-automation]
---

# Generate Integration Tests from API Specification

## Purpose
Automatically generate comprehensive integration tests from OpenAPI specifications to validate API endpoints, request/response schemas, and HTTP status codes.

## Context
Use this prompt when:
- Validating API implementation against OpenAPI spec
- Creating automated API contract tests
- Ensuring all endpoints are tested
- Verifying request/response schema compliance

---

## Prompt

You are a test automation specialist. Generate comprehensive integration tests from OpenAPI specifications that validate API contracts.

**INPUT**:
- OpenAPI 3.0 specification (YAML/JSON)
- Testing framework (Jest, PyTest, Go testing, etc.)
- Base URL or environment configuration
- Optional: Authentication credentials

**GENERATE**:

1. **Test Cases per Endpoint**:
   - Test for each HTTP method (GET, POST, PUT, DELETE)
   - Positive scenarios (2xx responses)
   - Negative scenarios (4xx/5xx responses)
   - Edge cases from schema definitions

2. **Request Validation Tests**:
   - Valid request bodies
   - Invalid request bodies (validation failures)
   - Missing required fields
   - Invalid data types
   - Out-of-range values

3. **Response Validation Tests**:
   - Correct HTTP status codes
   - Response schema matches OpenAPI spec
   - Response headers present
   - Data types correct
   - Required fields present

4. **Authentication Tests**:
   - Valid authentication (if required)
   - Missing authentication (401)
   - Invalid credentials (401)
   - Expired tokens (401)

5. **Error Response Tests**:
   - Standard error format
   - Error codes and messages
   - Field-level validation errors

6. **Contract Testing**:
   - Compare actual responses to OpenAPI schemas
   - Detect undocumented fields
   - Detect missing documented fields

**OUTPUT FORMAT**:
```typescript
// tests/integration/[resource].test.ts

import request from 'supertest';
import { app } from '../../src/app';

describe('[Resource] API Integration Tests', () => {
  // Setup
  beforeAll(async () => {
    // Initialize test database, seed data
  });

  afterAll(async () => {
    // Cleanup
  });

  describe('GET /api/v1/[resource]', () => {
    test('[OpenAPI operationId] - Success (200)', async () => {
      // Given
      // When
      const response = await request(app)
        .get('/api/v1/[resource]')
        .expect(200);

      // Then: Validate response schema
      expect(response.body).toMatchObject({
        // Expected structure from OpenAPI
      });
    });

    test('Invalid parameter - Bad Request (400)', async () => {
      // Test validation errors
    });
  });

  describe('POST /api/v1/[resource]', () => {
    test('[OpenAPI operationId] - Created (201)', async () => {
      // Test successful creation
    });

    test('Missing required field - Bad Request (400)', async () => {
      // Test validation errors
    });
  });
});
```

**CONSTRAINTS**:
- Test ALL endpoints defined in OpenAPI spec
- Validate response schemas strictly
- Test ALL defined status codes
- Include authentication tests
- Use realistic test data
- Tests must be executable (not pseudocode)

---

## Example

**Input**: OpenAPI spec for User Management API

**Output**:

```typescript
// tests/integration/users.test.ts
import request from 'supertest';
import { createApp } from '../../src/app';
import { setupTestDB, teardownTestDB, seedUsers } from '../helpers/db';

/**
 * Integration tests for User API
 * Generated from OpenAPI spec: /users paths
 */
describe('User API Integration Tests', () => {
  let app: any;

  beforeAll(async () => {
    // Setup test database
    await setupTestDB();
    app = createApp();
  });

  afterAll(async () => {
    await teardownTestDB();
  });

  beforeEach(async () => {
    // Seed test data before each test
    await seedUsers([
      { id: '550e8400-e29b-41d4-a716-446655440000', email: 'john@example.com', name: 'John Doe' },
      { id: '6ba7b810-9dad-11d1-80b4-00c04fd430c8', email: 'jane@example.com', name: 'Jane Smith' }
    ]);
  });

  // ============================================================
  // GET /api/v1/users - List Users
  // OpenAPI operationId: listUsers
  // ============================================================

  describe('GET /api/v1/users', () => {
    test('should return paginated user list with default parameters', async () => {
      // Given: Database has 2 users

      // When: GET /users without query parameters
      const response = await request(app)
        .get('/api/v1/users')
        .expect('Content-Type', /json/)
        .expect(200);

      // Then: Response matches OpenAPI schema
      expect(response.body).toMatchObject({
        data: expect.arrayContaining([
          expect.objectContaining({
            id: expect.any(String),
            email: expect.any(String),
            name: expect.any(String)
          })
        ])
      });

      // Validate specific data
      expect(response.body.data).toHaveLength(2);
      expect(response.body.data[0]).toMatchObject({
        id: '550e8400-e29b-41d4-a716-446655440000',
        email: 'john@example.com',
        name: 'John Doe'
      });
    });

    test('should return paginated user list with page parameter', async () => {
      // Given: Query parameter page=1

      // When
      const response = await request(app)
        .get('/api/v1/users')
        .query({ page: 1 })
        .expect(200);

      // Then
      expect(response.body.data).toBeDefined();
      expect(Array.isArray(response.body.data)).toBe(true);
    });

    test('should return 400 for invalid page parameter (negative)', async () => {
      // Given: Invalid page parameter

      // When
      const response = await request(app)
        .get('/api/v1/users')
        .query({ page: -1 })
        .expect(400);

      // Then: Error response matches OpenAPI error schema
      expect(response.body).toMatchObject({
        success: false,
        error: {
          code: 'VALIDATION_ERROR',
          message: expect.any(String)
        }
      });
    });

    test('should return 400 for invalid page parameter (not a number)', async () => {
      // When
      const response = await request(app)
        .get('/api/v1/users')
        .query({ page: 'invalid' })
        .expect(400);

      // Then
      expect(response.body.success).toBe(false);
    });

    test('should return empty array when page exceeds total pages', async () => {
      // Given: Page 999 (no data)

      // When
      const response = await request(app)
        .get('/api/v1/users')
        .query({ page: 999 })
        .expect(200);

      // Then
      expect(response.body.data).toEqual([]);
    });
  });

  // ============================================================
  // POST /api/v1/users - Create User
  // OpenAPI operationId: createUser
  // ============================================================

  describe('POST /api/v1/users', () => {
    test('should create user with valid data and return 201', async () => {
      // Given: Valid user creation data
      const userData = {
        email: 'newuser@example.com',
        name: 'New User'
      };

      // When: POST /users
      const response = await request(app)
        .post('/api/v1/users')
        .send(userData)
        .expect('Content-Type', /json/)
        .expect(201);

      // Then: Response matches OpenAPI User schema
      expect(response.body).toMatchObject({
        id: expect.stringMatching(/^[0-9a-f]{8}-[0-9a-f]{4}-4[0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i), // UUID v4
        email: 'newuser@example.com',
        name: 'New User'
      });

      // Verify Location header (optional in OpenAPI spec)
      expect(response.headers.location).toBeDefined();
      expect(response.headers.location).toMatch(/^\/api\/v1\/users\/.+$/);
    });

    test('should return 400 when email is missing (required field)', async () => {
      // Given: Missing required field 'email'
      const invalidData = {
        name: 'Incomplete User'
        // email missing
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(invalidData)
        .expect(400);

      // Then: Validation error with field details
      expect(response.body).toMatchObject({
        success: false,
        error: {
          code: 'VALIDATION_ERROR',
          message: expect.any(String),
          details: expect.arrayContaining([
            expect.objectContaining({
              field: 'email',
              message: expect.stringMatching(/required/i)
            })
          ])
        }
      });
    });

    test('should return 400 when email format is invalid', async () => {
      // Given: Invalid email format
      const invalidData = {
        email: 'not-an-email',
        name: 'Invalid Email User'
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(invalidData)
        .expect(400);

      // Then
      expect(response.body.error.details).toEqual(
        expect.arrayContaining([
          expect.objectContaining({
            field: 'email',
            message: expect.stringMatching(/invalid.*email/i)
          })
        ])
      );
    });

    test('should return 400 when name is too short (minLength: 2)', async () => {
      // Given: Name with 1 character (violates minLength: 2)
      const invalidData = {
        email: 'user@example.com',
        name: 'A'
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(invalidData)
        .expect(400);

      // Then
      expect(response.body.error.details).toEqual(
        expect.arrayContaining([
          expect.objectContaining({
            field: 'name',
            message: expect.stringMatching(/at least 2 characters/i)
          })
        ])
      );
    });

    test('should return 400 when name is missing (required field)', async () => {
      // Given: Missing required field 'name'
      const invalidData = {
        email: 'user@example.com'
        // name missing
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(invalidData)
        .expect(400);

      // Then
      expect(response.body.error.details).toEqual(
        expect.arrayContaining([
          expect.objectContaining({
            field: 'name',
            message: expect.stringMatching(/required/i)
          })
        ])
      );
    });

    test('should return 409 when email already exists (duplicate)', async () => {
      // Given: User with email already exists
      const duplicateData = {
        email: 'john@example.com', // Already in database
        name: 'Duplicate User'
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(duplicateData)
        .expect(409);

      // Then: Conflict error
      expect(response.body).toMatchObject({
        success: false,
        error: {
          code: 'USER_ALREADY_EXISTS',
          message: expect.stringMatching(/already exists/i)
        }
      });
    });

    test('should trim whitespace from name', async () => {
      // Given: Name with leading/trailing whitespace
      const userData = {
        email: 'trimtest@example.com',
        name: '  Trimmed User  '
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(userData)
        .expect(201);

      // Then: Whitespace trimmed
      expect(response.body.name).toBe('Trimmed User');
    });

    test('should reject request with extra undocumented fields', async () => {
      // Given: Extra field not in OpenAPI schema
      const dataWithExtraFields = {
        email: 'user@example.com',
        name: 'User',
        age: 30 // Not in schema
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(dataWithExtraFields)
        .expect(400);

      // Then: Validation error for unknown field
      expect(response.body.error.message).toMatch(/unknown.*field/i);
    });

    test('should handle multiple validation errors simultaneously', async () => {
      // Given: Multiple invalid fields
      const invalidData = {
        email: 'invalid-email',
        name: 'A' // Too short
      };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(invalidData)
        .expect(400);

      // Then: Multiple errors returned
      expect(response.body.error.details).toHaveLength(2);
      expect(response.body.error.details).toEqual(
        expect.arrayContaining([
          expect.objectContaining({ field: 'email' }),
          expect.objectContaining({ field: 'name' })
        ])
      );
    });
  });

  // ============================================================
  // Contract Validation Tests
  // ============================================================

  describe('OpenAPI Contract Validation', () => {
    test('GET /users response should not have undocumented fields', async () => {
      // When
      const response = await request(app)
        .get('/api/v1/users')
        .expect(200);

      // Then: Only documented fields present
      const allowedFields = ['data']; // From OpenAPI UserList schema
      const actualFields = Object.keys(response.body);

      actualFields.forEach(field => {
        expect(allowedFields).toContain(field);
      });
    });

    test('POST /users response should match User schema exactly', async () => {
      // Given
      const userData = { email: 'test@example.com', name: 'Test User' };

      // When
      const response = await request(app)
        .post('/api/v1/users')
        .send(userData)
        .expect(201);

      // Then: Exactly these fields, no more, no less
      const expectedFields = ['id', 'email', 'name']; // From OpenAPI User schema
      const actualFields = Object.keys(response.body);

      expect(actualFields.sort()).toEqual(expectedFields.sort());
    });

    test('Error response should match standard error schema', async () => {
      // When: Trigger any error
      const response = await request(app)
        .post('/api/v1/users')
        .send({})
        .expect(400);

      // Then: Standard error format
      expect(response.body).toMatchObject({
        success: false,
        error: {
          code: expect.any(String),
          message: expect.any(String)
        }
      });
    });
  });

  // ============================================================
  // Performance Tests (Optional)
  // ============================================================

  describe('Performance Tests', () => {
    test('GET /users should respond within 500ms', async () => {
      const start = Date.now();

      await request(app)
        .get('/api/v1/users')
        .expect(200);

      const duration = Date.now() - start;
      expect(duration).toBeLessThan(500);
    });

    test('POST /users should respond within 1000ms', async () => {
      const start = Date.now();

      await request(app)
        .post('/api/v1/users')
        .send({ email: 'perf@example.com', name: 'Perf Test' })
        .expect(201);

      const duration = Date.now() - start;
      expect(duration).toBeLessThan(1000);
    });
  });

  // ============================================================
  // Test Data Helpers
  // ============================================================

  /**
   * Generate valid user creation data
   */
  function validUserData(overrides = {}) {
    return {
      email: `user-${Date.now()}@example.com`,
      name: 'Test User',
      ...overrides
    };
  }

  /**
   * Generate invalid user creation data (for error testing)
   */
  function invalidUserData(invalidField: string) {
    const base = validUserData();
    switch (invalidField) {
      case 'email':
        return { ...base, email: 'invalid-email' };
      case 'name':
        return { ...base, name: 'A' }; // Too short
      case 'missing-email':
        return { name: base.name };
      case 'missing-name':
        return { email: base.email };
      default:
        return base;
    }
  }
});
```

```typescript
// tests/helpers/db.ts
/**
 * Test database helpers
 */
export async function setupTestDB() {
  // Connect to test database
  // Run migrations
  console.log('Test database setup complete');
}

export async function teardownTestDB() {
  // Drop all tables
  // Disconnect
  console.log('Test database teardown complete');
}

export async function seedUsers(users: any[]) {
  // Clear existing users
  // Insert test users
  console.log(`Seeded ${users.length} test users`);
}
```

```typescript
// tests/helpers/openapi-validator.ts
import Ajv from 'ajv';
import openApiSpec from '../../openapi.json';

/**
 * Validate response against OpenAPI schema
 */
export function validateResponseSchema(
  path: string,
  method: string,
  statusCode: number,
  responseBody: any
): boolean {
  const ajv = new Ajv({ strict: false });

  // Find schema in OpenAPI spec
  const operation = openApiSpec.paths[path][method];
  const responseSchema = operation.responses[statusCode.toString()].content['application/json'].schema;

  // Resolve $ref if needed
  const resolvedSchema = resolveRef(responseSchema, openApiSpec);

  // Validate
  const validate = ajv.compile(resolvedSchema);
  const valid = validate(responseBody);

  if (!valid) {
    console.error('Schema validation errors:', validate.errors);
  }

  return valid;
}

function resolveRef(schema: any, spec: any): any {
  if (schema.$ref) {
    const refPath = schema.$ref.replace('#/', '').split('/');
    let resolved = spec;
    for (const part of refPath) {
      resolved = resolved[part];
    }
    return resolved;
  }
  return schema;
}
```

```json
// jest.config.js
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/tests'],
  testMatch: ['**/*.test.ts'],
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  setupFilesAfterEnv: ['<rootDir>/tests/setup.ts']
};
```

```typescript
// tests/setup.ts
/**
 * Global test setup
 * Runs before all tests
 */
beforeAll(() => {
  // Set environment to test
  process.env.NODE_ENV = 'test';
});

// Increase timeout for integration tests
jest.setTimeout(10000);
```

---

## Usage Notes

- **When to use**: After API implementation complete, before deployment
- **When NOT to use**: For unit tests (use for integration/contract tests only)
- **Best practice**: Run tests in CI/CD pipeline on every commit
- **Iteration**: Regenerate when OpenAPI spec changes

---

## Quality Checklist

- [ ] All API endpoints have test cases
- [ ] All HTTP methods tested (GET, POST, PUT, DELETE)
- [ ] All status codes tested (200, 201, 400, 404, 500)
- [ ] Request validation tested (missing fields, invalid types)
- [ ] Response schemas validated against OpenAPI
- [ ] Authentication scenarios tested (if applicable)
- [ ] Error responses follow standard format
- [ ] Performance tests included (latency checks)
- [ ] Tests use realistic test data
- [ ] Tests are independent (can run in any order)
