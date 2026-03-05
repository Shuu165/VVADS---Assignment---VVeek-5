# VVADS---Assignment---VVeek-5
# 📓 Assignment Log Book API

A RESTful API built with **Next.js 14 App Router** for managing student assignment records.  
Includes full CRUD operations, input validation, Swagger/OpenAPI documentation, and a complete test suite.

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- npm or yarn

### Installation

```bash
git clone https://github.com/YOUR_USERNAME/assignment-logbook.git
cd assignment-logbook
npm install
```

### Run Development Server

```bash
npm run dev
```

Server starts at: **http://localhost:3000**

### Run Tests

> Make sure the dev server is running first.

```bash
npm test
# or: node tests/run-tests.js
```

---

## 📌 API Design Table

Base URL: `http://localhost:3000`

| # | Method   | Endpoint                    | Description                          | Request Body         | Success Response         | Error Responses             |
|---|----------|-----------------------------|--------------------------------------|----------------------|--------------------------|-----------------------------|
| 1 | `GET`    | `/api/assignments`          | List all assignments                 | —                    | `200` list of assignments | `500` internal error        |
| 2 | `POST`   | `/api/assignments`          | Create a new assignment              | JSON (see below)     | `201` created assignment  | `400` bad JSON, `422` validation error, `500` |
| 3 | `GET`    | `/api/assignments/:id`      | Get a single assignment by ID        | —                    | `200` assignment object   | `404` not found, `500`      |
| 4 | `PUT`    | `/api/assignments/:id`      | Update an existing assignment        | JSON (partial or full) | `200` updated assignment | `400`, `404`, `422`, `500`  |
| 5 | `DELETE` | `/api/assignments/:id`      | Delete an assignment by ID           | —                    | `200` success message     | `404` not found, `500`      |
| 6 | `GET`    | `/api/docs/openapi.json`    | Swagger/OpenAPI specification        | —                    | `200` OpenAPI 3.0.3 JSON  | —                           |

---

## 🔍 Query Parameters (GET /api/assignments)

| Parameter  | Type     | Description                                         | Example                      |
|------------|----------|-----------------------------------------------------|------------------------------|
| `status`   | `string` | Filter by status (`pending`, `in-progress`, `completed`, `overdue`) | `?status=completed` |
| `priority` | `string` | Filter by priority (`low`, `medium`, `high`)        | `?priority=high`             |
| `subject`  | `string` | Substring search on subject (case-insensitive)      | `?subject=Database`          |

---

## 📦 Data Model

### Assignment Object

| Field         | Type                | Required | Description                                       |
|---------------|---------------------|----------|---------------------------------------------------|
| `id`          | `string` (UUID)     | Auto     | Unique identifier                                 |
| `title`       | `string`            | ✅ Yes   | Assignment title                                  |
| `subject`     | `string`            | ✅ Yes   | Course/subject name                               |
| `description` | `string`            | No       | Detailed description of the assignment            |
| `dueDate`     | `string` (YYYY-MM-DD) | ✅ Yes | Due date in ISO date format                       |
| `status`      | `enum`              | No       | `pending` \| `in-progress` \| `completed` \| `overdue` (default: `pending`) |
| `priority`    | `enum`              | No       | `low` \| `medium` \| `high` (default: `medium`)   |
| `score`       | `number \| null`    | No       | Grade score 0–100, or null if ungraded            |
| `notes`       | `string`            | No       | Additional personal notes                         |
| `createdAt`   | `string` (ISO 8601) | Auto     | Creation timestamp                                |
| `updatedAt`   | `string` (ISO 8601) | Auto     | Last update timestamp                             |

---

## 📬 API Endpoints

### 1. `GET /api/assignments` — List All Assignments

Returns all assignments, optionally filtered.

**Response `200 OK`:**
```json
{
  "success": true,
  "count": 3,
  "data": [
    {
      "id": "a1b2c3d4-0001-0000-0000-000000000001",
      "title": "REST API Design with Next.js",
      "subject": "Web Development",
      "description": "Build a full CRUD REST API using Next.js App Router.",
      "dueDate": "2025-04-10",
      "status": "in-progress",
      "priority": "high",
      "score": null,
      "notes": "Remember to add error handling.",
      "createdAt": "2025-03-01T08:00:00.000Z",
      "updatedAt": "2025-03-05T10:00:00.000Z"
    }
  ]
}
```

---

### 2. `POST /api/assignments` — Create Assignment

**Request Body:**
```json
{
  "title": "Machine Learning Report",
  "subject": "Artificial Intelligence",
  "description": "Explore supervised learning algorithms.",
  "dueDate": "2025-04-15",
  "status": "in-progress",
  "priority": "high",
  "score": null,
  "notes": "Focus on neural networks."
}
```

**Response `201 Created`:**
```json
{
  "success": true,
  "message": "Assignment created successfully",
  "data": { "id": "...", "title": "Machine Learning Report", "..." }
}
```

**Response `422 Unprocessable Entity` (validation error):**
```json
{
  "success": false,
  "message": "Validation failed",
  "errors": [
    { "field": "dueDate", "message": "dueDate is required and must be in YYYY-MM-DD format" }
  ]
}
```

---

### 3. `GET /api/assignments/:id` — Get Assignment by ID

**Response `200 OK`:**
```json
{
  "success": true,
  "data": { "id": "a1b2c3d4-0001-0000-0000-000000000001", "title": "...", "..." }
}
```

**Response `404 Not Found`:**
```json
{
  "success": false,
  "message": "Assignment with id 'xyz' not found"
}
```

---

### 4. `PUT /api/assignments/:id` — Update Assignment

All fields optional. At least one must be provided.

**Request Body (partial update):**
```json
{
  "status": "completed",
  "score": 95
}
```

**Response `200 OK`:**
```json
{
  "success": true,
  "message": "Assignment updated successfully",
  "data": { "id": "...", "status": "completed", "score": 95, "..." }
}
```

---

### 5. `DELETE /api/assignments/:id` — Delete Assignment

**Response `200 OK`:**
```json
{
  "success": true,
  "message": "Assignment 'REST API Design with Next.js' deleted successfully"
}
```

---

## 🔴 HTTP Status Codes Used

| Code  | Meaning                    | When Used                                    |
|-------|----------------------------|----------------------------------------------|
| `200` | OK                         | Successful GET, PUT, DELETE                  |
| `201` | Created                    | Successful POST                              |
| `400` | Bad Request                | Malformed/invalid JSON body                  |
| `404` | Not Found                  | Assignment ID does not exist                 |
| `422` | Unprocessable Entity       | Body is valid JSON but fails validation      |
| `500` | Internal Server Error      | Unexpected server-side errors                |

---

## 📄 Swagger / API Docs

Interactive Swagger UI available at:

```
http://localhost:3000/api-docs
```

Raw OpenAPI 3.0.3 spec:

```
http://localhost:3000/api/docs/openapi.json
```

---

## 🧪 Test Coverage

The test suite in `tests/run-tests.js` covers **success** and **error** scenarios for all endpoints:

| Suite               | ✅ Success Scenarios                           | ❌ Error Scenarios                             |
|---------------------|------------------------------------------------|------------------------------------------------|
| `GET /assignments`  | List all, filter by status/priority/subject    | —                                              |
| `POST /assignments` | Minimal payload, full payload                  | Missing fields, bad date format, invalid status/score, empty body, invalid JSON |
| `GET /:id`          | Existing seed ID, newly created ID             | Non-existent ID                                |
| `PUT /:id`          | Partial update, score+notes, full update       | Non-existent ID, invalid status/score, empty body, bad JSON |
| `DELETE /:id`       | Delete existing, verify gone                   | Non-existent ID, double delete                 |
| `GET openapi.json`  | Valid OpenAPI 3.0.3 spec structure             | —                                              |

---

## 🗂 Project Structure

```
assignment-logbook/
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   ├── assignments/
│   │   │   │   ├── route.ts              # GET, POST /api/assignments
│   │   │   │   └── [id]/
│   │   │   │       └── route.ts          # GET, PUT, DELETE /api/assignments/:id
│   │   │   └── docs/
│   │   │       └── openapi.json/
│   │   │           └── route.ts          # OpenAPI spec endpoint
│   │   ├── api-docs/
│   │   │   └── page.tsx                  # Swagger UI page
│   │   ├── layout.tsx
│   │   └── page.tsx
│   └── lib/
│       ├── store.ts                      # In-memory data store + CRUD helpers
│       └── validation.ts                 # Request body validation
├── tests/
│   └── run-tests.js                      # Full test suite (success + error)
├── next.config.js
├── package.json
├── tsconfig.json
└── README.md
```

---

## ⚙️ Tech Stack

| Technology       | Purpose                            |
|------------------|------------------------------------|
| Next.js 14       | Framework & App Router             |
| TypeScript       | Type safety                        |
| In-Memory Store  | Data persistence (dev only)        |
| OpenAPI 3.0.3    | API specification standard         |
| Swagger UI       | Interactive API documentation      |
| Node.js `fetch`  | Test runner HTTP client            |

> **Note:** The in-memory store resets on server restart. For production, replace `src/lib/store.ts` with a real database (PostgreSQL, MongoDB, etc.).

---

## 👨‍💻 Author

**YOUR NAME HERE**  
Student Assignment Log Book — Web Development Assignment
