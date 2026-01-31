# API Routes Instructions

**Applies to:** `src/backend/routers/**/*.py`

## Purpose
These instructions apply specifically to FastAPI route handler files in the routers directory.

## Specific Guidelines

### Route Organization
- Group related endpoints in the same router file
- Use clear and consistent route prefixes
- Keep route handlers focused on HTTP concerns (validation, serialization)
- Delegate business logic to separate service functions when complex
- Use tags for API documentation grouping

### HTTP Method Usage
- `GET` - Retrieve data (no side effects)
- `POST` - Create new resources or perform actions
- `PUT` - Update entire resources
- `PATCH` - Partially update resources
- `DELETE` - Remove resources

### Status Codes
- `200 OK` - Successful GET, PUT, PATCH requests
- `201 Created` - Successful POST that creates a resource
- `204 No Content` - Successful DELETE
- `400 Bad Request` - Invalid input data
- `401 Unauthorized` - Missing or invalid authentication
- `403 Forbidden` - Authenticated but not authorized
- `404 Not Found` - Resource doesn't exist
- `409 Conflict` - Resource conflict (e.g., duplicate)
- `500 Internal Server Error` - Unexpected server errors

### Request Validation
- Use FastAPI's type hints for automatic validation
- Use Query, Path, Body for parameter validation
- Define Pydantic models for complex request bodies
- Set appropriate defaults for optional parameters
- Validate business rules before database operations

### Response Models
- Define Pydantic response models when possible
- Use `response_model` parameter in route decorators
- Return consistent response structures
- Include helpful metadata in responses
- Exclude sensitive fields from responses (passwords, internal IDs)

### Authentication Patterns
- For endpoints requiring authentication, check teacher credentials
- Use Optional[str] for optional auth parameters with Query
- Return 401 for missing or invalid credentials
- Return 403 when authenticated but not authorized for the action

### Query Parameters
- Use descriptive parameter names
- Provide clear descriptions in function docstrings
- Set sensible defaults for optional parameters
- Validate parameter values before using them
- Use enums for parameters with fixed sets of values

### Error Responses
- Always include a detail message in HTTPExceptions
- Make error messages user-friendly and actionable
- Don't expose internal implementation details
- Log detailed errors for debugging but return sanitized messages

## Code Examples

### Good Route Definition
```python
@router.get("/{activity_name}", response_model=ActivityDetail)
def get_activity(activity_name: str) -> Dict[str, Any]:
    """
    Get details of a specific activity by name
    
    Args:
        activity_name: The name of the activity to retrieve
        
    Returns:
        Activity details including participants and schedule
        
    Raises:
        HTTPException: 404 if activity not found
    """
    activity = activities_collection.find_one({"_id": activity_name})
    if not activity:
        raise HTTPException(
            status_code=404, 
            detail=f"Activity '{activity_name}' not found"
        )
    return activity
```

### Good Query Parameters
```python
@router.get("/", response_model=List[Activity])
def list_activities(
    day: Optional[str] = Query(None, description="Filter by day (e.g., 'Monday')"),
    min_slots: int = Query(0, ge=0, description="Minimum available slots required (filters activities with at least this many open spots)"),
    include_full: bool = Query(True, description="Include activities at full capacity")
) -> List[Activity]:
    """List all activities with optional filtering"""
    # Implementation
```

### Good Authentication Check
```python
@router.post("/{activity_name}/signup")
def signup_for_activity(
    activity_name: str,
    email: str,
    teacher_username: Optional[str] = Query(None, description="Teacher username for authentication")
):
    """Sign up a student for an activity"""
    # Verify authentication
    if not teacher_username:
        raise HTTPException(
            status_code=401,
            detail="Teacher authentication required for this action"
        )
    
    teacher = teachers_collection.find_one({"_id": teacher_username})
    if not teacher:
        raise HTTPException(
            status_code=401,
            detail="Invalid teacher credentials"
        )
    
    # Proceed with signup logic
```

### Good Error Handling
```python
@router.delete("/{activity_name}")
def delete_activity(activity_name: str, teacher_username: str):
    """Delete an activity"""
    try:
        result = activities_collection.delete_one({"_id": activity_name})
        if result.deleted_count == 0:
            raise HTTPException(
                status_code=404,
                detail=f"Activity '{activity_name}' not found"
            )
        return {"message": f"Activity '{activity_name}' deleted successfully"}
    except Exception as e:
        logger.error(f"Error deleting activity: {e}")
        raise HTTPException(
            status_code=500,
            detail="Failed to delete activity. Please try again."
        )
```
