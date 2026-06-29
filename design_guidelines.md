# Design Guidelines: School Attendance Tracking System

## Design Approach
**System-Based Approach** - Drawing from Material Design and productivity tools like Google Classroom, Asana, and Notion for clean, efficient data management interfaces.

## Core Design Principles
1. **Mobile-First Efficiency** - Touch-optimized for quick attendance marking on tablets and phones
2. **Data Clarity** - Clean presentation of student lists and attendance records
3. **Task-Focused** - Streamlined workflows for daily attendance tasks
4. **Role Clarity** - Clear visual distinction between teacher and admin views

## Typography
- **Primary Font**: Inter or Roboto via Google Fonts
- **Hierarchy**:
  - Page Headers: text-2xl md:text-3xl font-bold
  - Section Headers: text-xl font-semibold
  - Student Names: text-base font-medium
  - Labels/Metadata: text-sm text-gray-600
  - Body/Descriptions: text-base

## Layout System
- **Spacing Units**: Tailwind units of 2, 4, 6, and 8 (p-4, m-6, gap-8, etc.)
- **Container**: max-w-7xl mx-auto px-4 for main content areas
- **Card Spacing**: p-6 for cards, p-4 for mobile
- **List Items**: py-3 px-4 for student rows

## Component Library

### Authentication
- **Login Page**: Centered card (max-w-md) with school branding, clean form inputs, role selection dropdown, remember me checkbox

### Navigation
- **Top Bar**: Sticky header with app name/logo, current user info, class selector (teachers), logout button
- **Mobile**: Hamburger menu for secondary navigation

### Dashboard Layouts

**Teacher Dashboard**:
- Class selector dropdown in header
- Current class card showing: class name, student count, last attendance date
- Quick action buttons: "Mark Attendance Today", "View History"
- Recent attendance summary cards (grid-cols-1 md:grid-cols-2 lg:grid-cols-3)

**Admin Dashboard**:
- Stats overview: Total classes, total students, attendance rate (grid-cols-2 md:grid-cols-4)
- Class list table with: class name, teacher, student count, last updated
- Search and filter controls

### Attendance Interface
- **Date Picker**: Prominent at top with prev/next day arrows
- **Student List**: Full-width cards on mobile, table on desktop
- **Each Student Row**:
  - Student name (left-aligned, font-medium)
  - Status buttons group (right-aligned): Present (green accent), Absent (red accent), Late (yellow accent)
  - Active state: filled background, inactive: outline only
  - Touch target: min-h-12 for easy tapping
- **Bulk Actions**: "Mark All Present" button at top
- **Save Button**: Fixed bottom on mobile (sticky), inline on desktop

### Data Tables
- **Headers**: bg-gray-50, uppercase text-xs font-semibold tracking-wide
- **Rows**: border-b hover:bg-gray-50 transition
- **Responsive**: Stack to cards on mobile (< md breakpoint)

### Forms
- **Input Fields**: border rounded-lg px-4 py-2.5, focus:ring-2 focus:border-blue-500
- **Labels**: text-sm font-medium mb-2 block
- **Dropdowns**: Full-width on mobile, min-w-48 on desktop

### Status Indicators
- **Present**: Green badge or checkmark icon
- **Absent**: Red badge or x icon  
- **Late**: Yellow/orange badge or clock icon
- **Color Coding**: Use bg-green-100 text-green-800 badge pattern

### Cards
- **Base**: bg-white border rounded-lg shadow-sm p-6
- **Hover**: hover:shadow-md transition
- **Mobile**: p-4 on small screens

## Responsive Behavior
- **Mobile (<768px)**: Single column, stacked cards, fixed action buttons at bottom
- **Tablet (768-1024px)**: Two-column grids where appropriate, side-by-side forms
- **Desktop (>1024px)**: Full tables, multi-column dashboards, inline actions

## Animations
**Minimal and purposeful only**:
- Button hover: subtle scale or shadow lift
- Page transitions: none (instant)
- Loading states: simple spinner for data fetching

## Images
**No hero images or marketing imagery** - This is a functional tool. Only use:
- School logo/branding in header (if provided)
- User avatars (optional, can be initials in circles)
- Empty state illustrations for "no classes" or "no attendance records"