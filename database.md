# Database

Simple guide to the database models, tables, and relationships.

---

## Overview
- Database Engine: PostgreSQL 16
- Extensions:
  - `btree_gist`: Required for slot exclusion constraints
  - `pg_trgm`: Enables trigram fuzzy text similarity searches
- ORM: Drizzle ORM configured in [`drizzle.config.ts`](../backend/drizzle.config.ts)
- Database Connection: Initialized in [`src/db/index.ts`](../backend/src/db/index.ts)
- Migrations Path: [`migrations/`](../backend/migrations)
- Zod Validation: Defined in [`src/db/zod.ts`](../backend/src/db/zod.ts)
- Commands:
  - Generate migrations: `bun run db:generate`
  - Apply migrations: `bun run db:migrate`

---

## Tables & Models

### Authentication Tables ([`src/db/schema/auth.ts`](../backend/src/db/schema/auth.ts))
- `user`
  - Stores `id`, `name`, `email`, `role`, `banned`, `banReason`, `banExpires`
  - Index `user_role_idx` on `role`
  - GIN Trigram index `user_name_trigram_idx` (`gin_trgm_ops`) on `name` for high-speed fuzzy search
- `session`
  - Stores `id`, `token`, `expiresAt`, `ipAddress`, `userAgent`, `userId`
  - `impersonatedBy`: Stores original administrator ID during user impersonation sessions
- `account`
  - Credentials and authentication provider mappings
- `verification`
  - Verification tokens for email validation flows

### Core Role Profiles
- `scheduler` in [`src/db/schema/scheduler.ts`](../backend/src/db/schema/scheduler.ts)
  - References `user.id` (1:1, cascade delete)
  - Manages assigned teachers, students, and bookings
- `teacher` in [`src/db/schema/teacher.ts`](../backend/src/db/schema/teacher.ts)
  - References `user.id` (1:1, cascade delete)
  - `supervisorId` -> references `scheduler.id`
  - `availability` -> JSONB weekly availability schedule (`Availability`)
  - `leaves` -> Array of custom PostgreSQL `tstzrange` leave intervals (`full`, `multiple`, `partial`)
  - `experiences` -> JSONB array of previous work history (`Experience[]`)
  - `languages` -> Array of teaching languages (`LanguageCodes[]`)
- `student` in [`src/db/schema/student.ts`](../backend/src/db/schema/student.ts)
  - References `user.id` (1:1, cascade delete)
  - `mentorId` -> references assigned `teacher.id`
  - `supervisorId` -> references assigned `scheduler.id`
  - `program` -> Selected subscription plan (e.g. `finquo-30`, `finquo-60`)
  - `lessons` -> Array of assigned lesson IDs
  - `meetingLink` -> Personal class meeting URL
  - `paid`, `amount`, `remaining` -> Payment tracking with non-negative checks
  - `notes` -> JSONB array of staff observations (`Note[]`)
  - `address` -> JSONB student mailing address (`Address`)

### Learning & Classes
- `slot` in [`src/db/schema/slot.ts`](../backend/src/db/schema/slot.ts)
  - Class scheduling and booking record
  - Links `studentId`, `teacherId`, `schedulerId`, and `lessonId`
  - `startTime` and `expectedEndTime` (enforced `startTime < expectedEndTime`)
  - `meetingLink` -> Optional video conference URL for the session
  - `status`: `scheduled`, `request_reschedule`, `rescheduled`, `started`, `attended`
  - No-Overlap Rule: PostgreSQL GiST exclusion constraint (`slot_no_overlap_teacher_active`) prevents double-booking active sessions for a teacher
- `lesson` in [`src/db/schema/lesson.ts`](../backend/src/db/schema/lesson.ts)
  - Course curriculum lesson
  - `name`, `description`, `language`
  - `period` -> Lesson duration (default 1 hour interval)
  - `quiz` -> Embedded quiz JSONB schema
- `answer` in [`src/db/schema/lesson.ts`](../backend/src/db/schema/lesson.ts)
  - Student submitted answers for a lesson quiz
  - Links `studentId` and `lessonId`
  - `status`: `pending`, `answered`, `correct`, `partially_correct`, `wrong`, `retake`
  - `answer` -> Response payload JSONB

### Commerce & Logistics
- `payment` in [`src/db/schema/student.ts`](../backend/src/db/schema/student.ts)
  - Payment transaction log for student programs
  - `studentId` -> references `student.id`
  - `amount`, `currency` (`₹` or `$`), `transactionId`, `paidAt`
- `material` in [`src/db/schema/material.ts`](../backend/src/db/schema/material.ts)
  - Physical learning kit/materials dispatched to students
  - `studentId` -> recipient student
  - `schedulerId` -> dispatching coordinator
  - `lessons` -> associated lesson IDs
  - `order`: `packing`, `delivered`
  - `shippingAddress` -> recipient shipping details JSONB

---

## Custom Database Utilities & Columns
- `leaveRange` in [`src/db/column/leave-range.ts`](../backend/src/db/column/leave-range.ts):
  - Custom PostgreSQL `tstzrange` column type
  - Handles 3 leave modes:
    - `full`: Single day leave (stored as a 24-hour range)
    - `multiple`: Multi-day consecutive leave block
    - `partial`: Specific time window on a given date
- `ArrayAppend` in [`src/db/utils.ts`](../backend/src/db/utils.ts):
  - Drizzle helper calling PostgreSQL `array_append(column, value)` for atomic array updates
- `checkAgainstLeaves` in [`src/utils/leave.ts`](../backend/src/utils/leave.ts):
  - Utility function checking if a target date/time conflicts with any of a teacher's leave ranges

---

## Entity Relationships ([`src/db/schema/relations.ts`](../backend/src/db/schema/relations.ts))
- `user`: Directly joins `schedulerData` (1:1), `teacherData` (1:1), and `studentData` (1:1)
- `scheduler`: Has many Teachers, many Students, many Slots
- `teacher`: Belongs to 1 Scheduler (supervisor), has many Students (mentees), has many Slots
- `student`: Belongs to 1 Scheduler, belongs to 1 Teacher (mentor), has many Slots, Payments, and Materials
- `slot`: Connects 1 Teacher, 1 Student, 1 Scheduler, and 1 Lesson
- `lesson`: Has many Slots and many Answers
