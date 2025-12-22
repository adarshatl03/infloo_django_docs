# Infloo App Documentation & User Manual

## 1. Introduction
Infloo is a dynamic social engagement platform connecting Influencers, Brands, and Entrepreneurs. The application uses a robust **Role-Based Access Control (RBAC)** system to ensure users only access features relevant to their persona.

---

## 2. User Roles & Capabilities

### 🌟 Influencer
**Objective:** Share content (Stories), attend events, and grow audience.
*   **CAN:**
    *   Create and post limited-time **Stories**.
    *   View and "Join" **Events** hosted by Brands.
    *   Follow other users (Brands/Influencers).
    *   Verify identity by uploading proofs in Profile -> Verification.
    *   View personal analytics (Follower growth, Engagement).
*   **CANNOT:**
    *   Create Events (Only Brands/Entrepreneurs).
    *   Access any dashboard Admin/Staff pages.

### 🏢 Brand
**Objective:** Host events, promote products, and engage influencers for campaigns.
*   **CAN:**
    *   **Host Events**: Create product launch or promo events.
    *   View "Campaign Reach" analytics (Reach/Engagement metrics).
    *   Verify business legitimacy.

### 💼 Entrepreneur
**Objective:** Network, find partners, and gain visibility for ventures.
*   **CAN:**
    *   **Host Networking Events**: Create meetups or pitch sessions.
    *   View "Venture Interest" analytics (Profile visits, Potential Partner clicks).
    *   Connect with both Brands (for funding/partnership) and Influencers (for marketing).
*   **CANNOT:**
    *   Delete events involved in active disputes.

### 🛡️ Staff / Admin
**Objective:** Moderate content, verify users, and manage the platform ecosystem.
*   **CAN:**
    *   Access the **Admin Dashboard** (`/home`).
    *   **Verification Center**: Approve/Reject user identity docs (`auto-verifies` user on approval).
    *   **Reports & Moderation**: View flagged content and delete malicious Stories/Events.
    *   **User Management**: View user list and edit details (if permission granted).
    *   **Role Management**: Create custom staff roles via `/roles`.

---

## 3. Core Workflows

### ✅ User Verification Workflow
1.  **User Action**: User goes to **Profile -> Verification Tab** -> Uploads "Identity Proof" or "Business Proof".
    *   *System State*: Document status = `Pending`. User `identity_verified` = `False`.
2.  **Staff Action**:
    *   Staff (Brand/Influencer Manager) navigates to **Verification** in Sidebar.
    *   Clicks "View File" to inspect the upload.
    *   Clicks **Approve (Green Check)**.
3.  **Outcome**:
    *   Document status -> `Approved`.
    *   User `identity_verified` -> `True`.
    *   User sees "Verified" badge on profile.

### 🚩 Content Moderation Workflow
1.  **Reporting**: A user flags a Story or Event as "Inappropriate" (via App/API).
    *   *System State*: New `Report` created with status `Pending`.
2.  **Review**:
    *   Staff (Content Moderator) navigates to **Reports** in Sidebar.
    *   Reviews the evidence (link to Content).
3.  **Action**:
    *   **Dismiss**: Content stays, report is closed.
    *   **Delete Content**: Content is permanently removed from DB, Report marked `Resolved`.

### ✉️ Direct Messaging Workflow
1.  **Initiation**: User visits another profile and clicks the **"Message"** button.
    *   *Result*: Redirects to Chat interface with that user active.
2.  **Engagement**: Conversing via the **Messages** sidebar link.
    *   List of recent conversations is maintained on the left.
3.  **Real-time**: Leverages Django Channels (WebSockets) for instant message delivery.

### 🔐 Security & MFA Workflow
1.  **Enabling MFA**: Users can go to **Profile -> Security** to enable Two-Factor Authentication.
2.  **Setup**: Scan the QR Code using a TOTP app (Google Authenticator, Authy).
3.  **Verification**: After setup, every login will prompt for a 6-digit code.
4.  **Staff Requirement**: All Staff roles are recommended to have MFA enabled for dashboard access.

### 📅 Event Management
1.  **Creation**: Brand creates an event via Dashboard or API.
2.  **Participation**: Influencers click "Attend".
3.  **Analytics**: Brand sees "Campaign Reach" metric increase based on attendees.

---

## 4. Do's and Don'ts

### For Staff
*   **DO** check the file evidence before approving a verification request.
*   **DO** use the "Dismiss" button on reports if the content is actually safe (prevents clutter).
*   **DON'T** delete `Staff` roles unless you are a Superuser (can lock yourself out).
*   **DON'T** edit user profiles directly unless requested by the user for support.

### For Developers
*   **DO** use `StaffRequiredMixin` for any new dashboard views.
*   **DO** check `request.user.role` in templates is specific content is strictly for one persona.
*   **DON'T** hardcode permissions checks (`if user.username == 'admin'`). Always use `user.has_perm('app.action')`.

---

## 5. Troubleshooting
*   **"File attribute has no file" error**: Occurs if a Document object exists in DB but the file is missing on disk/S3. The system now handles this gracefully in the UI.
*   **Default Profile Image**: If a user has not uploaded an image, the system automatically serves `media/default.jpg`. Ensure this file exists in your media storage.
*   **User cannot see specific pages**: Check their Role permissions in `/roles`. Ensure the Group has the correct permissions assigned.

