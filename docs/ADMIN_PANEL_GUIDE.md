# Infloo Admin & Dashboard Guide

This document outlines the functionality of the Infloo Admin Dashboard and provides a guide on "Who Can Do What".

## Access Control & Roles

The system uses a role-based access control (RBAC) system.

| Role | Description | Key Permissions |
|------|-------------|-----------------|
| **Superuser** | Full system access | Manage all users, assign roles, access Django Admin, delete any content, manage system configuration. |
| **Staff** | Platform moderators | View user lists, verify documents, resolve reports, moderate events. |
| **Brand** | Business entities | Create Events, Post Campaigns, View Analytics, Initiate Collaborations. |
| **Entrepreneur** | Solo business users | Similar to Brand capabilities. |
| **Influencer** | Content Creators | Join events, Receive collaboration requests, View Participation Analytics. |

## Feature Reference Guide

### 1. User Management
* **Access**: Staff & Superusers
* **Location**: `Manage Access > Users` (`/dashboard/user`)
* **Capabilities**:
  * View full list of registered users.
  * Filter users by Role (Brand, Influencer, etc.) or Status (Active, Suspended).
  * Search by Username or Email.

### 2. Verification Center
* **Access**: Staff & Superusers
* **Location**: `Compliance > Verification` (`/dashboard/verification`)
* **Capabilities**:
  * Review pending identity documents uploaded by users.
  * **Approve**: Grants "Verified" badge to the user profile.
  * **Reject**: Sends notification to user to re-submit.

### 3. Events Management
* **Access**: All Users (Capabilities vary)
* **Location**: `Events` (`/dashboard/events`)
* **Shared Capabilities**:
  * View Event List with Tabs: **All**, **Ongoing** (Today), **Upcoming**, **Past**.
  * Filter by Search.
* **Brand/Entrepreneur**:
  * **Create Event**: Host new events.
  * **Manage Own**: Edit details or Cancel events they host.
  * **Track**: View list of attendees.
* **Influencer/General User**:
  * **Join**: Register for an event.
  * **Withdraw**: Leave an event before it starts.
* **Staff**:
  * **Moderation**: Can Edit or Terminate ANY event on the platform if it violates policies.

### 4. Collaborations Hub
* **Access**: All Users
* **Location**: `Collaborations` (`/dashboard/collaborations`)
* **Layout**: Split View (Left: Requests, Right: Active).
* **Workflow**:
  1. **Request**: A user sends a collaboration proposal (Title, Description).
  2. **Review**: The receiver sees it in the "Requests" panel.
  3. **Action**: Receiver clicks **Accept** or **Reject**.
  4. **Active**: Accepted projects move to the "Active Projects" panel.
  5. **Communication**: "Open Chat" button links directly to the messaging system.

### 5. Chat System
* **Access**: All Users
* **Location**: `Messages` (`/dashboard/chat`)
* **Capabilities**:
  * Real-time messaging with other users.
  * History is preserved.
  * Often initiated via Active Collaborations or User Profile.

### 6. Profile & Settings
* **Access**: All Users
* **Capabilities**:
  * **Theme Switcher**: Toggle between Light Mode and Dark Mode (preference saved).
  * **Edit Profile**: Update Bio, Profile Image, and Social Links. (Default image: `default.jpg`).
  * **Change Password**: Security settings.

## Developer Notes
* **Frontend**: Templates located in `templates/dashboard/`. Styles in `static/css/enhanced.css`.
* **Backend**: Views in `dashboard/views.py`.
* **Permissions**: Enforced via `StaffRequiredMixin` and `LoginRequiredMixin`.
