# Backend Code Instructions

**Applies to:** `src/backend/**/*.py`

## Purpose
These instructions apply to all Python backend code including database operations, API routes, and business logic.

## Specific Guidelines

### Database Operations
- Always use the existing MongoDB collections from `database.py`
- Use `activities_collection` for activity data
- Use `teachers_collection` for teacher/admin accounts
- Always validate that records exist before performing updates or deletes
- Use `find_one()` for single record queries with `_id` field
- Use `find()` for multiple records with proper filtering
- Handle MongoDB exceptions gracefully

### Password Security
- Always use `hash_password()` from database.py for new passwords
- Always use `verify_password()` from database.py for password verification
- Never log or expose password hashes
- Never store passwords in plain text

### API Endpoint Design
- Use FastAPI's `APIRouter` for organizing routes
- Define clear route prefixes (e.g., `/activities`, `/auth`)
- Use appropriate HTTP methods and status codes
- Validate query parameters and path parameters
- Use Pydantic models or type hints for request/response validation

### Authentication & Authorization
- Always check `teacher_username` parameter for protected endpoints
- Verify teacher exists in database before allowing operations
- Return 401 status code for authentication failures
- Return 403 status code for authorization failures
- Return 404 status code when resources are not found

### Error Handling
- Use `HTTPException` for API errors with appropriate status codes
- Provide clear, user-friendly error messages
- Log errors for debugging but don't expose sensitive information
- Validate input data before database operations

### Data Validation
- Check if students are already enrolled before adding them
- Validate email format for student emails
- Check activity capacity before enrollments (max_participants)
- Ensure activity names and student emails are not empty or invalid

## Code Examples

### Good Database Query
```python
activity = activities_collection.find_one({"_id": activity_name})
if not activity:
    raise HTTPException(status_code=404, detail="Activity not found")
```

### Good Authentication Check
```python
if not teacher_username:
    raise HTTPException(status_code=401, detail="Authentication required")

teacher = teachers_collection.find_one({"_id": teacher_username})
if not teacher:
    raise HTTPException(status_code=401, detail="Invalid credentials")
```

### Good Password Handling
```python
from ..database import hash_password, verify_password

# When creating a user
hashed = hash_password(plain_password)

# When verifying
is_valid = verify_password(stored_hash, provided_password)
```
