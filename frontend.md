# Frontend

Simple guide to the Next.js frontend application.

---

## Tech Stack
- Framework: Next.js 15 with React 19 configured in [`next.config.ts`](../frontend/next.config.ts)
- Styling: Tailwind CSS with Radix UI / shadcn/ui primitives configured in [`components.json`](../frontend/components.json)
- Client State & Fetching: TanStack React Query (`@tanstack/react-query`) via [`src/components/providers.tsx`](../frontend/src/components/providers.tsx)
- API Communication: `@orpc/client` configured in [`src/lib/api.ts`](../frontend/src/lib/api.ts)
- Auth Client: `better-auth/client` configured in [`src/lib/auth.ts`](../frontend/src/lib/auth.ts)
- PWA: Configured in [`pwa.config.ts`](../frontend/pwa.config.ts) with offline fallback in [`public/offline.html`](../frontend/public/offline.html) and [`public/sw.js`](../frontend/public/sw.js)

---

## Routes & Pages

### Public & Auth Routes
- [`/`](../frontend/src/app/page.tsx) - Landing page showing real-time backend connection status
- [`/login`](../frontend/src/app/(center)/login/page.tsx) - Unified authentication page (Sign In / Sign Up tabs with email & password)
- [`/error`](../frontend/src/app/(center)/error/page.tsx) - Displays authentication failure and permission errors
- [`/me`](../frontend/src/app/me/page.tsx) - User profile and account details

### Dashboard Routes (Sidebar Layout)
- Root Dashboard Layout: [`src/app/(sidebar)/layout.tsx`](../frontend/src/app/(sidebar)/layout.tsx)
- [`/scheduler`](../frontend/src/app/(sidebar)/scheduler/page.tsx) - Main coordinator/scheduler dashboard
- [`/scheduler/students`](../frontend/src/app/(sidebar)/scheduler/students/page.tsx) - Scheduler's student directory and management
- [`/scheduler/teachers`](../frontend/src/app/(sidebar)/scheduler/teachers/page.tsx) - Scheduler's teacher roster and management
- [`/teacher`](../frontend/src/app/(sidebar)/teacher/page.tsx) - Teacher personal dashboard and schedule overview
- [`/teacher/students`](../frontend/src/app/(sidebar)/teacher/students/page.tsx) - Teacher's assigned mentees list
- [`/admin`](../frontend/src/app/(sidebar)/admin/layout.tsx) - Administrator control panel

---

## Core Components
- Sidebar: [`src/app/(sidebar)/_components/scheduler.tsx`](../frontend/src/app/(sidebar)/_components/scheduler.tsx) & [`src/app/(sidebar)/_components/header.tsx`](../frontend/src/app/(sidebar)/_components/header.tsx)
- Authentication Forms: [`src/components/sign-in-form.tsx`](../frontend/src/components/sign-in-form.tsx) & [`src/components/sign-up-form.tsx`](../frontend/src/components/sign-up-form.tsx)
- App Providers: [`src/components/providers.tsx`](../frontend/src/components/providers.tsx) (Theme, ReactQuery, and PWA registration)
- UI Primitives: Located in [`src/components/ui/`](../frontend/src/components/ui)

---

## Data Fetching Pattern
- `lib/api.ts`: Creates type-safe RPCLink in [`src/lib/api.ts`](../frontend/src/lib/api.ts) pointed at backend `/api`
- `hooks/api.ts`: Creates TanStack Query helpers in [`src/hooks/api.ts`](../frontend/src/hooks/api.ts)
- Example usage in components:
  - `useQuery(api.health.queryOptions())`
  - `useQuery(api.scheduler.getStudents.queryOptions())`
