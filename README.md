# Infloo - Social Networking for Influencers

Infloo is a platform designed to bridge the gap between Influencers, Brands, and Entrepreneurs. It allows users to connect, chat, organize events, and verify their identities for trusted networking.

## Project Flow

The platform operates through two primary interfaces, catering to different user needs:

### 1. Web Dashboard (Django Templates)
*Designed for comprehensive management and analytics.*

-   **Authentication**: Session-based login/registration (`/`).
-   **Analytics**: View platform metrics (Influencer/Brand counts) and recent user activity.
-   **Profile Management**: Update personal details and business information.
-   **Event Management**: View and organize professional events.

### 2. Mobile API (DRF + WebSockets)
*Designed for on-the-go interaction and real-time networking.*

-   **Authentication**: JWT-based (Access/Refresh tokens) via `/api/v1/users/auth`.
-   **Verification**: Upload identity/business proofs for Admin approval (Blue Tick status).
-   **Networking**: Search users, send connection requests, and build your network.
-   **Real-time Engagement**:
    -   **Chat**: Instant messaging with WebSocket support.
    -   **Notifications**: Live alerts for requests and messages.

---

## Getting Started

### Prerequisites
- Python 3.10+
- PostgreSQL (or generic DB url)
- AWS Account (for S3 Media storage, optional)

### Installation
1.  **Clone the repository**:
    ```bash
    git clone https://github.com/adarshatl03/infloo_django.git
    cd infloo_django
    ```
2.  **Create Virtual Environment**:
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```
3.  **Install Dependencies**:
    ```bash
    pip install -r requirements.txt
    ```
4.  **Database Migrations**:
    ```bash
    python manage.py migrate
    ```
5.  **Run Server**:
    ```bash
    python manage.py runserver
    ```
    python manage.py runserver
    ```

### Testing & Verification
The project includes a comprehensive suite of verification scripts in `scripts/`.

1.  **Run End-to-End Test (Auth, Events, Chat, Cleanup)**:
    ```bash
    python scripts/verification/e2e_cleanup.py
    ```
2.  **Generate Dummy Data**:
    ```bash
    python scripts/data/populate.py
    ```

### Configuration
Configuration is managed in `infloo_project/config.py`.
-   **Database**: Update `DATABASE_URL` for your local or remote Postgres instance.
-   **S3 Storage**: Set `USE_S3 = True` and populate AWS Keys to enable cloud media storage.
-   **Security**: Ensure `DEBUG = False` and `SECRET_KEY` is secure in production.

---

## Error Handling

The API uses standard HTTP status codes to indicate success or failure.

| Code  | Meaning | Description |
| :--- | :--- | :--- |
| `200` | OK | Request succeeded. |
| `201` | Created | Resource successfully created. |
| `204` | No Content | Request succeeded, no body returned. |
| `400` | Bad Request | Invalid input (e.g., missing fields, wrong format). |
| `401` | Unauthorized | Authentication credentials missing or invalid. |
| `403` | Forbidden | Authenticated, but permissions denied (e.g., wrong role). |
| `404` | Not Found | Resource does not exist. |
| `500` | Server Error | Something went wrong on the server side. |

**Example Error Response**:
```json
{
    "detail": "Authentication credentials were not provided."
}
```


## API Reference

Base URL: `/api/v1`

### 1. User Management

**Authentication & Profile**

| Method  | Endpoint               | Platforms    | Access | Description                                                                     |
| :------ | :--------------------- | :----------- | :----- | :------------------------------------------------------------------------------ |
| `POST`  | `/users/auth/register` | Mobile & Web | Public | Register new user. Header: None. Body: `username`, `email`, `password`, `role`. |
| `POST`  | `/users/auth/login`    | Mobile & Web | Public | Login. Body: `username`, `password`. Response: `access`, `refresh` tokens.      |
| `POST`  | `/users/auth/refresh`  | Mobile       | Public | Refresh Access Token. Body: `refresh`.                                          |
| `GET`   | `/users/profile`       | Mobile & Web | Auth   | Get current user details. Response includes `is_verified`, `role`.              |
| `PATCH` | `/users/profile`       | Mobile & Web | Auth   | Update profile (Bio, Social Links).                                             |

**Verification**

| Method | Endpoint        | Platforms | Access | Description                                                                   |
| :----- | :-------------- | :-------- | :----- | :---------------------------------------------------------------------------- |
| `GET`  | `/users/verify` | Mobile    | Auth   | List uploaded documents and their status (`pending`, `approved`, `rejected`). |
| `POST` | `/users/verify` | Mobile    | Auth   | Upload document. Body: `file`, `doc_type` ('identity'/'business').            |

### 2. Connections

**Networking**

| Method  | Endpoint                   | Platforms | Access | Description                                                    |
| :------ | :------------------------- | :-------- | :----- | :------------------------------------------------------------- |
| `GET`   | `/connections`             | Mobile    | Auth   | List all connections (sent/received).                          |
| `POST`  | `/connections/request`     | Mobile    | Auth   | Send request. Body: `receiver` (user_id).                      |
| `PATCH` | `/connections/<id>/action` | Mobile    | Auth   | Accept/Reject request. Body: `status` ('accepted'/'rejected'). |

### 3. Events

**Events & Meetups**

| Method | Endpoint            | Platforms    | Access      | Description                                                     |
| :----- | :------------------ | :----------- | :---------- | :-------------------------------------------------------------- |
| `GET`  | `/events`           | Mobile & Web | Public      | List all events.                                                |
| `POST` | `/events`           | Mobile       | Auth        | Create event. Body: `title`, `description`, `date`, `location`. |
| `GET`  | `/events/<id>`      | Mobile & Web | Public/Auth | Get event details.                                              |
| `POST` | `/events/<id>/join` | Mobile       | Auth        | Mark interest/join. Body: `status` ('interested'/'going').      |

### 4. Chat

**Real-time Messaging**

| Method | Endpoint | Platforms | Access | Description                                                                         |
| :----- | :------- | :-------- | :----- | :---------------------------------------------------------------------------------- |
| `GET`  | `/chat`  | Mobile    | Auth   | List messages. Query Param: `?user_id=<id>` to get conversation with specific user. |
| `POST` | `/chat`  | Mobile    | Auth   | Send message. Body: `receiver`, `content`.                                          |

### 5. Notifications

**Alerts**

| Method  | Endpoint                   | Platforms | Access | Description                |
| :------ | :------------------------- | :-------- | :----- | :------------------------- |
| `GET`   | `/notifications`           | Mobile    | Auth   | List all notifications.    |
| `PATCH` | `/notifications/<id>/read` | Mobile    | Auth   | Mark notification as read. |
 
### 6. Stories (Status)
 
**Temporary 24-hour Updates**
 
| Method | Endpoint | Platforms | Access | Description |
| :----- | :------- | :-------- | :----- | :---------- |
| `GET` | `/stories` | Mobile | Auth | **Feed**: List active stories from accepted connections. |
| `POST` | `/stories` | Mobile | Auth | Create story. Body: `content_file` (image/video), `caption` (optional). |
| `GET` | `/stories/my_stories` | Mobile | Auth | List current user's active stories. |

## Frontend Integration (WebSockets)

### 1. Connecting to Chat

To implement live chat, connect to the WebSocket endpoint for a specific room (or user conversation).

**Endpoint**: `ws://<host>/ws/chat/<room_name>/`

```javascript
const roomName = "room1"; // Dynamic room name
const chatSocket = new WebSocket(
  "ws://" + window.location.host + "/ws/chat/" + roomName + "/"
);

chatSocket.onopen = function (e) {
  console.log("Chat connected");
};

chatSocket.onmessage = function (e) {
  const data = JSON.parse(e.data);
  console.log("New Message:", data.message);
  // Update UI with data.message and data.sender
};

chatSocket.onclose = function (e) {
  console.error("Chat socket closed unexpectedly");
};

// Sending a message
function sendMessage(message, sender) {
  chatSocket.send(
    JSON.stringify({
      message: message,
      sender: sender,
    })
  );
}
```

### 2. Live Notifications

To receive real-time alerts, connect to the notification socket.

**Endpoint**: `ws://<host>/ws/notifications/`

```javascript
const notifSocket = new WebSocket(
  "ws://" + window.location.host + "/ws/notifications/"
);

notifSocket.onmessage = function (e) {
  const data = JSON.parse(e.data);
  console.log("Notification:", data);
  /* 
    Example Payload:
    {
        "id": 12,
        "type": "system",
        "content": "Welcome to Infloo!",
        "created_at": "...",
        "is_read": false
    }
    */
  // Show toast or increment badge counter
};
```

### 3. Authentication Strategies

**A. Web Clients (Session-based)**
For clients sharing the domain (like the dashboard), the browser automatically sends the session cookie. No extra configuration needed.

**B. Mobile/External Clients (Token-based)**
Clients using JWT must pass the token as a query parameter.

**Format**: `ws://<host>/ws/<endpoint>/?token=<your_access_token>`

**Example**:

```javascript
const token = "eyJhbGciOi...";
const socket = new WebSocket(
  "ws://" + window.location.host + "/ws/chat/room1/?token=" + token
);
```

> **Note**: This token is validated by the `JwtAuthMiddleware`.
 
 ---
 
 ## App Modules Overview
 
 Detailed breakdown of each Django app and its implemented functionality.
 
 ### 1. Users (`users`)
 **Goal**: Manage authentication, profiles, and identity verification.
 - **Models**:
     - `User`: Extended AbstractUser with `role` (Influencer, Brand, Entrepreneur, Staff).
     - `Profile`: One-to-one link to User. Stores bio, social links, and business category.
     - `Document`: For Blue Tick verification. Stores uploaded proofs (Identity/Business) and status (Pending/Approved).
 - **Key Features**:
     - JWT Authentication.
     - Profile Management (Bio, Avatar).
     - Document Verification flow for admins.
 
 ### 2. Connections (`connections`)
 **Goal**: Build the social graph.
 - **Models**:
     - `Connection`: Stores `sender`, `receiver`, and `status`.
 - **Key Features**:
     - Unidirectional or Bidirectional graph (currently Request -> Accept flow).
     - Status: `pending`, `accepted`, `rejected`.
     - Filters visibility of content (Events, Stories) based on connection status.
    - **Automatic Notifications**: Alerts users on Request Sent and Request Accepted.
 
 ### 3. Stories (`stories`)
 **Goal**: Temporary content sharing (Status Updates).
 - **Models**:
     - `Story`: User-generated content with `content_file`, `caption`, and fixed `expires_at` (24h).
 - **Key Features**:
     - **Feed Algorithm**: Shows active stories from `accepted` connections only.
     - **Auto-Expiration**: Content is hidden after 24 hours.
     - **Media Support**: Unified upload for Images and Videos.
 
 ### 4. Events (`events`)
 **Goal**: Professional networking meetups.
 - **Models**:
     - `Event`: Hosted by a User. Includes Date, Location, Title.
     - `EventAttendee`: Tracks participation (`interested`, `going`, `invited`).
 - **Key Features**:
     - CRUD for events.
     - RSVP system for attendees.
     - Integration with Dashboard for analytics.
     - **Automatic Notifications**: Alerts connections when a user Hosts or Joins an event.
 
 ### 5. Chat (`chat`)
 **Goal**: Real-time communication.
 - **Models**:
     - `Message`: Stores plain text content and optional attachments. Linked to Sender/Receiver.
 - **Key Features**:
     - **WebSocket Integration**: Live delivery using Django Channels (`daphne`).
     - **Persisted History**: Messages are saved to DB for retrieval on load.
     - **Attachments**: File upload support in chat stream.
 
 ### 6. Notifications (`notifications`)
 **Goal**: System-wide alerts.
 - **Models**:
     - `Notification`: Types include Follow Requests, Event Invites, Messages, System alerts.
 - **Key Features**:
     - Real-time push via WebSockets.
     - Read/Unread status tracking.
 
 ### 7. Dashboard (`dashboard`)
 **Goal**: Web-based administration and analytics interface.
 - **Tech**: Django Templates (Server-Side Rendering).
 - **Key Features**:
     - **Analytics**: Platform stats (Total Influencers/Brands).
     - **User Management**: Profile editing forms.
     - **Event Browsing**: visual list of active events.

---

## Detailed Feature Workflows

This section outlines the step-by-step logic and interaction flow for key platform features.

### 1. Authentication & User Management
**Lifecycle**: `Guest` -> `User` -> `Profile`

1.  **Registration**: Guest creates an account (`POST /api/v1/users/auth/register`).
2.  **Login**: User logs in to get tokens (`POST /api/v1/users/auth/login`).
3.  **Profile**: User views or updates their details (`GET/PATCH /api/v1/users/profile`).

**Scenario & API Flow**:
> **Scenario**: Alice joins Infloo.
> **1. Register**:
> `POST /api/v1/users/auth/register`
> ```json
> { "username": "alice", "email": "alice@test.com", "password": "secure123", "role": "influencer" }
> ```
> **2. Login**:
> `POST /api/v1/users/auth/login`
> ```json
> { "access": "eyJ...", "refresh": "eyJ..." }
> ```
> **3. Update Bio**:
> `PATCH /api/v1/users/profile`
> ```json
> { "bio": "Lifestyle Vlogger from NYC" }
> ```

### 2. Verification (Blue Tick)
**Lifecycle**: `User` -> `Document` -> `Admin`

1.  **Submission**: User uploads a document (Identity/Business) via `POST /api/v1/users/verify`.
    -   *Status*: Starts as `pending`.
2.  **Review**: Admin reviews the document via Django Admin or Dashboard.
    -   *Action*: Admin updates status to `approved` or `rejected`.
3.  **Result**:
    -   If `approved`: User's `is_verified` flag is set to `True` (Blue Tick appears).
    -   If `rejected`: User can try uploading again.
    
**Scenario & API Flow**:
> **Scenario**: Alice uploads her Driving License.
> **1. API Request**:
> `POST /api/v1/users/verify`
> ```json
> {
>   "doc_type": "identity",
>   "file": "(binary)" 
> }
> ```
> **2. Admin Action**: (Dashboard) -> Updates status to `approved`.
> **3. Result**: `GET /api/v1/users/profile` -> `{"is_verified": true}`

### 3. Networking (Connections)
**Lifecycle**: `Sender` -> `Receiver` -> `Connection`

1.  **Request**: User A sends a request to User B via `POST /api/v1/connections/request`.
    -   *Status*: `pending`.
    -   *Notification*: User B receives a "Follow Request" alert.
2.  **Action**: User B accepts or rejects via `PATCH /api/v1/connections/{id}/action`.
3.  **Outcome**:
    -   `accepted`: Users are now "connected". Content becomes visible. *Notification*: User A receives "Request Accepted" alert.
    -   `rejected`: Request is closed. No link is formed.

**Scenario & API Flow**:
> **Scenario**: Bob sends a request to Alice.
> **1. API Request**:
> `POST /api/v1/connections/request`
> ```json
> { "receiver": 12 } // Alice's ID
> ```
> **Notification (Alice)**:
> ```json
> { "type": "follow", "message": "Bob wants to follow you." }
> ```
> **2. Alice Accepts**:
> `PATCH /api/v1/connections/101/action`
> ```json
> { "status": "accepted" }
> ```
> **Notification (Bob)**:
> ```json
> { "type": "system", "message": "Alice accepted your follow request." }
> ```

### 4. Events & Notifications
**Lifecycle**: `Host` -> `Event` -> `Attendees`

1.  **Creation**: User creates an event (`POST /api/v1/events`).
    -   *Trigger**: System finds all `accepted` connections of the Host.
    -   **Alert**: Sends a specific `event` notification to all friends: *"User is hosting..."*.
2.  **Participation**: Another user RSVPs as `going` or `interested` (`POST /api/v1/events/{id}/join`).
    -   **Trigger**: System finds all connections of the Participant.
    -   **Alert**: Sends an update to friends: *"User is going to..."*.

**Scenario & API Flow**:
> **Scenario**: Alice creates "Summer Gala". Bob joins.
> **1. Alice Hosts**:
> `POST /api/v1/events`
> ```json
> { "title": "Summer Gala", "date": "2025-06-01T10:00:00Z", "location": "NYC" }
> ```
> **2. Notification (Bob's WebSocket)**:
> ```json
> { "type": "event", "message": "alice_influencer is hosting a new event: Summer Gala" }
> ```
> **3. Bob Joins**:
> `POST /api/v1/events/55/join`
> ```json
> { "status": "going" }
> ```

### 5. Stories (Status Updates)
**Lifecycle**: `User` -> `Story` -> `Access Control`

1.  **Upload**: User uploads an image/video via `POST /api/v1/stories`.
    -   *Metadata*: `created_at` is set to now. `expires_at` is strictly `now + 24h` (Backend forced).
2.  **Feed Generation**: When User B requests the feed (`GET /api/v1/stories`):
    -   System checks: "Is User B connected to User A?" AND "Is Story < 24h old?".
    -   *Result*: Only active stories from friends are returned.
3.  **Expiration**: Once `now > expires_at`, the story is automatically filtered out from all feeds.

**Scenario & API Flow**:
> **Scenario**: Alice posts a photo.
> **1. Upload**:
> `POST /api/v1/stories`
> ```json
> { "caption": "Morning Coffee", "content_file": "(binary)" }
> ```
> **2. Bob Views Feed**:
> `GET /api/v1/stories`
> ```json
> [
>   {
>     "id": 88,
>     "user": "alice_influencer",
>     "caption": "Morning Coffee",
>     "expires_at": "2025-12-21T10:00:00Z"
>   }
> ]
> ```

### 6. Chat (Real-time & History)
**Lifecycle**: `WebSocket` <-> `REST API`

1.  **Live Messaging** (WebSockets):
    -   Endpoint: `ws://<host>/ws/chat/<room_name>/`
    -   Flow: Users connect -> Send JSON message -> Server broadcasts to room.
    -   *Purpose*: Instant UI updates (Typing indicators, live bubbles).
2.  **Persistence** (REST API):
    -   Endpoint: `POST /api/v1/chat`
    -   Flow: Client sends message payload to API -> Saved to DB (`Message` model).
    -   *Purpose*: Long-term history retrieval (`GET /chat/?user_id=x`).
    -   *Note*: A robust client typically sends to API first, then broadcasts to WS, or lets the server handle the WS broadcast after saving.

**Scenario & API Flow**:
> **Scenario**: Alice types "Hi Bob".
> **1. Save to DB**:
> `POST /api/v1/chat`
> ```json
> { "receiver": 12, "content": "Hi Bob" }
> ```
> **2. Broadcast for UI**:
> `WebSocket Send`
> ```json
> { "message": "Hi Bob", "sender": "alice" }
> ```
> **3. Retrieve History**:
> `GET /api/v1/chat/?user_id=12`
> ```json
> [ { "id": 5, "content": "Hi Bob", "timestamp": "..." } ]
> ```

### 7. Notifications Management
**Lifecycle**: `Event/System` -> `Notification` -> `ReadStatus`

1.  **List**: User checks their alerts (`GET /api/v1/notifications`).
2.  **Action**: User marks an alert as read (`PATCH /api/v1/notifications/{id}/read`).

**Scenario & API Flow**:
> **Scenario**: Alice views her alerts.
> **1. Get List**:
> `GET /api/v1/notifications`
> ```json
> [ { "id": 99, "type": "follow_request", "message": "Bob wants to follow you", "is_read": false } ]
> ```
> **2. Mark Read**:
> `PATCH /api/v1/notifications/99/read`

---

## Troubleshooting

### Common Issues

#### 1. Database Connection Timeout
If you see `psycopg2.OperationalError: connection to server... timed out`, check your **AWS RDS Settings**:
-   **Publicly Accessible**: Must be set to `Yes`.
-   **Security Group**: Ensure the inbound rule allows TCP on port `5432` from your IP address (`0.0.0.0/0` for broad access, or your specific IP).
-   **VPC**: Ensure the database is in a public subnet or has an Internet Gateway attached.

#### 2. WebSocket Connection Failure
If WebSockets fail to connect (`Handshake status 403`):
-   Ensure you are passing the `?token=...` query parameter for mobile clients.
-   Check that `ALLOWED_HOSTS` includes your domain or `*`.

