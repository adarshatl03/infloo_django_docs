# Infloo API Workflow Sequence

This document outlines the API calls executed in the full system test, presented in the order of a user journey.

## Response Standard
All API responses follow a consistent structure for success and failure.

**Success (200/201):**
```json
{
  "message": "Human readable success message.",
  "data": { ...object or list... }
}
```

**Error (400/401/403/404/500):**
```json
{
  "error": "Short error description or type.",
  "details": { "field_name": ["Specific validation error"] } // Optional
}
```

---

## 1. Prerequisite: Get User Types
**Endpoint:** `GET /api/v1/user-types`

**Description:** Fetch available user roles for registration.

**Response (200 OK):**
```json
{
  "message": "User types retrieved successfully.",
  "data": [
    { "id": 1, "name": "Influencer", "slug": "influencer" },
    { "id": 2, "name": "Brand", "slug": "brand" }
  ]
}
```

---

## 2. User Registration
**Endpoint:** `POST /api/v1/users/auth/register/`

**Description:** Registers a new user with a specific role.

**Payload:**
```json
{
  "username": "brand1",
  "email": "brand@infloo.in",
  "password": "Secret@123",
  "user_type_id": 2,  
  "phone": "1234567890"
}
```

**Response (201 Created):**
```json
{
  "message": "Registration successful.",
  "data": {
    "id": 2,
    "username": "brand1",
    "email": "brand@infloo.in",
    "role": "brand",
    "is_active": true
  }
}
```

**Error Response (400 Bad Request):**
```json
{
  "error": "Validation Error",
  "details": {
    "username": ["A user with that username already exists."],
    "password": ["This password is too short."]
  }
}
```

---

## 3. Document Upload (Onboarding)
**Endpoint:** `POST /api/v1/users/verify/`

**Description:** User uploads a document for identity/business verification.

**Headers:** `Authorization: Bearer <token>`

**Payload:** `type="business"`, `file=<binary>`

**Response (201 Created):**
```json
{
  "message": "Document uploaded successfully.",
  "data": {
    "id": 1,
    "user": 2,
    "type": "business",
    "status": "pending"
  }
}
```

**Error Response (401 Unauthorized):**
```json
{
  "detail": "Authentication credentials were not provided."
}
```

---

## 4. Create Event
**Endpoint:** `POST /api/v1/events/`

**Description:** Brand user creates a new event.

**Headers:** `Authorization: Bearer <token>`

**Payload:**
```json
{
  "title": "Summer Launch",
  "description": "Launching our new summer collection.",
  "date": "2025-06-01T18:00:00Z",
  "location": "New York, NY",
  "capacity": 100
}
```

**Response (201 Created):**
```json
{
  "message": "Event created successfully.",
  "data": {
    "id": 1,
    "title": "Summer Launch",
    "host": { "username": "brand1" }
  }
}
```

**Error Response (403 Forbidden):**
*If an Influencer tries to create an event:*
```json
{
  "detail": "You do not have permission to perform this action."
}
```

---

## 5. Join Event
**Endpoint:** `POST /api/v1/events/{id}/join/`

**Description:** A user (Influencer/Entrepreneur) joins an event.

**Headers:** `Authorization: Bearer <token>`

**Payload:** `{"status": "interested"}`

**Response (200 OK):**
```json
{
  "message": "You have joined the event successfully.",
  "data": { "id": 1, "status": "interested" }
}
```

**Error Response (403 Forbidden):**
*If a Brand user tries to join an event:*
```json
{
  "error": "Brands cannot join events."
}
```

---

## 6. Connection Request
**Endpoint:** `POST /api/v1/connections/request/`

**Description:** User sends a connection request to another user.

**Payload:** `{"receiver_id": 2}`

**Response (201 Created):**
```json
{
  "message": "Connection request sent successfully.",
  "data": { "status": "pending" }
}
```

**Error Response (400 Bad Request):**
```json
{
  "error": "Validation Error",
  "details": { "non_field_errors": ["Connection request already sent."] }
}
```

---

## 7. Connection Action
**Endpoint:** `PATCH /api/v1/connections/{id}/action/`

**Payload:** `{"status": "accepted"}`

**Response (200 OK):**
```json
{
  "message": "Request accepted successfully.",
  "data": { "status": "accepted" }
}
```

**Error Response (404 Not Found):**
```json
{
  "detail": "Not found."
}
```

---

## 8. Chat Message
**Endpoint:** `POST /api/v1/chat/`

**Payload:** `{"receiver_id": 2, "content": "Hello"}`

**Response (201 Created):**
```json
{
  "message": "Message sent.",
  "data": { "content": "Hello", "timestamp": "..." }
}
```

**Error Response (400 Bad Request):**
```json
{
  "error": "Validation Error",
  "details": { "receiver_id": ["This field is required."] }
}
```

---

## 9. Post Story
**Endpoint:** `POST /api/v1/stories/`

**Payload:** `content_file`, `caption`

**Response (201 Created):**
```json
{
  "message": "Story created successfully.",
  "data": { "id": 1, "expires_at": "..." }
}
```

**Error Response (400 Bad Request):**
```json
{
  "error": "Validation Error",
  "details": { "content_file": ["No file was submitted."] }
}
```

---

# WebSocket API

## 1. Chat Socket
**URL:** `ws://<host>/ws/chat/<room_name>`

**Events (Receive):**
```json
{
  "message": "Hello world",
  "sender": "brand1"
}
```

**Error (Socket Close):**
*   Code 4003: Authentication failed.

---

# Additional API Reference

## Users Module
*   `POST /api/v1/users/auth/login`
    *   **Error (401):** `{"detail": "No active account found with the given credentials"}`
*   `GET /api/v1/users/profile`
*   `PATCH /api/v1/users/profile`
*   `GET /api/v1/users/profile/{username}`

## Events Module
*   `GET /api/v1/events`
*   `GET /api/v1/events/{id}`
    *   **Error (404):** `{"detail": "Not found."}`
*   `PUT /api/v1/events/{id}`
*   `DELETE /api/v1/events/{id}`

## Connections Module
*   `GET /api/v1/connections`

## Chat Module
*   `GET /api/v1/chat`

## Stories Module
*   `GET /api/v1/stories`
*   `DELETE /api/v1/stories/{id}`

## Notifications Module
*   `GET /api/v1/notifications`
*   `POST /api/v1/notifications/read`
