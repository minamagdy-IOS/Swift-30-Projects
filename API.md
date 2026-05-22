# Parent App - Complete API Documentation

**Version:** 1.0  
**Base URL:** `https://api.parent.eu/api`  
**Date:** May 23, 2026

---

## Table of Contents

- [A. User Roles & Permissions](#a-user-roles--permissions)
- [B. API Documentation](#b-api-documentation)
  - [1. Authentication Module](#1-authentication-module)
  - [2. Newsfeed Module](#2-newsfeed-module)
  - [3. Dashboard Module](#3-dashboard-module)
  - [4. Messages Module](#4-messages-module)
  - [5. Child Profile Module](#5-child-profile-module)
  - [6. Calendar Module](#6-calendar-module)
  - [7. Billing Transaction Module](#7-billing-transaction-module)

---

## A. User Roles & Permissions

### Overview

The application uses a role-based access control (RBAC) system with three primary user types: **Parent (Contact)**, **Staff**, and **Admin**. Each role has specific permissions that control access to features and data.

### 1. Parent (Contact) Role

**Description:** Parents or guardians linked to one or more children in the institution.

**Key Permissions:**
- `view_my_newsfeed` - View personal newsfeed
- `view_child_profile` - View child's profile information
- `view_child_gallery` - Access child's photos/videos
- `view_child_daily_report` - View daily status reports
- `view_child_calendar` - View child-specific calendar events
- `view_my_calendar` - View personal calendar
- `receive_messages` - Receive messages from staff
- `send_message` - Send messages to staff (if enabled)
- `reply_on_event` - Respond to calendar events
- `report_vacation` - Report child vacation/absence
- `report_sick` - Report child sick leave
- `view_invoice` - View billing invoices
- `view_child_contacts` - View child's emergency contacts
- `view_child_permissions_list` - View permissions granted to child
- `view_child_pickup_info` - View authorized pickup persons
- `edit_child_permission_reply` - Respond to permission requests
- `view_child_journey` - View child development journey (if enabled)

**Access Restrictions:**
- Cannot view institution-wide newsfeed (only child-related posts)
- Cannot create posts (may submit for approval based on institution settings)
- Cannot access other children's profiles
- Cannot view staff schedules or internal communications
- Limited calendar access (child-specific events only)

**Data Scope:**
- Own profile and linked children only
- Messages related to own children
- Invoices and billing for own account
- Calendar events involving own children

---

### 2. Staff Role

**Description:** Teachers, caregivers, and administrative staff working at the institution.

**Key Permissions:**

**Newsfeed & Communication:**
- `view_institute_newsfeed` - View all posts in institution
- `post_news_feed` - Create posts
- `post_news_feed_without_approval` - Post without approval
- `edit_newsfeed` - Edit own posts
- `delete_newsfeed` - Delete own posts
- `receive_messages` - Receive messages
- `send_message` - Send messages to parents/staff

**Child Management:**
- `view_child_profile` - View all child profiles
- `edit_child_profile_information` - Edit child basic info
- `report_edit_child_actions_status` - Record daily statuses
- `view_child_statuses_history` - View status history
- `checkin_checkout_child` - Check-in/out children
- `view_child_health_info` - View health information
- `edit_child_health_info` - Update health records
- `register_staff_statuses` - Record own attendance

**Calendar & Events:**
- `view_institute_calendar` - View full calendar
- `create_event` - Create events
- `edit_event` - Edit events
- `delete_event` - Delete events
- `view_event_replies` - View parent responses
- `create_activity` - Create activities
- `edit_activity` - Edit activities

**Child Development (if enabled):**
- `send_directly_observation` - Post observations directly
- `send_for_review_observation` - Submit observations for review
- `edit_observation` - Edit observations
- `delete_observation` - Delete observations
- `view_child_journey` - View development journey
- `add_next_steps` - Add development goals

**Classroom Management:**
- `view_class_details` - View classroom details
- `view_class_children` - View classroom roster
- `add_child_to_class` - Assign children to class
- `manage_room_planning` - Manage room schedules

**Access Restrictions:**
- Cannot access billing/financial data (unless specific permission)
- Cannot modify institution settings
- Cannot create/manage user roles
- Cannot access company-level data

**Data Scope:**
- All children in assigned classrooms
- Institution-wide posts and events
- Messages with parents and other staff
- Observations and development records for assigned children

---

### 3. Admin Role

**Description:** Institution administrators with elevated privileges.

**Inherits All Staff Permissions, Plus:**

**User Management:**
- `create_institute_user` - Create new users
- `create_institute_role` - Create custom roles
- `edit_institute_role` - Modify roles
- `delete_institute_role` - Delete roles
- `assign_role_to_user` - Assign roles to users
- `lock_staff_member` - Lock/unlock staff accounts
- `view_staff_profile` - View all staff profiles
- `create_staff` - Add new staff members
- `edit_staff` - Edit staff information

**Billing & Financial:**
- `view_payers` - View all payers
- `add_payer` - Add new payers
- `create_invoice` - Generate invoices
- `create_payment` - Record payments
- `create_refund` - Process refunds
- `view_products` - View billing products
- `create_plan` - Create billing plans
- `manage_adhoc` - Manage ad-hoc charges
- `manage_discounts` - Manage discounts
- `invoice_settings` - Configure invoice settings

**System Administration:**
- `manage_institute_options` - Configure institution settings
- `manage_institute_tools` - Enable/disable modules
- `view_admin_dashboard` - Access admin dashboard
- `manage_age_groups` - Configure age groups
- `add_class` - Create classrooms
- `edit_class` - Modify classrooms
- `add_group` - Create groups
- `edit_group` - Modify groups

**Advanced Features:**
- `manage_child_development_settings` - Configure child development
- `manage_staff_schedule` - Manage staff schedules
- `manage_inquiry_about` - Manage inquiries
- `create_survey` - Create surveys
- `view_survey_response` - View survey results
- `manage_food_plan` - Manage meal plans

**Data Scope:**
- Full access to all data within the institution
- All children, parents, and staff
- Financial and billing records
- System logs and reports

---

### Permission Checking System

The application uses a centralized permission checking system (`PermissionCodes` class):

```dart
// Check single permission
bool canPost = PermissionCodes.check('post_news_feed');

// Check child-specific permission
bool canViewProfile = PermissionCodes.checkPermissionGranted(
  'view_child_profile', 
  childId
);

// Check module enabled
bool isBillingEnabled = PermissionCodes.checkModule(
  'enabled_apps', 
  'billing'
);
```

### Role Hierarchy

```
System Admin (Super Admin)
    ├── Company Admin
    │   └── Institution Admin
    │       ├── Staff (various roles)
    │       └── Parent (Contact)
```

---

## B. API Documentation

### Common Headers

All authenticated requests require the following headers:

```http
Content-Type: application/json
Accept: application/json
Accept-Language: en (or ar, fr, etc.)
Authorization: Bearer {access_token}
institution-id: {institution_id}
Device-Model: {device_model}
Platform: Flutter-Android | Flutter-iOS
Version: {app_version}
User-Agent: ParentApp/{version} ({OS}; {Device})
```

### Common Response Format

All API responses follow this structure:

```json
{
  "Status": "Success" | "Failed",
  "Message": "Human readable message",
  "Data": { /* Response data */ },
  "Errors": [ /* Array of error objects */ ]
}
```

### Status Codes

- `200` - Success
- `201` - Created successfully
- `400` - Bad request
- `401` - Unauthorized (invalid/expired token)
- `403` - Forbidden (insufficient permissions)
- `404` - Resource not found
- `406` - Token expired (specific to app)
- `408` - Token expired (alternate)
- `410` - Token needs refresh
- `500+` - Server error / Maintenance

---

## 1. Authentication Module

### 1.1 Login

**Endpoint:** `POST /v3/login`

**Description:** Authenticate user with email/password. Returns access token and user data.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123",
  "device_id": "unique-device-identifier"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Login successful",
  "Data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 12345,
      "full_name": "John Doe",
      "email": "john.doe@example.com",
      "mobile": "+1234567890",
      "avatar": "https://cdn.parent.eu/avatars/12345.jpg",
      "language": {
        "code": "en",
        "name": "English"
      },
      "company": {
        "id": 1,
        "name": "ABC Daycare Network"
      },
      "institutions": [
        {
          "id": 100,
          "name": "Sunshine Daycare",
          "logo": "https://cdn.parent.eu/logos/100.png",
          "type": "daycare"
        }
      ],
      "children": [
        {
          "id": 5001,
          "first_name": "Emma",
          "last_name": "Doe",
          "avatar": "https://cdn.parent.eu/children/5001.jpg",
          "date_of_birth": "15/03/2020",
          "classroom": {
            "id": 10,
            "name": "Toddlers Room"
          }
        }
      ],
      "role": {
        "id": 5,
        "name": "Parent",
        "code": "parent",
        "permissions": [
          {
            "id": 1,
            "name": "View My Newsfeed",
            "permission_code": "view_my_newsfeed",
            "is_system_permission": 1
          }
        ]
      }
    },
    "child_development_last_update": "2026-05-20 10:30:00",
    "mfa_required": false,
    "activation_required": false
  }
}
```

**MFA Required Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "mfa_required": true,
    "mfa_session_token": "mfa_session_abc123",
    "user_has_mfa": true,
    "is_device_trusted": false,
    "must_setup_mfa": false,
    "available_methods": {
      "email": true,
      "sms": true,
      "authenticator": false
    },
    "default_method": "email",
    "can_trust_devices": true,
    "trust_devices_period": 30
  }
}
```

**Error Response (401):**
```json
{
  "Status": "Failed",
  "Message": "Invalid credentials",
  "Errors": [
    {
      "field": "password",
      "message": "Incorrect password"
    }
  ]
}
```

---

### 1.2 PIN Code Verification

**Endpoint:** `POST /v1/users/pincode/check`

**Description:** Verify PIN code for quick login (parent users).

**Headers:**
```http
Authorization: Bearer {pin_check_token}
```

**Request Body:**
```json
{
  "pincode": "1234"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "PIN verified successfully",
  "Data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": { /* Same as login response */ }
  }
}
```

---

### 1.3 Token Refresh

**Endpoint:** `GET /v1/user/token/refresh`

**Description:** Refresh expired access token using the current token.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "access_token": "new_token_here",
    "expires_in": 3600
  }
}
```

---

### 1.4 Reset Password Request

**Endpoint:** `POST /v1/password/token`

**Description:** Request password reset link via email or SMS.

**Request Body:**
```json
{
  "email": "user@example.com"
}
```
OR
```json
{
  "mobile": "+1234567890"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Reset link sent successfully"
}
```

---

### 1.5 Reset Password

**Endpoint:** `PUT /v1/password/reset`

**Description:** Reset password using token from email/SMS.

**Request Body:**
```json
{
  "token": "reset_token_from_email",
  "password": "newSecurePassword123"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Password reset successfully"
}
```

---

### 1.6 Account Activation

**Endpoint:** `GET /v1/activation/{token}`

**Description:** Validate activation token from new account email.

**Success Response (200):**
```json
{
  "Status": "Success",
  "success": true,
  "Data": {
    "user": {
      "id": 12345,
      "email": "newuser@example.com",
      "first_name": "Jane"
    },
    "requires_otp": true,
    "available_methods": {
      "email": true,
      "sms": true
    }
  }
}
```

---

### 1.7 Send Activation OTP

**Endpoint:** `POST /v1/activation/otp/send`

**Description:** Send OTP to verify contact method during activation.

**Request Body:**
```json
{
  "activation_token": "activation_token_here"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "success": true,
  "Message": "OTP sent successfully",
  "retry_after": 60
}
```

---

### 1.8 Verify Activation OTP

**Endpoint:** `POST /v1/activation/otp/verify`

**Description:** Verify OTP code during activation.

**Request Body:**
```json
{
  "activation_token": "activation_token_here",
  "otp": "123456"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "success": true,
  "Data": {
    "password_setup_token": "token_for_password_setup"
  }
}
```

---

### 1.9 Set Activation Password

**Endpoint:** `POST /v1/activation/password/set`

**Description:** Set password during account activation.

**Request Body:**
```json
{
  "password_setup_token": "token_from_otp_verification",
  "password": "securePassword123"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Account activated successfully",
  "Data": {
    "token": "access_token_here",
    "user": { /* User object */ }
  }
}
```

---

### 1.10 SSO Login Redirect

**Endpoint:** `GET /v1/auth/{provider}/redirect`

**Description:** Get SSO redirect URL for OAuth login (Google, Microsoft, etc.).

**Parameters:**
- `provider` (path): `google` | `microsoft` | `apple`
- `device_id` (query): Device identifier

**Success Response (200):**
```json
{
  "Status": "Success",
  "success": true,
  "Data": {
    "redirect_url": "https://accounts.google.com/o/oauth2/v2/auth?..."
  }
}
```

---

### 1.11 SSO Login Callback

**Endpoint:** `GET /v1/auth/{provider}/callback`

**Description:** Complete SSO login after OAuth redirect.

**Parameters:**
- `provider` (path): `google` | `microsoft` | `apple`
- `sso_token` (query): Token from OAuth provider
- `provider` (query): Provider name

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "token": "access_token_here",
    "user": { /* User object */ }
  }
}
```

---

### 1.12 Logout

**Endpoint:** `POST /notifications/tokens/remove`

**Description:** Remove FCM token and logout user.

**Request Body:**
```json
{
  "token": "fcm_token_here",
  "user_id": 12345
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Logged out successfully"
}
```

---

## 2. Newsfeed Module

### 2.1 Get All Newsfeed (Staff)

**Endpoint:** `GET /v3/institutions/{institution_id}/newsfeeds`

**Description:** Retrieve institution-wide newsfeed posts (staff only).

**Permission Required:** `view_institute_newsfeed`

**Query Parameters:**
- `last_index` (optional): Last post ID for pagination
- `is_archive` (optional): `true` | `false` - Filter archived posts
- `status` (optional): `list_published` (for staff with approval permission)
- `_without_cache` (optional): `true` - Force fresh data

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "data": [
      {
        "id": 1001,
        "newsfeed": {
          "id": 5001,
          "title": "Field Trip Tomorrow!",
          "body": "<p>Don't forget to pack a lunch for tomorrow's trip to the zoo!</p>",
          "type": "event",
          "is_featured": false,
          "is_bookmarked": false,
          "created_at": "2026-05-22 14:30:00"
        },
        "author": {
          "id": 201,
          "full_name": "Sarah Johnson",
          "avatar": "https://cdn.parent.eu/avatars/201.jpg",
          "role": "Teacher"
        },
        "attachments": [
          {
            "id": 3001,
            "type": "photo",
            "url": "https://cdn.parent.eu/posts/3001.jpg",
            "thumbnail_url": "https://cdn.parent.eu/posts/3001_thumb.jpg",
            "mime_type": "image/jpeg",
            "name": "zoo_flyer.jpg",
            "tagged_children": [5001, 5002]
          }
        ],
        "recipients": {
          "classes": [
            {
              "id": 10,
              "name": "Toddlers Room"
            }
          ],
          "children": [],
          "groups": []
        },
        "stats": {
          "likes_count": 15,
          "comments_count": 3,
          "views_count": 42,
          "is_liked": false,
          "is_seen": true
        },
        "newsfeed_status": {
          "code": "published",
          "name": "Published"
        },
        "institution_id": 100,
        "published_at": "2026-05-22 14:30:00"
      }
    ],
    "meta": {
      "current_page": 1,
      "last_page": 5,
      "per_page": 20,
      "total": 87
    }
  }
}
```

---

### 2.2 Get My Newsfeed (Parent)

**Endpoint:** `GET /v3/institutions/{institution_id}/newsfeeds/users/{user_id}`

**Description:** Retrieve child-related posts for parent users.

**Permission Required:** `view_my_newsfeed`

**Query Parameters:**
- `last_index` (optional): Last post ID for pagination
- `activity_mode` (optional): `0` | `1` - Filter by activities
- `is_archive` (optional): `true` | `false`
- `status` (optional): `pending,rejected` - View pending/rejected posts
- `_without_cache` (optional): `true`

**Success Response (200):**
Same structure as Get All Newsfeed, but filtered to posts involving user's children.

---

### 2.3 Get Single Post

**Endpoint:** `GET /v2/institutions/{institution_id}/newsfeeds/{post_id}`

**Description:** Retrieve details of a specific post.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "id": 1001,
    "newsfeed": { /* Post details */ },
    "author": { /* Author details */ },
    "attachments": [ /* Media files */ ],
    "recipients": { /* Target audience */ },
    "stats": { /* Engagement stats */ },
    "comments": [
      {
        "id": 2001,
        "comment": "Sounds great!",
        "author": {
          "id": 303,
          "full_name": "Emily Brown",
          "avatar": "url"
        },
        "created_at": "2026-05-22 15:00:00",
        "likes_count": 2
      }
    ]
  }
}
```

---

### 2.4 Create Post

**Endpoint:** `POST /v2/institutions/{institution_id}/newsfeeds` (Parent)  
**Endpoint:** `POST /v3/institutions/{institution_id}/newsfeeds` (Curriculum Builder/Staff)

**Description:** Create new newsfeed post.

**Permission Required:** `post_news_feed` or `post_news_feed_without_approval`

**Request Body:**
```json
{
  "title": "Great Day at School!",
  "body": "<p>Emma had a wonderful time painting today!</p>",
  "type": "general",
  "is_draft": false,
  "attachments": [
    {
      "type": "photo",
      "url": "https://cdn.parent.eu/uploads/abc123.jpg",
      "thumbnail_url": "https://cdn.parent.eu/uploads/abc123_thumb.jpg",
      "mime_type": "image/jpeg",
      "name": "painting.jpg",
      "tagged_children": [5001]
    }
  ],
  "recipients": {
    "recipient_type": "children",
    "children_ids": [5001],
    "class_ids": [],
    "group_ids": []
  },
  "is_anonymous": false,
  "allow_comments": true,
  "allow_likes": true
}
```

**Success Response (200/201):**
```json
{
  "Status": "Success",
  "Message": "Post created successfully",
  "Data": {
    "id": 1002,
    "newsfeed": { /* Created post */ },
    "newsfeed_status": {
      "code": "published",
      "name": "Published"
    }
  }
}
```

---

### 2.5 Update Post

**Endpoint:** `PUT /v1/newsfeeds/{post_id}` (Basic)  
**Endpoint:** `PUT /v2/newsfeeds/{post_id}` (Curriculum Builder)

**Description:** Update existing post.

**Permission Required:** `edit_newsfeed` (own posts only unless admin)

**Request Body:**
Same as Create Post

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Post updated successfully",
  "Data": { /* Updated post */ }
}
```

---

### 2.6 Delete Post

**Endpoint:** `DELETE /v1/newsfeeds/{post_id}`

**Description:** Delete a post.

**Permission Required:** `delete_newsfeed`

**Query Parameters:**
- `multiple_institutions_batch_id` (optional): Delete cross-institution post

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Post deleted successfully"
}
```

---

### 2.7 Like Post

**Endpoint:** `POST /v1/likes`

**Description:** Like or unlike a post.

**Request Body:**
```json
{
  "content_id": 1001,
  "content_type": "newsfeed",
  "institution_id": 100,
  "is_like": true
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Post liked successfully"
}
```

---

### 2.8 Get Post Likes

**Endpoint:** `GET /v1/likes/{post_id}/newsfeed/{institution_id}`

**Description:** Get list of users who liked a post.

**Query Parameters:**
- `page` (optional): Page number

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "data": [
      {
        "id": 12345,
        "full_name": "John Doe",
        "avatar": "url",
        "liked_at": "2026-05-22 16:00:00"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 15
    }
  }
}
```

---

### 2.9 Get Post Views (Seen)

**Endpoint:** `GET /v1/seens/{post_id}/newsfeed/{institution_id}`

**Description:** Get list of users who viewed a post.

**Query Parameters:**
- `page` (optional): Page number

**Success Response (200):**
Same structure as Get Post Likes

---

### 2.10 Bookmark Post

**Endpoint:** `PUT /v2/institutions/{institution_id}/newsfeeds/{post_id}/bookmark`

**Description:** Bookmark or unbookmark a post.

**Request Body:**
```json
{
  "bookmark": true
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Post bookmarked successfully"
}
```

---

### 2.11 Pin as Featured

**Endpoint:** `PUT /v2/institutions/{institution_id}/newsfeeds/{post_id}/pin-as-featured`

**Description:** Pin post as featured (staff only).

**Permission Required:** `can_pin_as_featured_posts`

**Request Body:**
```json
{
  "pin_as_featured": true
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Post pinned as featured"
}
```

---

### 2.12 Get Featured Posts

**Endpoint:** `GET /v3/institutions/{institution_id}/newsfeeds`

**Description:** Get featured posts.

**Query Parameters:**
- `is_featured` (required): `true`
- `last_index` (optional): Pagination

**Success Response (200):**
Same structure as Get All Newsfeed

---

### 2.13 Approve Post (Staff)

**Endpoint:** `PUT /v2/institutions/{institution_id}/newsfeeds/{post_id}/publish`

**Description:** Approve pending post for publication.

**Permission Required:** `post_news_feed_without_approval`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Post approved and published"
}
```

---

### 2.14 Reject Post (Staff)

**Endpoint:** `PUT /v2/institutions/{institution_id}/newsfeeds/{post_id}/reject`

**Description:** Reject pending post with comment.

**Permission Required:** `post_news_feed_without_approval`

**Request Body:**
```json
{
  "comment": "Please update the photo before resubmitting."
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Post rejected"
}
```

---

### 2.15 Get Pending Posts

**Endpoint:** `GET /v3/institutions/{institution_id}/newsfeeds/pending-approval` (Staff)  
**Endpoint:** `GET /v3/institutions/{institution_id}/newsfeeds/users/{user_id}?status=pending,rejected` (Parent)

**Description:** Get posts awaiting approval or rejected.

**Permission Required:** `post_news_feed_without_approval` (staff) or `view_my_newsfeed` (parent)

**Success Response (200):**
Same structure as Get All Newsfeed

---

### 2.16 Get Pending Approvals Count

**Endpoint:** `GET /v2/institutions/{institution_id}/newsfeeds/pending-approval/count` (Staff)  
**Endpoint:** `GET /v3/institutions/{institution_id}/newsfeeds/pending-approval/count/users/{user_id}` (Parent)

**Description:** Get count of pending posts.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "pending_count": 3
  }
}
```

---

### 2.17 Get Bookmarked Posts

**Endpoint:** `GET /v3/institutions/{institution_id}/newsfeeds/users/{user_id}?is_bookmarked=true`

**Description:** Get user's bookmarked posts.

**Success Response (200):**
Same structure as Get All Newsfeed

---

### 2.18 Get Post History/Logs

**Endpoint:** `GET /v1/institutions/{institution_id}/newsfeeds/{post_id}/logs`

**Description:** Get edit history of a post.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": [
    {
      "id": 701,
      "action": "created",
      "user": {
        "id": 201,
        "full_name": "Sarah Johnson"
      },
      "timestamp": "2026-05-22 14:30:00",
      "changes": {}
    },
    {
      "id": 702,
      "action": "updated",
      "user": {
        "id": 201,
        "full_name": "Sarah Johnson"
      },
      "timestamp": "2026-05-22 14:35:00",
      "changes": {
        "title": {
          "old": "Trip Tomorrow",
          "new": "Field Trip Tomorrow!"
        }
      }
    }
  ]
}
```

---

### 2.19 Get Drafts

**Endpoint:** `GET /v1/institutions/{institution_id}/drafts`

**Description:** Get saved draft posts.

**Query Parameters:**
- `page` (optional): Page number
- `type` (optional): Filter by post type

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "data": [
      {
        "draft_id": 4001,
        "id": null,
        "title": "Upcoming Event",
        "body": "<p>Draft content...</p>",
        "attachments": [],
        "created_at": "2026-05-20 10:00:00",
        "updated_at": "2026-05-22 09:00:00"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 2
    }
  }
}
```

---

### 2.20 Get Draft Count

**Endpoint:** `GET /v1/institutions/{institution_id}/drafts/count`

**Description:** Get count of saved drafts.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "draft_count": 2
  }
}
```

---

### 2.21 Delete Draft

**Endpoint:** `DELETE /v1/institutions/{institution_id}/drafts/?drafts={draft_id}`

**Description:** Delete a saved draft.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Draft deleted successfully"
}
```

---

## 3. Dashboard Module

### 3.1 Get Dashboard Chart (Staff)

**Endpoint:** `GET /v2/institutions/{institution_id}/attendance/chart`

**Description:** Get attendance chart data for institution dashboard.

**Permission Required:** Staff access

**Query Parameters:**
- `date` (required): Date in format `dd/MM/yyyy`
- `classrooms` (optional): Comma-separated classroom IDs
- `tags` (optional): Comma-separated tag IDs
- `view` (optional): `tags` - Group by tags instead of classrooms

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "date": "23/05/2026",
    "summary": {
      "total_children": 120,
      "present": 105,
      "absent": 10,
      "late": 5,
      "expected": 115
    },
    "by_classroom": [
      {
        "classroom_id": 10,
        "classroom_name": "Toddlers Room",
        "total": 25,
        "present": 22,
        "absent": 2,
        "late": 1,
        "statuses": {
          "checked_in": 20,
          "checked_out": 0,
          "on_trip": 2,
          "sick": 1,
          "vacation": 2
        }
      }
    ],
    "staff_attendance": {
      "total": 15,
      "present": 14,
      "absent": 1
    }
  }
}
```

---

### 3.2 Get Classroom Chart

**Endpoint:** `GET /v2/institutions/{institution_id}/classes/{classroom_id}/attendance/chart`

**Description:** Get attendance chart for specific classroom.

**Query Parameters:**
- `date` (required): Date in format `dd/MM/yyyy`

**Success Response (200):**
Similar to Dashboard Chart, filtered for specific classroom

---

### 3.3 Get Dashboard Rooms

**Endpoint:** `GET /v2/institutions/{institution_id}/classes/dashboard`

**Description:** Get classroom overview for dashboard.

**Query Parameters:**
- `classrooms` (optional): Filter by classroom IDs
- `date` (optional): Filter by date

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "classes": [
      {
        "id": 10,
        "name": "Toddlers Room",
        "age_range": "1-2 years",
        "capacity": 25,
        "current_count": 22,
        "staff_count": 3,
        "lead_teacher": {
          "id": 201,
          "full_name": "Sarah Johnson",
          "avatar": "url"
        },
        "children": [
          {
            "id": 5001,
            "first_name": "Emma",
            "last_name": "Doe",
            "avatar": "url",
            "current_status": {
              "code": "checked_in",
              "name": "Checked In",
              "time": "08:30:00"
            }
          }
        ]
      }
    ]
  }
}
```

---

### 3.4 Get Child Statuses

**Endpoint:** `GET /v2/institutions/{institution_id}/child-statuses`

**Description:** Get current status of all children (meals, sleep, etc.).

**Query Parameters:**
- `date` (required): Date in format `dd/MM/yyyy`
- `classroom_id` (optional): Filter by classroom

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "statuses": [
      {
        "child_id": 5001,
        "child_name": "Emma Doe",
        "classroom": "Toddlers Room",
        "check_in": {
          "time": "08:30:00",
          "type": "normal"
        },
        "meal_statuses": [
          {
            "meal_type": "breakfast",
            "eaten": "all",
            "time": "09:00:00"
          }
        ],
        "sleep_status": {
          "start_time": "12:00:00",
          "end_time": "14:00:00",
          "duration": 120
        },
        "toilet_statuses": [
          {
            "time": "10:30:00",
            "type": "success"
          }
        ],
        "mood": {
          "status": "happy",
          "time": "15:00:00"
        }
      }
    ]
  }
}
```

---

### 3.5 Post Child Status (Staff)

**Endpoint:** `POST /v1/institutions/{institution_id}/child-statuses`

**Description:** Record child status (meal, sleep, toilet, mood, etc.).

**Permission Required:** `report_edit_child_actions_status`

**Request Body:**
```json
{
  "child_id": 5001,
  "status_type": "meal",
  "date": "23/05/2026",
  "time": "12:00:00",
  "meal_type": "lunch",
  "eaten": "all",
  "note": "Ate all lunch, very hungry today!"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Status recorded successfully",
  "Data": {
    "status_id": 8001,
    "child_id": 5001,
    "status_type": "meal",
    "timestamp": "2026-05-23 12:00:00"
  }
}
```

---

### 3.6 Get Child Status History

**Endpoint:** `GET /v1/institutions/{institution_id}/children/{child_id}/statuses/history`

**Description:** Get historical status data for a child.

**Permission Required:** `view_child_statuses_history`

**Query Parameters:**
- `start_date` (required): Format `dd/MM/yyyy`
- `end_date` (required): Format `dd/MM/yyyy`
- `status_type` (optional): Filter by type (meal, sleep, etc.)

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "child": {
      "id": 5001,
      "name": "Emma Doe"
    },
    "date_range": {
      "start": "20/05/2026",
      "end": "23/05/2026"
    },
    "statuses": [
      {
        "date": "23/05/2026",
        "meal_statuses": [ /* ... */ ],
        "sleep_statuses": [ /* ... */ ],
        "toilet_statuses": [ /* ... */ ],
        "mood_statuses": [ /* ... */ ]
      }
    ]
  }
}
```

---

### 3.7 Get Room Collaborators

**Endpoint:** `GET /v1/institutions/{institution_id}/classes/{classroom_id}/collaborators`

**Description:** Get staff assigned to a classroom.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "collaborators": [
      {
        "id": 201,
        "full_name": "Sarah Johnson",
        "avatar": "url",
        "role": "Lead Teacher",
        "is_lead": true
      },
      {
        "id": 202,
        "full_name": "Mike Smith",
        "avatar": "url",
        "role": "Assistant",
        "is_lead": false
      }
    ]
  }
}
```

---

### 3.8 Export Sleep Report

**Endpoint:** `GET /institutions/{institution_id}/sleep-statuses/export`

**Description:** Download Excel report of sleep statuses.

**Query Parameters:**
- `from` (required): Start date `dd/MM/yyyy`
- `to` (required): End date `dd/MM/yyyy`
- `classroom` (optional): Classroom ID
- `children` (optional): Comma-separated child IDs

**Success Response (200):**
Returns Excel file (`.xlsx`)

---

### 3.9 Check Child Vacation Conflict

**Endpoint:** `GET /v1/holidays/children/{child_id}/check/attendance-conflict`

**Description:** Check if vacation dates conflict with existing records.

**Query Parameters:**
- `status_check`: `false`
- `start_date` (required): Format `dd/MM/yyyy`
- `end_date` (required): Format `dd/MM/yyyy`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "conflict": false,
    "conflicting_dates": []
  }
}
```

---

### 3.10 Get Dashboard Statistics

**Endpoint:** `GET /v1/institutions/{institution_id}/dashboard/statistics`

**Description:** Get summary statistics for dashboard widgets.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "children_count": {
      "total": 120,
      "present_today": 105,
      "absent_today": 15
    },
    "staff_count": {
      "total": 15,
      "present_today": 14
    },
    "pending_approvals": {
      "posts": 3,
      "observations": 2
    },
    "unread_messages": 5,
    "upcoming_events": 2,
    "unpaid_invoices": 12
  }
}
```

---

## 4. Messages Module

### 4.1 Get Conversations List

**Endpoint:** `GET /v1/conversations/institutions/{institution_id}`

**Description:** Get list of conversations (inbox).

**Permission Required:** `receive_messages`

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `type` (optional): `un-archived` | `archived`
- `per_page` (optional): Items per page (default: 20)

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "data": [
      {
        "id": 6001,
        "name": "Emma's Progress",
        "description": null,
        "avatar": "url",
        "is_group": false,
        "is_muted": false,
        "is_archived": false,
        "participants": [
          {
            "id": 201,
            "full_name": "Sarah Johnson",
            "avatar": "url",
            "role": "Teacher"
          },
          {
            "id": 12345,
            "full_name": "John Doe",
            "avatar": "url",
            "role": "Parent"
          }
        ],
        "last_message": {
          "id": 9001,
          "message": "Thank you for the update!",
          "sender": {
            "id": 12345,
            "full_name": "John Doe"
          },
          "created_at": "2026-05-22 16:30:00",
          "is_seen": true
        },
        "unread_count": 0,
        "created_at": "2026-05-20 10:00:00",
        "updated_at": "2026-05-22 16:30:00"
      }
    ],
    "meta": {
      "current_page": 1,
      "last_page": 2,
      "total": 15
    }
  }
}
```

---

### 4.2 Get Unread Conversations

**Endpoint:** `GET /v1/conversations/institutions/{institution_id}/unread-list`

**Description:** Get conversations with unread messages.

**Query Parameters:**
- `page` (optional): Page number

**Success Response (200):**
Same structure as Get Conversations List, filtered to unread only

---

### 4.3 Get Conversation Details

**Endpoint:** `GET /v1/conversations/{conversation_id}/institutions/{institution_id}`

**Description:** Get messages in a conversation.

**Query Parameters:**
- `page` (optional): Page number (default: 1)

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "conversation": {
      "id": 6001,
      "name": "Emma's Progress",
      "is_group": false,
      "participants": [ /* ... */ ]
    },
    "messages": [
      {
        "id": 9001,
        "conversation_id": 6001,
        "message": "Thank you for the update!",
        "sender": {
          "id": 12345,
          "full_name": "John Doe",
          "avatar": "url"
        },
        "attachments": [
          {
            "id": 7001,
            "type": "photo",
            "url": "url",
            "mime_type": "image/jpeg"
          }
        ],
        "is_seen": true,
        "seen_by": [
          {
            "user_id": 201,
            "seen_at": "2026-05-22 16:35:00"
          }
        ],
        "created_at": "2026-05-22 16:30:00"
      }
    ],
    "meta": {
      "current_page": 1,
      "last_page": 3,
      "total": 42
    }
  }
}
```

---

### 4.4 Create Conversation

**Endpoint:** `POST /v2/institutions/{institution_id}/conversations`

**Description:** Start a new conversation.

**Permission Required:** `send_message`

**Request Body:**
```json
{
  "name": "Meeting Discussion",
  "description": "Planning next week's activities",
  "is_group": true,
  "recipients": {
    "user_ids": [201, 202, 203]
  }
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Conversation created successfully",
  "Data": {
    "conversation_id": 6002,
    "name": "Meeting Discussion",
    "participants": [ /* ... */ ]
  }
}
```

---

### 4.5 Send Message

**Endpoint:** `POST /v1/messages`

**Description:** Send message in a conversation.

**Permission Required:** `send_message`

**Request Body:**
```json
{
  "conversation_id": 6001,
  "message": "Hello! Just checking in on Emma's progress.",
  "attachments": [
    {
      "type": "photo",
      "url": "https://cdn.parent.eu/uploads/photo.jpg",
      "mime_type": "image/jpeg",
      "name": "photo.jpg"
    }
  ]
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Message sent successfully",
  "Data": {
    "id": 9002,
    "conversation_id": 6001,
    "message": "Hello! Just checking in on Emma's progress.",
    "sender": {
      "id": 12345,
      "full_name": "John Doe"
    },
    "created_at": "2026-05-23 10:15:00"
  }
}
```

---

### 4.6 Mark Conversation as Seen

**Endpoint:** `PUT /v1/conversations/{conversation_id}/seen`

**Description:** Mark all messages in conversation as read.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Conversation marked as seen"
}
```

---

### 4.7 Delete Message

**Endpoint:** `DELETE /v1/messages/{message_id}/delete`

**Description:** Delete a message (own messages only).

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Message deleted successfully"
}
```

---

### 4.8 Archive Conversation

**Endpoint:** `PUT /v1/conversations/{conversation_id}/archive`

**Description:** Archive a conversation.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Conversation archived"
}
```

---

### 4.9 Unarchive Conversation

**Endpoint:** `PUT /v1/conversations/{conversation_id}/un-archive`

**Description:** Restore archived conversation.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Conversation restored"
}
```

---

### 4.10 Mute Conversation

**Endpoint:** `PUT /v1/conversations/{conversation_id}/mute`

**Description:** Mute/unmute conversation notifications.

**Request Body:**
```json
{
  "is_mute": true
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Conversation muted"
}
```

---

### 4.11 Delete Conversation

**Endpoint:** `DELETE /v1/conversations/{conversation_id}/delete`

**Description:** Delete conversation (non-group).

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Conversation deleted"
}
```

---

### 4.12 Exit Group Conversation

**Endpoint:** `PUT /v1/conversations/{conversation_id}/exit`

**Description:** Leave a group conversation.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "You have left the group"
}
```

---

### 4.13 Get Conversation Info

**Endpoint:** `GET /v1/conversations/{conversation_id}/institutions/{institution_id}/details`

**Description:** Get detailed conversation information (group name, members, etc.).

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "id": 6001,
    "name": "Toddlers Room - Parents",
    "description": "Communication for Toddlers Room parents",
    "avatar": "url",
    "is_group": true,
    "created_by": {
      "id": 201,
      "full_name": "Sarah Johnson"
    },
    "admins": [201],
    "members": [
      {
        "id": 12345,
        "full_name": "John Doe",
        "avatar": "url",
        "is_admin": false
      }
    ],
    "created_at": "2026-05-01 09:00:00"
  }
}
```

---

### 4.14 Update Group Info

**Endpoint:** `PUT /v1/conversations/{conversation_id}/institutions/{institution_id}/details`

**Description:** Update group name, description, or avatar (admins only).

**Request Body:**
```json
{
  "name": "Updated Group Name",
  "description": "New description",
  "avatar": "https://cdn.parent.eu/avatars/group.jpg"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Group updated successfully"
}
```

---

### 4.15 Add Participants to Group

**Endpoint:** `PUT /v1/conversations/{conversation_id}/institutions/{institution_id}/add-participants`

**Description:** Add users to group conversation (admins only).

**Request Body:**
```json
{
  "user_ids": [303, 304, 305]
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Participants added successfully"
}
```

---

### 4.16 Remove Participant from Group

**Endpoint:** `PUT /v1/conversations/{conversation_id}/institutions/{institution_id}/remove-participant`

**Description:** Remove user from group (admins only).

**Request Body:**
```json
{
  "user_id": 303
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Participant removed successfully"
}
```

---

### 4.17 Toggle Group Admin

**Endpoint:** `PUT /v1/conversations/{conversation_id}/institutions/{institution_id}/toggle-admin`

**Description:** Grant/revoke admin rights (admins only).

**Request Body:**
```json
{
  "user_id": 303
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Admin status updated"
}
```

---

### 4.18 Get Conversation Attachments

**Endpoint:** `GET /v1/conversations/{conversation_id}/institutions/{institution_id}/{media_type}`

**Description:** Get media files from conversation.

**Path Parameters:**
- `media_type`: `media` | `documents` | `links`

**Query Parameters:**
- `page` (optional): Page number
- `search` (optional): Search query

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "attachments": [
      {
        "id": 7001,
        "type": "photo",
        "url": "url",
        "thumbnail_url": "url",
        "name": "photo.jpg",
        "size": 1024000,
        "mime_type": "image/jpeg",
        "uploaded_by": {
          "id": 201,
          "full_name": "Sarah Johnson"
        },
        "created_at": "2026-05-22 14:00:00"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 15
    }
  }
}
```

---

### 4.19 Get Recipients List

**Endpoint:** `GET /v2/institutions/{institution_id}/recipients`

**Description:** Get available recipients for creating conversations.

**Query Parameters:**
- `category` (required): `children` | `staff` | `parents` | `channels`
- `keyword` (optional): Search term
- `page` (optional): Page number
- `type`: `messages`
- `per_page` (optional): Items per page

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "recipients": [
      {
        "id": 201,
        "full_name": "Sarah Johnson",
        "avatar": "url",
        "role": "Teacher",
        "classroom": "Toddlers Room"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 12
    }
  }
}
```

---

## 5. Child Profile Module

### 5.1 Get My Children (Parent)

**Endpoint:** `GET /v2/my/children`

**Description:** Get list of children linked to parent account.

**Permission Required:** Parent role

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "children": [
      {
        "id": 5001,
        "first_name": "Emma",
        "last_name": "Doe",
        "full_name": "Emma Doe",
        "avatar": "https://cdn.parent.eu/children/5001.jpg",
        "date_of_birth": "15/03/2020",
        "age": "6 years 2 months",
        "gender": "female",
        "classroom": {
          "id": 10,
          "name": "Toddlers Room",
          "age_range": "1-2 years"
        },
        "institution": {
          "id": 100,
          "name": "Sunshine Daycare"
        },
        "enrollment_status": "current",
        "role": {
          "id": 5,
          "name": "Child",
          "permissions": [
            {
              "id": 1,
              "name": "View Profile",
              "permission_code": "view_child_profile"
            }
          ]
        }
      }
    ]
  }
}
```

---

### 5.2 Get Child Basic Information

**Endpoint:** `GET /v1/children/{child_id}/profile`

**Description:** Get child's basic profile information.

**Permission Required:** `view_child_profile`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "child": {
      "id": 5001,
      "first_name": "Emma",
      "last_name": "Doe",
      "full_name": "Emma Doe",
      "avatar": "url",
      "date_of_birth": "15/03/2020",
      "age": "6 years 2 months",
      "gender": "female",
      "nationality": "American",
      "languages": ["English", "Spanish"],
      "enrollment_date": "01/09/2024",
      "enrollment_status": "current",
      "classroom": {
        "id": 10,
        "name": "Toddlers Room"
      },
      "teachers": [
        {
          "id": 201,
          "full_name": "Sarah Johnson",
          "avatar": "url",
          "role": "Lead Teacher"
        }
      ]
    }
  }
}
```

---

### 5.3 Get Child Health Information

**Endpoint:** `GET /v1/children/{child_id}/health-info`

**Description:** Get child's health and allergy information.

**Permission Required:** `view_child_health_info`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "child_id": 5001,
    "allergies": [
      {
        "id": 801,
        "allergen": "Peanuts",
        "severity": "severe",
        "reaction": "Anaphylaxis",
        "treatment": "EpiPen available in classroom",
        "added_date": "01/09/2024"
      }
    ],
    "medical_conditions": [
      {
        "id": 901,
        "condition": "Asthma",
        "severity": "mild",
        "medication": "Albuterol inhaler as needed",
        "notes": "Triggers: exercise, cold air"
      }
    ],
    "medications": [
      {
        "id": 1001,
        "name": "Albuterol",
        "dosage": "2 puffs",
        "frequency": "As needed",
        "administration_notes": "Use spacer"
      }
    ],
    "emergency_procedures": "Call 911 if severe asthma attack. EpiPen in case of allergic reaction.",
    "blood_type": "A+",
    "doctor": {
      "name": "Dr. Smith",
      "phone": "+1234567890"
    }
  }
}
```

---

### 5.4 Update Child Health Information (Staff)

**Endpoint:** `PUT /v1/children/{child_id}/health-info`

**Description:** Update health information.

**Permission Required:** `edit_child_health_info`

**Request Body:**
```json
{
  "allergies": [
    {
      "allergen": "Peanuts",
      "severity": "severe",
      "reaction": "Anaphylaxis",
      "treatment": "EpiPen available"
    }
  ],
  "medical_conditions": [ /* ... */ ],
  "medications": [ /* ... */ ],
  "emergency_procedures": "Updated procedures...",
  "blood_type": "A+"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Health information updated successfully"
}
```

---

### 5.5 Get Child Contacts

**Endpoint:** `GET /v1/children/{child_id}/contacts`

**Description:** Get emergency contacts and authorized contacts for child.

**Permission Required:** `view_child_contacts`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "child_id": 5001,
    "primary_contacts": [
      {
        "id": 12345,
        "full_name": "John Doe",
        "relationship": "Father",
        "email": "john@example.com",
        "mobile": "+1234567890",
        "avatar": "url",
        "is_primary": true,
        "can_pickup": true,
        "linked": true
      }
    ],
    "emergency_contacts": [
      {
        "id": 12346,
        "full_name": "Jane Smith",
        "relationship": "Grandmother",
        "phone": "+9876543210",
        "can_pickup": true,
        "priority": 1
      }
    ],
    "authorized_pickups": [
      {
        "id": 12347,
        "full_name": "Bob Johnson",
        "relationship": "Family Friend",
        "phone": "+1122334455",
        "id_verified": true,
        "photo": "url"
      }
    ]
  }
}
```

---

### 5.6 Get Child Pickup Information

**Endpoint:** `GET /v1/children/{child_id}/pickup-info`

**Description:** Get authorized pickup persons.

**Permission Required:** `view_child_pickup_info`

**Success Response (200):**
Similar to Get Child Contacts, focused on pickup authorization

---

### 5.7 Get Child Calendar

**Endpoint:** `GET /v2/institutions/{institution_id}/calendars?child_id={child_id}`

**Description:** Get calendar events for specific child.

**Permission Required:** `view_child_calendar`

**Query Parameters:**
- `child_id` (required): Child ID
- `year` (optional): Year
- `week` (optional): Week number range

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "events": [
      {
        "id": 3001,
        "title": "Zoo Field Trip",
        "type": "event",
        "date": "24/05/2026",
        "start_time": "09:00:00",
        "end_time": "15:00:00",
        "description": "Annual zoo visit with lunch",
        "location": "City Zoo",
        "requires_response": true,
        "response_status": "accepted"
      },
      {
        "id": 3002,
        "title": "Parent-Teacher Conference",
        "type": "meeting",
        "date": "28/05/2026",
        "start_time": "16:00:00",
        "end_time": "16:30:00",
        "description": "Discuss Emma's progress"
      }
    ]
  }
}
```

---

### 5.8 Get Child Permissions List

**Endpoint:** `GET /v1/children/{child_id}/permissions`

**Description:** Get permissions/consents granted for child.

**Permission Required:** `view_child_permissions_list`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "child_id": 5001,
    "permissions": [
      {
        "id": 401,
        "type": "photo_consent",
        "title": "Photo & Video Consent",
        "description": "Permission to photograph/video child for educational purposes",
        "status": "granted",
        "granted_by": {
          "id": 12345,
          "full_name": "John Doe"
        },
        "granted_date": "01/09/2024",
        "expires": null
      },
      {
        "id": 402,
        "type": "field_trip",
        "title": "Field Trip - Zoo",
        "description": "Permission for zoo field trip on 24/05/2026",
        "status": "pending",
        "requested_date": "15/05/2026",
        "deadline": "22/05/2026"
      }
    ]
  }
}
```

---

### 5.9 Reply to Permission Request (Parent)

**Endpoint:** `PUT /v1/children/{child_id}/permissions/{permission_id}/reply`

**Description:** Accept or decline permission request.

**Permission Required:** `edit_child_permission_reply`

**Request Body:**
```json
{
  "status": "granted",
  "note": "Emma is excited for the trip!"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Permission updated successfully"
}
```

---

### 5.10 Get Child Gallery

**Endpoint:** `GET /v1/children/{child_id}/gallery`

**Description:** Get child's photo/video gallery.

**Permission Required:** `view_child_gallery`

**Query Parameters:**
- `page` (optional): Page number
- `type` (optional): `photo` | `video`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "media": [
      {
        "id": 7001,
        "type": "photo",
        "url": "https://cdn.parent.eu/media/7001.jpg",
        "thumbnail_url": "https://cdn.parent.eu/media/7001_thumb.jpg",
        "caption": "Emma painting today!",
        "uploaded_by": {
          "id": 201,
          "full_name": "Sarah Johnson"
        },
        "uploaded_date": "23/05/2026",
        "source": "newsfeed",
        "source_id": 1001
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 156
    }
  }
}
```

---

### 5.11 Get Child Development Journey

**Endpoint:** `GET /v1/children/{child_id}/journey`

**Description:** Get child development observations and milestones.

**Permission Required:** `view_child_journey`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "child_id": 5001,
    "observations": [
      {
        "id": 2001,
        "title": "Sharing with Friends",
        "description": "Emma shared her toys with other children during playtime",
        "date": "20/05/2026",
        "author": {
          "id": 201,
          "full_name": "Sarah Johnson"
        },
        "curriculum_areas": [
          {
            "id": 1,
            "name": "Personal, Social and Emotional Development"
          }
        ],
        "attachments": [
          {
            "type": "photo",
            "url": "url"
          }
        ]
      }
    ],
    "milestones": [
      {
        "id": 301,
        "title": "First Words",
        "description": "Said first clear word",
        "achieved_date": "10/01/2025",
        "age_at_achievement": "10 months"
      }
    ]
  }
}
```

---

### 5.12 Get Child Daily Report (Parent)

**Endpoint:** `GET /v1/children/{child_id}/daily-report`

**Description:** Get summary of child's day.

**Permission Required:** `view_child_daily_report`

**Query Parameters:**
- `date` (required): Format `dd/MM/yyyy`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "child_id": 5001,
    "date": "23/05/2026",
    "attendance": {
      "check_in": "08:30:00",
      "check_out": "17:00:00",
      "dropped_by": "Father",
      "picked_by": "Mother"
    },
    "meals": [
      {
        "type": "breakfast",
        "eaten": "all",
        "time": "09:00:00",
        "note": "Enjoyed pancakes"
      },
      {
        "type": "lunch",
        "eaten": "most",
        "time": "12:00:00",
        "note": "Left vegetables"
      },
      {
        "type": "snack",
        "eaten": "all",
        "time": "15:00:00"
      }
    ],
    "sleep": {
      "start_time": "13:00:00",
      "end_time": "15:00:00",
      "duration": 120,
      "quality": "good"
    },
    "toilet": [
      {
        "time": "10:30:00",
        "type": "success"
      },
      {
        "time": "14:30:00",
        "type": "success"
      }
    ],
    "mood": {
      "morning": "happy",
      "afternoon": "happy",
      "overall": "Great day! Very engaged in activities."
    },
    "activities": [
      {
        "name": "Painting",
        "time": "10:00:00",
        "note": "Created beautiful rainbow painting"
      },
      {
        "name": "Story Time",
        "time": "14:00:00",
        "note": "Listened attentively to new book"
      }
    ],
    "notes": "Emma had a wonderful day. Very social and helpful with other children."
  }
}
```

---

### 5.13 Get Children List (Staff)

**Endpoint:** `GET /v1/children/institutions/{institution_id}`

**Description:** Get list of all children in institution.

**Query Parameters:**
- `keyword` (optional): Search term
- `page` (optional): Page number

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "data": [
      {
        "id": 5001,
        "first_name": "Emma",
        "last_name": "Doe",
        "avatar": "url",
        "date_of_birth": "15/03/2020",
        "classroom": {
          "id": 10,
          "name": "Toddlers Room"
        },
        "status": "current"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 120
    }
  }
}
```

---

### 5.14 Get Siblings

**Endpoint:** `GET /v2/children/institutions/{institution_id}/list`

**Description:** Get list of children for sibling linking.

**Query Parameters:**
- `keyword` (optional): Search term
- `filter`: `1,2,3` (statuses)
- `page` (optional): Page number
- `hide_anonymized`: `true`

**Success Response (200):**
Similar to Get Children List

---

## 6. Calendar Module

### 6.1 Get Calendar Events

**Endpoint:** `GET /v2/institutions/{institution_id}/calendars`

**Description:** Get calendar events for institution or specific child.

**Permission Required:** `view_institute_calendar` or `view_my_calendar`

**Query Parameters:**
- `child_id` (optional): Filter by child
- `year` (optional): Year number
- `week` (optional): Week range (e.g., `20,21`)

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "events": [
      {
        "id": 3001,
        "title": "Zoo Field Trip",
        "type": "event",
        "date": "24/05/2026",
        "start_time": "09:00:00",
        "end_time": "15:00:00",
        "all_day": false,
        "description": "<p>Annual zoo visit. Lunch provided.</p>",
        "location": "City Zoo",
        "organizer": {
          "id": 201,
          "full_name": "Sarah Johnson"
        },
        "recipients": {
          "classes": [10],
          "children": [],
          "groups": []
        },
        "requires_response": true,
        "response_deadline": "22/05/2026",
        "my_response": "accepted",
        "responses_summary": {
          "accepted": 20,
          "declined": 2,
          "pending": 3
        },
        "attachments": [
          {
            "type": "document",
            "url": "url",
            "name": "permission_slip.pdf"
          }
        ],
        "created_at": "15/05/2026",
        "updated_at": "16/05/2026"
      },
      {
        "id": 3002,
        "title": "School Closed - Holiday",
        "type": "holiday",
        "date": "30/05/2026",
        "all_day": true,
        "description": "Memorial Day - School closed"
      }
    ]
  }
}
```

---

### 6.2 Get User Calendar

**Endpoint:** `GET /v2/institutions/{institution_id}/calendars/users/{user_id}`

**Description:** Get calendar for specific user (parent view).

**Permission Required:** `view_my_calendar`

**Query Parameters:**
- `year` (optional): Year
- `week` (optional): Week range

**Success Response (200):**
Same structure as Get Calendar Events

---

### 6.3 Get Calendar Overview

**Endpoint:** `GET /v1/institutions/{institution_id}/calendars/overview`  
**Endpoint:** `GET /v1/institutions/{institution_id}/calendars/me/overview` (Parent)

**Description:** Get calendar month overview with event indicators.

**Query Parameters:**
- `months` (required): Comma-separated month strings (e.g., `2026-05,2026-06`)
- `child_id` (optional): Filter by child

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": [
    {
      "date": "2026-05-24",
      "types": ["event", "holiday"]
    },
    {
      "date": "2026-05-28",
      "types": ["meeting"]
    }
  ]
}
```

---

### 6.4 Get Single Day Events

**Endpoint:** `GET /v1/institutions/{institution_id}/calendars/{date}`  
**Endpoint:** `GET /v1/institutions/{institution_id}/calendars/users/{user_id}/{date}`

**Description:** Get all events for specific date.

**Path Parameters:**
- `date`: Format `dd-MM-yyyy`

**Query Parameters:**
- `child_id` (optional): Filter by child

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "date": "24/05/2026",
    "events": [ /* Event objects */ ],
    "holidays": [ /* Holiday objects */ ],
    "activities": [ /* Activity objects */ ]
  }
}
```

---

### 6.5 Get Upcoming Events

**Endpoint:** `GET /v2/institutions/{institution_id}/calendars/upcoming-events`

**Description:** Get upcoming events for newsfeed widget.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "upcoming_events": [
      {
        "id": 3001,
        "title": "Zoo Field Trip",
        "date": "24/05/2026",
        "start_time": "09:00:00",
        "type": "event",
        "requires_response": true,
        "response_status": "pending"
      }
    ]
  }
}
```

---

### 6.6 Get Event Details

**Endpoint:** `GET /v1/events/{event_id}`

**Description:** Get detailed information about an event.

**Permission Required:** `view_event`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "id": 3001,
    "title": "Zoo Field Trip",
    "type": "event",
    "date": "24/05/2026",
    "start_time": "09:00:00",
    "end_time": "15:00:00",
    "all_day": false,
    "description": "<p>Annual zoo visit...</p>",
    "location": "City Zoo",
    "organizer": {
      "id": 201,
      "full_name": "Sarah Johnson",
      "avatar": "url"
    },
    "recipients": {
      "classes": [
        {
          "id": 10,
          "name": "Toddlers Room"
        }
      ],
      "children": [],
      "groups": []
    },
    "requires_response": true,
    "response_deadline": "22/05/2026",
    "allow_multiple_responses": false,
    "attachments": [
      {
        "id": 7001,
        "type": "document",
        "url": "url",
        "name": "permission_slip.pdf"
      }
    ],
    "created_at": "15/05/2026",
    "updated_at": "16/05/2026"
  }
}
```

---

### 6.7 Get Event Replies

**Endpoint:** `GET /v1/events/{event_id}/replies`

**Description:** Get responses to an event (staff only).

**Permission Required:** `view_event_replies`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "replies": [
      {
        "id": 501,
        "event_id": 3001,
        "user": {
          "id": 12345,
          "full_name": "John Doe",
          "avatar": "url"
        },
        "child": {
          "id": 5001,
          "full_name": "Emma Doe"
        },
        "response": "accepted",
        "note": "Emma is very excited!",
        "replied_at": "16/05/2026 10:30:00"
      }
    ],
    "summary": {
      "accepted": 20,
      "declined": 2,
      "pending": 3,
      "total": 25
    }
  }
}
```

---

### 6.8 Reply to Event (Parent)

**Endpoint:** `POST /v1/events/{event_id}/replies`

**Description:** Respond to an event invitation.

**Permission Required:** `reply_on_event`

**Request Body:**
```json
{
  "child_id": 5001,
  "response": "accepted",
  "note": "Emma will attend. Please note her peanut allergy."
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Response recorded successfully",
  "Data": {
    "reply_id": 501,
    "response": "accepted"
  }
}
```

---

### 6.9 Create Event (Staff)

**Endpoint:** `POST /v1/events`

**Description:** Create new calendar event.

**Permission Required:** `create_event`

**Request Body:**
```json
{
  "title": "Summer Festival",
  "type": "event",
  "date": "15/06/2026",
  "start_time": "14:00:00",
  "end_time": "17:00:00",
  "all_day": false,
  "description": "<p>Join us for our annual summer festival!</p>",
  "location": "School Playground",
  "recipients": {
    "recipient_type": "classes",
    "class_ids": [10, 11, 12],
    "children_ids": [],
    "group_ids": []
  },
  "requires_response": true,
  "response_deadline": "10/06/2026",
  "attachments": [
    {
      "type": "document",
      "url": "url",
      "name": "event_details.pdf"
    }
  ]
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Event created successfully",
  "Data": {
    "id": 3003,
    "title": "Summer Festival",
    "date": "15/06/2026"
  }
}
```

---

### 6.10 Update Event (Staff)

**Endpoint:** `PUT /v1/events/{event_id}`

**Description:** Update existing event.

**Permission Required:** `edit_event`

**Request Body:**
Same as Create Event

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Event updated successfully"
}
```

---

### 6.11 Delete Event (Staff)

**Endpoint:** `DELETE /v1/events/{event_id}`

**Description:** Delete calendar event.

**Permission Required:** `delete_event`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Event deleted successfully"
}
```

---

### 6.12 Get Activity Details

**Endpoint:** `GET /v1/activities/{activity_id}`

**Description:** Get details of a recurring activity.

**Permission Required:** `view_activity`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "id": 4001,
    "name": "Music Class",
    "description": "Weekly music and movement class",
    "day_of_week": "monday",
    "start_time": "10:00:00",
    "end_time": "10:45:00",
    "start_date": "01/09/2024",
    "end_date": "30/06/2025",
    "classroom": {
      "id": 10,
      "name": "Toddlers Room"
    },
    "instructor": {
      "id": 203,
      "full_name": "Music Teacher"
    }
  }
}
```

---

### 6.13 Create Activity (Staff)

**Endpoint:** `POST /v1/activities`

**Description:** Create recurring activity.

**Permission Required:** `create_activity`

**Request Body:**
```json
{
  "name": "Art Class",
  "description": "Creative arts and crafts",
  "day_of_week": "wednesday",
  "start_time": "14:00:00",
  "end_time": "15:00:00",
  "start_date": "01/09/2024",
  "end_date": "30/06/2025",
  "classroom_ids": [10],
  "instructor_id": 204
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Activity created successfully",
  "Data": {
    "id": 4002
  }
}
```

---

### 6.14 Update Activity (Staff)

**Endpoint:** `PUT /v1/activities/{activity_id}`

**Description:** Update recurring activity.

**Permission Required:** `edit_activity`

**Request Body:**
Same as Create Activity

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Activity updated successfully"
}
```

---

### 6.15 Delete Activity (Staff)

**Endpoint:** `DELETE /v1/activities/{activity_id}`

**Description:** Delete recurring activity.

**Permission Required:** `delete_activity`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Activity deleted successfully"
}
```

---

### 6.16 Report Vacation (Parent)

**Endpoint:** `POST /v1/holidays`

**Description:** Report child vacation/absence.

**Permission Required:** `report_vacation`

**Request Body:**
```json
{
  "child_id": 5001,
  "start_date": "01/06/2026",
  "end_date": "07/06/2026",
  "type": "vacation",
  "note": "Family vacation to beach"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Vacation reported successfully",
  "Data": {
    "holiday_id": 5001
  }
}
```

---

### 6.17 Get Holiday Details

**Endpoint:** `GET /v3/institutions/{institution_id}/holidays/{holiday_id}`  
**Endpoint:** `GET /institutions/{institution_id}/holidays/{holiday_id}?child_id={child_id}` (Parent)

**Description:** Get details of vacation/holiday report.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "id": 5001,
    "child": {
      "id": 5001,
      "full_name": "Emma Doe"
    },
    "type": "vacation",
    "start_date": "01/06/2026",
    "end_date": "07/06/2026",
    "note": "Family vacation to beach",
    "reported_by": {
      "id": 12345,
      "full_name": "John Doe"
    },
    "reported_at": "20/05/2026 10:00:00"
  }
}
```

---

### 6.18 Update Vacation (Parent)

**Endpoint:** `PUT /v1/children/vacations/{vacation_id}`

**Description:** Edit vacation dates or note.

**Request Body:**
```json
{
  "date": "01/06/2026",
  "note": "Updated vacation note"
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Vacation updated successfully"
}
```

---

### 6.19 Delete Vacation (Parent)

**Endpoint:** `DELETE /v1/children/vacations/{vacation_id}`

**Description:** Cancel vacation report.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Vacation deleted successfully"
}
```

---

### 6.20 Get Recipients for Event

**Endpoint:** `GET /v2/recipients/{institution_id}/event`

**Description:** Get available recipients for event creation.

**Query Parameters:**
- `keyword` (optional): Search term
- `page` (optional): Page number

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "data": [
      {
        "id": 10,
        "name": "Toddlers Room",
        "type": "classroom",
        "child_count": 25
      },
      {
        "id": 5001,
        "name": "Emma Doe",
        "type": "child",
        "classroom": "Toddlers Room"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 45
    }
  }
}
```

---

## 7. Billing Transaction Module

### 7.1 Get All Classes

**Endpoint:** `GET /v1/institutions/{institution_id}/all-classes`

**Description:** Get list of all classrooms for filtering.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "classes": [
      {
        "id": 10,
        "name": "Toddlers Room",
        "age_range": "1-2 years"
      },
      {
        "id": 11,
        "name": "Preschool A",
        "age_range": "3-4 years"
      }
    ]
  }
}
```

---

### 7.2 Get Payers List (Parent/Staff)

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/payers`

**Description:** Get list of payers with balance information.

**Permission Required:** Parent (own account) or `view_payers` (staff)

**Query Parameters:**
- `page` (optional): Page number
- `order_by` (optional): `name` | `balance` | `updated_at`
- `order` (optional): `asc` | `desc`
- `name` (optional): Search by payer or child name
- `balance` (optional): Filter by balance amount
- `balance_operator` (optional): `eq` | `gt` | `lt` | `gte` | `lte`
- `payers_without_invoice`: `on`
- `payers_without_payment_method`: `on`

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "payers": [
      {
        "id": 8001,
        "name": "John Doe",
        "email": "john@example.com",
        "mobile": "+1234567890",
        "balance": 1250.50,
        "currency": "USD",
        "children": [
          {
            "id": 5001,
            "full_name": "Emma Doe",
            "classroom": "Toddlers Room"
          }
        ],
        "payment_method": {
          "type": "credit_card",
          "last_four": "4242"
        },
        "last_payment": {
          "amount": 500.00,
          "date": "01/05/2026"
        },
        "next_invoice": {
          "amount": 850.00,
          "due_date": "01/06/2026"
        }
      }
    ],
    "meta": {
      "current_page": 1,
      "last_page": 3,
      "total": 45
    }
  }
}
```

---

### 7.3 Download Payers Report

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/payers/download`

**Description:** Download payers list as CSV or Excel.

**Query Parameters:**
- `format` (required): `csv` | `xlsx` | `aging`
- `order_by` (optional): Sort field
- `order` (optional): Sort direction
- `payer_name` (optional): Filter by payer name
- `child_name` (optional): Filter by child name
- `balance` (optional): Balance filter
- `balance_operator` (optional): Balance comparison

**Success Response (200):**
Returns file download (CSV or Excel)

---

### 7.4 Get Invoices List

**Endpoint:** `POST /v1/institutions/{institution_id}/billing/invoices`

**Description:** Get list of invoices with filters.

**Permission Required:** Parent (own invoices) or `view_invoice` (staff)

**Request Body:**
```json
{
  "page": 1,
  "order_by": "due_date",
  "order": "desc",
  "status": "unpaid,overdue",
  "start_date": "01/05/2026",
  "end_date": "31/05/2026",
  "class_id": "10",
  "name": "John",
  "institutions": [100]
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "invoices": [
      {
        "id": 9001,
        "invoice_number": "INV-2026-05-001",
        "payer": {
          "id": 8001,
          "name": "John Doe"
        },
        "child": {
          "id": 5001,
          "full_name": "Emma Doe",
          "classroom": "Toddlers Room"
        },
        "issue_date": "01/05/2026",
        "due_date": "15/05/2026",
        "status": "unpaid",
        "subtotal": 800.00,
        "tax": 50.00,
        "total": 850.00,
        "paid": 0.00,
        "balance": 850.00,
        "currency": "USD",
        "line_items": [
          {
            "id": 101,
            "description": "Tuition - May 2026",
            "quantity": 1,
            "unit_price": 800.00,
            "total": 800.00
          }
        ]
      }
    ],
    "totals": {
      "subtotal": 4500.00,
      "tax": 350.00,
      "total": 4850.00,
      "paid": 2000.00,
      "balance": 2850.00
    },
    "meta": {
      "current_page": 1,
      "last_page": 2,
      "total": 28
    }
  }
}
```

---

### 7.5 Get Invoice Details

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/invoices/{invoice_id}`

**Description:** Get detailed invoice information.

**Permission Required:** Parent (own) or `view_invoice` (staff)

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "invoice": {
      "id": 9001,
      "invoice_number": "INV-2026-05-001",
      "payer": {
        "id": 8001,
        "name": "John Doe",
        "email": "john@example.com",
        "address": {
          "line1": "123 Main St",
          "city": "Anytown",
          "state": "CA",
          "zip": "12345"
        }
      },
      "child": {
        "id": 5001,
        "full_name": "Emma Doe"
      },
      "issue_date": "01/05/2026",
      "due_date": "15/05/2026",
      "status": "unpaid",
      "line_items": [
        {
          "id": 101,
          "description": "Tuition - May 2026",
          "quantity": 1,
          "unit_price": 800.00,
          "discount": 0.00,
          "tax_rate": 0.00,
          "total": 800.00
        }
      ],
      "subtotal": 800.00,
      "discount": 0.00,
      "tax": 50.00,
      "total": 850.00,
      "paid": 0.00,
      "balance": 850.00,
      "currency": "USD",
      "notes": "Thank you for your business!",
      "payment_history": []
    }
  }
}
```

---

### 7.6 Download Invoice

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/invoices/{invoice_id}/download`

**Description:** Download invoice as PDF.

**Success Response (200):**
Returns PDF file

---

### 7.7 Get Payments List

**Endpoint:** `POST /v1/institutions/{institution_id}/billing/payments`

**Description:** Get payment transaction history.

**Permission Required:** Parent (own) or `view_invoice` (staff)

**Request Body:**
```json
{
  "page": 1,
  "child_status": "current,upcoming",
  "order_by": "date",
  "order": "desc",
  "with_subsidies": 1,
  "type": "payment,refund",
  "payment_status": "completed,pending",
  "payment_method": "cash,credit_card,check,electronic_transfer,parent_pay",
  "start_date": "01/05/2026",
  "end_date": "31/05/2026",
  "creation_start_date": "",
  "creation_end_date": "",
  "institutions": [100],
  "class_id": "10",
  "name": "John",
  "transaction_ref": ""
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "payments": [
      {
        "id": 10001,
        "transaction_ref": "PAY-2026-05-001",
        "payer": {
          "id": 8001,
          "name": "John Doe"
        },
        "child": {
          "id": 5001,
          "full_name": "Emma Doe"
        },
        "amount": 850.00,
        "currency": "USD",
        "payment_method": "credit_card",
        "payment_date": "10/05/2026",
        "status": "completed",
        "type": "payment",
        "invoice": {
          "id": 9001,
          "invoice_number": "INV-2026-05-001"
        },
        "created_by": {
          "id": 12345,
          "full_name": "John Doe"
        },
        "created_at": "10/05/2026 14:30:00"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 18
    }
  }
}
```

---

### 7.8 Get Payment Totals

**Endpoint:** `POST /v1/payments/totals`

**Description:** Get payment totals summary.

**Request Body:**
Same as Get Payments List

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "total_payments": 15250.00,
    "total_refunds": 500.00,
    "net_total": 14750.00,
    "currency": "USD",
    "by_method": {
      "credit_card": 10000.00,
      "cash": 3000.00,
      "check": 2250.00
    }
  }
}
```

---

### 7.9 Get Plans List

**Endpoint:** `POST /v1/institutions/{institution_id}/billing/plans`

**Description:** Get billing plans for children.

**Permission Required:** Parent (own) or `view_plan` (staff)

**Request Body:**
```json
{
  "page": 1,
  "order_by": "start_date",
  "order": "desc",
  "child_status": "current,upcoming",
  "name": "Emma",
  "class_id": "10",
  "plan_status": "active,upcoming",
  "institutions": [100]
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "plans": [
      {
        "id": 7001,
        "name": "Full-Time Care Plan",
        "child": {
          "id": 5001,
          "full_name": "Emma Doe",
          "classroom": "Toddlers Room"
        },
        "payer": {
          "id": 8001,
          "name": "John Doe"
        },
        "start_date": "01/09/2024",
        "end_date": "31/08/2025",
        "status": "active",
        "billing_frequency": "monthly",
        "amount": 800.00,
        "currency": "USD",
        "sessions_per_week": 5,
        "hours_per_day": 8
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 3
    }
  }
}
```

---

### 7.10 Get Plan Details

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/plans/{plan_id}`

**Description:** Get detailed plan information.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "plan": {
      "id": 7001,
      "name": "Full-Time Care Plan",
      "child": {
        "id": 5001,
        "full_name": "Emma Doe"
      },
      "payer": {
        "id": 8001,
        "name": "John Doe"
      },
      "start_date": "01/09/2024",
      "end_date": "31/08/2025",
      "status": "active",
      "billing_frequency": "monthly",
      "amount": 800.00,
      "currency": "USD",
      "sessions": [
        {
          "day": "monday",
          "start_time": "08:00:00",
          "end_time": "16:00:00"
        },
        {
          "day": "tuesday",
          "start_time": "08:00:00",
          "end_time": "16:00:00"
        }
      ],
      "discounts": [
        {
          "name": "Sibling Discount",
          "type": "percentage",
          "value": 10
        }
      ],
      "charges": [
        {
          "name": "Base Tuition",
          "amount": 800.00
        },
        {
          "name": "Meal Plan",
          "amount": 50.00
        }
      ],
      "total": 850.00
    }
  }
}
```

---

### 7.11 Get Deposits List

**Endpoint:** `POST /v1/institutions/{institution_id}/billing/deposits`

**Description:** Get deposit transactions.

**Permission Required:** Parent (own) or staff

**Request Body:**
```json
{
  "page": 1,
  "order_by": "date",
  "order": "desc",
  "child_status": "current,upcoming",
  "name": "John",
  "class_id": "10",
  "start_date": "01/01/2026",
  "end_date": "31/05/2026",
  "institutions": [100]
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "deposits": [
      {
        "id": 11001,
        "child": {
          "id": 5001,
          "full_name": "Emma Doe"
        },
        "payer": {
          "id": 8001,
          "name": "John Doe"
        },
        "amount": 500.00,
        "currency": "USD",
        "date": "01/09/2024",
        "type": "enrollment_deposit",
        "status": "held",
        "refundable": true,
        "refund_date": "31/08/2025",
        "notes": "Enrollment deposit for 2024-2025 school year"
      }
    ],
    "meta": {
      "current_page": 1,
      "total": 2
    }
  }
}
```

---

### 7.12 Pay Invoice (Parent)

**Endpoint:** `POST /v1/institutions/{institution_id}/billing/pay`

**Description:** Process payment for invoice (redirects to payment gateway).

**Permission Required:** Parent

**Request Body:**
```json
{
  "invoice_id": 9001,
  "amount": 850.00,
  "payment_method": "credit_card",
  "card_token": "tok_abc123",
  "save_card": true
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Payment processed successfully",
  "Data": {
    "payment_id": 10002,
    "transaction_ref": "PAY-2026-05-002",
    "status": "completed"
  }
}
```

---

### 7.13 Get Payment Statement (Parent)

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/statement`

**Description:** Get account statement summary.

**Query Parameters:**
- `start_date` (optional): Format `dd/MM/yyyy`
- `end_date` (optional): Format `dd/MM/yyyy`
- `child_id` (optional): Filter by child

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "statement": {
      "payer": {
        "id": 8001,
        "name": "John Doe"
      },
      "period": {
        "start": "01/01/2026",
        "end": "31/05/2026"
      },
      "opening_balance": 0.00,
      "invoices_total": 4250.00,
      "payments_total": 3000.00,
      "adjustments": 0.00,
      "closing_balance": 1250.00,
      "currency": "USD",
      "transactions": [
        {
          "date": "01/05/2026",
          "type": "invoice",
          "description": "Invoice INV-2026-05-001",
          "amount": 850.00,
          "balance": 850.00
        },
        {
          "date": "10/05/2026",
          "type": "payment",
          "description": "Payment - Credit Card",
          "amount": -850.00,
          "balance": 0.00
        }
      ]
    }
  }
}
```

---

### 7.14 Download Statement

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/statement/download`

**Description:** Download account statement as PDF.

**Query Parameters:**
- `start_date` (required): Format `dd/MM/yyyy`
- `end_date` (required): Format `dd/MM/yyyy`
- `child_id` (optional): Filter by child

**Success Response (200):**
Returns PDF file

---

### 7.15 Get Payment Methods (Parent)

**Endpoint:** `GET /v1/institutions/{institution_id}/billing/payment-methods`

**Description:** Get saved payment methods.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Data": {
    "payment_methods": [
      {
        "id": "pm_abc123",
        "type": "credit_card",
        "brand": "visa",
        "last_four": "4242",
        "exp_month": 12,
        "exp_year": 2027,
        "is_default": true,
        "created_at": "01/09/2024"
      }
    ]
  }
}
```

---

### 7.16 Add Payment Method (Parent)

**Endpoint:** `POST /v1/institutions/{institution_id}/billing/payment-methods`

**Description:** Add new payment method.

**Request Body:**
```json
{
  "type": "credit_card",
  "card_token": "tok_abc123",
  "set_as_default": true
}
```

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Payment method added successfully",
  "Data": {
    "payment_method_id": "pm_def456"
  }
}
```

---

### 7.17 Delete Payment Method (Parent)

**Endpoint:** `DELETE /v1/institutions/{institution_id}/billing/payment-methods/{payment_method_id}`

**Description:** Remove saved payment method.

**Success Response (200):**
```json
{
  "Status": "Success",
  "Message": "Payment method deleted successfully"
}
```

---

## Error Responses

### Authentication Errors

**401 Unauthorized:**
```json
{
  "Status": "Failed",
  "Message": "Authentication required"
}
```

**403 Forbidden:**
```json
{
  "Status": "Failed",
  "Message": "Insufficient permissions to access this resource",
  "Errors": [
    {
      "code": "permission_denied",
      "message": "You don't have permission to view this resource"
    }
  ]
}
```

**406/408/410 Token Expired:**
```json
{
  "Status": "Failed",
  "Message": "Token expired, please refresh",
  "Errors": [
    {
      "code": "token_expired",
      "message": "Your session has expired"
    }
  ]
}
```

---

### Validation Errors

**400 Bad Request:**
```json
{
  "Status": "Failed",
  "Message": "Validation failed",
  "Errors": [
    {
      "field": "email",
      "message": "Invalid email format"
    },
    {
      "field": "password",
      "message": "Password must be at least 8 characters"
    }
  ]
}
```

---

### Resource Errors

**404 Not Found:**
```json
{
  "Status": "Failed",
  "Message": "Resource not found",
  "Errors": [
    {
      "code": "not_found",
      "message": "The requested resource does not exist"
    }
  ]
}
```

---

### Rate Limiting

**429 Too Many Requests:**
```json
{
  "Status": "Failed",
  "Message": "Too many requests",
  "retry_after": 60
}
```

---

### Server Errors

**500 Internal Server Error:**
```json
{
  "Status": "Failed",
  "Message": "An internal error occurred. Please try again later."
}
```

**503 Service Unavailable (Maintenance):**
```json
{
  "Status": "Failed",
  "Message": "System under maintenance",
  "expected_return": "Saturday, 24 Jan 2026 at 7:00 AM EST"
}
```

---

## Appendix

### Date Format Standards

- **API Requests/Responses:** `dd/MM/yyyy` (e.g., `23/05/2026`)
- **Time Format:** `HH:mm:ss` (24-hour, e.g., `14:30:00`)
- **Datetime Format:** `yyyy-MM-dd HH:mm:ss` (e.g., `2026-05-23 14:30:00`)

**Note:** The API automatically normalizes dates from various formats (ISO, Arabic/Persian numerals) to the standard format.

---

### Pagination

Most list endpoints support pagination:

**Query Parameters:**
- `page`: Page number (default: 1)
- `per_page`: Items per page (default: 20, max varies by endpoint)

**Response Meta:**
```json
{
  "meta": {
    "current_page": 1,
    "last_page": 5,
    "per_page": 20,
    "total": 87,
    "from": 1,
    "to": 20
  }
}
```

---

### File Upload

Files must be uploaded separately via the attachment service before referencing in requests.

**Endpoint:** `POST /v1/attachments/upload`

**Request:** Multipart form data

**Response:**
```json
{
  "Status": "Success",
  "Data": {
    "file": "https://cdn.parent.eu/uploads/abc123.jpg",
    "thumbnail": "https://cdn.parent.eu/uploads/abc123_thumb.jpg"
  }
}
```

---

### Supported File Types

- **Images:** JPEG, PNG, GIF, WebP
- **Videos:** MP4, MOV, AVI
- **Documents:** PDF, DOC, DOCX, XLS, XLSX, PPT, PPTX
- **Audio:** MP3, WAV, M4A

---

### Rate Limits

- **Authentication endpoints:** 5 requests per minute per IP
- **Standard endpoints:** 60 requests per minute per user
- **Upload endpoints:** 10 requests per minute per user

---

### Webhooks (Staff/Admin)

The system can send webhooks for certain events. Contact support for configuration.

**Available Events:**
- `newsfeed.created`
- `newsfeed.published`
- `message.received`
- `invoice.created`
- `payment.completed`
- `child.checked_in`
- `child.checked_out`

---

### Support

**API Support:** api-support@parent.eu  
**Documentation:** https://docs.parent.eu  
**Status Page:** https://status.parent.eu

---

**End of Documentation**
