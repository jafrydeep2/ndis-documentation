# NDIS MVP Database Schema

## Overview
This document outlines the complete database schema for the NDIS MVP (National Disability Insurance Scheme) Participant Management System. The schema is designed to support comprehensive participant management, staff coordination, service delivery, invoicing, and compliance tracking.

## Database Tables

### 1. USERS TABLE
**Purpose**: Store user authentication and profile information for all system users.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `email` | VARCHAR(255) | Unique, Not Null | User email address |
| `password_hash` | VARCHAR(255) | Not Null | Hashed password |
| `name` | VARCHAR(255) | Not Null | Full name |
| `role` | ENUM | Not Null | 'super_admin', 'support_worker', 'support_coordinator', 'allied_health' |
| `phone` | VARCHAR(20) | | Contact phone number |
| `avatar_url` | VARCHAR(500) | | Profile picture URL |
| `is_active` | BOOLEAN | Default: true | Account status |
| `last_login` | TIMESTAMP | | Last login timestamp |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Email (Unique)
- Role
- Is_active

---

### 2. PARTICIPANTS TABLE
**Purpose**: Store comprehensive participant information and NDIS plan details.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `ndis_number` | VARCHAR(20) | Unique, Not Null | NDIS participant number |
| `name` | VARCHAR(255) | Not Null | Participant full name |
| `email` | VARCHAR(255) | | Contact email |
| `phone` | VARCHAR(20) | | Contact phone |
| `address` | TEXT | | Full address |
| `date_of_birth` | DATE | | Birth date |
| `emergency_contact_name` | VARCHAR(255) | | Emergency contact name |
| `emergency_contact_phone` | VARCHAR(20) | | Emergency contact phone |
| `primary_disability` | VARCHAR(255) | | Primary disability type |
| `plan_start_date` | DATE | | NDIS plan start date |
| `plan_end_date` | DATE | | NDIS plan end date |
| `plan_manager` | VARCHAR(100) | | Self-managed, Plan-managed, Agency-managed |
| `total_budget` | DECIMAL(10,2) | | Total NDIS budget |
| `used_budget` | DECIMAL(10,2) | Default: 0.00 | Used budget amount |
| `support_needs` | TEXT | | Support requirements |
| `goals` | TEXT | | Participant goals |
| `medical_info` | TEXT | | Medical information |
| `notes` | TEXT | | Additional notes |
| `status` | ENUM | Default: 'active' | 'active', 'inactive', 'pending' |
| `created_by` | UUID | Foreign Key: users.id | Creator user ID |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- NDIS number (Unique)
- Status
- Plan end date
- Created by

---

### 3. STAFF_MEMBERS TABLE
**Purpose**: Store detailed staff information including roles, specializations, and rates.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `user_id` | UUID | Foreign Key: users.id, Unique | Associated user account |
| `specialization` | VARCHAR(255) | | Allied health specialization |
| `hourly_rate` | DECIMAL(8,2) | Not Null | Hourly billing rate |
| `start_date` | DATE | Not Null | Employment start date |
| `certifications` | TEXT | | Professional certifications |
| `status` | ENUM | Default: 'active' | 'active', 'inactive', 'on_leave' |
| `assigned_participants_count` | INTEGER | Default: 0 | Number of assigned participants |
| `total_hours` | DECIMAL(8,2) | Default: 0.00 | Total worked hours |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- User ID (Unique)
- Status
- Specialization

---

### 4. NDIS_ITEM_CODES TABLE
**Purpose**: Store NDIS item codes with pricing and categorization information.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `code` | VARCHAR(50) | Unique, Not Null | NDIS item code |
| `description` | TEXT | Not Null | Service description |
| `category` | VARCHAR(100) | Not Null | Core Support, Capacity Building, Capital Support |
| `unit_type` | VARCHAR(50) | Not Null | Hour, Trip, Item, etc. |
| `price` | DECIMAL(8,2) | Not Null | Unit price |
| `gst_applicable` | BOOLEAN | Default: false | GST applicability |
| `effective_date` | DATE | Not Null | Price effective date |
| `status` | ENUM | Default: 'active' | 'active', 'inactive', 'deprecated' |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Code (Unique)
- Category
- Status
- Effective date

---

### 5. SESSION_LOGS TABLE
**Purpose**: Store detailed session logs and service delivery records.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `participant_id` | UUID | Foreign Key: participants.id, Not Null | Participant ID |
| `staff_id` | UUID | Foreign Key: users.id, Not Null | Staff member ID |
| `service_type` | VARCHAR(100) | Not Null | Type of service provided |
| `session_date` | DATE | Not Null | Session date |
| `start_time` | TIME | Not Null | Session start time |
| `end_time` | TIME | Not Null | Session end time |
| `duration_hours` | DECIMAL(4,2) | Not Null | Session duration |
| `travel_kilometers` | DECIMAL(6,2) | Default: 0.00 | Travel distance |
| `notes` | TEXT | | Session notes |
| `outcomes` | TEXT | | Session outcomes |
| `next_steps` | TEXT | | Next steps |
| `participant_mood` | ENUM | | 'excellent', 'good', 'neutral', 'challenging', 'difficult' |
| `challenges` | TEXT | | Challenges faced |
| `achievements` | TEXT | | Achievements made |
| `status` | ENUM | Default: 'pending' | 'pending', 'approved', 'rejected' |
| `approved_by` | UUID | Foreign Key: users.id | Approver user ID |
| `approved_at` | TIMESTAMP | | Approval timestamp |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Participant ID
- Staff ID
- Session date
- Status
- Service type

---

### 6. INVOICES TABLE
**Purpose**: Store invoice information and billing details.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `invoice_number` | VARCHAR(50) | Unique, Not Null | Invoice number |
| `participant_id` | UUID | Foreign Key: participants.id, Not Null | Participant ID |
| `staff_id` | UUID | Foreign Key: users.id, Not Null | Staff member ID |
| `issue_date` | DATE | Not Null | Invoice issue date |
| `due_date` | DATE | Not Null | Payment due date |
| `subtotal` | DECIMAL(10,2) | Not Null | Subtotal amount |
| `gst_amount` | DECIMAL(10,2) | Not Null | GST amount |
| `total_amount` | DECIMAL(10,2) | Not Null | Total invoice amount |
| `status` | ENUM | Default: 'draft' | 'draft', 'sent', 'paid', 'overdue' |
| `notes` | TEXT | | Invoice notes |
| `sent_at` | TIMESTAMP | | Sent timestamp |
| `paid_at` | TIMESTAMP | | Payment timestamp |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Invoice number (Unique)
- Participant ID
- Staff ID
- Status
- Issue date

---

### 7. INVOICE_ITEMS TABLE
**Purpose**: Store individual line items within invoices.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `invoice_id` | UUID | Foreign Key: invoices.id, Not Null | Invoice ID |
| `ndis_item_code_id` | UUID | Foreign Key: ndis_item_codes.id, Not Null | NDIS item code ID |
| `quantity` | DECIMAL(6,2) | Not Null | Quantity provided |
| `rate` | DECIMAL(8,2) | Not Null | Unit rate |
| `amount` | DECIMAL(10,2) | Not Null | Line item amount |
| `description` | TEXT | | Item description |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |

**Indexes**:
- Invoice ID
- NDIS item code ID

---

### 8. DOCUMENTS TABLE
**Purpose**: Store document metadata and file information.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `name` | VARCHAR(255) | Not Null | Document name |
| `type` | ENUM | Not Null | 'ndis_plan', 'assessment', 'report', 'certificate', 'photo', 'other' |
| `participant_id` | UUID | Foreign Key: participants.id | Associated participant |
| `uploaded_by` | UUID | Foreign Key: users.id, Not Null | Uploader user ID |
| `file_path` | VARCHAR(500) | Not Null | File storage path |
| `file_size` | BIGINT | Not Null | File size in bytes |
| `mime_type` | VARCHAR(100) | | File MIME type |
| `upload_date` | TIMESTAMP | Default: NOW() | Upload timestamp |
| `expiry_date` | DATE | | Document expiry date |
| `status` | ENUM | Default: 'current' | 'current', 'expired', 'expiring_soon' |
| `tags` | JSON | | Array of tag strings |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Participant ID
- Type
- Status
- Expiry date
- Uploaded by

---

### 9. PARTICIPANT_GOALS TABLE
**Purpose**: Store participant goals and progress tracking.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `participant_id` | UUID | Foreign Key: participants.id, Not Null | Participant ID |
| `category` | VARCHAR(100) | Not Null | Goal category |
| `goal` | TEXT | Not Null | Goal description |
| `description` | TEXT | | Detailed description |
| `progress_percentage` | INTEGER | Default: 0 | Progress percentage |
| `status` | ENUM | Default: 'not_started' | 'not_started', 'in_progress', 'nearly_complete', 'completed' |
| `due_date` | DATE | | Goal due date |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Participant ID
- Status
- Due date

---

### 10. PROGRESS_NOTES TABLE
**Purpose**: Store detailed progress notes and session outcomes.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `participant_id` | UUID | Foreign Key: participants.id, Not Null | Participant ID |
| `session_log_id` | UUID | Foreign Key: session_logs.id | Associated session log |
| `worker_id` | UUID | Foreign Key: users.id, Not Null | Worker user ID |
| `date` | DATE | Not Null | Note date |
| `session_type` | VARCHAR(100) | | Session type |
| `duration` | VARCHAR(50) | | Session duration |
| `notes` | TEXT | | Progress notes |
| `outcomes` | JSON | | Array of outcome strings |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |

**Indexes**:
- Participant ID
- Session log ID
- Worker ID
- Date

---

### 11. CALENDAR_EVENTS TABLE
**Purpose**: Store calendar events and appointments.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `title` | VARCHAR(255) | Not Null | Event title |
| `description` | TEXT | | Event description |
| `participant_id` | UUID | Foreign Key: participants.id | Associated participant |
| `staff_id` | UUID | Foreign Key: users.id | Assigned staff member |
| `event_type` | ENUM | | 'session', 'assessment', 'meeting', 'reminder', 'other' |
| `start_datetime` | TIMESTAMP | Not Null | Event start time |
| `end_datetime` | TIMESTAMP | Not Null | Event end time |
| `location` | VARCHAR(255) | | Event location |
| `status` | ENUM | Default: 'scheduled' | 'scheduled', 'confirmed', 'completed', 'cancelled' |
| `created_by` | UUID | Foreign Key: users.id, Not Null | Creator user ID |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Participant ID
- Staff ID
- Start datetime
- Status
- Event type

---

### 12. SYSTEM_SETTINGS TABLE
**Purpose**: Store system-wide configuration settings.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `setting_key` | VARCHAR(100) | Unique, Not Null | Setting key |
| `setting_value` | TEXT | | Setting value |
| `setting_type` | ENUM | Default: 'string' | 'string', 'number', 'boolean', 'json' |
| `description` | TEXT | | Setting description |
| `is_editable` | BOOLEAN | Default: true | Whether setting can be edited |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- Setting key (Unique)

---

### 13. USER_SETTINGS TABLE
**Purpose**: Store user-specific settings and preferences.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `user_id` | UUID | Foreign Key: users.id, Not Null | User ID |
| `setting_key` | VARCHAR(100) | Not Null | Setting key |
| `setting_value` | TEXT | | Setting value |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |
| `updated_at` | TIMESTAMP | Default: NOW() | Record update time |

**Indexes**:
- User ID + Setting key (Composite Unique)
- User ID

---

### 14. AUDIT_LOGS TABLE
**Purpose**: Store system audit trail for compliance and security.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `user_id` | UUID | Foreign Key: users.id | User who performed action |
| `action` | VARCHAR(100) | Not Null | Action performed |
| `table_name` | VARCHAR(100) | | Affected table |
| `record_id` | UUID | | Affected record ID |
| `old_values` | JSON | | Previous values |
| `new_values` | JSON | | New values |
| `ip_address` | VARCHAR(45) | | User IP address |
| `user_agent` | TEXT | | User agent string |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |

**Indexes**:
- User ID
- Action
- Table name
- Created at

---

### 15. NOTIFICATIONS TABLE
**Purpose**: Store system notifications and alerts.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | UUID | Primary Key | Unique identifier |
| `user_id` | UUID | Foreign Key: users.id, Not Null | Recipient user ID |
| `type` | ENUM | | 'info', 'warning', 'error', 'success' |
| `title` | VARCHAR(255) | Not Null | Notification title |
| `message` | TEXT | | Notification message |
| `is_read` | BOOLEAN | Default: false | Read status |
| `read_at` | TIMESTAMP | | Read timestamp |
| `related_table` | VARCHAR(100) | | Related table name |
| `related_id` | UUID | | Related record ID |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation time |

**Indexes**:
- User ID
- Is read
- Type
- Created at

---

## Table Relationships

### Primary Relationships:

| Relationship | From Table | To Table | Type | Description |
|--------------|------------|----------|------|-------------|
| 1 | Users | Staff_Members | 1:1 | User account to staff profile |
| 2 | Users | Session_Logs | 1:Many | Staff member to sessions |
| 3 | Users | Invoices | 1:Many | Staff member to invoices |
| 4 | Participants | Session_Logs | 1:Many | Participant to sessions |
| 5 | Participants | Invoices | 1:Many | Participant to invoices |
| 6 | Participants | Documents | 1:Many | Participant to documents |
| 7 | Participants | Participant_Goals | 1:Many | Participant to goals |
| 8 | Participants | Progress_Notes | 1:Many | Participant to progress notes |
| 9 | Invoices | Invoice_Items | 1:Many | Invoice to line items |
| 10 | NDIS_Item_Codes | Invoice_Items | 1:Many | Item codes to invoice items |

### Secondary Relationships:

| Relationship | From Table | To Table | Type | Description |
|--------------|------------|----------|------|-------------|
| 11 | Users | Documents | 1:Many | Uploader to documents |
| 12 | Users | Calendar_Events | 1:Many | Staff to calendar events |
| 13 | Users | Audit_Logs | 1:Many | User to audit entries |
| 14 | Users | Notifications | 1:Many | User to notifications |
| 15 | Users | User_Settings | 1:Many | User to settings |

---

## Data Types and Constraints

### Enums Used:

| Enum Name | Values | Description |
|-----------|--------|-------------|
| User Roles | super_admin, support_worker, support_coordinator, allied_health | User role types |
| Participant Status | active, inactive, pending | Participant status |
| Staff Status | active, inactive, on_leave | Staff employment status |
| Session Status | pending, approved, rejected | Session approval status |
| Invoice Status | draft, sent, paid, overdue | Invoice payment status |
| Document Status | current, expired, expiring_soon | Document validity status |
| Goal Status | not_started, in_progress, nearly_complete, completed | Goal progress status |
| Event Status | scheduled, confirmed, completed, cancelled | Calendar event status |

### Important Constraints:

| Constraint | Table | Field | Description |
|------------|-------|-------|-------------|
| Unique | Participants | ndis_number | NDIS numbers must be unique |
| Unique | Users | email | Email addresses must be unique |
| Unique | Invoices | invoice_number | Invoice numbers must be unique |
| Not Null | Session_Logs | participant_id, staff_id | Sessions require both participant and staff |
| Foreign Key | All tables | Various | Proper referential integrity |
| Default | Various | status fields | Appropriate default status values |

---

## Security Considerations

### Data Protection:

| Security Measure | Implementation | Description |
|------------------|----------------|-------------|
| Password Hashing | bcrypt or similar | Secure password storage |
| Data Encryption | AES-256 | Sensitive data encryption |
| Audit Logging | Comprehensive | All data modifications tracked |
| Role-Based Access | Database level | Access control by user role |
| Session Management | Timeout controls | Secure session handling |

### Compliance Requirements:

| Requirement | Implementation | Description |
|-------------|----------------|-------------|
| NDIS Data Retention | 7+ years | Long-term data retention |
| Privacy Act 1988 | Data protection | Australian privacy compliance |
| GDPR Considerations | Data portability | International compliance |
| Audit Trail | Comprehensive logging | Compliance tracking |
| Document Security | Access controls | Secure file storage |

---

## Performance Considerations

### Indexing Strategy:

| Index Type | Tables | Purpose |
|------------|--------|---------|
| Composite | Session_Logs, Invoices | Multi-field queries |
| Partial | Status fields | Filtered queries |
| Date-based | Session_Logs, Audit_Logs | Time-series data |
| Full-text | Documents | Content search |

### Partitioning Strategy:

| Table | Partition By | Purpose |
|-------|--------------|---------|
| Session_Logs | Date | Large dataset management |
| Audit_Logs | Date | Compliance retention |
| Documents | Type | Storage optimization |

---

## Backup and Recovery

### Backup Strategy:

| Backup Type | Frequency | Purpose |
|-------------|-----------|---------|
| Full Backup | Daily | Complete system backup |
| Transaction Log | Hourly | Point-in-time recovery |
| Document Storage | Separate | File system backup |
| Cross-region | Real-time | Disaster recovery |

### Recovery Procedures:

| Procedure | RTO | RPO | Description |
|-----------|-----|-----|-------------|
| Automated Testing | Weekly | | Backup verification |
| Recovery Time | 4 hours | | System restoration time |
| Recovery Point | 1 hour | | Data loss tolerance |
| Documented Procedures | All scenarios | | Recovery guidelines |

---

## Migration Strategy

### Phase 1: Core Tables
1. Users, Participants, Staff_Members
2. NDIS_Item_Codes, System_Settings
3. Basic audit and notification tables

### Phase 2: Service Delivery
1. Session_Logs, Progress_Notes
2. Participant_Goals, Calendar_Events
3. Enhanced audit logging

### Phase 3: Financial Management
1. Invoices, Invoice_Items
2. Financial reporting and analytics
3. Payment tracking and reconciliation

### Phase 4: Document Management
1. Documents table and file storage
2. Document workflow and approvals
3. Compliance monitoring and alerts

---

## Monitoring and Maintenance

### Key Metrics:

| Metric | Measurement | Target |
|--------|-------------|--------|
| Database Performance | Query response times | < 100ms average |
| Storage Growth | Monthly growth rates | < 20% monthly |
| User Activity | Concurrent users | Peak time monitoring |
| Data Quality | Validation error rates | < 1% error rate |
| Security Events | Failed login attempts | Alert on > 5 attempts |

### Maintenance Tasks:

| Task | Frequency | Description |
|------|-----------|-------------|
| Index Maintenance | Weekly | Statistics updates |
| Data Archiving | Monthly | Old record cleanup |
| Security Reviews | Quarterly | Access audits |
| Compliance Assessments | Annually | Data retention reviews |

---

This schema provides a comprehensive foundation for the NDIS MVP system, supporting all current functionality while allowing for future expansion and compliance requirements. 
