# GitHub Copilot Instructions - Mergington High School API

## Project Overview
This is a FastAPI-based web application for managing extracurricular activities at Mergington High School. The system allows teachers to manage student enrollments in various school activities.

## General Guidelines

### Code Style
- Follow PEP 8 style guidelines for Python code
- Use meaningful variable names that clearly describe their purpose
- Keep functions focused on a single responsibility
- Write clear docstrings for all functions and classes
- Use type hints for function parameters and return values

### Security Best Practices
- Never commit passwords or sensitive data in plain text
- Always use parameterized queries or ORMs to prevent SQL/NoSQL injection
- Use Argon2 for password hashing (already implemented in database.py)
- Validate and sanitize all user inputs
- Implement proper authentication and authorization checks
- Use HTTPS in production environments

### Database Practices
- Use MongoDB connection string from environment variables in production
- Always validate data before inserting into the database
- Use meaningful collection and field names
- Handle database errors gracefully with proper error messages
- Close database connections properly

### API Design
- Follow RESTful conventions for endpoint naming
- Use appropriate HTTP methods (GET, POST, PUT, DELETE)
- Return meaningful HTTP status codes
- Include clear error messages in API responses
- Document all endpoints with proper descriptions and examples

### Testing
- Write tests for all new features
- Test edge cases and error conditions
- Ensure tests are isolated and don't depend on external state
- Mock external dependencies in unit tests

### Dependencies
- Keep dependencies up to date
- Document all required dependencies in requirements.txt
- Use virtual environments for development
- Check for security vulnerabilities in dependencies regularly

## Project Structure
- `/src` - Main application code
  - `app.py` - FastAPI application setup
  - `/backend` - Backend logic and API routes
    - `database.py` - Database configuration and utilities
    - `/routers` - API endpoint definitions
  - `/static` - Frontend static files (HTML, CSS, JavaScript)
