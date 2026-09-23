# 30-Day Build Plan — Finquo Web App
One-to-One Teaching Platform

# PRD - Product Requirements Document

## What is this?
This is an app that connects students with teachers for one-to-one classes.

## Why are we building it?
Right now, matching a student with the right teacher and managing their classes is manual and messy. This app makes it simple and organized.

## Who uses it?
- **Admin** — Adds teachers and assigns them to schedulers. Manages the whole platform.
- **Scheduler** — Matches teachers with students. Books class timings. Sends materials and gifts to students.
- **Teacher** — Sets their availability. Teaches classes. Shares lessons.
- **Student** — Gets added to the platform after buying a course. Attends classes, does lessons, and takes quizzes.
- **Enrollment Advisor** — Create accounts for students after course purchase
- **QA Auditor** — Audit each sessions if necessary
- **Finance** — Collect payments from students and pay teachers based on hourly rate

## How does a student join?
A student buys a course first. After that, they are added to the platform — they don't sign up on their own.

## What can the app do?
- Let the right people log in safely, based on their role.
- Let an Admin add teachers and assign them to a Scheduler.
- Let a Scheduler book class slots between a teacher and a student.
- Let a Scheduler send study materials and gifts to students.
- Show teachers their calendar and let them set free time slots.
- Run live classes through Zoom.
- Let students view lessons inside the app.
- Let students take quizzes inside the app.
- Track how each student and teacher is performing.
- Send booking and reminder alerts over WhatsApp.
- Use AI to help with some parts of the experience (details to be finalized).

## What does success look like?
- Students get matched and booked into classes without manual back-and-forth.
- Teachers know their schedule and can teach without confusion.
- The Admin can manage teachers and schedulers easily from one place.
- Students can learn, take quizzes, and see their progress in one app.

## What's not included right now?
- Students browsing and picking their own teacher (Scheduler does this instead).
- In-app live chat between students and teachers.
- Payments inside the app (handled separately, before enrollment).

# 30-Day Build Plan — One-to-One Teaching Platform

**What changed in this version:**
- Added a new screen for the Scheduler to send materials and gifts to students. This pushed the plan out by one day, so it now runs **31 days** (Sep 22 – Oct 22) instead of 30 — let me know if you'd rather cut a day elsewhere to keep it at 30.
- Lessons and Quiz backend work are split into their own separate days.
- Zoom (video calling) and AI-powered features are built in the **last week**, alongside final testing — assumed to mean AI is used for something like smart suggestions/automated help; let me know exactly what you want the AI to do and I'll make that day more specific.
- The UI week has **one full day dedicated to each user role's screens** (Admin, Scheduler, Teacher, Student).

### Status Legend

| Symbol | Meaning |
|--------|---------|
| ✅ | Done — finished and working |
| 🚧 | In Progress — being worked on right now |
| ▢ | Not Started — hasn't been picked up yet |
| ❌ | Blocked / Issue — stuck or something's wrong, needs attention |

## Week 1 (Sep 22 – Sep 29): Database & API Writing

| Date | Feature | What it means | Status |
|------------|---------|----------------|--------|
| Sep 22 (Tue) | Login & Signup logic | Admins, teachers, schedulers, and students can log in safely (with different access levels) | ✅ |
| Sep 23 (Wed) | Secure Data Storage | Setting up the system that safely stores all user info | 🚧 |
| Sep 24 (Thu) | Connecting the Pieces + Roles & Permissions | Linking storage to the app; setting up who can do what (Admin/Scheduler/Teacher/Student) | ▢ |
| Sep 25 (Fri) | Student Enrollment + Teacher Profile | Behind-the-scenes work to add a student after purchase, and to set up teacher profiles | ▢ |
| Sep 26 (Sat) | Teacher–Scheduler Assignment + Teacher Availability | Behind-the-scenes work for admins to assign teachers to schedulers, and for teachers to set their free time slots | ▢ |
| Sep 27 (Sun) | Booking Calendar | Behind-the-scenes work for schedulers to book time slots between teachers and students | ▢ |
| Sep 28 (Mon) | Lessons + Performance Tracking | Behind-the-scenes work for lesson content and tracking how students/teachers are doing | ▢ |
| Sep 29 (Tue) | Quiz System | Behind-the-scenes work for creating and grading quizzes | ▢ |

## Week 2 (Sep 30 – Oct 09): UI Screens — One Day Per Role

| Date | Feature | What it means | Status |
|------------|---------|----------------|--------|
| Sep 30 (Wed) | Admin Screens | Everything an Admin sees: managing teachers, schedulers, enrollments | ▢ |
| Oct 01 (Thu) | Scheduler Screens | Everything a Scheduler sees: assigning teachers, managing the calendar | ▢ |
| Oct 02 (Fri) | Scheduler — Send Materials & Gifts | The screen where a Scheduler can send study materials and gifts to students | ▢ |
| Oct 03 (Sat) | Teacher Screens | Everything a Teacher sees: profile, availability, upcoming classes | ▢ |
| Oct 04 (Sun) | Student Screens | Everything a Student sees: profile, enrolled course, upcoming classes | ▢ |
| Oct 05 (Mon) | Booking Calendar Screen | The screen to view and book available time slots | ▢ |
| Oct 06 (Tue) | Lessons Screen | The screen where students view lesson content | ▢ |
| Oct 07 (Wed) | Quiz Screen | The screen where students take quizzes | ▢ |
| Oct 08 (Thu) | Performance Dashboard Screen | The screen showing student/teacher performance and progress | ▢ |
| Oct 09 (Fri) | WhatsApp Notifications | Setting up booking and reminder alerts sent over WhatsApp | ▢ |

## Week 3 (Oct 10 – Oct 17): Infrastructure & Deployment on AWS

| Date | Feature | What it means | Status |
|------------|---------|----------------|--------|
| Oct 10 (Sat) | Cloud Servers Setup (AWS) | Setting up the servers that will run the app | ▢ |
| Oct 11 (Sun) | Managed Database Setup (Aurora Postgres) | Setting up a reliable, managed home for all the app's data | ▢ |
| Oct 12 (Mon) | File Storage Setup (S3) | Setting up where uploaded files (documents, images) are stored | ▢ |
| Oct 13 (Tue) | Background Task Handling (RabbitMQ) | Setting up a system that handles behind-the-scenes tasks (like sending a WhatsApp alert) reliably, without slowing the app down | ▢ |
| Oct 14 (Wed) | Speed Layer (Redis) | Setting up a "fast memory" that speeds up the app by remembering frequently-used info | ▢ |
| Oct 15 (Thu) | Domain, DNS & Security Setup | Connecting your website address to the live app and setting up secure connections | ▢ |
| Oct 16 (Fri) | Deployment Automation (GitHub Actions) | Setting up the process that pushes updates live automatically whenever changes are made | ▢ |
| Oct 17 (Sat) | Staging Trial Run & Fixes | Running the app in a live-like environment to catch and fix issues before going public | ▢ |

## Week 4 (Oct 18 – Oct 22): Zoom, AI Features & Launch

| Date | Feature | What it means | Status |
|------------|---------|----------------|--------|
| Oct 18 (Sun) | Zoom Integration — Setup | Behind-the-scenes work to auto-generate a Zoom link for each booked class | ▢ |
| Oct 19 (Mon) | Zoom Integration — Screen | The "Join via Zoom" button and class screen | ▢ |
| Oct 20 (Tue) | AI Features — Setup & Screen | Behind-the-scenes and screen work for AI-powered features *(needs more detail from you on what this should do)* | ▢ |
| Oct 21 (Wed) | Final Bug Fixing & Polish | Fixing issues and small improvements across the whole app | ▢ |
| Oct 22 (Thu) | Launch | Making the app officially live and accessible to real users | ▢ |
