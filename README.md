# FlowSync — Setup & Fix Notes

## 🚀 Quick Start

```bash
# 1. Install dependencies
npm install

# 2. Start the server
node server.js
# OR for auto-restart on changes:
npx nodemon server.js

# 3. Open in browser
# http://localhost:3001
```

---

## 🔐 Admin / Super User

| Field | Value |
|-------|-------|
| Username | `sharifmolla354` |
| Password | `7506035297` |
| Role | Super Admin |

The admin account is **automatically created** the first time the server starts. It cannot be deleted from the admin panel.

---

## ✅ All Fixes Applied

### 1. Data now saves to MongoDB Atlas (not localStorage)
- **Problem**: The frontend had a full localStorage mock that intercepted ALL API calls, meaning nothing ever reached MongoDB.
- **Fix**: Removed the entire mock backend. The `call()` function now makes real `fetch()` requests to `http://localhost:3001/api`.

### 2. Admin username fixed
- **Problem**: Code used `shariiffx` as admin username.
- **Fix**: Changed to `sharifmolla354` with password `7506035297`. Admin is seeded automatically on server start.

### 3. Demo user removed
- **Problem**: A "demo" user was created on every page load via `seedDemo()`.
- **Fix**: Removed `seedDemo()` entirely. The server also deletes the legacy demo user from MongoDB on startup.

### 4. Password reset now works
- **Problem**: The reset-password.html called `/auth/verify-token` and `/auth/reset-password` routes that didn't exist in server.js.
- **Fix**: 
  - Added `POST /api/auth/forgot-password` — generates a reset token
  - Added `GET /api/auth/verify-token/:token` — verifies token validity
  - Added `POST /api/auth/reset-password` — updates password and deletes token
  - Added "Reset password" tab in the login screen so users can request a link
  - Admin panel has a tool to generate reset links for any user
  - `reset-password.html` now uses relative URLs so it works when served by the Express server

### 5. Schedule saving fixed
- **Problem**: Same mock intercept issue — schedules were saved to localStorage, not MongoDB.
- **Fix**: All calls now go to the real API.

### 6. Admin can see all user data
- **Problem**: Same root cause. Admin endpoints returned localStorage data, not MongoDB data.
- **Fix**: Real API calls, admin sees live MongoDB data including all users, schedules, and tracker activity.

### 7. Admin panel improvements
- Shows user roles (admin vs user)
- Cannot delete the super admin account
- Has a "Generate reset link" tool for any user
- Excludes admin from leaderboard

---

## 📁 File Structure

```
flowsync/
├── server.js           ← Express + MongoDB API (fixed)
├── index.html          ← Main app frontend (fixed)
├── reset-password.html ← Password reset page (fixed)
├── package.json
└── README.md
```

---

## 🔑 Password Reset Flow

**For users:**
1. Go to login page → click "Reset password" tab
2. Enter your username → click "Get reset link"
3. Copy the generated link and open it in your browser
4. Enter and confirm your new password

**For admin:**
1. Log in as admin → go to "Admin DB" tab
2. Use the "Generate password reset link" tool at the top
3. Enter the username → copy the link → share with user

---

## 🌐 API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/health` | Server health check |
| POST | `/api/register` | Register new user |
| POST | `/api/login` | Login |
| POST | `/api/auth/forgot-password` | Request password reset |
| GET | `/api/auth/verify-token/:token` | Verify reset token |
| POST | `/api/auth/reset-password` | Complete password reset |
| GET | `/api/schedules/:username` | Get user schedules |
| POST | `/api/schedules` | Save/update schedule |
| DELETE | `/api/schedules/:username/:name` | Delete schedule |
| GET | `/api/weeklyplans/:username` | Get weekly plans |
| POST | `/api/weeklyplans` | Save weekly plan |
| DELETE | `/api/weeklyplans/:username/:name` | Delete weekly plan |
| GET | `/api/tracker/:username` | Get tracker data |
| POST | `/api/tracker` | Create tracker day |
| PATCH | `/api/tracker/:username/:date` | Update task checkboxes |
| GET | `/api/appliedranges/:username` | Get applied calendar ranges |
| POST | `/api/appliedranges` | Save applied range |
| GET | `/api/admin/stats` | Global statistics |
| GET | `/api/admin/users` | All users (no passwords) |
| GET | `/api/admin/schedules` | All schedules |
| GET | `/api/admin/tracker` | All tracker data |
| DELETE | `/api/admin/users/:username` | Delete user + all data |
