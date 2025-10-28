# Task Management System API Documentation

## Base URL
```
Production: https://your-app-name.onrender.com
Development: http://localhost:3000
```

## Authentication

This API uses JWT (JSON Web Token) for authentication. Include the token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

**Token Expiration:** 7 days

## Response Format

All API responses follow this standard format:

```json
{
  "success": boolean,
  "message": "string (optional)",
  "data": object | array (optional),
  "count": number (for list endpoints),
  "error": "string (on error)"
}
```

## Status Codes

- `200` - OK (successful GET, PUT, DELETE)
- `201` - Created (successful POST)
- `400` - Bad Request (validation errors)
- `401` - Unauthorized (missing/invalid token)
- `404` - Not Found
- `500` - Internal Server Error

---

## Authentication Endpoints

### Register User
**POST** `/api/auth/register`

Create a new user account.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "name": "John Doe"
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "cuid_string",
      "email": "user@example.com",
      "name": "John Doe",
      "createdAt": "2024-01-01T10:00:00.000Z",
      "updatedAt": "2024-01-01T10:00:00.000Z"
    },
    "token": "jwt_token_string"
  }
}
```

**Error Response (400):**
```json
{
  "success": false,
  "message": "User already exists"
}
```

### Login User
**POST** `/api/auth/login`

Authenticate user and receive JWT token.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "cuid_string",
      "email": "user@example.com",
      "name": "John Doe",
      "createdAt": "2024-01-01T10:00:00.000Z",
      "updatedAt": "2024-01-01T10:00:00.000Z"
    },
    "token": "jwt_token_string"
  }
}
```

**Error Response (401):**
```json
{
  "success": false,
  "message": "Invalid credentials"
}
```

### Get Current User Profile
**GET** `/api/auth/me`

Get authenticated user's profile information.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "cuid_string",
    "email": "user@example.com",
    "name": "John Doe"
  }
}
```

---

## Task Endpoints

> **Note:** All task endpoints require authentication. Include the Authorization header with Bearer token.

### Get All Tasks
**GET** `/api/tasks`

Retrieve all tasks for the authenticated user.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "success": true,
  "count": 2,
  "data": [
    {
      "id": "task_id_1",
      "title": "Complete API Documentation",
      "description": "Write comprehensive API docs",
      "status": "in_progress",
      "priority": "high",
      "dueDate": "2024-01-15T23:59:59.000Z",
      "assignedTo": "john@example.com",
      "createdAt": "2024-01-01T10:00:00.000Z",
      "updatedAt": "2024-01-01T12:00:00.000Z",
      "userId": "user_id",
      "subtasks": [
        {
          "id": "subtask_id_1",
          "title": "Research API standards",
          "description": "Look into REST API best practices",
          "completed": true,
          "taskId": "task_id_1",
          "createdAt": "2024-01-01T10:30:00.000Z",
          "updatedAt": "2024-01-01T11:00:00.000Z"
        }
      ]
    }
  ]
}
```

### Get Task by ID
**GET** `/api/tasks/{id}`

Retrieve a specific task by ID.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Parameters:**
- `id` (string) - Task ID

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "task_id_1",
    "title": "Complete API Documentation",
    "description": "Write comprehensive API docs",
    "status": "in_progress",
    "priority": "high",
    "dueDate": "2024-01-15T23:59:59.000Z",
    "assignedTo": "john@example.com",
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T12:00:00.000Z",
    "userId": "user_id",
    "subtasks": []
  }
}
```

**Error Response (404):**
```json
{
  "success": false,
  "error": "Task not found"
}
```

### Create Task
**POST** `/api/tasks`

Create a new task.

**Headers:**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body:**
```json
{
  "title": "New Task Title",
  "description": "Task description",
  "status": "pending",
  "priority": "medium",
  "dueDate": "2024-01-15T23:59:59.000Z",
  "assignedTo": "john@example.com",
  "subtasks": [
    {
      "title": "Subtask 1",
      "description": "First subtask",
      "completed": false
    }
  ]
}
```

**Field Descriptions:**
- `title` (string, required) - Task title
- `description` (string, required) - Task description
- `status` (string, required) - One of: `pending`, `in-progress`, `completed`, `cancelled`
- `priority` (string, required) - One of: `low`, `medium`, `high`, `urgent`
- `dueDate` (string, optional) - ISO 8601 date string
- `assignedTo` (string, optional) - Email of assigned user
- `subtasks` (array, optional) - Array of subtask objects

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "new_task_id",
    "title": "New Task Title",
    "description": "Task description",
    "status": "pending",
    "priority": "medium",
    "dueDate": "2024-01-15T23:59:59.000Z",
    "assignedTo": "john@example.com",
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T10:00:00.000Z",
    "userId": "user_id",
    "subtasks": [
      {
        "id": "subtask_id",
        "title": "Subtask 1",
        "description": "First subtask",
        "completed": false,
        "taskId": "new_task_id",
        "createdAt": "2024-01-01T10:00:00.000Z",
        "updatedAt": "2024-01-01T10:00:00.000Z"
      }
    ]
  }
}
```

### Update Task
**PUT** `/api/tasks/{id}`

Update an existing task.

**Headers:**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Parameters:**
- `id` (string) - Task ID

**Request Body:** (All fields optional)
```json
{
  "title": "Updated Task Title",
  "description": "Updated description",
  "status": "completed",
  "priority": "high",
  "dueDate": "2024-01-20T23:59:59.000Z",
  "assignedTo": "jane@example.com"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "task_id",
    "title": "Updated Task Title",
    "description": "Updated description",
    "status": "completed",
    "priority": "high",
    "dueDate": "2024-01-20T23:59:59.000Z",
    "assignedTo": "jane@example.com",
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T15:00:00.000Z",
    "userId": "user_id",
    "subtasks": []
  }
}
```

### Delete Task
**DELETE** `/api/tasks/{id}`

Delete a task and all its subtasks.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Parameters:**
- `id` (string) - Task ID

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "deleted_task_id",
    "title": "Deleted Task",
    "description": "This task was deleted",
    "status": "pending",
    "priority": "medium",
    "dueDate": null,
    "assignedTo": null,
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T10:00:00.000Z",
    "userId": "user_id",
    "subtasks": []
  }
}
```

---

## Subtask Endpoints

### Get Subtasks for Task
**GET** `/api/tasks/{taskId}/subtasks`

Get all subtasks for a specific task.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Parameters:**
- `taskId` (string) - Parent task ID

**Response (200):**
```json
{
  "success": true,
  "count": 2,
  "data": [
    {
      "id": "subtask_id_1",
      "title": "Subtask 1",
      "description": "First subtask",
      "completed": false,
      "taskId": "parent_task_id",
      "createdAt": "2024-01-01T10:00:00.000Z",
      "updatedAt": "2024-01-01T10:00:00.000Z"
    }
  ]
}
```

### Get Subtask by ID
**GET** `/api/subtasks/{id}`

Get a specific subtask by ID.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Parameters:**
- `id` (string) - Subtask ID

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "subtask_id",
    "title": "Subtask Title",
    "description": "Subtask description",
    "completed": true,
    "taskId": "parent_task_id",
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T12:00:00.000Z",
    "task": {
      "id": "parent_task_id",
      "title": "Parent Task",
      "userId": "user_id"
    }
  }
}
```

### Create Subtask
**POST** `/api/tasks/{taskId}/subtasks`

Create a new subtask for a specific task.

**Headers:**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Parameters:**
- `taskId` (string) - Parent task ID

**Request Body:**
```json
{
  "title": "New Subtask",
  "description": "Subtask description",
  "completed": false
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "new_subtask_id",
    "title": "New Subtask",
    "description": "Subtask description",
    "completed": false,
    "taskId": "parent_task_id",
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T10:00:00.000Z"
  }
}
```

### Update Subtask
**PUT** `/api/subtasks/{id}`

Update an existing subtask.

**Headers:**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Parameters:**
- `id` (string) - Subtask ID

**Request Body:** (All fields optional)
```json
{
  "title": "Updated Subtask Title",
  "description": "Updated description",
  "completed": true
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "subtask_id",
    "title": "Updated Subtask Title",
    "description": "Updated description",
    "completed": true,
    "taskId": "parent_task_id",
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T15:00:00.000Z"
  }
}
```

### Delete Subtask
**DELETE** `/api/subtasks/{id}`

Delete a specific subtask.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Parameters:**
- `id` (string) - Subtask ID

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "deleted_subtask_id",
    "title": "Deleted Subtask",
    "description": "This subtask was deleted",
    "completed": false,
    "taskId": "parent_task_id",
    "createdAt": "2024-01-01T10:00:00.000Z",
    "updatedAt": "2024-01-01T10:00:00.000Z"
  }
}
```

---

## Protected Route Example

### Test Protected Route
**GET** `/api/protected`

A test endpoint to verify authentication is working.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "success": true,
  "message": "This is a protected route",
  "user": {
    "id": "user_id",
    "email": "user@example.com",
    "name": "John Doe"
  }
}
```

---

## Data Models

### User Model
```typescript
{
  id: string          // Unique identifier
  email: string       // User email (unique)
  name: string        // User's full name
  createdAt: DateTime // Account creation timestamp
  updatedAt: DateTime // Last update timestamp
}
```

### Task Model
```typescript
{
  id: string               // Unique identifier
  title: string            // Task title
  description: string      // Task description
  status: TaskStatus       // pending | in_progress | completed | cancelled
  priority: Priority       // low | medium | high | urgent
  dueDate: DateTime?       // Optional due date
  assignedTo: string?      // Optional assigned user email
  createdAt: DateTime      // Creation timestamp
  updatedAt: DateTime      // Last update timestamp
  userId: string           // Owner user ID
  subtasks: Subtask[]      // Array of subtasks
}
```

### Subtask Model
```typescript
{
  id: string          // Unique identifier
  title: string       // Subtask title
  description: string // Subtask description
  completed: boolean  // Completion status
  taskId: string      // Parent task ID
  createdAt: DateTime // Creation timestamp
  updatedAt: DateTime // Last update timestamp
}
```

---

## Error Handling

### Common Error Responses

**Authentication Error (401):**
```json
{
  "success": false,
  "message": "Access token required"
}
```

**Invalid Token (401):**
```json
{
  "success": false,
  "message": "Invalid token"
}
```

**Token Expired (401):**
```json
{
  "success": false,
  "message": "Token expired"
}
```

**Resource Not Found (404):**
```json
{
  "success": false,
  "error": "Task not found"
}
```

**Access Denied (404):**
```json
{
  "success": false,
  "error": "Task not found or access denied"
}
```

**Server Error (500):**
```json
{
  "success": false,
  "message": "Something went wrong!",
  "error": "Error details (development only)"
}
```

---

## Notes

1. **Status Conversion**: When sending task status in requests, use kebab-case (`in-progress`). The API automatically converts it to snake_case (`in_progress`) for database storage.

2. **Date Format**: All dates should be in ISO 8601 format (e.g., `2024-01-15T23:59:59.000Z`).

3. **User Isolation**: Users can only access their own tasks and subtasks. The API automatically filters data based on the authenticated user.

4. **Cascade Deletion**: Deleting a task automatically deletes all its subtasks.

5. **Content-Type**: Always include `Content-Type: application/json` header for POST and PUT requests.

6. **CORS**: The API supports CORS for cross-origin requests.

---

## Environment Variables

The following environment variables are required:

- `DATABASE_URL` - PostgreSQL database connection string
- `JWT_SECRET` - Secret key for JWT token signing
- `PORT` - Server port (default: 3000)
- `NODE_ENV` - Environment (development/production)

---

## Getting Started

1. **Register a new user** using `/api/auth/register`
2. **Login** using `/api/auth/login` to get your JWT token
3. **Include the token** in the Authorization header for all subsequent requests
4. **Create tasks** using `/api/tasks`
5. **Add subtasks** to your tasks using `/api/tasks/{taskId}/subtasks`
6. **Manage your tasks** using the various CRUD endpoints

## Rate Limiting

Currently, there are no rate limits implemented. In production, consider implementing rate limiting to prevent abuse.

## Support

For issues or questions, please refer to the project repository or contact the development team.