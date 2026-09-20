# Plan: REST API for a Todo List

## Phase 0 – Reuse Audit
- No existing `.a5c/reuse-audit.json` found. No prior migrations, API routes, or SDK dependencies to integrate.
- Starting fresh: no legacy code to reuse.

## Phase 1 – Architecture & Design

### 1.1 Requirements
- Build a REST API for managing a todo list
- Core CRUD operations: create, read, update, delete todos
- Persistence: store todos in a database (SQLite recommended for simplicity)
- Authentication: basic API key or JWT (simplified for MVP)
- Output: JSON responses, proper HTTP status codes

### 1.2 Endpoints
| Method | Path | Description |
|--------|------|-------------|
| POST   | `/todos` | Create a new todo |
| GET    | `/todos` | List all todos |
| GET    | `/todos/{id}` | Get a specific todo |
| PUT    | `/todos/{id}` | Update a todo |
| DELETE | `/todos/{id}` | Delete a todo |

### 1.3 Data Model (Todo)
- `id` (UUID) – unique identifier
- `title` (string) – task description
- `completed` (boolean) – completion status
- `created_at` (timestamp) – when created
- `updated_at` (timestamp) – last modified time

### 1.4 Technology Stack
- **Language**: Node.js (JavaScript/TypeScript)
- **Framework**: Express.js (lightweight, standard)
- **Database**: SQLite (built-in, no setup)
- **Validation**: Joi or built-in express-validator
- **Structure**: Clean separation of routes, controllers, middleware

### 1.5 Implementation Steps
1. Initialize project (package.json, dependencies)
2. Set up Express server with basic middleware (body parser, CORS)
3. Create SQLite database schema for todos
4. Implement CRUD controllers
5. Wire routes to controller functions
6. Add error handling & validation
7. Write unit tests for core endpoints
8. Document API (OpenAPI/Swagger)

### 1.6 Success Criteria
- API returns 200 for valid requests
- Proper 404 for missing resources
- Proper 201 on creation, 204 on delete
- Todos persist across restarts (SQLite)
- Clear error messages for invalid inputs

## Phase 2 – Execution
- Develop the API following the plan
- Test locally with curl/postman
- Verify persistence and edge cases
- Deploy to staging (optional)

## Phase 3 – Review & Polish
- Add logging
- Improve error messages
- Write README with usage examples
- Final testing
