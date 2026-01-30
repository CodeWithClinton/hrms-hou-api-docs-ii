


# 📘 Sessions & Semesters API

All endpoints require **authentication**.

```
Authorization: Bearer <access_token>
```

Base URL (example):

```
BASE_URL/hrms/
```

---

## 🔹 Semester Endpoints

### 1. List & Create Semesters

**URL**

```
GET /semester_list_create/
POST /semester_list_create/?session_id=<session_id>
```

#### GET – List Semesters

* Returns the **latest 10 semesters**
* Can be filtered by session

**Query Params (optional)**

| Param      | Type   | Description                 |
| ---------- | ------ | --------------------------- |
| session_id | number | Filter semesters by session |

**Example**

```
GET /semester_list_create/?session_id=1
```

---

#### POST – Create Semester

**Query Params (required)**

| Param      | Description                        |
| ---------- | ---------------------------------- |
| session_id | Session ID the semester belongs to |

**Request Body**

```json
{
  "name": "first"
}
```

> `name` is automatically uppercased on the server.

**Responses**

* `201 Created` → new semester created
* `200 OK` → semester already exists

---

### 2. Semester Detail (Retrieve / Update / Delete)

**URL**

```
/semesters/{semester_id}/
```

---

#### GET – Retrieve One Semester

```
GET /semesters/3/
```

---

#### PUT / PATCH – Update Semester

**Query Params (required)**

| Param      | Description    |
| ---------- | -------------- |
| session_id | New session ID |

**Request Body (optional)**

```json
{
  "name": "second"
}
```

---

#### DELETE – Delete Semester

```
DELETE /semesters/3/
```

**Response**

```json
{
  "message": "Semester deleted successfully"
}
```

---

## 🔹 Active Session & Semester

### 3. Set Active Session

**URL**

```
POST /set_active_session/{session_id}/
```

**Behavior**

* Sets the given session as active
* Automatically deactivates all others

**Response**

```json
{
  "message": "Session set as active",
  "session": { ... }
}
```

---

### 4. Set Active Semester

**URL**

```
POST /set_active_semester/{semester_id}/
```

**Behavior**

* Sets the given semester as active
* Automatically deactivates all others

---

### 5. Get Active Session & Semester

**URL**

```
GET /get_active_session_semester/
```

**Response**

```json
{
  "active_session": {
    "id": 1,
    "name": "2023/2024",
    "is_active": true
  },
  "active_semester": {
    "id": 2,
    "name": "FIRST",
    "is_active": true
  }
}
```

> Use this endpoint to determine the **currently selected academic context** for the app.

---

## 🔹 Data Shape Notes (Frontend)

### Semester Object

```json
{
  "id": 2,
  "name": "FIRST",
  "session": "2023/2024",
  "is_active": true,
  "created_at": "2025-01-01T10:00:00Z"
}
```

### Session Object

```json
{
  "id": 1,
  "name": "2023/2024",
  "is_active": true
}
```



---

# 📊 Appraisal, Leave & Staff Statistics API

All endpoints require **authentication**.

```
Authorization: Bearer <access_token>
```

---

## 🔹 Appraisal Statistics

### 1. Appraisal Overview Stats

**URL**

```
GET /appraisals/stats/
```

**Access**

* Authenticated users
* **Roles required:** `HOD`, `DEAN`, `VC`

**Purpose**

* Returns stats for the **currently active appraisal cycle**
* Useful for dashboards

**Response**

```json
{
  "active_appraisal_cycle": {
    "id": 3,
    "name": "2025 Annual Review",
    "type": "Academic Staff",
    "start_date": "2025-01-01",
    "end_date": "2025-03-31",
    "is_active": true
  },
  "appraisal_in_progress": 12,
  "pending_appraisal": 5
}
```

**Notes**

* `appraisal_in_progress` → status = `draft`
* `pending_appraisal` → status = `submitted`
* If no active cycle exists, values return `null` / `0`

---

## 🔹 Leave & Appraisal Summary (Time-based)

### 2. Leave & Appraisal Summary Stats

**URL**

```
GET /leave_appraisal_summary_stats/
```

**Purpose**

* Shows activity trends for **last week, month, and year**
* Ideal for analytics cards

**Response**

```json
{
  "leave_stats": {
    "last_week": 4,
    "last_month": 15,
    "last_year": 120
  },
  "appraisal_stats": {
    "last_week": 3,
    "last_month": 18,
    "last_year": 200
  }
}
```

---

## 🔹 Staff Summary

### 3. Staff Summary Stats

**URL**

```
GET /staff_summary_stats/
```

**Purpose**

* High-level staff distribution snapshot

**Response**

```json
{
  "staff_stats": {
    "active_staff": 240,
    "on_probation": 18,
    "on_study_leave": 7,
    "retired_staff": 35
  }
}
```

**Definitions**

* **On probation** → employed within last 6 months
* **On study leave** → active leave of type `study`

---

## 🔹 Staff Details

### 4. Detailed Staff Information

**URL**

```
GET /detailed_staff_info/{staff_id}/
```

**Purpose**

* Fetch full profile of a single staff member

**Response**

* Serialized via `DetailedStaffSerializer`
* Includes extended staff data (profile, relations, etc.)

---

## 🔹 Appraisal Records

### 5. Completed Appraisals

**URL**

```
GET /completed_appraisals/stats/
```

**Query Params (optional)**

| Param     | Description                |
| --------- | -------------------------- |
| period_id | Filter by appraisal period |
| staff_id  | Filter by staff            |

**Purpose**

* Returns **reviewed (completed)** appraisals only

**Response**

```json
[
  {
    "id": 12,
    "staff": 5,
    "staff_name": "John Doe",
    "appraisal_period": 3,
    "period_name": "2025 Annual Review",
    "status": "reviewed",
    "date_created": "2025-02-10T09:00:00Z"
  }
]
```

---

## 🔹 Appraisal Periods

### 6. Past Appraisal Periods

**URL**

```
GET /past_appraisal_periods/
```

**Purpose**

* Returns appraisal cycles that have **ended**
* Ordered by most recent

---

### 7. Active Appraisal Period

**URL**

```
GET /active_appraisal_period/
```

**Purpose**

* Fetch the **currently active appraisal cycle**

**Response**

```json
{
  "active_appraisal": {
    "id": 3,
    "name": "2025 Annual Review",
    "type": "Academic Staff",
    "start_date": "2025-01-01",
    "end_date": "2025-03-31",
    "is_active": true
  }
}
```

If none exists:

```json
{
  "active_appraisal": null
}
```

---

## 🔹 Appraisal Object Notes (Frontend)

### Appraisal

* `status` values:

  * `draft`
  * `submitted`
  * `reviewed`
* Each staff can have **only one appraisal per period**



Here’s a **concise README section** for the Leave endpoints (frontend-focused, filters + response shapes).

---

# 🏖️ Leave Management API

All endpoints require **authentication**.

```
Authorization: Bearer <access_token>
```

---

## 🔹 Leave Stats

### 1) Leave Summary Stats

**URL**

```
GET /leave_stats/
```

**Purpose**
Dashboard numbers for leave overview.

**Response**

```json
{
  "leave_stats": {
    "total_staff_applied": 120,
    "active_leaves": 14,
    "pending_leaves": 6,
    "approved_leaves": 50
  }
}
```

**Notes**

* `total_staff_applied` = number of unique staff that have ever applied for leave
* `active_leaves` counts leaves where:

  * `is_active = true`, OR
  * today is between `start_date` and `end_date`

---

## 🔹 Leave Type Breakdown

### 2) Leave Type Stats

**URL**

```
GET /leave_type_stats/
```

**Purpose**
Returns count of leaves grouped by `leave_type`.

**Response**

```json
{
  "leave_type_stats": [
    {
      "leave_type": "ANNUAL",
      "leave_type_display": "Annual Leave",
      "count": 25
    },
    {
      "leave_type": "SICK",
      "leave_type_display": "Sick Leave",
      "count": 10
    }
  ]
}
```

---

## 🔹 List Leaves (With Filters + Pagination)

### 3) List Leaves

**URL**

```
GET /list_leaves/
```

**Purpose**
Returns a paginated list of leave records. Supports multiple optional filters.

### Query Params (all optional)

| Param      | Example          | Description                                                |
| ---------- | ---------------- | ---------------------------------------------------------- |
| status     | `PENDING`        | Filter by leave status (`PENDING`, `APPROVED`, `REJECTED`) |
| leave_type | `ANNUAL`         | Filter by leave type (e.g. `ANNUAL`, `SICK`, `STUDY`)      |
| is_active  | `true` / `false` | Filter by active flag                                      |
| staff_id   | `12`             | Filter leaves for a staff member                           |
| start_from | `2026-01-01`     | Filter by `start_date >= start_from`                       |
| start_to   | `2026-01-31`     | Filter by `start_date <= start_to`                         |
| q          | `john`           | Search staff by name/email                                 |

**Examples**

```http
GET /list_leaves/?status=pending&leave_type=annual
GET /list_leaves/?is_active=true
GET /list_leaves/?staff_id=5&start_from=2026-01-01&start_to=2026-01-31
GET /list_leaves/?q=miracle
```

**Response (paginated)**
Pagination fields depend on `SimplePagination`, but the response format is typically like:

```json
{
  "count": 120,
  "next": "http://.../list_leaves/?page=2",
  "previous": null,
  "results": [
    {
      "id": 10,
      "staff": {
        "id": 5,
        "full_name": "John Doe"
      },
      "leave_type": "ANNUAL",
      "start_date": "2026-01-10",
      "end_date": "2026-01-20",
      "is_active": true,
      "status": "APPROVED",
      "reason": "Vacation"
    }
  ]
}
```

---

## 🔹 Leave Detail

### 4) Retrieve One Leave

**URL**

```
GET /leave_detail/{leave_id}/
```

**Example**

```
GET /leave_detail/10/
```

**Response**
Returns a single Leave object (same shape as list items).

---



# ⬆️ Promotion API

All endpoints require **authentication**.

```
Authorization: Bearer <access_token>
```

---

## 🔹 Promotion Stats

### 1) Promotion Summary Stats

**URL**

```
GET /promotion_stats/
```

**Purpose**
Dashboard overview for promotions.

**Response**

```json
{
  "promotion_stats": {
    "total_staff_applied": 45,
    "pending_promotions": 12,
    "rejected_promotions": 5,
    "approved_promotions": 20
  }
}
```

**Notes**

* `total_staff_applied` = number of **unique staff** that have ever created a promotion record
* `pending_promotions` counts promotions with status:

  * `SUBMITTED` or `UNDER_REVIEW`

---

## 🔹 List Promotions (Filters + Pagination)

### 2) List Promotions

**URL**

```
GET /list_promotions/
```

**Purpose**
Returns a **paginated list** of promotion applications (newest first).

### Query Params (optional)

| Param  | Example     | Description                                                                     |
| ------ | ----------- | ------------------------------------------------------------------------------- |
| status | `SUBMITTED` | Filter by status (`DRAFT`, `SUBMITTED`, `UNDER_REVIEW`, `APPROVED`, `REJECTED`) |
| q      | `john`      | Search staff by name/email                                                      |

**Examples**

```http
GET /list_promotions/?status=under_review
GET /list_promotions/?q=clinton
GET /list_promotions/?status=approved&q=jane
```

**Response (paginated)**
Pagination fields depend on `SimplePagination`, but typically looks like:

```json
{
  "count": 80,
  "next": "http://.../list_promotions/?page=2",
  "previous": null,
  "results": [
    {
      "id": 7,
      "staff": {
        "id": 12,
        "full_name": "John Doe"
      },
      "current_rank": "Lecturer II",
      "proposed_rank": "Lecturer I",
      "status": "UNDER_REVIEW",
      "session": 1,
      "semester": 2,
      "department": 5,
      "submitted_at": "2026-01-10T12:00:00Z",
      "created_at": "2026-01-09T09:00:00Z"
    }
  ]
}
```

---

## 🔹 Promotion Detail

### 3) Retrieve One Promotion

**URL**

```
GET /promotion_detail/{promotion_id}/
```

**Example**

```
GET /promotion_detail/7/
```

**Response**
Returns a single Promotion object (same shape as list items).

---


