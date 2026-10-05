# Backend

Simple guide to the Finquo backend service.

---

## Server & Architecture
- Framework: [Hono](https://hono.dev) mounted at `/api` in [`src/index.ts`](../backend/src/index.ts)
- RPC Layer: `@orpc/server` routing via [`src/api/routers/index.ts`](../backend/src/api/routers/index.ts)
- API Docs: OpenAPI / Scalar reference UI served at `/api/reference` via [`src/index.ts`](../backend/src/index.ts)
- Runtime: Bun (`bun run dev`) or Node.js (`node out/index.mjs`) or Cloudflare Workers (`wrangler dev`)
- Package Exports: Defined in [`package.json`](../backend/package.json), exposing `.`, `./config`, `./auth`, and `./api`
- Error Handling: ORPC interceptor in [`src/index.ts`](../backend/src/index.ts) converts Zod validation errors to HTTP 422 `INPUT_VALIDATION_FAILED`

---

## Auth & Permissions
- Auth Provider: Better Auth configured in [`src/auth/index.ts`](../backend/src/auth/index.ts) with Drizzle adapter
- Default Role: `user`
- Auth Mode: Email & Password enabled
- Session Cookie: `auth-session` (prefix `finquo-auth`, `SameSite=None`, `Secure`, `HttpOnly`)
- Base Auth Route: `/api/auth/*` handled by Better Auth
- Impersonation: Supported via session `impersonatedBy` tracking in [`src/db/schema/auth.ts`](../backend/src/db/schema/auth.ts) and [`src/api/procedure.ts`](../backend/src/api/procedure.ts)
- Access Control & Roles: Defined in [`src/auth/permissions.ts`](../backend/src/auth/permissions.ts)
  - `admin`: Full manage permissions across users, schedulers, teachers, students, qa auditors, and slots
  - `scheduler`: Can `update` scheduler, `update`/`delete` students and teachers, full CRUD on `slot`
  - `teacher`: Can `update` students and teacher data, `get`/`list`/`update` on `slot`
  - `enrollment_advisor`: Can `create` users and students, `update` advisor profile
  - `student`: Can `update` student profile, `get`/`list`/`update` on `slot`
  - `qa_auditor`: `get`/`list` on `slot`
  - `user`: Base role for newly registered accounts
- Procedures (Guards): Implemented in [`src/api/procedure.ts`](../backend/src/api/procedure.ts)
  - `publicProcedure`: No authentication required
  - `protectedProcedure`: Requires a valid user session
  - `adminProcedure`: Restricted to `admin` role
  - `schedulerProcedure`: Restricted to `scheduler` role
  - `teacherProcedure`: Restricted to `teacher` role
  - `studentProcedure`: Restricted to `student` role
  - `enrollmentAdvisorProcedure`: Restricted to `enrollment_advisor` role
  - `qaAuditorProcedure`: Restricted to `qa_auditor` role
  - `checkPermission(sector, action)`: Granular capability check on specific sectors (`scheduler`, `student`, `teacher`, `qa_auditor`, `enrollment_advisor`, `slot`)

---

## API Endpoints & Routers

### Core Endpoints
- `GET /api/` - Root health ping in [`src/index.ts`](../backend/src/index.ts) (returns `"OK"`)
- `GET /api/health` - RPC health check procedure in [`src/api/routers/index.ts`](../backend/src/api/routers/index.ts)
- `GET /api/private` - Returns authenticated session user in [`src/api/routers/index.ts`](../backend/src/api/routers/index.ts)
- `ALL /api/reference/*` - OpenAPI interactive documentation in [`src/index.ts`](../backend/src/index.ts)

### Admin Router ([`src/api/routers/admin.ts`](../backend/src/api/routers/admin.ts))
- `admin.getUsers` - Search users by name using trigram similarity (`similarity()` / `%` operator), filter by role
- `admin.promote` - Change user role (`admin`, `scheduler`, `teacher`, `student`, etc.)
- `admin.getSlots` - List all class slots in the system
- `admin.updateTeacher` - Reassign teacher supervisor
- `admin.updateStudent` - Reassign student mentor, supervisor, or update meeting link

### Common Router ([`src/api/routers/common.ts`](../backend/src/api/routers/common.ts))
- `common.getUser` - Fetch user details with joined relations (`schedulerData`, `teacherData`, `studentData`)
- `common.getLanguages` - Returns supported languages dictionary (`ml-IN`, `en-IN`)
- `common.getCountries` - Returns supported countries dictionary (`IN`, `US`)
- `common.getCurrencies` - Returns supported currencies dictionary (`₹`, `$`)

### Scheduler Router ([`src/api/routers/scheduler.ts`](../backend/src/api/routers/scheduler.ts))
- `scheduler.get` - Returns the scheduler profile for the logged-in user
- `scheduler.getTeachers` - Returns all teachers assigned to this supervisor
- `scheduler.getStudents` - Returns all students assigned to this supervisor
- `scheduler.getSlots` - Returns all calendar booking slots created by this scheduler
- `scheduler.searchTeachers` - Fuzzy search supervised teachers by name (trigram similarity)
- `scheduler.searchStudents` - Fuzzy search supervised students by name (trigram similarity)
- `scheduler.checkTeacherAvailability` - Validates teacher schedule on a given date against weekly availability rules and scheduled leaves
- `scheduler.createSlot` - Schedule a new class slot (`startTime < expectedEndTime`)
- `scheduler.rescheduleSlot` - Reschedule an existing slot with new time bounds
- `scheduler.createLesson` - Create curriculum lesson with language selection
- `scheduler.updateStudentLessons` - Assign curriculum lessons to a student
- `scheduler.updateTeacher` - Update details of a supervised teacher
- `scheduler.updateStudent` - Update details of a supervised student

### Teacher Router ([`src/api/routers/teacher.ts`](../backend/src/api/routers/teacher.ts))
- `teacher.get` - Returns the teacher profile for the logged-in user
- `teacher.getSupervisor` - Returns the supervisor (scheduler) assigned to this teacher
- `teacher.getStudents` - Returns all students mentored by this teacher
- `teacher.getSlots` - Returns all teaching slots assigned to this teacher
- `teacher.update` - Update teacher profile information
- `teacher.updateAvailability` - Set weekly availability schedule (mon-sun enabled flags and time ranges)
- `teacher.updateLeaves` - Replace entire leave schedule (`full`, `multiple`, or `partial` leaves)
- `teacher.addLeave` - Append a single leave entry to teacher's leave list

### Student Router ([`src/api/routers/student.ts`](../backend/src/api/routers/student.ts))
- `student.get` - Returns the student profile for the logged-in user
- `student.getMentor` - Returns the assigned mentor (teacher)
- `student.getSupervisor` - Returns the assigned supervisor (scheduler)
- `student.getSlots` - Returns all scheduled class slots for this student
- `student.update` - Update personal profile details

### Sidebar Router ([`src/api/routers/sidebar.ts`](../backend/src/api/routers/sidebar.ts))
- `sidebar.example` - Returns mock sidebar navigation items
- `sidebar.teachers` - Returns list of teachers for sidebar display

### Enrollment Router ([`src/api/routers/enrollment.ts`](../backend/src/api/routers/enrollment.ts))
- `enrollment.createAccount` - Protected action (checks `student:update`), queries supervised students

---

## Configuration & Business Rules
- Weekly Availability in [`src/config/time.ts`](../backend/src/config/time.ts):
  - Days: `mon`, `tue`, `wed`, `thu`, `fri`, `sat`, `sun`
  - `periodZod`: Session length between 15 and 250 minutes (stored as milliseconds)
  - `availabilityZod`: Record of daily configuration with active toggle and time ranges (`{ start, end }`)
- Subscription Plans in [`src/config/plans.ts`](../backend/src/config/plans.ts):
  - `finquo-30`: 30 classes (INR 1,000 / USD 10)
  - `finquo-60`: 60 classes (INR 2,000 / USD 20)
- Profile Types in [`src/config/profile.ts`](../backend/src/config/profile.ts):
  - `noteZod`: Staff notes with writer ID and timestamp
  - `addressZod`: Multi-line physical address with postal code and country
  - `schoolingZod`: Education boards (`icse`, `cbse`, `state`, `homeschool`, `online`, `other`, `unknown`)
  - `experienceZod`: Work history entries with organization, dates, and author
- Quiz System in [`src/config/quiz.ts`](../backend/src/config/quiz.ts):
  - Question types: `choices` (multi-select), `options` (single-select), `text` (free-form)
