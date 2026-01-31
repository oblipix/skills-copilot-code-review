# Frontend Code Instructions

**Applies to:** `src/static/**/*.{html,css,js}`

## Purpose
These instructions apply to all frontend code including HTML, CSS, and JavaScript files.

## Specific Guidelines

### JavaScript Code
- Use modern JavaScript (ES6+) features
- Use `const` for variables that don't change, `let` for variables that do
- Avoid using `var`
- Use async/await for asynchronous operations
- Handle errors in API calls gracefully with try-catch blocks
- Provide user feedback for all actions (loading states, success/error messages)

### API Communication
- Use `fetch` API for HTTP requests
- Always include proper headers (Content-Type, etc.)
- Handle HTTP error responses appropriately
- Show loading indicators during API calls
- Display meaningful error messages to users
- Store authentication tokens/sessions carefully:
  - Prefer HttpOnly cookies for sensitive tokens (immune to XSS)
  - If using localStorage/sessionStorage, be aware of XSS vulnerabilities
  - Never store sensitive data without proper XSS protections

### DOM Manipulation
- Cache DOM element references when used multiple times
- Use event delegation for dynamic content
- Avoid memory leaks by removing event listeners when elements are removed
- Use semantic HTML elements
- Ensure accessibility with proper ARIA labels and keyboard navigation

### User Experience
- Provide immediate visual feedback for user actions
- Disable buttons during operations to prevent double-submissions
- Show loading spinners or progress indicators for long operations
- Display clear success and error messages
- Validate form inputs on the client side before submission

### Security
- Sanitize user inputs before displaying them in the DOM
- Prevent XSS attacks by avoiding `innerHTML` with user-generated content
- Use `textContent` or create elements programmatically for user data
- Validate all data received from the API
- Be cautious with localStorage/sessionStorage:
  - These are vulnerable to XSS attacks
  - Never store highly sensitive data (passwords, tokens) without proper protections
  - Consider HttpOnly cookies for authentication tokens instead

### CSS Styling
- Use meaningful class names that describe purpose, not appearance
- Keep styles modular and reusable
- Use CSS variables for consistent theming
- Ensure responsive design for different screen sizes
- Follow mobile-first approach when applicable

## Code Examples

### Good API Call
```javascript
async function fetchActivities() {
    try {
        const response = await fetch('/activities/');
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Failed to fetch activities:', error);
        showErrorMessage('Failed to load activities. Please try again.');
        return null;
    }
}
```

### Good DOM Manipulation (Preventing XSS)
```javascript
// Good - using textContent
const nameElement = document.createElement('span');
nameElement.textContent = userProvidedName;

// Bad - vulnerable to XSS
element.innerHTML = userProvidedName;
```

### Good Form Validation
```javascript
function validateEmail(email) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
}

// Before submitting
if (!validateEmail(emailInput.value)) {
    showError('Please enter a valid email address');
    return;
}
```

### Good User Feedback
```javascript
async function signupForActivity(activityName, email) {
    const button = document.querySelector('.signup-button');
    button.disabled = true;
    button.textContent = 'Signing up...';
    
    try {
        await performSignup(activityName, email);
        showSuccessMessage('Successfully signed up!');
    } catch (error) {
        showErrorMessage('Failed to sign up. Please try again.');
    } finally {
        button.disabled = false;
        button.textContent = 'Sign Up';
    }
}
```
