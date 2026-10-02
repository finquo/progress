# Backend

Simple guide to the Finquo backend service.

---

## Server & Architecture
- **Framework**: [Hono](https://hono.dev) mounted at `/api`
- **RPC Layer**: [@orpc/server](https://orpc.unnoq.com) for type-safe client-server communication
- **API Docs**: OpenAPI / Scalar reference UI available at `/api/reference`
- **Runtime**: Bun (`bun run dev`) or Node.js (`node out/index.mjs`) or Cloudflare Workers (`wrangler dev`)
- **Error Handling**: ORPC interceptor formats Zod validation errors to HTTP 422 `INPUT_VALIDATION_FAILED`

---

## Auth & Permissions
- **Auth Provider**: Better Auth (`better-auth`) with Drizzle adapter
- **Auth Mode**: Email & Password enabled
- **Session Cookie**: `auth-session` (prefix `finquo-auth`, SameSite=None, Secure, HttpOnly)
- **Base Auth Route**: `/api/auth/*` (handles sign-in, sign-up, sign-out, session retrieval)
- **Roles**:
  - `admin`: Full manage permissions (`create`, `update`, `delete`) on schedulers, teachers, students, qa auditors
  - `scheduler`: Can `update` scheduler, `update`/`delete` students, `update`/`delete` teachers
  - `teacher`: Can `update` students and `update` teacher data
  - `enrollment_advisor`: Can `create` students and `update` advisor data
  - `student`: Can `update` own student details
  - `qa_auditor`: Read-only / auditing role
- **Procedures (Guards)**:
  - `publicProcedure`: No authentication required
  - `protectedProcedure`: Requires a valid user session
  - `adminProcedure`: Restricted to `admin` role
  - `schedulerProcedure`: Restricted to `scheduler` role
  - `teacherProcedure`: Restricted to `teacher` role
  - `studentProcedure`: Restricted to `student` role
  - `enrollmentAdvisorProcedure`: Restricted to `enrollment_advisor` role
  - `qaAuditorProcedure`: Restricted to `qa_auditor` role
  - `checkPermission(sector, action)`: Granular capability check on specific resources

---

## API Endpoints & Routers

### Core Endpoints
- `GET /api/` - Root health ping (returns `"OK"`)
- `GET /api/health` - RPC health check procedure
- `GET /api/private` - Returns currently authenticated session user
- `ALL /api/reference/*` - OpenAPI specification and interactive API documentation

### Sidebar Router (`sidebar`)
- `sidebar.example` - Returns mock sidebar navigation items
- `sidebar.teachers` - Returns list of teachers for sidebar display

### Scheduler Router (`scheduler`)
- `scheduler.get` - Returns the scheduler profile for the logged-in user
- `scheduler.getTeachers` - Returns all teachers assigned to this supervisor
- `scheduler.getStudents` - Returns all students assigned to this supervisor
- `scheduler.getSlots` - Returns all calendar booking slots created by this scheduler

### Teacher Router (`teacher`)
- `teacher.get` - Returns the teacher profile for the logged-in user
- `teacher.getSupervisor` - Returns the supervisor (scheduler) assigned to this teacher
- `teacher.getStudents` - Returns all students mentored by this teacher
- `teacher.getSlots` - Returns all teaching slots assigned to this teacher

### Student Router (`student`)
- [`student.get`](../backend/src/index.ts) - Returns the student profile for the logged-in user
- `student.getMentor` - Returns the assigned mentor (teacher)
- `student.getSupervisor` - Returns the assigned supervisor (scheduler)
- `student.getSlots` - Returns all scheduled class slots for this student

### Enrollment Router (`enrollment`)
- `enrollment.createAccount` - Protected action (checks `student:update`), queries supervised students

---

## Configuration & Business Rules
- **Plans (`plans.ts`)**:
  - `finquo-30`: 30 classes (Demo Plans)
  - `finquo-60`: 60 classes (Demo Plans)
- **Quiz System (`quiz.ts`)**:
  - Question types: `choices` (multi-select), `options` (single-select), `text` (free-form)
- **Data Types (`type.d.ts`)**:
  - `Note`: Timestamped notes written by staff
  - `Address`: Multi-line physical address with postal code and country
  - `Schooling`: Educational boards (`icse`, `cbse`, `state`, `homeschool`, `online`, `other`)
  - `Experience`: Teacher work history entries
