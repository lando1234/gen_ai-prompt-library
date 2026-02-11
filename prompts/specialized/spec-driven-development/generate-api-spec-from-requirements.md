---
type: specialized
complexity: intermediate
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, api-specification, openapi, rest-api, api-design]
---

# Generate API Specification from Requirements

## Purpose
Generate comprehensive OpenAPI 3.0 specifications from functional requirements or user stories with complete schemas, endpoints, authentication, and examples.

## Context
Use this prompt when:
- Designing RESTful APIs from requirements
- Need contract-first API development
- Coordinating frontend and backend development
- Generating API documentation and client SDKs

---

## Prompt

You are an API architect specializing in contract-first design. Generate a complete OpenAPI 3.0 specification from requirements.

**INPUT**:
- Functional requirements or user stories
- Domain entities and relationships
- Optional: Authentication method, rate limits, error handling preferences

**GENERATE**:

1. **API Metadata**:
   - API title and version
   - Base URL and server configurations
   - Contact information and license

2. **Authentication & Security**:
   - Security schemes (JWT, API Key, OAuth2)
   - Global security requirements
   - Scope definitions

3. **Endpoints**:
   - Path definitions with HTTP methods
   - Request parameters (path, query, headers)
   - Request body schemas
   - Response schemas (success and error)
   - Status codes with descriptions

4. **Data Schemas**:
   - Component schemas for all entities
   - Validation rules (required, min/max, pattern)
   - Nested objects and arrays
   - Enums for fixed value sets

5. **Examples**:
   - Request examples for each endpoint
   - Response examples (success and error cases)
   - Realistic sample data

6. **Error Handling**:
   - Standard error response schema
   - Error codes and messages
   - Validation error format

**OUTPUT FORMAT**:
```yaml
openapi: 3.0.3
info:
  title: [API Name]
  version: [version]
  description: [description]
  contact:
    name: [Team Name]
    email: [email]
  license:
    name: [License]
    url: [URL]

servers:
  - url: [base URL]
    description: [environment]

tags:
  - name: [tag]
    description: [description]

paths:
  /resource:
    get:
      summary: [operation summary]
      description: [detailed description]
      operationId: [operationId]
      tags: [list of tags]
      parameters:
        - name: [param name]
          in: [path|query|header]
          description: [description]
          required: [boolean]
          schema:
            type: [type]
      responses:
        '200':
          description: [success description]
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/[Schema]'
              examples:
                [example-name]:
                  value: [example data]
        '400':
          $ref: '#/components/responses/BadRequest'

components:
  schemas:
    [EntityName]:
      type: object
      required:
        - [field1]
        - [field2]
      properties:
        [fieldName]:
          type: [string|number|boolean|array|object]
          description: [field description]
          example: [example value]
          [validation constraints]

  responses:
    BadRequest:
      description: Invalid request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

  securitySchemes:
    [schemeName]:
      type: [http|apiKey|oauth2]
      [scheme configuration]

security:
  - [schemeName]: []
```

**CONSTRAINTS**:
- Follow OpenAPI 3.0.3 specification
- Use `$ref` for reusable components
- Include examples for all endpoints
- Define validation rules explicitly
- Document all possible status codes
- Use semantic versioning

---

## Example

**Input Requirements**:
```
Feature: User Management API
- Create new users (email, name, age)
- Get user by ID
- List users with pagination
- Update user details
- Delete user (soft delete)

Validation:
- Email must be valid format
- Name: 2-100 characters
- Age: 18-120
- Authentication: JWT Bearer tokens
```

**Output**:

```yaml
openapi: 3.0.3
info:
  title: User Management API
  version: 1.0.0
  description: |
    RESTful API for managing user accounts with CRUD operations.

    ## Features
    - User registration and profile management
    - JWT-based authentication
    - Pagination and filtering
    - Input validation and error handling

  contact:
    name: API Support Team
    email: api-support@example.com
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.example.com/v1
    description: Production server
  - url: https://staging-api.example.com/v1
    description: Staging server
  - url: http://localhost:3000/v1
    description: Local development server

tags:
  - name: Users
    description: User account management operations
  - name: Authentication
    description: Authentication and authorization endpoints

paths:
  /users:
    get:
      summary: List all users
      description: |
        Retrieve a paginated list of users. Supports filtering, sorting, and pagination.

        **Authentication required**: Yes (Bearer token)
        **Rate limit**: 100 requests per minute
      operationId: listUsers
      tags:
        - Users
      parameters:
        - name: page
          in: query
          description: Page number (1-indexed)
          required: false
          schema:
            type: integer
            minimum: 1
            default: 1
          example: 1
        - name: limit
          in: query
          description: Number of items per page
          required: false
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
          example: 20
        - name: sort
          in: query
          description: Sort field and direction
          required: false
          schema:
            type: string
            enum: [name:asc, name:desc, createdAt:asc, createdAt:desc]
            default: createdAt:desc
          example: name:asc
        - name: search
          in: query
          description: Search query for name or email
          required: false
          schema:
            type: string
            minLength: 2
            maxLength: 100
          example: john
      responses:
        '200':
          description: Successful response with paginated user list
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
                  pagination:
                    $ref: '#/components/schemas/Pagination'
              examples:
                success:
                  value:
                    data:
                      - id: "550e8400-e29b-41d4-a716-446655440000"
                        email: "john.doe@example.com"
                        name: "John Doe"
                        age: 30
                        createdAt: "2024-01-15T10:30:00Z"
                        updatedAt: "2024-01-15T10:30:00Z"
                      - id: "6ba7b810-9dad-11d1-80b4-00c04fd430c8"
                        email: "jane.smith@example.com"
                        name: "Jane Smith"
                        age: 28
                        createdAt: "2024-01-14T08:20:00Z"
                        updatedAt: "2024-01-14T08:20:00Z"
                    pagination:
                      page: 1
                      limit: 20
                      total: 150
                      totalPages: 8
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '429':
          $ref: '#/components/responses/TooManyRequests'
        '500':
          $ref: '#/components/responses/InternalServerError'
      security:
        - bearerAuth: []

    post:
      summary: Create a new user
      description: |
        Register a new user account with email, name, and age.

        **Authentication required**: No (public endpoint)
        **Rate limit**: 10 requests per hour per IP
      operationId: createUser
      tags:
        - Users
      requestBody:
        required: true
        description: User data for registration
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserCreate'
            examples:
              valid:
                summary: Valid user creation
                value:
                  email: "new.user@example.com"
                  name: "New User"
                  age: 25
              minimal:
                summary: Minimal required fields
                value:
                  email: "minimal@example.com"
                  name: "Min"
                  age: 18
      responses:
        '201':
          description: User created successfully
          headers:
            Location:
              description: URL of the newly created user
              schema:
                type: string
              example: /v1/users/550e8400-e29b-41d4-a716-446655440000
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
              examples:
                created:
                  value:
                    id: "550e8400-e29b-41d4-a716-446655440000"
                    email: "new.user@example.com"
                    name: "New User"
                    age: 25
                    createdAt: "2024-01-20T14:30:00Z"
                    updatedAt: "2024-01-20T14:30:00Z"
        '400':
          $ref: '#/components/responses/BadRequest'
        '409':
          description: User with this email already exists
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
              examples:
                conflict:
                  value:
                    error:
                      code: "USER_ALREADY_EXISTS"
                      message: "A user with this email already exists"
                      field: "email"
        '429':
          $ref: '#/components/responses/TooManyRequests'
        '500':
          $ref: '#/components/responses/InternalServerError'

  /users/{userId}:
    get:
      summary: Get user by ID
      description: |
        Retrieve detailed information about a specific user.

        **Authentication required**: Yes (Bearer token)
        **Authorization**: Users can only access their own profile unless admin
      operationId: getUserById
      tags:
        - Users
      parameters:
        - name: userId
          in: path
          description: Unique user identifier (UUID)
          required: true
          schema:
            type: string
            format: uuid
          example: "550e8400-e29b-41d4-a716-446655440000"
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
              examples:
                user:
                  value:
                    id: "550e8400-e29b-41d4-a716-446655440000"
                    email: "john.doe@example.com"
                    name: "John Doe"
                    age: 30
                    createdAt: "2024-01-15T10:30:00Z"
                    updatedAt: "2024-01-15T10:30:00Z"
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          description: User not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
              examples:
                notFound:
                  value:
                    error:
                      code: "USER_NOT_FOUND"
                      message: "User with ID 550e8400-e29b-41d4-a716-446655440000 not found"
        '500':
          $ref: '#/components/responses/InternalServerError'
      security:
        - bearerAuth: []

    patch:
      summary: Update user
      description: |
        Update user profile information. Only provided fields will be updated (partial update).

        **Authentication required**: Yes (Bearer token)
        **Authorization**: Users can only update their own profile
      operationId: updateUser
      tags:
        - Users
      parameters:
        - name: userId
          in: path
          description: Unique user identifier
          required: true
          schema:
            type: string
            format: uuid
          example: "550e8400-e29b-41d4-a716-446655440000"
      requestBody:
        required: true
        description: Fields to update (all optional, at least one required)
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserUpdate'
            examples:
              updateName:
                summary: Update name only
                value:
                  name: "John Smith"
              updateMultiple:
                summary: Update multiple fields
                value:
                  name: "John Updated"
                  age: 31
      responses:
        '200':
          description: User updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
              examples:
                updated:
                  value:
                    id: "550e8400-e29b-41d4-a716-446655440000"
                    email: "john.doe@example.com"
                    name: "John Updated"
                    age: 31
                    createdAt: "2024-01-15T10:30:00Z"
                    updatedAt: "2024-01-20T16:45:00Z"
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          description: User not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '500':
          $ref: '#/components/responses/InternalServerError'
      security:
        - bearerAuth: []

    delete:
      summary: Delete user
      description: |
        Soft delete a user account. The account is marked as deleted but data is retained.

        **Authentication required**: Yes (Bearer token)
        **Authorization**: Users can only delete their own account
      operationId: deleteUser
      tags:
        - Users
      parameters:
        - name: userId
          in: path
          description: Unique user identifier
          required: true
          schema:
            type: string
            format: uuid
          example: "550e8400-e29b-41d4-a716-446655440000"
      responses:
        '204':
          description: User deleted successfully (no content)
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          description: User not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '500':
          $ref: '#/components/responses/InternalServerError'
      security:
        - bearerAuth: []

components:
  schemas:
    User:
      type: object
      description: User account information
      required:
        - id
        - email
        - name
        - age
        - createdAt
        - updatedAt
      properties:
        id:
          type: string
          format: uuid
          description: Unique user identifier
          readOnly: true
          example: "550e8400-e29b-41d4-a716-446655440000"
        email:
          type: string
          format: email
          description: User email address (unique, lowercase)
          maxLength: 254
          example: "john.doe@example.com"
        name:
          type: string
          description: User full name
          minLength: 2
          maxLength: 100
          example: "John Doe"
        age:
          type: integer
          description: User age in years
          minimum: 18
          maximum: 120
          example: 30
        createdAt:
          type: string
          format: date-time
          description: Account creation timestamp (ISO 8601)
          readOnly: true
          example: "2024-01-15T10:30:00Z"
        updatedAt:
          type: string
          format: date-time
          description: Last update timestamp (ISO 8601)
          readOnly: true
          example: "2024-01-15T10:30:00Z"

    UserCreate:
      type: object
      description: Data required to create a new user
      required:
        - email
        - name
        - age
      properties:
        email:
          type: string
          format: email
          description: User email address (must be unique)
          maxLength: 254
          example: "new.user@example.com"
        name:
          type: string
          description: User full name
          minLength: 2
          maxLength: 100
          example: "New User"
        age:
          type: integer
          description: User age in years
          minimum: 18
          maximum: 120
          example: 25

    UserUpdate:
      type: object
      description: Fields that can be updated (all optional, at least one required)
      minProperties: 1
      properties:
        name:
          type: string
          description: User full name
          minLength: 2
          maxLength: 100
          example: "Updated Name"
        age:
          type: integer
          description: User age in years
          minimum: 18
          maximum: 120
          example: 31

    Pagination:
      type: object
      description: Pagination metadata
      required:
        - page
        - limit
        - total
        - totalPages
      properties:
        page:
          type: integer
          description: Current page number (1-indexed)
          minimum: 1
          example: 1
        limit:
          type: integer
          description: Items per page
          minimum: 1
          maximum: 100
          example: 20
        total:
          type: integer
          description: Total number of items
          minimum: 0
          example: 150
        totalPages:
          type: integer
          description: Total number of pages
          minimum: 0
          example: 8

    Error:
      type: object
      description: Standard error response structure
      required:
        - error
      properties:
        error:
          type: object
          required:
            - code
            - message
          properties:
            code:
              type: string
              description: Machine-readable error code
              example: "VALIDATION_ERROR"
            message:
              type: string
              description: Human-readable error message
              example: "Validation failed for one or more fields"
            field:
              type: string
              description: Field that caused the error (if applicable)
              example: "email"
            details:
              type: array
              description: Detailed validation errors (if multiple)
              items:
                type: object
                properties:
                  field:
                    type: string
                    example: "age"
                  message:
                    type: string
                    example: "Age must be at least 18"

  responses:
    BadRequest:
      description: Invalid request (validation error)
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          examples:
            validationError:
              summary: Validation error example
              value:
                error:
                  code: "VALIDATION_ERROR"
                  message: "Validation failed"
                  details:
                    - field: "email"
                      message: "Invalid email format"
                    - field: "age"
                      message: "Age must be between 18 and 120"

    Unauthorized:
      description: Authentication required or token invalid
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          examples:
            missingToken:
              summary: Missing authentication token
              value:
                error:
                  code: "UNAUTHORIZED"
                  message: "Authentication required"
            invalidToken:
              summary: Invalid or expired token
              value:
                error:
                  code: "INVALID_TOKEN"
                  message: "Authentication token is invalid or expired"

    Forbidden:
      description: Insufficient permissions
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          examples:
            forbidden:
              value:
                error:
                  code: "FORBIDDEN"
                  message: "You do not have permission to access this resource"

    TooManyRequests:
      description: Rate limit exceeded
      headers:
        X-RateLimit-Limit:
          description: Request limit per window
          schema:
            type: integer
          example: 100
        X-RateLimit-Remaining:
          description: Remaining requests in current window
          schema:
            type: integer
          example: 0
        X-RateLimit-Reset:
          description: Time when rate limit resets (Unix timestamp)
          schema:
            type: integer
          example: 1705761600
        Retry-After:
          description: Seconds until rate limit resets
          schema:
            type: integer
          example: 60
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          examples:
            rateLimited:
              value:
                error:
                  code: "RATE_LIMIT_EXCEEDED"
                  message: "Too many requests. Please try again in 60 seconds."

    InternalServerError:
      description: Unexpected server error
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          examples:
            serverError:
              value:
                error:
                  code: "INTERNAL_SERVER_ERROR"
                  message: "An unexpected error occurred. Please try again later."

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: |
        JWT Bearer token authentication.

        **How to obtain a token:**
        1. Call POST /auth/login with email and password
        2. Receive JWT token in response
        3. Include token in Authorization header: `Bearer <token>`

        **Token expiry:** 15 minutes
        **Refresh:** Use POST /auth/refresh with refresh token

security:
  - bearerAuth: []
```

---

## Usage Notes

- **When to use**: After functional spec approval, before backend implementation
- **When NOT to use**: For internal APIs with no external consumers
- **Best practice**: Review with frontend team before implementation
- **Iteration**: Update spec as API evolves, maintain changelog

---

## Quality Checklist

- [ ] All endpoints documented with summary and description
- [ ] Request/response schemas defined in components
- [ ] Validation rules specified (min/max, required, pattern)
- [ ] Examples provided for all operations
- [ ] Error responses documented with codes
- [ ] Authentication/authorization documented
- [ ] Rate limits specified (if applicable)
- [ ] Follows RESTful naming conventions
- [ ] Uses semantic versioning
- [ ] Validated with OpenAPI tools (Swagger Editor)
