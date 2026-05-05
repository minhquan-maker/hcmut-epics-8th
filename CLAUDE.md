# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**AquaGuard** is a multi-platform flood emergency management system connecting three user roles: Citizens (affected users), Rescuers (field teams), and Admins (system managers).

The `Epics 8th/` directory is the **EPICS 8th iteration workspace** — it contains documentation files (reports, slides, surveys, business model). The actual source code lives in sibling directories (`AquaGuard-iOS-main/` and `Others/`). The parent `AquaGuard/` directory also holds `Green Hackathon/` and other project materials.

## Directory Layout

```
AquaGuard/                          # Parent project directory
├── AquaGuard-iOS-main/            # Native iOS app (SwiftUI, MVVM)
│   ├── AquaGuard/                 # App entry point (AquaGuardApp.swift)
│   ├── Models/Models.swift        # Data models, enums, mock data
│   ├── ViewModels/               # HomeVM, MapVM, ReportVM
│   ├── Views/                    # SwiftUI views (Home, Map, Report, Rescue, Safety)
│   └── Captures/                 # App screenshots
├── Others/                        # Web app source code, iOS code, docs
│   ├── AquaGuard_Documentation.md # Full web app technical docs
│   ├── AquaGuard-iOS-main/       # iOS app (alternate location)
│   ├── PROJECT_DESCRIPTION.txt    # Web app feature overview
│   └── aquaguard-roles-usecase.html  # Interactive RBAC + use-case diagram
├── Green Hackathon/               # Hackathon materials
└── Epics 8th/                    # EPICS iteration workspace (this directory)
    ├── CLAUDE.md                 # This file
    ├── memory.md                 # Project state tracking & decisions
    ├── features.md               # Complete feature specification
    ├── benefit.md                # Benefits methodology (3 claims + citations)
    ├── overallreport.md          # Final consolidated report
    ├── spec.md                   # Presentation specification (22 slides + script)
    ├── business-model.html       # Business model (dark navy theme)
    ├── business_report_web.html  # Business model (white design + Service Lifecycle)
    ├── survery_report_web.html   # Interactive survey report (Chart.js, bilingual EN/VI)
    ├── surveydata.xlsx           # Source survey data (24 questions, EN/VI bilingual)
    ├── business.txt              # Business model source text
    ├── chart.png                 # Bar chart image (infrastructure conditions)
    ├── chart.pptx                # Same chart as PPTX (editable)
    └── template.png              # Template image
```

## Platform Architecture

### Web App
- **Stack:** React 19 + Vite 6 + Leaflet 1.9.4 + TailwindCSS 4.0.0
- **V2 Backend:** Node.js + Express + PostgreSQL + WebSocket (Socket.io)
- **V1 Backend (prototype):** Firebase (Auth + Firestore)
- **Auth:** JWT (phone + password) + Google OAuth
- **Storage:** Cloudinary (SOS photos)
- **Deploy:** Docker Compose
- Source code is not in this workspace — see `AquaGuard_Documentation.md` for full architecture.

### iOS App
- **Stack:** Swift 5.9, SwiftUI, MapKit, CoreLocation
- **Architecture:** MVVM
- **Target:** iOS 26.0+
- **Entry point:** `AquaGuard/AquaGuardApp.swift`
- **Navigation:** 5-tab TabView in `ContentView.swift` (Home, Map, Report, Rescue, Safety)
- Open via `open AquaGuard.xcodeproj` in Xcode

## Design System (V2 Documentation Pages)

All V2 HTML pages share a unified visual system:
- **Palette:** Deep (#1E4E79), Teal (#1FB6A6), Blue (#4A90E2), Blue-bg (#EDF3FA), Gray (#6B7C93/#F4F7FA), White, Orange (#E25822)
- **Font:** Inter (Google Fonts)
- **Layout:** White card-based on light gray page background
- **Responsive:** 900px / 640px / 480px / 380px breakpoints
- See `business_report_web.html` and `survery_report_web.html` as reference implementations

The HTML pages have two design variants:
- **White design** (`business_report_web.html`, `survery_report_web.html`): Card-based light theme with the palette above
- **Dark theme** (`business-model.html`): Dark navy background, teal accents — use the white design as the canonical reference for new pages

## RBAC System

| Role | Capabilities |
|------|-------------|
| **Citizen** | SOS requests, family safety tracking, flood map, safety protocols |
| **Rescuer** | Rescue queue, team missions (Leader/Co-leader/Member sub-roles), flood map |
| **Admin** | User management, SOS assignment, rescue team oversight, flood map editor, analytics |

Frontend guard: `canAccessPage(role, page)` in `src/config/rbac.js`. Backend middleware: `authMiddleware`, `requireAdmin`, `requireRoles([...])`.

## Development Workflow

This is a **documentation-first workspace**. Source code lives in sibling directories:
- **Web app**: `../Others/` (React + Vite; source code NOT in this workspace)
- **iOS app**: `../AquaGuard-iOS-main/` (open via `open AquaGuard.xcodeproj` in Xcode)
- **Docs & reports**: This directory (`Epics 8th/`)

For the web app, run from `AquaGuard-iOS-main/` or `Others/AquaGuard-iOS-main/`. For the iOS app, run from `AquaGuard-iOS-main/`. See `memory.md` for full project status and delivery tracking.

## Key Documentation References

- `memory.md` — Project decisions, file tracking, and V2 completion status (start here)
- `overallreport.md` — Final consolidated report
- `benefit.md` — Quantified benefits (97% faster pre-alert, 45% faster rescue, 85% faster analytics)
- `spec.md` — Presentation specification (22 slides + 5-min script)
- `features.md` — Complete feature specification for all 3 roles
- `business_report_web.html` — Business model (white design, Service Lifecycle 3-phase, responsive)
- `survery_report_web.html` — Interactive survey report (Chart.js, bilingual EN/VI, 91 respondents, 24 questions)
- `chart.pptx` — Editable PPTX version of survey infrastructure chart
- `AquaGuard_Documentation.md` (in `Others/`) — Full web app architecture
- `AquaGuard-iOS-main/README.md` (in `Others/`) — iOS app overview

## V1 vs V2

| Component | V1 | V2 |
|-----------|----|----|
| Backend | Firebase only | Node.js + Express + PostgreSQL |
| Auth | Firebase Auth | JWT + Google OAuth |
| Real-time | Firestore listeners | WebSocket (live GPS tracking) |
| SOS Flow | Citizen → Rescuer view | Full E2E: assign → accept → track → complete → cancel |
| Rescuer Teams | No sub-roles | Leader / Co-leader / Member hierarchy |
| iOS App | None | SwiftUI MVP |
