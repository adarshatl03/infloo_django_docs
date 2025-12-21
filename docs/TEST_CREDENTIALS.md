Use these credentials to test the various Role permissions in the Dashboard.

## 0. Superadmin (Reset Script)
**Username:** `admin`
**Password:** `Secret@123`
**Email:** `admin@infloo.in`
**Capabilities:** Full control, bypasses verification enforcement.

## 1. Brand Manager
**Username:** `brand_manager`
**Password:** `password123`
**Role:** Staff (Group: Brand Manager)
**Approvals:**
- Can Verify Brand Users (Check `is_verified` or Documents).
- **Workflow:** Go to *Users* -> Filter by 'Brand' -> Edit User -> Check verification docs -> Set Verified.

## 2. Influencer Manager
**Username:** `influencer_mgr`
**Password:** `password123`
**Role:** Staff (Group: Influencer Manager)
**Approvals:**
- Can Verify Influencer Users.
- **Workflow:** Go to *Users* -> Filter by 'Influencer' -> Edit User.

## 3. Content Moderator
**Username:** `moderator_alice`
**Password:** `password123`
**Role:** Staff (Group: Content Moderator)
**Capabilities:**
- Can Delete Events and Stories that violate policies.
- **CANNOT** Approve/Verify users.

## 4. Regular Users (Examples)
**Brand:** `nike` / `password123`
**Influencer:** `traveltom` / `password123`

---
*Note: To login, go to `/login`*
