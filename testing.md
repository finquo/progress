# Testing Guide

Manual, feature-focused test plan for **https://test.finquo.ai**.
Platform business: admins onboard teachers/students and assign them to schedulers;
schedulers manage their people and schedule 1-to-1 paid sessions; kids join sessions,
parents pay offline. Test every item below with all roles that can see the page.

**Getting accounts:**
- Admin: contact the developer (aka me).
- Other roles: use the temporary [Create Account page](https://test.finquo.ai/login/create)
  — it gets disabled in production — or create them as admin.

---

## 1. Create user / update roles

- [ ] Admin: [create users](https://test.finquo.ai/admin/users/new) with each role.
- [ ] [Users table](https://test.finquo.ai/admin/users): edit a user's role (row menu
      → Edit Role) → list + badges update.
- [ ] Impersonate a user from the row menu → act as them → stop impersonating.
- [ ] Revoke Sessions and Delete User actually work (not just a toast).
- [ ] No-role user sees the "account not fully created" screen
      ([/user](https://test.finquo.ai/user)); after a role is assigned they land on
      their [role home](https://test.finquo.ai/me).

## 2. Change supervisor (teacher / student)

- [ ] Teacher profile (as admin/scheduler): change the owning scheduler → saved,
      visible on the [teachers table](https://test.finquo.ai/teachers) supervisor
      column and detail page.
- [ ] Same for student from the [students table](https://test.finquo.ai/students).
- [ ] Supervisor dropdown only lists scheduler users (no teachers/students).
- [ ] Hover card on the supervisor column opens the right scheduler.

## 3. Programs & lessons

- [ ] Create program with name/description/price for at least one country; validation
      blocks empty pricing → [create program](https://test.finquo.ai/programs/new)
- [ ] [Programs table](https://test.finquo.ai/programs): lesson count, per-country
      price columns, quick-view drawer.
- [ ] [Create lesson](https://test.finquo.ai/lessons/new) (name, language, duration,
      program) → appears in the [lessons table](https://test.finquo.ai/lessons),
      lesson count updates.
- [ ] Lesson detail page renders and edits persist after reload → open a row from
      the lessons table.

## 4. Assign program to students

- [ ] Admin/scheduler can set/change a student's program on the student profile →
      open from the [students table](https://test.finquo.ai/students).
- [ ] Assignment shows everywhere: students table program column, student detail,
      student's own [program page](https://test.finquo.ai/student/program).
- [ ] A student without a program sees the "no program assigned" empty state.

## 5. Create slots & join sessions

- [ ] [Create session](https://test.finquo.ai/slots/new): student + teacher + lesson +
      date + time + save. BUILDER IS INCOMPLETE — check that every mandatory field is
      actually enforced.
- [ ] Checks teacher availability and existing bookings — unavailable times cannot be
      selected (yes/no?).
- [ ] Cannot pick a lesson the student's program doesn't have.
- [ ] [Sessions table](https://test.finquo.ai/slots): correct student/teacher/scheduler
      per row, status filters work.
- [ ] Session detail page data matches what was created; edit drawer updates persist →
      open a row from the sessions table.
- [ ] Join flow: open the call page → pre-join → in-call video works both sides →
      from a session row, or directly at
      [test.finquo.ai/call/&lt;slot-id&gt;](https://test.finquo.ai/call/).
- [ ] Refresh/rejoin works; leave and end behave.

## 6. Recordings & processing (Media Pipeline)

The recording and all processing live on a session's own **Media** page:
open a row from the [sessions table](https://test.finquo.ai/slots) → Media tab, or
directly at [test.finquo.ai/slots/&lt;slot-id&gt;/media](https://test.finquo.ai/slots/).
It shows a step timeline from recording to summaries.

- [ ] During the call (section 5): the record button appears for
      admin/scheduler/teacher; guests never see it. Toggle it on from the pre-join
      "Instant recording" option or the in-call button.
- [ ] Pipeline is locked until the session is marked **Attended** — it shows the
      "Session not attended" notice before that. Mark the session attended (section 5)
      and confirm the timeline unlocks.
- [ ] **Recording Saved** step shows `video.mp4` after a recorded call → **Video**
      download button produces a playable .mp4.
- [ ] **Converted to Audio** step → **Audio** download button gives the .mp3; run it
      via its Retry/convert action if not auto-run.
- [ ] Two processing branches, both worth testing:
      - **Direct:** full transcript (`main.json`) → summary. Download both.
      - **Split:** audio split into chunks → each chunk transcribed → chunk summary.
        Each chunk row has its own Audio + Transcript downloads; all chunks must show
        "Transcript ready" before the chunk summary step unlocks.
- [ ] Step-by-step: each step has a Retry action (starts the job). Confirm retrying
      works, failures show a "Step failed" alert with the reason, and a completed step
      stays marked after **Refresh**.
- [ ] Trigger one step failure on purpose if possible (e.g. run a job before its input
      exists) — the button should be disabled instead, not error out.

## 7. Business-logic / cross-feature sanity

Confirm these cannot happen (if any CAN happen, flag it):

- [ ] Create a slot for a student who has **no program assigned** →
      [create session](https://test.finquo.ai/slots/new).
- [ ] Create a slot with a lesson that is **not in the student's program/curriculum**.
- [ ] Create a slot that **overlaps** another booking for the same teacher or student.
- [ ] Schedule outside the teacher's availability window or during leaves →
      [availability page](https://test.finquo.ai/teacher/availability).
- [ ] A non-privileged role (teacher/student) edits slots, users, roles, or programs.
- [ ] Guest call attendees can record, end for all, or see session details.
- [ ] Sessions past/attended can be rebooked to the same time, re-joinable forever.
- [ ] Reassigning a program retroactively breaks the student's existing lesson
      timeline ([student lessons](https://test.finquo.ai/student/lessons)) or attended
      counts.
- [ ] Deleting a program/lesson/user that still has dependent data (slots, sessions,
      enrollments) — should be blocked or accessible-safe.

## Status page sanity (5 min)

- [ ] [/status](https://test.finquo.ai/status) shows Connected when the API is
      healthy.

## Acceptance

A test pass is green only if every checkbox above is verified **for every relevant
role** and no business-logic rule in section 7 is violated anywhere in the product.
