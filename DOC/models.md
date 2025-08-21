## AuthApp/models.py

```python
from django.contrib.auth.models import AbstractUser
from django.db import models
import uuid


class User(AbstractUser):
    ROLE_CHOICES = [
        ('admin', 'Admin'),
        ('employee', 'Employee'),
        ('student', 'Student'),
        ('candidate', 'Candidate'),
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
    
    # Account status
    is_active = models.BooleanField(default=True)
    is_temporary = models.BooleanField(default=False)  # For temporary accounts
    
    def __str__(self):
        return f"{self.username} ({self.role})"


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


class Trash(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    model_name = models.CharField(max_length=100)
    object_data = models.JSONField()
    deleted_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, blank=True)
    deleted_at = models.DateTimeField(auto_now_add=True)


class ActivityLog(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='activities')
    action = models.CharField(max_length=255)
    timestamp = models.DateTimeField(auto_now_add=True)
    related_object_type = models.CharField(max_length=100, blank=True)
    related_object_id = models.CharField(max_length=50, blank=True)
```

## AdminApp/models.py

```python
from django.db import models
from AuthApp.models import User


class Event(models.Model):
    CATEGORY_CHOICES = [
        ('academic', 'Academic'),
        ('cultural', 'Cultural'),
        ('sports', 'Sports'),
        ('holiday', 'Holiday'),
        ('other', 'Other'),
    ]
    
    title = models.CharField(max_length=200)
    description = models.TextField()
    category = models.CharField(max_length=20, choices=CATEGORY_CHOICES)
    date = models.DateField()
    start_time = models.TimeField(blank=True, null=True)
    end_time = models.TimeField(blank=True, null=True)
    location = models.CharField(max_length=200, blank=True)
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="events")
    created_at = models.DateTimeField(auto_now_add=True)
    is_active = models.BooleanField(default=True)


class Notice(models.Model):
    CATEGORY_CHOICES = [
        ('general', 'General'),
        ('academic', 'Academic'),
        'emergency', 'Emergency'),
        ('event', 'Event'),
        ('other', 'Other'),
    ]
    
    title = models.CharField(max_length=200)
    content = models.TextField()
    category = models.CharField(max_length=20, choices=CATEGORY_CHOICES)
    priority = models.CharField(max_length=10, choices=[('low', 'Low'), ('medium', 'Medium'), ('high', 'High')])
    created_at = models.DateTimeField(auto_now_add=True)
    expiry_date = models.DateField(blank=True, null=True)
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="notices")
    is_active = models.BooleanField(default=True)


class WeekendCalendar(models.Model):
    date = models.DateField(unique=True)
    is_weekend = models.BooleanField(default=True)
    description = models.CharField(max_length=100, blank=True)
    
    def __str__(self):
        return f"{self.date} - {'Weekend' if self.is_weekend else 'Holiday'}"


class FinancialOverview(models.Model):
    month = models.DateField()
    income = models.DecimalField(max_digits=15, decimal_places=2, default=0)
    expenses = models.DecimalField(max_digits=15, decimal_places=2, default=0)
    fees_collected = models.DecimalField(max_digits=15, decimal_places=2, default=0)
    salaries_paid = models.DecimalField(max_digits=15, decimal_places=2, default=0)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ('month',)
```

## EmployeeApp/models.py

```python
from django.db import models
from AuthApp.models import User


# HR Models
class JobPost(models.Model):
    ROLE_CHOICES = [
        ('admin', 'Admin'),
        ('faculty', 'Faculty'),
        ('hr', 'HR'),
        ('finance', 'Finance'),
        ('marketing', 'Marketing'),
        ('it', 'IT'),
        ('teacher', 'Teacher'),
        ('other', 'Other'),
    ]
    
    title = models.CharField(max_length=200)
    description = models.TextField()
    role = models.CharField(max_length=20, choices=ROLE_CHOICES)
    min_requirements = models.TextField()
    salary_range = models.CharField(max_length=100)
    location = models.CharField(max_length=100)
    language = models.CharField(max_length=50, default='English')
    availability = models.CharField(max_length=100)
    application_instructions = models.TextField()
    posted_by = models.ForeignKey(User, on_delete=models.CASCADE, related_name="job_posts")
    created_at = models.DateTimeField(auto_now_add=True)
    deadline = models.DateField()
    is_active = models.BooleanField(default=True)


class Application(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('under_review', 'Under Review'),
        ('interview_scheduled', 'Interview Scheduled'),
        ('accepted', 'Accepted'),
        ('rejected', 'Rejected'),
        ('hired', 'Hired'),
    ]
    
    job = models.ForeignKey(JobPost, on_delete=models.CASCADE, related_name="applications")
    candidate = models.ForeignKey(User, on_delete=models.CASCADE, related_name="applications")
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="pending")
    applied_at = models.DateTimeField(auto_now_add=True)
    resume = models.FileField(upload_to='resumes/', blank=True, null=True)
    cover_letter = models.TextField(blank=True)


class InterviewSchedule(models.Model):
    application = models.ForeignKey(Application, on_delete=models.CASCADE, related_name="interviews")
    scheduled_date = models.DateTimeField()
    interviewer = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="interviews_conducted")
    notes = models.TextField(blank=True)
    status = models.CharField(max_length=20, choices=[('scheduled', 'Scheduled'), ('completed', 'Completed'), ('cancelled', 'Cancelled')])
    feedback = models.TextField(blank=True)


# Finance Models
class Salary(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('approved', 'Approved'),
        ('paid', 'Paid'),
        ('rejected', 'Rejected'),
    ]
    
    employee = models.ForeignKey(User, on_delete=models.CASCADE, related_name="salaries")
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    month = models.DateField()
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="pending")
    created_at = models.DateTimeField(auto_now_add=True)
    approved_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="salaries_approved")
    payment_date = models.DateField(blank=True, null=True)
    
    class Meta:
        unique_together = ('employee', 'month')


class Expense(models.Model):
    CATEGORY_CHOICES = [
        ('utilities', 'Utilities'),
        ('maintenance', 'Maintenance'),
        ('supplies', 'Supplies'),
        ('marketing', 'Marketing'),
        ('travel', 'Travel'),
        ('other', 'Other'),
    ]
    
    category = models.CharField(max_length=20, choices=CATEGORY_CHOICES)
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    description = models.TextField(blank=True)
    date = models.DateField()
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="expenses_created")
    approved_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="expenses_approved")
    created_at = models.DateTimeField(auto_now_add=True)
    status = models.CharField(max_length=20, choices=[('pending', 'Pending'), ('approved', 'Approved'), ('rejected', 'Rejected')])


class Transaction(models.Model):
    TYPE_CHOICES = [
        ('fee', 'Fee Payment'),
        ('salary', 'Salary Payment'),
        ('expense', 'Expense'),
        ('purchase', 'Purchase'),
        ('refund', 'Refund'),
        ('other', 'Other'),
    ]
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="transactions")
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    transaction_type = models.CharField(max_length=50, choices=TYPE_CHOICES)
    description = models.TextField(blank=True)
    date = models.DateField()
    created_at = models.DateTimeField(auto_now_add=True)


# Faculty/Teacher Models
class Course(models.Model):
    TYPE_CHOICES = [
        ('online', 'Online'),
        ('regular', 'Regular'),
        ('diploma', 'Diploma'),
        ('offline', 'Offline'),
    ]
    STATUS_CHOICES = [
        ('draft', 'Draft'),
        ('pending_approval', 'Pending Approval'),
        ('active', 'Active'),
        ('inactive', 'Inactive'),
        ('closed', 'Closed'),
    ]
    
    title = models.CharField(max_length=200)
    description = models.TextField()
    course_type = models.CharField(max_length=20, choices=TYPE_CHOICES)
    price = models.DecimalField(max_digits=10, decimal_places=2, default=0)
    duration = models.CharField(max_length=50)  # e.g., "8 weeks", "3 months"
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="draft")
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="courses_created")
    created_at = models.DateTimeField(auto_now_add=True)
    syllabus = models.FileField(upload_to='syllabi/', blank=True, null=True)
    
    def __str__(self):
        return self.title


class CourseTeacher(models.Model):
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="teachers")
    teacher = models.ForeignKey(User, on_delete=models.CASCADE, related_name="courses_teaching")
    is_primary = models.BooleanField(default=False)
    assigned_at = models.DateTimeField(auto_now_add=True)
    assigned_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="teacher_assignments")
    
    class Meta:
        unique_together = ('course', 'teacher')


class Assignment(models.Model):
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="assignments")
    title = models.CharField(max_length=200)
    description = models.TextField()
    due_date = models.DateField()
    total_marks = models.DecimalField(max_digits=5, decimal_places=2)
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="assignments_created")
    created_at = models.DateTimeField(auto_now_add=True)
    is_active = models.BooleanField(default=True)


class LessonPlan(models.Model):
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="lesson_plans")
    title = models.CharField(max_length=200)
    content = models.TextField()
    date = models.DateField()
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="lesson_plans_created")
    created_at = models.DateTimeField(auto_now_add=True)


class Attendance(models.Model):
    STATUS_CHOICES = [
        ('present', 'Present'),
        ('absent', 'Absent'),
        ('leave', 'On Leave'),
        ('late', 'Late'),
    ]
    
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    date = models.DateField()
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="present")
    check_in_time = models.TimeField(blank=True, null=True)
    check_out_time = models.TimeField(blank=True, null=True)
    marked_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="attendance_marked")
    notes = models.TextField(blank=True)
    
    class Meta:
        unique_together = ('user', 'date')


class ClassRoutine(models.Model):
    DAY_CHOICES = [
        ('monday', 'Monday'),
        ('tuesday', 'Tuesday'),
        ('wednesday', 'Wednesday'),
        ('thursday', 'Thursday'),
        ('friday', 'Friday'),
        ('saturday', 'Saturday'),
        ('sunday', 'Sunday'),
    ]
    
    teacher = models.ForeignKey(User, on_delete=models.CASCADE, related_name='class_routines')
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name='class_routines')
    day_of_week = models.CharField(max_length=10, choices=DAY_CHOICES)
    start_time = models.TimeField()
    end_time = models.TimeField()
    room = models.CharField(max_length=50, blank=True)
    is_active = models.BooleanField(default=True)
    
    class Meta:
        unique_together = ('teacher', 'course', 'day_of_week', 'start_time')
```

## StudentApp/models.py

```python
from django.db import models
from AuthApp.models import User
from EmployeeApp.models import Course


class Enrollment(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('approved', 'Approved'),
        ('rejected', 'Rejected'),
        ('ongoing', 'Ongoing'),
        ('completed', 'Completed'),
        ('dropped', 'Dropped'),
    ]
    
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name="enrollments")
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="enrollments")
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="pending")
    fee_paid = models.BooleanField(default=False)
    enrolled_at = models.DateTimeField(auto_now_add=True)
    completion_date = models.DateField(blank=True, null=True)
    
    class Meta:
        unique_together = ('student', 'course')


class ExamResult(models.Model):
    enrollment = models.ForeignKey(Enrollment, on_delete=models.CASCADE, related_name="results")
    exam_name = models.CharField(max_length=100)
    marks_obtained = models.DecimalField(max_digits=5, decimal_places=2)
    total_marks = models.DecimalField(max_digits=5, decimal_places=2)
    grade = models.CharField(max_length=5, blank=True)
    passed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    remarks = models.TextField(blank=True)


class Certificate(models.Model):
    TYPE_CHOICES = [
        ('online', 'Online Course'),
        ('offline', 'Offline Course'),
        ('diploma', 'Diploma'),
        ('achievement', 'Achievement'),
    ]
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('approved', 'Approved'),
        ('issued', 'Issued'),
        ('rejected', 'Rejected'),
    ]
    
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name="certificates")
    course = models.ForeignKey(Course, on_delete=models.CASCADE, related_name="certificates")
    certificate_type = models.CharField(max_length=20, choices=TYPE_CHOICES)
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="pending")
    issue_date = models.DateField(blank=True, null=True)
    verified = models.BooleanField(default=False)
    certificate_number = models.CharField(max_length=50, unique=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)


class GuardianReport(models.Model):
    REPORT_TYPE_CHOICES = [
        ('monthly', 'Monthly Report'),
        ('results', 'Results Report'),
        ('payments', 'Payments Report'),
        ('attendance', 'Attendance Report'),
        ('other', 'Other'),
    ]
    
    student = models.ForeignKey(User, on_delete=models.CASCADE, related_name="guardian_reports")
    report_type = models.CharField(max_length=20, choices=REPORT_TYPE_CHOICES)
    content = models.TextField()
    sent_at = models.DateTimeField(auto_now_add=True)
    sent_to = models.EmailField()  # Guardian's email
    is_read = models.BooleanField(default=False)


class FeePayment(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('paid', 'Paid'),
        ('overdue', 'Overdue'),
        ('cancelled', 'Cancelled'),
    ]
    
    enrollment = models.ForeignKey(Enrollment, on_delete=models.CASCADE, related_name="fee_payments")
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    due_date = models.DateField()
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="pending")
    paid_at = models.DateTimeField(blank=True, null=True)
    payment_method = models.CharField(max_length=50, blank=True)
    transaction_id = models.CharField(max_length=100, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
```

## CandidateApp/models.py

```python
from django.db import models
from AuthApp.models import User
from EmployeeApp.models import JobPost


class CandidateProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='candidate_profile')
    resume = models.FileField(upload_to='resumes/', blank=True, null=True)
    skills = models.TextField(blank=True)
    experience = models.TextField(blank=True)
    education = models.TextField(blank=True)
    expected_salary = models.CharField(max_length=100, blank=True)
    availability = models.CharField(max_length=100, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)


class JobApplication(models.Model):
    STATUS_CHOICES = [
        ('applied', 'Applied'),
        ('under_review', 'Under Review'),
        ('interview_scheduled', 'Interview Scheduled'),
        ('rejected', 'Rejected'),
        ('hired', 'Hired'),
    ]
    
    candidate = models.ForeignKey(User, on_delete=models.CASCADE, related_name="job_applications")
    job_post = models.ForeignKey(JobPost, on_delete=models.CASCADE, related_name="applications")
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default="applied")
    applied_at = models.DateTimeField(auto_now_add=True)
    cover_letter = models.TextField(blank=True)
    notes = models.TextField(blank=True)
    
    class Meta:
        unique_together = ('candidate', 'job_post')


class InterviewInvitation(models.Model):
    application = models.ForeignKey(JobApplication, on_delete=models.CASCADE, related_name="interview_invitations")
    scheduled_date = models.DateTimeField()
    location = models.CharField(max_length=200, blank=True)
    online_meeting_link = models.URLField(blank=True)
    interviewer = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name="interviews")
    notes = models.TextField(blank=True)
    status = models.CharField(max_length=20, choices=[('scheduled', 'Scheduled'), ('completed', 'Completed'), ('cancelled', 'Cancelled')])
    feedback = models.TextField(blank=True)
```

## Summary

Key features implemented:
- Role-based user management with extended profile information
- Soft delete functionality with Trash model
- Activity logging for user actions
- Comprehensive models for HR processes (job posts, applications, interviews)
- Financial tracking (salaries, expenses, transactions)
- Course management with teacher assignments
- Attendance tracking for all user types
- Student enrollment, results, and certificates
- Candidate profiles and job application tracking