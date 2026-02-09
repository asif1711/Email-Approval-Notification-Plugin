# Digital Logbook – Email Notification System

A WordPress plugin that handles **email notifications** for the Digital Logbook system.  
This plugin is responsible for notifying **line managers** and **students** during the logbook approval lifecycle.

The system is **database-driven**, **idempotent**, and **integration-ready**, designed to work reliably in both local and future production environments.

---
**Version:** 0.1  
**Author:** Nurul Islam  
**Dependencies:** WordPress 6.9 · Nginx · PHP 7.4.30 · MySQL DB · Mailpit · PDF Generator Plugin

## ✨ Features

- Sends **PDF logbook email** to the assigned line manager
- Sends **approval / rejection notification** to the student
- Secure approval workflow using **one-time tokens**
- Email delivery tracked at database level (no duplicates)
- Retry-safe notification execution
- Clean separation from PDF generation logic
- Compatible with local SMTP (Mailpit) and real SMTP (later)

---

## 🧩 Responsibilities (Scope)

This plugin handles **only notifications**:

- 📧 Manager email (with PDF attached)
- ✅ Approval / rejection processing
- 📬 Student notification after decision
- 🗃 Email state tracking in database

---

## 📦 Database Requirements

This plugin depends on the following table (shared with the PDF generator):

### `wp_cf7_pdf_logs`

Required columns:

| Column | Purpose |
|------|--------|
| `id` | Primary logbook identifier |
| `form_id` | Source CF7 form ID |
| `student_name` | Student name |
| `enrollment_no` | Student enrollment number |
| `course_name` | Course / program name |
| `manager_email` | Line manager email |
| `email_sent` | Manager email sent flag |
| `email_sent_at` | Timestamp of manager email |
| `approval_status` | `pending / approved / rejected` |
| `approval_token` | One-time approval token |
| `approval_token_expires_at` | Token expiry |
| `approved_at` | Approval timestamp |
| `student_notified` | Student notification flag |
| `student_notified_at` | Student email timestamp |
| `pdf_file` | Generated PDF filename |
| `created_at` | Logbook creation time |

---

## 🔄 Email Workflow

### 1. Manager Notification
- Triggered after PDF generation
- PDF attached
- Approval + rejection links included
- Email sent **once** and tracked in DB

### 2. Approval / Rejection
- Line manager clicks secure link
- Token validated and expired after use
- Approval status updated atomically

### 3. Student Notification
- Triggered after approval/rejection
- Student email resolved via enrollment number
- Sent once only (retry-safe)
- Fully DB-tracked

---

## 🔐 Security & Safety

- One-time approval tokens
- Token expiry enforced
- Duplicate email prevention
- Safe retry logic using DB state
- No emails sent inside page rendering logic

---

## 🛠 Architecture
digital-logbook/
│
├── digital-logbook.php
│     ├── Approval handling
│     ├── Enrollment resolution hooks
│     └── Plugin bootstrap
│
├── includes/
│     ├── send-manager-emails.php
│     └── send-student-notifications.php
│
├── email/
│     └── EmailService.php
│
└── README.md


---

## 🔌 Integration Points

### Student Email Resolution

The plugin expects a resolver function:
  dl_resolve_student_email_by_enrollment($enrollment_no)

Current implementation can:
  Query a local ACC/mock table
  Be replaced later with:
    External DB
    API
    SSO / ERP system

No refactor required.

📧 SMTP Handling

  Uses WordPress wp_mail()
  Local development supported (Mailpit)
  Real SMTP can be configured later
  Email logic is SMTP-agnostic
  This ensures the plugin remains functional and integratable regardless of deployment environment.

✅ Status

  Core notification logic: Complete
  Manager email flow: Working
  Student notification flow: Working
  Approval security: Working
  Database tracking: Working
  Production SMTP: Configurable later

