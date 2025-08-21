# InstraCore Institute Management System Documentation

## Overview

InstraCore is a role-based Institute Management System built with
**Django**.\
The system provides dashboards and features tailored to each role
(Admin, Employee, Student, Candidate) with strict permission control.

------------------------------------------------------------------------

```

instracore/
├── manage.py
├── requirements.txt
├── instracore/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── AuthApp/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   ├── forms.py
│   └── templates/
│       ├── master.html
│       ├── login.html
│       ├── register.html
│       ├── profile.html
│       ├── notifications.html
│       └── setup.html
├── AdminApp/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   └── templates/
│       ├── dashboard.html
│       ├── users.html
│       ├── courses.html
│       ├── attendance.html
│       ├── events.html
│       ├── accounts.html
│       └── reports.html
├── EmployeeApp/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   └── templates/
│       ├── faculty_dashboard.html
│       ├── hr_dashboard.html
│       ├── finance_dashboard.html
│       ├── teacher_dashboard.html
│       └── other_dashboard.html
├── StudentApp/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   └── templates/
│       ├── dashboard.html
│       ├── academics.html
│       ├── finance.html
│       ├── resources.html
│       ├── certificates.html
│       └── courses.html
├── CandidateApp/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   └── templates/
│       └── dashboard.html
└── static/
    ├── css/
    │   └── style.css
    ├── js/
    │   └── main.js
    └── images/

```

---

## User Roles & Access

### Roles:

-   **Admin**
-   **Employee**
    -   Faculty
    -   HR
    -   Finance
    -   Marketing
    -   IT
    -   Teacher
    -   Others (Librarian, Security Guard, Guest Lecturer, etc.)
-   **Student**

> Registration is only available for **students** (self-register).\
> Employees and Admins are created by an **Admin**.

------------------------------------------------------------------------

## Dashboards

### 1. **Admin Dashboard**

-   **Summary**
    -   Total Students (active/inactive)
    -   Total Teachers (active/inactive)
    -   Total Courses (active/inactive)
    -   Total Staff (active/inactive)
-   **Course Overview** (active/inactive)
-   **Attendance List** (students, teachers, staff) → with charts
-   **Events & Notice Board**
-   **Accounts**
    -   Total earning, expense, fees collection
    -   Unpaid student fees
-   **Reports/Downloads** (employee/student/course/account data)

------------------------------------------------------------------------

### 2. **Faculty Dashboard**

-   Overview of teachers, students, and courses
-   Manage offline courses:
    -   Add new courses (time, description, price, etc.)
    -   Assign teachers
    -   Close courses
-   Review requests (course closure, teacher requirement)
-   Download student/course reports

------------------------------------------------------------------------

### 3. **HR Dashboard**

-   Manage employees (excluding Admin)
-   Job posts & applications
-   Schedule interviews
-   Reports/Downloads (employee data)

------------------------------------------------------------------------

### 4. **Finance Dashboard**

-   Salary & expense management
-   Track transactions
-   Approve/reject salary offers from HR
-   Approve/reject course pricing from teachers
-   Generate monthly expense sheet
-   Reports/Downloads (transactions, salary sheets)

------------------------------------------------------------------------

### 5. **Teacher Dashboard**

-   **Class routine** (daily, monthly)
-   **Attendance summary**
-   **Lesson plans**
-   **Course Management**
    -   **Online Courses** → Create, update, expire/delete
    -   **Offline Courses** → Accept/reject, manage assignments,
        assessments
-   Notify guardians (email/SMS link)
-   Close courses (faculty approval required)

------------------------------------------------------------------------

### 6. **Student Dashboard**

-   **Academics**
    -   Attendance
    -   Daily classes & schedules
    -   Performance charts
    -   Exam results
    -   Leave status
-   **Finance**
    -   Fees reminder
-   **Resources**
    -   Syllabus
    -   Notice board
-   **Certificates**
    -   Online → auto-issued after passing exam
    -   Offline → apply after passing exam
-   **Courses**
    -   Browse/buy **online courses**
    -   Register for **Regular/Diploma** courses
    -   Track completed courses + certificates

------------------------------------------------------------------------

## Permissions

### Admin

-   Full CRUD on all accounts (Admin, Employee, Student).
-   Approve HR job posts.
-   View/download all reports.
-   Delete certificates (others cannot).

### Faculty

-   Cannot create their own account (Admin only).
-   CRUD on students.
-   Manage offline courses.

### HR

-   Create/manage job posts.
-   Offer salaries (Finance must approve).

### Finance

-   Approve salaries & course pricing.
-   Track all financial data.
-   Generate monthly expense sheets.

### Teacher

-   **Online**: Full course CRUD, sales tracking.
-   **Offline**: Accept/reject courses, manage students, assessments,
    start/end classes.

### Student

-   Browse/register courses.
-   Access results, attendance, certificates.
-   Payments & fee tracking.
-   Guardian notifications (monthly reports, payments, results).

------------------------------------------------------------------------

## System Instructions

-   Project Name: **InstraCore**
-   Apps:
    -   `AuthApp` (authentication, profiles, master template)
    -   `AdminApp`
    -   `EmployeeApp` (faculty, HR, finance, teacher, others)
    -   `StudentApp`
-   Index page:
    -   Public info (about, contact, testimonials)
    -   Login
    -   Authenticated users are redirected to their dashboards
-   Layout:
    -   `master.html` in `AuthApp`
    -   Common footer (year auto from JS)
    -   Dynamic navbar (role-based)
        -   Profile dropdown (profile, notifications, quick actions,
            logout)
-   Frontend: **Bootstrap**
-   Each app:
    -   Own `models.py`, `views.py`, `urls.py`
    -   Own templates, static files (CSS/JS)

------------------------------------------------------------------------

## Models (High-Level)

### Common

-   **User** (extended from Django `AbstractUser`)
-   **Notification**
-   **AuditLog**
-   **Trash**

### Admin

-   **Event**
-   **Notice**

### HR

-   **JobPost**
-   **Application**
-   **InterviewSchedule**

### Finance

-   **Salary**
-   **Expense**
-   **Transaction**

### Faculty / Teacher

-   **Course**
-   **Assignment**
-   **LessonPlan**
-   **Attendance**

### Student

-   **Enrollment**
-   **ExamResult**
-   **Certificate**
-   **GuardianReport**

------------------------------------------------------------------------

# Models.py Drafts

## AuthApp/models.py

``` python
from django.contrib.auth.models import AbstractUser
from django.db import models
import uuid


# -------------------------
# User & Profile
# -------------------------
class User(AbstractUser):
    ROLE_CHOICES = [
        ('admin', 'Admin'),
        ('employee', 'Employee'),
        ('student', 'Student'),
        ('candidate', 'Candidate')
    ]
    SUBROLE_CHOICES = [
        ('faculty', 'Faculty'),
        ('hr', 'HR'),
        ('finance', 'Finance'),
        ('marketing', 'Marketing'),
        ('it', 'IT'),
        ('teacher', 'Teacher'),
        ('other', 'Other'),
    ]

    role = models.CharField(max_length=20, choices=ROLE_CHOICES)
    sub_role = models.CharField(max_length=30, choices=SUBROLE_CHOICES, blank=True, null=True)

    # Profile fields
    image = models.ImageField(upload_to="profiles/", blank=True, null=True)
    bio = models.TextField(blank=True)
    date_of_birth = models.DateField(blank=True, null=True)
    phone = models.CharField(max_length=20, blank=True)
    gender = models.CharField(max_length=10, blank=True)
    location = models.CharField(max_length=100, blank=True)
    country = models.CharField(max_length=50, blank=True)

    # Social links
    facebook = models.URLField(blank=True)
    twitter = models.URLField(blank=True)
    instagram = models.URLField(blank=True)
    linkedin = models.URLField(blank=True)

    def __str__(self):
        return f"{self.username} ({self.role})"


# -------------------------
# Notifications & Logs
# -------------------------
class Notification(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="notifications")
    message = models.TextField()
    action_link = models.URLField(blank=True, null=True)
    is_read = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)


class AuditLog(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    user = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
    action = models.CharField(max_length=255)
    model_name = models.CharField(max_length=100)
    object_id = models.CharField(max_length=50)
    created_at = models.DateTimeField(auto_now_add=True)


# -------------------------
# Trash (Soft Delete Backup)
# -------------------------
class Trash(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    model_name = models.CharField(max_length=100)
    object_data = models.JSONField()   # Store deleted object as JSON
    deleted_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, blank=True)
    deleted_at = models.DateTimeField(auto_now_add=True)

```

## AdminApp/models.py

``` python
from django.db import models
from AuthApp.models import User


class Event(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    date = models.DateField()
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="events")


class Notice(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="notices")

```

## EmployeeApp/models.py

``` python
from django.db import models
from AuthApp.models import User


# -------------------------
# HR MODELS
# -------------------------
class JobPost(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    posted_by = models.ForeignKey(User, on_delete=models.CASCADE, related_name="job_posts")
    created_at = models.DateTimeField(auto_now_add=True)


class Application(models.Model):
    job = models.ForeignKey(JobPost, on_delete=models.CASCADE, related_name="applications")
    applicant_name = models.CharField(max_length=200)
    applicant_email = models.EmailField()
    status = models.CharField(max_length=20, default="pending")  # pending/accepted/rejected
    applied_at = models.DateTimeField(auto_now_add=True)


class InterviewSchedule(models.Model):
    application = models.ForeignKey(Application, on_delete=models.CASCADE, related_name="interviews")
    scheduled_date = models.DateTimeField()
    notes = models.TextField(blank=True)


# -------------------------
# FINANCE MODELS
# -------------------------
class Salary(models.Model):
    employee = models.ForeignKey(User, on_delete=models.CASCADE, related_name="salaries")
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    status = models.CharField(max_length=20, default="pending")  # pending/approved/paid
    created_at = models.DateTimeField(auto_now_add=True)


class Expense(models.Model):
    category = models.CharField(max_length=100)
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    description = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)


class Transaction(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="transactions")
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    transaction_type = models.CharField(max_length=50)  # fee, purchase, refund, etc.
    created_at = models.DateTimeField(auto_now_add=True)


# -------------------------
# FACULTY / TEACHER MODELS
# -------------------------
class Course(models.Model):
    TYPE_CHOICES = [
        ('online', 'Online'),
        ('regular', 'Regular'),
        ('diploma', 'Diploma'),
        ('offline', 'Offline'),
    ]
    title = models.CharField(max_length=200)
    description = models.TextField()
    course_type = models.CharField(max_length=20, choices=TYPE_CHOICES)
    price = models.DecimalField(max_digits=10, decimal_places=2, default=0)
    assigned_teachers = models.ManyToManyField(User, related_name="courses")
    status = models.CharField(max_length=20, default="active")  # active/inactive/closed
    created_at = models.DateTimeField(auto_now_add=True)


class Assignment(models.Model):
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="assignments")
    title = models.CharField(max_length=200)
    description = models.TextField()
    due_date = models.DateField()


class LessonPlan(models.Model):
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="lesson_plans")
    title = models.CharField(max_length=200)
    content = models.TextField()
    date = models.DateField()


class Attendance(models.Model):
    ATTENDEE_TYPE = [
        ('student', 'Student'),
        ('teacher', 'Teacher'),
        ('staff', 'Staff'),
    ]
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    attendee_type = models.CharField(max_length=20, choices=ATTENDEE_TYPE)
    date = models.DateField()
    status = models.CharField(max_length=20, default="present")  # present/absent/leave

```

## StudentApp/models.py

``` python
from django.db import models
from AuthApp.models import User
from EmployeeApp.models import Course


class Enrollment(models.Model):
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name="enrollments")
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="enrollments")
    status = models.CharField(max_length=20, default="ongoing")  # ongoing/completed
    fee_paid = models.BooleanField(default=False)
    enrolled_at = models.DateTimeField(auto_now_add=True)


class ExamResult(models.Model):
    enrollment = models.ForeignKey(Enrollment, on_delete=models.CASCADE, related_name="results")
    marks_obtained = models.DecimalField(max_digits=5, decimal_places=2)
    total_marks = models.DecimalField(max_digits=5, decimal_places=2)
    passed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)


class Certificate(models.Model):
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name="certificates")
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="certificates")
    issue_date = models.DateField(auto_now_add=True)
    verified = models.BooleanField(default=False)


class GuardianReport(models.Model):
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name="guardian_reports")
    report_type = models.CharField(max_length=50)  # monthly, results, payments, etc.
    content = models.TextField()
    sent_at = models.DateTimeField(auto_now_add=True)

```
