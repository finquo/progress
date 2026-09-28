# Database

Simple guide to the database models, tables, and relationships.

---

## Overview
- **Database Engine**: PostgreSQL 16
- **ORM**: Drizzle ORM (`drizzle-orm`, `drizzle-kit`)
- **Connection Driver**: `postgres`
- **Migrations Path**: `backend/migrations/`
- **Commands**:
  - Generate migrations: `bun run db:generate`
  - Apply migrations: `bun run db:migrate`

---

## Tables & Models

### Authentication Tables (Better Auth)
- `user` - System users (stores `id`, `name`, `email`, `role`, `banned`, etc.)
- `session` - Active user sessions with IP, user-agent, and expiry
- `account` - Credentials and auth providers
- `verification` - Verification tokens for email validation

### Core Role Profiles
- `scheduler`
  - References `user.id` (1:1, cascade delete)
  - Manages teachers, students, and schedules
- `teacher`
  - References `user.id` (1:1, cascade delete)
  - `supervisorId` -> references `scheduler.id`
  - `experiences` -> JSONB array of previous work history
  - `languages` -> Array of spoken/teaching languages
- `student`
  - References `user.id` (1:1, cascade delete)
  - `mentorId` -> references assigned `teacher.id`
  - `supervisorId` -> references assigned `scheduler.id`
  - `program` -> Selected subscription plan (e.g. `finquo-30`, `finquo-60`)
  - `lessons` -> Array of assigned lesson IDs
  - `meetingLink` -> Personal class meeting URL
  - `paid`, `amount`, `remaining` -> Payment status with non-negative checks
  - `notes` -> JSONB array of staff observations
  - `address` -> JSONB student mailing address

### Learning & Classes
- `slot`
  - Class scheduling and booking record
  - Links `studentId`, `teacherId`, `schedulerId`, and `lessonId`
  - `startTime` and `expectedEndTime` (enforced `startTime < expectedEndTime`)
  - `meetingLink` -> Video call URL for the session
  - `status`: `scheduled`, `request_reschedule`, `rescheduled`, `started`, `attended`
  - **No-Overlap Rule**: PostgreSQL GiST constraint prevents overlapping active sessions for the same teacher
- `lesson`
  - Course curriculum lesson
  - `name`, `description`, `language`
  - `period` -> Lesson duration (default 1 hour interval)
  - `quiz` -> Embedded quiz JSONB schema
- `answer`
  - Student submitted answers for a lesson quiz
  - Links `studentId` and `lessonId`
  - `status`: `pending`, `answered`, `correct`, `partially_correct`, `wrong`, `retake`
  - `answer` -> Response payload JSONB

### Commerce & Logistics
- `payment`
  - Payment transaction log for student programs
  - `studentId` -> references `student.id`
  - `amount`, `currency` (₹ or $), `transactionId`, `paidAt`
- `material`
  - Physical learning kit/materials dispatched to students
  - `studentId` -> recipient student
  - `schedulerId` -> dispatching coordinator
  - `lessons` -> associated lesson IDs
  - `order` status: `packing`, `delivered`
  - `shippingAddress` -> recipient shipping details JSONB

---

## Entity Relationships
- **Scheduler**: Has many Teachers, many Students, many Slots
- **Teacher**: Belongs to 1 Scheduler (supervisor), has many Students (mentees), has many Slots
- **Student**: Belongs to 1 Scheduler, belongs to 1 Teacher (mentor), has many Slots, Payments, and Materials
- **Slot**: Connects 1 Teacher, 1 Student, 1 Scheduler, and 1 Lesson
- **Lesson**: Has many Slots and many Answers
