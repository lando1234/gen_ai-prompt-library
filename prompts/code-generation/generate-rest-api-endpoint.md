---
type: code-generation
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [api, rest, backend, openapi, fastapi, express, spring-boot]
---

# Generate REST API Endpoint from Specification

## Purpose
Generate production-ready REST API endpoints from OpenAPI specifications or requirements, including validation, error handling, and documentation.

## Context
Use this prompt when:
- Building new API endpoints from specifications
- Implementing OpenAPI/Swagger contracts
- Need consistent API structure across services
- Scaffolding CRUD operations quickly

---

## Prompt

You are a backend API developer. Generate a production-ready REST API endpoint based on the provided specification.

**INPUT**:
- Endpoint specification (OpenAPI/Swagger or text description)
- Target framework (FastAPI, Express.js, Spring Boot, etc.)
- Optional: Authentication requirements, rate limiting

**REQUIREMENTS**:

1. **Endpoint Implementation**:
   - HTTP method and path
   - Request validation (body, query params, path params)
   - Response serialization with proper status codes
   - Error handling with meaningful messages

2. **Data Validation**:
   - Type checking for all inputs
   - Range/length validation where applicable
   - Required vs optional fields
   - Custom validation rules if specified

3. **Error Handling**:
   - 400 Bad Request: Invalid input
   - 401 Unauthorized: Auth failure
   - 404 Not Found: Resource missing
   - 500 Internal Server Error: Unexpected failures
   - Structured error responses with details

4. **Documentation**:
   - Docstrings/comments explaining purpose
   - OpenAPI annotations (if framework supports)
   - Example requests/responses

5. **Security**:
   - Input sanitization
   - SQL injection prevention
   - Rate limiting (if specified)
   - Authentication/authorization checks

**OUTPUT FORMAT**:
```
# API Endpoint Implementation

## Endpoint: [METHOD] /path

### Code:
```[language]
[full implementation]
```

### Request Example:
```json
[example request]
```

### Response Examples:

**Success (200/201):**
```json
[success response]
```

**Error (400):**
```json
{
  "error": "Bad Request",
  "message": "Validation failed",
  "details": [...]
}
```

### Notes:
- [Implementation decisions]
- [Security considerations]
- [Performance notes]
```

**CONSTRAINTS**:
- Follow framework conventions (RESTful, idiomatic code)
- Include comprehensive error handling
- Validate all inputs before processing
- Return structured, predictable responses
- Include auth checks if endpoint requires authentication

---

## Example

**Input Specification**:
```yaml
paths:
  /api/users:
    post:
      summary: Create new user
      requestBody:
        content:
          application/json:
            schema:
              type: object
              required: [email, name]
              properties:
                email:
                  type: string
                  format: email
                name:
                  type: string
                  minLength: 2
                age:
                  type: integer
                  minimum: 18
      responses:
        201:
          description: User created
        400:
          description: Invalid input
```

**Framework**: FastAPI (Python)

**Output**:
```python
# API Endpoint Implementation

## Endpoint: POST /api/users

### Code:
```python
from fastapi import APIRouter, HTTPException, status
from pydantic import BaseModel, EmailStr, Field, validator
from typing import Optional
import re

router = APIRouter()

class UserCreate(BaseModel):
    """Request model for creating a new user."""
    email: EmailStr
    name: str = Field(..., min_length=2, max_length=100)
    age: Optional[int] = Field(None, ge=18, le=120)

    @validator('name')
    def validate_name(cls, v):
        """Ensure name contains only letters and spaces."""
        if not re.match(r'^[a-zA-Z\s]+$', v):
            raise ValueError('Name must contain only letters and spaces')
        return v.strip()

class UserResponse(BaseModel):
    """Response model for user creation."""
    id: int
    email: str
    name: str
    age: Optional[int]
    created_at: str

@router.post(
    "/api/users",
    response_model=UserResponse,
    status_code=status.HTTP_201_CREATED,
    summary="Create new user",
    description="Creates a new user with email, name, and optional age"
)
async def create_user(user: UserCreate):
    """
    Create a new user in the system.

    Args:
        user: User data including email, name, and optional age

    Returns:
        UserResponse: Created user with ID and timestamp

    Raises:
        HTTPException 400: Invalid input data
        HTTPException 409: Email already exists
        HTTPException 500: Database error
    """
    try:
        # Check if email already exists
        existing_user = await db.users.find_one({"email": user.email})
        if existing_user:
            raise HTTPException(
                status_code=status.HTTP_409_CONFLICT,
                detail={
                    "error": "Conflict",
                    "message": f"User with email {user.email} already exists"
                }
            )

        # Create user in database
        user_data = user.dict()
        user_data["created_at"] = datetime.utcnow().isoformat()

        result = await db.users.insert_one(user_data)
        user_data["id"] = result.inserted_id

        return UserResponse(**user_data)

    except HTTPException:
        raise
    except Exception as e:
        # Log error for debugging
        logger.error(f"Failed to create user: {str(e)}", exc_info=True)
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail={
                "error": "Internal Server Error",
                "message": "Failed to create user. Please try again later."
            }
        )
```

### Request Example:
```json
POST /api/users
Content-Type: application/json

{
  "email": "user@example.com",
  "name": "John Doe",
  "age": 25
}
```

### Response Examples:

**Success (201 Created):**
```json
{
  "id": 12345,
  "email": "user@example.com",
  "name": "John Doe",
  "age": 25,
  "created_at": "2024-02-09T10:30:00Z"
}
```

**Error (400 Bad Request):**
```json
{
  "detail": [
    {
      "loc": ["body", "email"],
      "msg": "value is not a valid email address",
      "type": "value_error.email"
    }
  ]
}
```

**Error (409 Conflict):**
```json
{
  "error": "Conflict",
  "message": "User with email user@example.com already exists"
}
```

**Error (500 Internal Server Error):**
```json
{
  "error": "Internal Server Error",
  "message": "Failed to create user. Please try again later."
}
```

### Notes:
- **Validation**: Pydantic handles type/format validation automatically
- **Security**: Email uniqueness check prevents duplicates; name validation prevents injection
- **Error Handling**: Specific HTTP codes for different failure modes
- **Logging**: Errors logged for debugging without exposing internals to client
- **Idempotency**: Email uniqueness check makes this operation idempotent
- **Performance**: Uses async/await for non-blocking database operations

---

## Usage Notes

- **When to use**: Implementing new API endpoints from specs or requirements
- **When NOT to use**: For trivial endpoints (better to write manually)
- **Best practice**: Test generated endpoint with various inputs including edge cases
- **Iteration**: Refine based on actual API usage patterns and errors

---

## Quality Checklist

- [ ] Endpoint implements specified HTTP method and path
- [ ] Request validation covers all required and optional fields
- [ ] Error handling includes 400, 401, 404, 500 with structured responses
- [ ] Documentation includes docstrings and examples
- [ ] Security measures in place (sanitization, auth checks)
- [ ] Response follows consistent structure
- [ ] Code follows framework conventions
- [ ] Logging included for debugging
