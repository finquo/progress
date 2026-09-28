# Frontend

Simple guide to the Next.js frontend application.

---

## Tech Stack
- **Framework**: Next.js 15 with React 19 (App Router)
- **Styling**: Tailwind CSS with Radix UI / shadcn/ui primitives
- **Client State & Fetching**: TanStack React Query (`@tanstack/react-query`)
- **API Communication**: `@orpc/client` for end-to-end typed queries & mutations
- **Auth Client**: `better-auth/client` with magic link and schema inference plugins
- **PWA**: `@ducanh2912/next-pwa` with offline fallback (`public/offline.html`, `public/sw.js`)

---

## Routes & Pages

### Public & Auth Routes
- `/` - Landing page showing real-time backend connection status
- `/login` - Unified authentication page (Sign In / Sign Up tabs with email & password)
- `/error` - Displays authentication failure and permission errors
- `/me` - User profile and account details

### Dashboard Routes (Sidebar Layout)
- `/scheduler` - Main coordinator/scheduler dashboard
- `/scheduler/students` - Scheduler's student directory and management
- `/scheduler/teachers` - Scheduler's teacher roster and management
- `/teacher` - Teacher personal dashboard and schedule overview
- `/teacher/students` - Teacher's assigned mentees list
- `/admin` - Administrator control panel

---

## Core Components
- `SidebarProvider` (`components/ui/sidebar.tsx`) - Collapsible responsive sidebar layout
- `SchedulerSidebar` (`app/(sidebar)/_components/scheduler.tsx`) - Navigation links for scheduler workflows
- `SignInForm` & `SignUpForm` (`components/sign-in-form.tsx`, `components/sign-up-form.tsx`) - Better-Auth driven auth widgets
- `Providers` (`components/providers.tsx`) - Combines ThemeProvider, ReactQueryProvider, and PWARegistration
- `UI Primitives` (`components/ui/`) - Buttons, inputs, dialogs, dropdowns, tables, avatars, alerts, badges

---

## Data Fetching Pattern
- `lib/api.ts`: Creates type-safe `RPCLink` pointed at backend `/api`
- `hooks/api.ts`: Wraps oRPC client into TanStack React Query query options
- Example usage in components:
  - `useQuery(api.health.queryOptions())`
  - `useQuery(api.scheduler.getStudents.queryOptions())`
