# Infloo - Influencer Connection Platform

**Infloo** is a comprehensive backend system designed to bridge the gap between Influencers, Brands, and Entrepreneurs. Built with Django and Django Rest Framework (DRF), it facilitates seamless connections, event management, real-time messaging, and story sharing.

## 🚀 Tech Stack

- **Framework**: Django & Django Rest Framework (DRF)
- **Database**: PostgreSQL
- **Real-time**: Django Channels (WebSockets) & Redis (Layer)
- **Authentication**: JWT (SimpleJWT)
- **Documentation**: OpenAPI (Drf-spectacular)
- **Storage**: AWS S3 (Static & Media)

## 📚 Documentation
Detailed documentation is available in the `docs/` folder:
- [Infrastructure Setup](docs/INFRASTRUCTURE.md)
- [Application Setup](docs/SETUP.md)
- [Deployment Guide](docs/DEPLOYMENT.md)

### Recent Updates
- [Authentication Flow Documentation](.agent/authentication_flow.md) - Comprehensive guide to user authentication
- [December 21, 2025 Changelog](.agent/CHANGELOG_2025-12-21.md) - Latest features and improvements

### Key Features Added (Dec 2025)
- ✅ **Standardized Pagination**: Default 10 items per page across all tables
- ✅ **Dynamic Page Size**: Select from 10, 25, 50, or 100 items per page
- ✅ **Permission-Based Links**: Links only appear if users have view permissions
- ✅ **Events Tab Filtering**: Server-side filtering with pagination (All/Past/Ongoing/Upcoming)
- ✅ **Enhanced UI**: 4px gaps between buttons, improved styling, better accessibility
- ✅ **Forgot Password**: OTP-based password reset flow
- ✅ **Verified-Only Access**: Strict frontend enforcement restricting unverified users from Events/Chat


---

## 🔐 Authentication & Authorization

The project uses **JWT (JSON Web Token)** authentication to secure API endpoints.

### User Roles
The system defines four distinct roles, each with specific capabilities:
1.  **Influencer**: Users who want to showcase their portfolio and connect with brands.
2.  **Entrepreneur**: Individuals looking for influencers to promote their ventures.
3.  **Brand**: Companies seeking partnerships.
4.  **Staff**: Internal administrators (created via Admin panel).

### Token Type
- **Access Token**: Short-lived (default ~5-30 mins). Used in the `Authorization` header.
- **Refresh Token**: Long-lived (default ~1-7 days). Used to generate new access tokens.

**Header Format:**
```http
Authorization: Bearer <access_token>
```

---

## 🔄 Core Workflows

### 1. User Registration & Profile
- **Flow**: User signs up with email/password and selects a role.
- **Validation**: Role must be one of `['influencer', 'entrepreneur', 'brand']`.
- **Profile**: Automatically created upon registration. Users can update bio, social links (Influencers), or business details (Brands).
    - **Default Image**: If no image is provided, a default placeholder is automatically assigned from `media/default.jpg`.

### 2. Verification Process
- **Flow**: Users upload identity or business documents to get the `is_verified` badge.
- **Logic**:
    - User uploads a document (Image/PDF).
    - Status is set to `Pending` initially.
    - Admin reviews and updates status to `Approved` or `Rejected`.
    - Once approved, the User's `is_verified` field becomes `True`.

### 3. Connections
- **Flow**: A networking model similar to LinkedIn.
- **Rules**:
    - Users can send connection requests to others.
    - Receiver can `Accept` or `Reject` the request.
    - Connections are unique pairs (A cannot request B if B already requested A).

### 4. Events
- **Flow**: Users can host events and others can join.
- **Participation**:
    - Statuses: `Interested`, `Going`, `Invited`.
    - Attendees are linked to the event with their chosen status.

### 5. Stories
- **Flow**: Ephemeral content sharing.
- **Logic**:
    - Stories expire automatically after **24 hours**.
    - API filters out expired stories by default.

### 6. Real-time Chat
- **Flow**: WebSocket-based messaging between users.
- **Routing**: `ws://<host>/ws/chat/<room_name>/`
- **Architecture**: Messages are broadcast to the room group and can be persisted in the database.

---

## 🏰 Role-Based Admin Dashboard Strategy

To manage the Infloo platform effectively, we have designed a **Role-Based Access Control (RBAC)** architecture. This maps specific business responsibilities to distinct "Dashboard Views" within the Admin Panel.

### 1. The "Compliance Officer" (Verification Team)
*Goal: Ensure only legitimate users get verified.*
- **Visible Menus**:
    - `Users` > **Documents**
    - `Users` > **Users** (Read-Only)
- **Approval Page Workflow**:
    1.  Click **Documents**.
    2.  Use Filter (Right Sidebar) -> Select **Status: Pending**.
    3.  Click a Document ID to open the **Detail Page**.
    4.  Review the `File` (Identity/Business Proof).
    5.  **Action**: Change `Status` to **Approved** or **Rejected**.
    6.  (Optional) Add a note in `Admin Comment`.

### 2. The "Community Manager" (Moderation Team)
*Goal: Keep the platform safe and clean.*
- **Visible Menus**:
    - `Stories` > **Stories**
    - `Events` > **Events**
    - `Chat` > **Messages**
- **Moderation Workflow**:
    1.  Browse **Stories** (Sorted by `Created At`).
    2.  **Action**: If a story violates policy, select it and assert **Delete**.
    3.  Browse **Events**.
    4.  **Action**: Edit offensive `Titles` or `Descriptions`.

### 3. The "Access Manager" (HR / Lead Staff)
*Goal: Manage internal staff and their permissions.*
- **Visible Menus**:
    - `Authentication` > **Groups** (The "Roles" Menu)
    - `Authentication` > **Users** (The "Staff" list)
- **Role Creation Workflow (Permissions Page)**:
    1.  Go to **Groups** -> **Add Group**.
    2.  **Name**: "Junior Moderators".
    3.  **Permissions Select Board**:
        - Search "Story" -> Select `Can delete story`.
        - Search "Event" -> Select `Can change event`.
    4.  **Save**.
    5.  Go to **Users** -> Select a Staff member -> Add to "Junior Moderators" group.

### 4. The "User Support Specialist"
*Goal: Debug user issues without changing data.*
- **Visible Menus**:
    - `Connections` > **Connections**
    - `Users` > **Profiles**
    - `Notifications` > **Notifications**
- **Workflow**:
    - Detailed Read-Only access to invesitgate reports like "I can't see my connection request".

---

## 📚 API Reference (Exhaustive)

### 👤 User Management

#### Register User
**POST** `/api/users/auth/register`
- **Access**: Public
- **Payload**:
  ```json
  {
      "username": "john_doe",
      "email": "john@example.com",
      "password": "securepassword123",
      "role": "influencer",
      "phone": "+1234567890"
  }
  ```

#### Login (Obtain Token)
**POST** `/api/users/auth/login`
- **Access**: Public
- **Payload**:
  ```json
  { "username": "john", "password": "password" }
  ```
- **Response**: `{ "refresh": "...", "access": "..." }`

#### Refresh Token
**POST** `/api/users/auth/refresh`
- **Access**: Public
- **Payload**: `{ "refresh": "token_string" }`
- **Response**: `{ "access": "new_access_token" }`

#### Get Profile
**GET** `/api/users/profile`
- **Access**: Authenticated
- **Response**: User details + Profile (bio, links).

#### Update Profile
**PUT / PATCH** `/api/users/profile`
- **Access**: Authenticated
- **Payload**:
  ```json
  { "bio": "Updated bio", "company_name": "New Corp" }
  ```

#### Verification Upload
**POST** `/api/users/verify`
- **Access**: Authenticated
- **Payload**: `file` (Binary), `doc_type` ("identity"/"business").

---

### 🤝 Connections

#### List Connections
**GET** `/api/connections`
- **Access**: Authenticated
- **Filter**: Shows friends/connections of logged-in user.

#### Send Request
**POST** `/api/connections/request`
- **Access**: Authenticated
- **Payload**: `{ "receiver": 2 }`

#### Respond to Request
**PATCH** `/api/connections/{id}/action`
- **Access**: Authenticated (Receiver only)
- **Payload**: `{ "status": "accepted" }` (or "rejected")

---

### 📅 Events

#### List Events
**GET** `/api/events`
- **Access**: Public

#### Create Event
**POST** `/api/events`
- **Access**: Authenticated
- **Payload**:
  ```json
  {
      "title": "Launch Party",
      "date": "2025-01-01T20:00:00Z",
      "location": "Zoom"
  }
  ```

#### Get Event Details
**GET** `/api/events/{id}`
- **Access**: Public

#### Update Event
**PUT / PATCH** `/api/events/{id}`
- **Access**: Authenticated (Host only)
- **Payload**: `{ "location": "Changed Location" }`

#### Delete Event
**DELETE** `/api/events/{id}`
- **Access**: Authenticated (Host only)

#### Join Event
**POST** `/api/events/{id}/join`
- **Access**: Authenticated
- **Payload**: `{ "status": "going" }`

---

### 📸 Stories

#### List My Stories
**GET** `/api/stories/my_stories`
- **Access**: Authenticated

#### List Active Feed
**GET** `/api/stories`
- **Access**: Authenticated
- **Description**: Shows stories from followed users (logic pending) or public active stories.

#### Post Story
**POST** `/api/stories`
- **Access**: Authenticated
- **Payload**: `content_file` (File), `caption` (Text).

#### View Story
**GET** `/api/stories/{id}`
- **Access**: Authenticated

#### Update Story
**PUT / PATCH** `/api/stories/{id}`
- **Access**: Authenticated (Owner only)
- **Payload**: `{ "caption": "Edited caption" }`

#### Delete Story
**DELETE** `/api/stories/{id}`
- **Access**: Authenticated (Owner only)

---

### 💬 Chat

#### List Messages
**GET** `/api/chat`
- **Access**: Authenticated
- **Description**: History of messages (if persistence is enabled).

#### WebSocket Connection
**URL**: `ws://<host>/ws/chat/<room_name>/`
- **Protocol**: `ws://` (or `wss://` in prod).
- **Events**:
  - `send`: `{ "message": "Hi", "sender": "me" }`
  - `receive`: `{ "message": "Hi", "sender": "me" }`

---

### 🔔 Notifications

#### List Notifications
**GET** `/api/notifications`
- **Access**: Authenticated

#### Mark Read
**PATCH** `/api/notifications/{id}/read`
- **Access**: Authenticated

---

## 🗄️ Database Models & Validations

### User (Custom User Model)
- **Table**: `users_user`
- **Key Fields**: `username`, `email`, `role`, `is_verified`.
- **Validations**: `role` must be in predefined choices.

### Profile
- **Table**: `users_profile`
- **Relationship**: One-to-One with User.
- **Fields**: `bio`, `social_links` (JSON), `company_name`, `business_category`.

### Connection
- **Table**: `connections_connection`
- **Constraints**: `unique_together = ('sender', 'receiver')`.

### Story
- **Table**: `stories_story`
- **Logic**: `expires_at` is set to `now + 24h` by default.

---

## 🛠️ Setup & workflows

### Prerequisites
- Python 3.10+
- PostgreSQL
- Redis (for Channels)

### Installation

1.  **Clone Repository**
    ```bash
    git clone <repo_url>
    cd infloo_django
    ```

2.  **Install Dependencies**
    ```bash
    pip install -r requirements.txt
    ```

3.  **Environment Variables**
    Create a `infloo_project/config.py` or sets OS env vars for:
    - `SECRET_KEY`
    - `DATABASE_URL`
    - `ALLOWED_HOSTS`

4.  **Database Migration**
    ```bash
    python manage.py migrate
    ```

5.  **Run Server**
    ```bash
    # For HTTP & WebSockets (using Daphne is recommended for production)
    python manage.py runserver
    ```

---
*Documentation auto-generated by Infloo Backend Team.*
