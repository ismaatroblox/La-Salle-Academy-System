# School Attendance Tracking System

## Overview

This is a school attendance tracking application built for teachers and administrators to efficiently manage student attendance. The system uses a mobile-first design approach optimized for tablets and phones, allowing teachers to quickly mark attendance for their classes and administrators to monitor attendance across all classes. 

The application follows a Material Design-inspired system-based approach, drawing from productivity tools like Google Classroom and Notion for clean, efficient data management interfaces.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture

**Framework & Build Tools**
- React 18 with TypeScript for type-safe component development
- Vite as the build tool and development server
- Wouter for client-side routing (lightweight alternative to React Router)
- TanStack React Query for server state management and data fetching

**UI Component System**
- shadcn/ui component library (Radix UI primitives with Tailwind styling)
- "new-york" style variant configured for a modern, clean aesthetic
- Tailwind CSS for utility-first styling with custom design tokens
- Custom CSS variables for theming (light/dark mode support built-in)
- Mobile-first responsive design with breakpoints at 768px

**State Management Strategy**
- React Query for server state (attendance records, class data, user sessions)
- React Hook Form for form state management with Zod validation
- Session-based authentication state managed via HTTP-only cookies

**Design System**
- Typography: Inter/Roboto font families via Google Fonts
- Spacing: Tailwind units (2, 4, 6, 8) for consistent spacing
- Component hierarchy: Cards, buttons, form elements following Material Design principles
- Color system: HSL-based custom properties for flexible theming

### Backend Architecture

**Server Framework**
- Express.js for HTTP server and API routes
- Node.js HTTP server with session management
- Development: Vite middleware integration for HMR
- Production: Static file serving from built client bundle

**Session Management**
- Express-session with MemoryStore for development
- HTTP-only cookies with configurable security settings
- Session secret via environment variable
- 24-hour session lifetime

**API Design**
- RESTful endpoints organized by feature area:
  - `/api/auth/*` - Authentication (login, logout, session check)
  - `/api/classes/*` - Class data retrieval
  - `/api/attendance/*` - Attendance record CRUD operations
  - `/api/admin/*` - Admin-only endpoints for system-wide data
- Middleware-based authentication (requireAuth, requireAdmin)
- Zod schema validation for request payloads

**Data Storage Strategy**
- PostgreSQL database (Replit managed) for ALL persistent storage:
  - Users (staff/teachers/admins)
  - Parents (separate table from staff users)  
  - Classes and student rosters
  - Attendance records
  - Grades
  - Justifications
  - Announcements
  - Active sessions
- Database storage layer (`server/db-storage.ts`) provides CRUD operations for all entities
- Drizzle ORM for type-safe database queries using WebSocket driver (neon-serverless)
- In-memory storage (MemStorage class) for session management and temporary data only

**Database Tables**
- `users` - Staff credentials (teachers/admins) with assigned classes
- `parents` - Parent credentials with linked student names
- `classes` - Class names 
- `students` - Student names linked to classes
- `attendance_records` - Attendance data (date, status, marked by)
- `grades` - Formative and final exam grades per student/class
- `justifications` - Absence/late justifications with approval workflow
- `announcements` - System-wide announcements
- `active_sessions` - User session tracking
- `audit_logs` - Immutable audit trail for all system actions

**Data Import (Optional)**
- One-time import from Google Sheets available for initial data setup
- Import endpoint: POST `/api/admin/import-from-sheets`
- Uses upsert pattern for safe re-imports without duplicates

**Authentication Model**
- Database-only credential validation (fast, no API limits)
- Separate authentication for staff (users table) and parents (parents table)
- Role-based access control (teacher, coordinator, admin/system_developer, parent)
- Role hierarchy: System Developer (admin) > Coordinator > Teacher > Parent
- Teachers have access limited to assigned classes
- Coordinators can switch between Teacher View (limited to assigned classes) and Coordinator View (full access like admin) via session role toggle. On login, coordinators are prompted to choose their initial view.
- System Developers (admin role) have system-wide access to all classes and data; they cannot be messaged by anyone
- Parents can only view their linked students' data
- **Session Role**: Coordinators have a `sessionRole` field (teacher | coordinator) in their session that determines their current access level and which dashboard they see

### External Dependencies

**Google Sheets Integration (Optional Import Only)**
- Google Sheets is NO LONGER used for runtime operations
- Only used for optional one-time data import via admin dashboard
- Google Sheets API v4 via googleapis npm package
- OAuth2 authentication using Replit Connectors system
- Environment variables (required only for import feature):
  - `GOOGLE_SPREADSHEET_ID` - Target spreadsheet ID
  - `REPLIT_CONNECTORS_HOSTNAME` - Connector API endpoint

**Replit-Specific Integrations**
- Replit Connectors for Google Sheets OAuth flow
- Vite plugins for Replit development environment:
  - `@replit/vite-plugin-runtime-error-modal` - Error overlay
  - `@replit/vite-plugin-cartographer` - Development tooling
  - `@replit/vite-plugin-dev-banner` - Development banner
- Environment detection via `REPL_ID` variable

**Third-Party UI Libraries**
- Radix UI primitives for accessible component foundations
- Lucide React for icon system
- date-fns for date manipulation and formatting
- cmdk for command palette functionality
- embla-carousel-react for carousel components
- react-day-picker for calendar/date picker
- vaul for drawer components

**Development Tools**
- Drizzle ORM configuration present (drizzle.config.ts) but not actively used
- PostgreSQL dialect configured for potential future database migration
- TypeScript for type safety across full stack
- ESBuild for production server bundling

**Key Architectural Decisions**

1. **Full PostgreSQL Storage**: All data is stored in PostgreSQL database - users, parents, classes, students, attendance, grades, justifications. Google Sheets dependencies have been completely removed for runtime operations, making the system faster and more reliable without API rate limits.

2. **Database-Only Authentication**: Login validates credentials against PostgreSQL only. Staff authenticate against the users table, parents authenticate against the parents table. No external API calls during login.

3. **Persistent Attendance Storage**: Attendance records are now persisted in the database, surviving server restarts. WebSocket driver (neon-serverless with ws package) is used for proper PostgreSQL array handling.

4. **Session-Based Auth**: Uses traditional session cookies rather than JWT tokens. Simpler for server-rendered scenarios and eliminates token refresh complexity.

5. **Monorepo Structure**: Client, server, and shared code in single repository with path aliases (@/, @shared/) for clean imports.

6. **Mobile-First Design**: Touch-optimized interfaces with larger tap targets and simplified workflows for marking attendance on tablets/phones in classroom settings.

7. **Parent Portal**: Parents have a separate authentication table and can only view their linked students' grades, attendance, and submit justifications for absences/lateness.

8. **Audit Logging System**: Comprehensive immutable audit trail tracks all user actions across the system. Logs are append-only (no update/delete) and capture user, role, action, category, target details, IP address, and timestamp. Admin dashboard includes dedicated "Logs" page with filtering, search, pagination, and statistics. Categories tracked: auth, attendance, grades, students, staff, justifications, announcements, settings.

9. **Bidirectional Messaging System**: Full messaging capabilities across user types with inbox support:
   - **Admin capabilities**: Message individual parents (about students), individual teachers, all parents in a class; has inbox for messages from teachers and parents
   - **Teacher capabilities**: Message individual parents about students, all parents in their assigned classes, or admins; has inbox for messages from parents and admins
   - **Parent capabilities**: Message teachers (about their students) or admins; has inbox for messages from teachers and admins (UI in Spanish)
   - **Inbox functionality**: Each role has an inbox showing received messages with unread count badges, mark as read, and mark all read features
   - **Quick messaging**: From Students section, admins can click "Message Parent" to compose messages directly from student reports
   - **Messages table**: Stores sender (fromUser, fromRole), recipient type, target user/class, subject, content, read status, timestamps
   - **recipientType field**: 'parent', 'teacher', 'class', 'admin'
   - **API endpoints**: `/api/messages/teacher-inbox`, `/api/messages/admin-inbox`, `/api/messages/from-parent`, `/api/parent/available-teachers`
   - **Authorization**: Teachers restricted to their assigned classes; parents can only message teachers assigned to their children's classes
   - **Audit logging**: All messages logged with SEND_MESSAGE action including full recipient details

10. **Admin Database Management**: Comprehensive admin interface for database CRUD operations:
   - **Database tab**: Dedicated tab in admin dashboard for managing all entities
   - **Classes management**: Create, edit, rename, delete classes (with cascading updates to students, attendance, grades, justifications, and user assignments)
   - **Students management**: Create, edit, move between classes, delete students (with cascading updates to related records and parent associations)
   - **Staff management**: Create, edit, delete teacher/admin accounts with role and class assignment management
   - **Parents management**: Create, edit, delete parent accounts with student linkage management
   - **Audit logging**: All database modifications logged with appropriate action types (CREATE_CLASS, UPDATE_STUDENT, DELETE_PARENT, etc.)
   - **API endpoints**: `/api/admin/database/{type}` for POST/PUT/DELETE operations on class, student, staff, parent

11. **Application Disable/Maintenance Mode**: Allows admins to lock the entire system with a Spanish message:
   - **Application tab**: Dedicated tab in admin dashboard for managing system-wide settings
   - **Toggle functionality**: Disable/enable the entire application with a single button
   - **Custom reason**: Optional reason field displayed to non-admin users in Spanish
   - **Blue lock screen**: Full-screen Spanish maintenance page shown to teachers and parents when disabled
   - **Admin bypass**: Admins can always access the system to re-enable it
   - **Database table**: `application_settings` stores status, reason, who disabled, and when
   - **Audit logging**: ENABLE_APPLICATION and DISABLE_APPLICATION actions logged to audit trail
   - **API endpoints**: GET `/api/application-status` (public), GET/POST `/api/admin/application-status` (admin-only)