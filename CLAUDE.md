# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**AquaGuard** is a multi-platform flood emergency management system connecting three user roles: Citizens (affected users), Rescuers (field teams), and Admins (system managers).

`Epics 8th/` is the EPICS iteration workspace — it contains documentation, reports, HTML pages, and survey data. Source code lives in sibling directories under `../Others/`.

## Source Code Locations

- **Web app:** `../Others/AquaGuard-iOS-main/` (note: directory name is misleading, it contains the React web app)
- **iOS app:** `../Others/AquaGuard-iOS-main/AquaGuard-iOS-main/` or `../AquaGuard-iOS-main/`
- **Full web app docs:** `../Others/AquaGuard_Documentation.md`

## Development Commands

### Web App (`../Others/AquaGuard-iOS-main/`)
```bash
npm install
npm run dev          # Start dev server (Vite)
npm run build        # Production build
npm run lint         # ESLint check
```

### iOS App (`../Others/AquaGuard-iOS-main/AquaGuard-iOS-main/`)
```bash
open AquaGuard.xcodeproj    # Open in Xcode
# Build via Xcode: Product → Build (⌘B)
# Run on simulator: ⌘R
# Run unit tests: ⌘U
```

## Architecture

### Web App — Key Patterns

**Client-side page switching:** The main `Dashboard.jsx` uses React state (`activePage`) to switch between views, NOT React Router nested routes. This means `/` always renders Dashboard, which internally handles routing via `handleNavigate(page)` → `setActivePage(page)`.

**RBAC enforcement:** `canAccessPage(role, page)` in `src/config/rbac.js` gates navigation. Backend uses middleware `authMiddleware`, `requireAdmin`, `requireRoles([...])`.

**Firebase lazy init:** `src/config/firebase.js` only creates Firebase instances when getter functions are called (not at module load time). This avoids initialization errors during SSR/build.

**V2 migration:** V1 uses Firebase-only (Auth + Firestore). V2 replaces backend with Node.js + Express + PostgreSQL + WebSocket. The React frontend stays the same, connected to V2 via REST API + Socket.io.

### iOS App — Key Patterns

**MVVM architecture:** `AquaGuard/` (app entry), `Models/Models.swift` (data + mock), `ViewModels/` (HomeVM, MapVM, ReportVM), `Views/` (SwiftUI views organized by tab: Home, Map, Report, Rescue, Safety).

**5-tab navigation:** `ContentView.swift` uses `TabView` with exactly 5 tabs matching the web app's mobile nav.

### SOS State Machine

```
pending → assigned → in_progress → resolved
                           ↘ cancelled
```

Admin assigns groups (`PUT /api/sos/:id/assign`). Leader/Co-leader self-assign from queue. WebSocket shares rescuer GPS with citizen during `in_progress`.

## Design System (V2 Documentation Pages)

All V2 HTML pages share a unified visual system:
- **Palette:** Deep (#1E4E79), Teal (#1FB6A6), Blue (#4A90E2), Blue-bg (#EDF3FA), Gray (#6B7C93/#F4F7FA), White, Orange (#E25822)
- **Font:** Inter (Google Fonts)
- **Layout:** White card-based on light gray page background
- **Responsive:** 900px / 640px / 480px / 380px breakpoints

Two design variants:
- **White design** (`business_report_web.html`, `survery_report_web.html`): Card-based light theme — use as the canonical reference for new pages
- **Dark theme** (`business-model.html`): Dark navy background, teal accents

## RBAC System

| Role | Capabilities |
|------|-------------|
| **Citizen** | SOS requests, family safety tracking, flood map, safety protocols |
| **Rescuer** | Rescue queue, team missions (Leader/Co-leader/Member sub-roles), flood map |
| **Admin** | User management, SOS assignment, rescue team oversight, flood map editor, analytics |

Frontend guard: `canAccessPage(role, page)` in `src/config/rbac.js`. Backend middleware: `authMiddleware`, `requireAdmin`, `requireRoles([...])`.

### Rescuer Team Sub-roles

| Sub-role | Permissions |
|----------|------------|
| **Leader** | Accept/complete/cancel SOS, invite, promote, kick, edit group, disband |
| **Co-leader** | Accept/complete/cancel SOS, invite, kick (no promote/disband) |
| **Member** | View only — no SOS operations |

## Key Documentation References

- `memory.md` — Project decisions and file tracking
- `overallreport.md` — Consolidated report (problem, architecture, business model, roadmap)
- `benefit.md` — Quantified benefits with methodology and citations
- `spec.md` — Presentation specification (22 slides, script framework)
- `features.md` — Complete feature specification for all 3 roles and the E2E rescue flow
- `survery_report_web.html` — Interactive survey report (note: "survery" typo in filename)
- `../Others/AquaGuard_Documentation.md` — Full web app architecture and API endpoints
- `../Others/AquaGuard-iOS-main/README.md` — iOS app overview

## V1 vs V2

| Component | V1 | V2 |
|-----------|----|----|
| Backend | Firebase only | Node.js + Express + PostgreSQL |
| Auth | Firebase Auth | JWT + Google OAuth |
| Real-time | Firestore listeners | WebSocket (live GPS tracking) |
| SOS Flow | Citizen submits, Rescuer views | Full E2E: assign → accept → track → complete → cancel |
| Family Safety | Basic list | Full family network + map location sharing |
| Rescuer Teams | No sub-roles | Leader / Co-leader / Member hierarchy |
| Role Verification | None | Role passwords for admin/rescuer registration |
| Deployment | Vercel | Docker Compose |
| iOS App | None | SwiftUI MVP |