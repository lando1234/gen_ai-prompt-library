---
type: code-generation
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [validation, schemas, zod, joi, pydantic, security]
---

# Generate Data Validation Schemas

## Purpose
Generate runtime validation schemas for APIs and data processing with comprehensive rules, error messages, and type safety.

## Context
Use this prompt when:
- Building APIs that accept user input
- Processing external data (APIs, files, forms)
- Need type safety at runtime
- Implementing input sanitization

---

## Prompt

You are a data validation specialist. Generate comprehensive validation schemas with clear rules and error messages.

**INPUT**:
- Data structure to validate (JSON, TypeScript interface, or description)
- Validation library (Zod, Joi, Pydantic, etc.)
- Optional: Custom validation rules

**GENERATE**:

1. **Schema Definition**:
   - All fields with proper types
   - Required vs optional fields
   - Default values where appropriate

2. **Validation Rules**:
   - Type checking (string, number, boolean, etc.)
   - Length constraints (min/max)
   - Format validation (email, URL, UUID)
   - Range validation (min/max for numbers)
   - Enum validation (allowed values)
   - Pattern matching (regex)
   - Custom business rules

3. **Error Messages**:
   - Clear, actionable messages
   - Field-specific errors
   - Custom messages for complex rules

4. **Nested Validation**:
   - Objects within objects
   - Arrays with item schemas
   - Conditional validation

**OUTPUT FORMAT**:
```
# Validation Schema: [Entity Name]

## Schema Definition
```[language]
[complete schema code]
```

## Validation Examples

### Valid Input
```json
[example valid data]
```

### Invalid Inputs with Errors
```json
[example 1 - specific validation failure]
```
Error: [expected error message]

## Usage in API
```[language]
[how to use schema in route/controller]
```

## Notes
- [Custom validation logic explained]
- [Edge cases handled]
```

**CONSTRAINTS**:
- Validate all inputs before processing
- Provide specific error messages (not generic "invalid")
- Handle nested structures correctly
- Support both creation and update schemas (full vs partial)
- Consider security (max lengths to prevent DoS)

---

## Example

**Input**: User registration schema

**Library**: Zod (TypeScript)

**Requirements**:
- email: valid email format
- password: 8-64 chars, must include uppercase, lowercase, number
- age: 18-120
- username: 3-20 alphanumeric chars
- agreeToTerms: must be true

**Output**:

```typescript
# Validation Schema: User Registration

## Schema Definition

```typescript
import { z } from 'zod';

// Custom validators
const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,64}$/;
const usernameRegex = /^[a-zA-Z0-9_-]{3,20}$/;

// User registration schema
export const userRegistrationSchema = z.object({
  email: z
    .string({
      required_error: 'Email is required',
      invalid_type_error: 'Email must be a string',
    })
    .email('Please provide a valid email address')
    .max(254, 'Email must not exceed 254 characters') // RFC 5321
    .toLowerCase()
    .trim(),

  password: z
    .string({
      required_error: 'Password is required',
    })
    .min(8, 'Password must be at least 8 characters')
    .max(64, 'Password must not exceed 64 characters')
    .regex(
      passwordRegex,
      'Password must include uppercase, lowercase, and a number'
    ),

  username: z
    .string({
      required_error: 'Username is required',
    })
    .regex(
      usernameRegex,
      'Username must be 3-20 characters (letters, numbers, underscore, hyphen only)'
    )
    .trim(),

  age: z
    .number({
      required_error: 'Age is required',
      invalid_type_error: 'Age must be a number',
    })
    .int('Age must be a whole number')
    .min(18, 'You must be at least 18 years old')
    .max(120, 'Please enter a valid age'),

  agreeToTerms: z
    .boolean({
      required_error: 'You must agree to terms and conditions',
    })
    .refine((val) => val === true, {
      message: 'You must agree to terms and conditions',
    }),

  // Optional fields
  phoneNumber: z
    .string()
    .regex(/^\+?[1-9]\d{1,14}$/, 'Please provide a valid phone number (E.164 format)')
    .optional(),

  referralCode: z
    .string()
    .length(8, 'Referral code must be exactly 8 characters')
    .toUpperCase()
    .optional(),
});

// Type inference from schema
export type UserRegistration = z.infer<typeof userRegistrationSchema>;

// Partial schema for updates (all fields optional)
export const userUpdateSchema = userRegistrationSchema.partial().extend({
  // Password update requires current password
  currentPassword: z.string().optional(),
}).refine(
  (data) => {
    // If password is being updated, currentPassword is required
    if (data.password && !data.currentPassword) {
      return false;
    }
    return true;
  },
  {
    message: 'Current password is required when updating password',
    path: ['currentPassword'],
  }
);
```

## Validation Examples

### Valid Input
```json
{
  "email": "user@example.com",
  "password": "SecurePass123",
  "username": "john_doe",
  "age": 25,
  "agreeToTerms": true,
  "phoneNumber": "+14155552671",
  "referralCode": "FRIEND20"
}
```
✅ Validation passes

### Invalid Inputs with Errors

**Example 1: Invalid email**
```json
{
  "email": "not-an-email",
  "password": "SecurePass123",
  "username": "john_doe",
  "age": 25,
  "agreeToTerms": true
}
```
❌ Error:
```json
{
  "errors": [
    {
      "path": ["email"],
      "message": "Please provide a valid email address"
    }
  ]
}
```

**Example 2: Weak password**
```json
{
  "email": "user@example.com",
  "password": "weak",
  "username": "john_doe",
  "age": 25,
  "agreeToTerms": true
}
```
❌ Errors:
```json
{
  "errors": [
    {
      "path": ["password"],
      "message": "Password must be at least 8 characters"
    },
    {
      "path": ["password"],
      "message": "Password must include uppercase, lowercase, and a number"
    }
  ]
}
```

**Example 3: Underage user**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123",
  "username": "john_doe",
  "age": 16,
  "agreeToTerms": true
}
```
❌ Error:
```json
{
  "errors": [
    {
      "path": ["age"],
      "message": "You must be at least 18 years old"
    }
  ]
}
```

**Example 4: Terms not agreed**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123",
  "username": "john_doe",
  "age": 25,
  "agreeToTerms": false
}
```
❌ Error:
```json
{
  "errors": [
    {
      "path": ["agreeToTerms"],
      "message": "You must agree to terms and conditions"
    }
  ]
}
```

## Usage in API

```typescript
import express from 'express';
import { userRegistrationSchema, UserRegistration } from './schemas';

const router = express.Router();

router.post('/register', async (req, res) => {
  try {
    // Validate and parse request body
    const validatedData: UserRegistration = userRegistrationSchema.parse(req.body);

    // Data is now guaranteed to be valid and typed
    // Proceed with user creation...
    const user = await createUser(validatedData);

    res.status(201).json({ success: true, user });
  } catch (error) {
    if (error instanceof z.ZodError) {
      // Format validation errors
      return res.status(400).json({
        success: false,
        errors: error.errors.map((err) => ({
          path: err.path,
          message: err.message,
        })),
      });
    }

    // Handle other errors
    res.status(500).json({ success: false, error: 'Internal server error' });
  }
});

// Safe parsing (doesn't throw)
router.post('/register-safe', async (req, res) => {
  const result = userRegistrationSchema.safeParse(req.body);

  if (!result.success) {
    return res.status(400).json({
      success: false,
      errors: result.error.errors,
    });
  }

  // result.data is now typed and validated
  const user = await createUser(result.data);
  res.status(201).json({ success: true, user });
});
```

## Notes

**Custom Validation Logic**:
- `agreeToTerms` uses `.refine()` to ensure it's explicitly `true` (not just truthy)
- `userUpdateSchema` has cross-field validation: password update requires currentPassword

**Security Considerations**:
- Max email length (254) prevents DoS via extremely long emails
- Max password length (64) prevents bcrypt limitations (72 byte max)
- Username regex prevents special characters that could cause issues
- Phone number regex follows E.164 standard

**Edge Cases Handled**:
- Email is normalized (toLowerCase, trim) to prevent duplicate accounts
- Username is trimmed to prevent leading/trailing spaces
- Referral code is normalized to uppercase for consistency
- Age must be integer (no 25.5 years old)

**Type Safety**:
- `z.infer<typeof schema>` generates TypeScript type automatically
- No need to maintain separate interfaces

**Performance**:
- Validation is synchronous and fast
- Complex regex only runs on relevant fields
- Early returns on first validation failure (unless collecting all errors)

---

## Usage Notes

- **When to use**: Any API endpoint accepting user input
- **When NOT to use**: Internal function parameters (use TypeScript types)
- **Best practice**: Validate at API boundary, trust internally
- **Iteration**: Add validation rules as new requirements emerge

---

## Quality Checklist

- [ ] All fields have type validation
- [ ] Required vs optional clearly specified
- [ ] Error messages are clear and actionable
- [ ] Min/max lengths set (security against DoS)
- [ ] Format validation for emails, URLs, etc.
- [ ] Custom validation for business rules
- [ ] Nested objects/arrays validated
- [ ] Examples show valid and invalid cases
- [ ] Usage example in actual API route
