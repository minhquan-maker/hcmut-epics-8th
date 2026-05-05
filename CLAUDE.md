# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**AquaGuard** is a multi-platform flood emergency management system connecting three user roles: Citizens (affected users), Rescuers (field teams), and Admins (system managers).

The `Epics 8th/` directory is the **EPICS 8th iteration workspace** — it contains documentation files (reports, slides, surveys, business model HTML pages). The actual source code lives in sibling directories. The parent `AquaGuard/` directory also holds `Green Hackathon/` and other project materials.

## Directory Layout

```
AquaGuard/                          # Parent project directory
├── AquaGuard-iOS-main/            # Native iOS app (SwiftUI, MVVM)
│   ├── AquaGuard/                 # App entry point (AquaGuardApp.swift)
│   ├── Models/Models.swift        # Data models, enums, mock data
│   ├── ViewModels/               # HomeVM, MapVM, ReportVM
│   └── Views/                    # SwiftUI views (Home, Map, Report, Rescue, Safety)
├── Others/                        # Web app source code, docs
│   ├── AquaGuard_Documentation.md # Full web app technical docs
│   ├── AquaGuard-iOS-main/       # iOS app (alternate location)
│   ├── PROJECT_DESCRIPTION.txt    # Web app feature overview
│   └── aquaguard-roles-usecase.html  # Interactive RBAC + use-case diagram
├── Green Hackathon/               # Hackathon materials
└── Epics 8th/                    # EPICS iteration workspace (this directory)
    ├── CLAUDE.md                 # This file
    ├── memory.md                 # Project decisions, file tracking, V2 completion status
    ├── features.md               # Complete feature specification (Citizen/Rescuer/Admin)
    ├── benefit.md                # Benefits methodology (97% faster pre-alert, 45% faster rescue, 85% faster analytics)
    ├── overallreport.md          # Final consolidated report
    ├── spec.md                   # Presentation specification (22 slides + 5-min script)
    ├── business-model.html       # Business model (dark navy theme)
    ├── business_report_web.html  # Business model (white design + Service Lifecycle)
    ├── survery_report_web.html   # Interactive survey report (Chart.js, bilingual EN/VI, 91 respondents, 24 questions)
    │                                 ⚠️ Filename has a typo: "survery" not "survey" — used as-is in links
    ├── surveydata.xlsx           # Source survey data (24 questions, EN/VI bilingual)
    ├── chart.pptx                # Editable PPTX of survey infrastructure chart
    └── template.png              # Presentation template image
```

## Architecture

### Web App
- **Stack:** React 19 + Vite 6 + Leaflet 1.9.4 + TailwindCSS 4.0.0
- **V2 Backend:** Node.js + Express + PostgreSQL + WebSocket (Socket.io)
- **V1 Backend (prototype):** Firebase (Auth + Firestore)
- **Auth:** JWT (phone + password) + Google OAuth
- **Storage:** Cloudinary (SOS photos)
- **Deploy:** Docker Compose
- Source code lives in `../Others/` — see `AquaGuard_Documentation.md` for full architecture.

### iOS App
- **Stack:** Swift 5.9, SwiftUI, MapKit, CoreLocation
- **Architecture:** MVVM
- **Target:** iOS 26.0+
- **Navigation:** 5-tab TabView (Home, Map, Report, Rescue, Safety)
- Open via `open AquaGuard.xcodeproj` in Xcode

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

- `memory.md` — Project decisions, file tracking, V2 delivery status (start here)
- `overallreport.md` — Final consolidated report (problem, solution, architecture, business model, roadmap)
- `benefit.md` — Quantified benefits with methodology, calculations, and 8 citations (UNDRR, FEMA, World Bank, GSO, MONRE, MARD)
- `spec.md` — Presentation specification (22 slides, 6 sections, 5-minute script framework, visual assets checklist)
- `features.md` — Complete feature specification for all 3 roles, including the E2E rescue flow and SOS state machine
- `survery_report_web.html` — Interactive survey report (note: "survery" typo in filename)
- `AquaGuard_Documentation.md` (in `../Others/`) — Full web app architecture, API endpoints, DB schema
- `AquaGuard-iOS-main/README.md` (in `../Others/`) — iOS app overview

## SOS End-to-End Flow

```
Citizen sends SOS (GPS + description + photos + urgency)
    │
    ├── Admin assigns to rescue group (PUT /api/sos/:id/assign)
    │   OR Rescuer self-assigns from queue (Leader/Co-leader only)
    │
    ▼
Rescuer accepts → status: pending → in_progress
    │
    ├── WebSocket: rescuer GPS shared with citizen (real-time tracking)
    │
    ▼
Rescuer completes → status: resolved
    │
    ▼
Data flows into analytics dashboard
```

**SOS States:** `pending` → `assigned` → `in_progress` → `resolved` / `cancelled`

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