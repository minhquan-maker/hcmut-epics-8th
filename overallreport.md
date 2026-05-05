# AquaGuard EPICS V2 — Final Consolidated Report

> Phiên bản: 1.0.0
> Ngày: 2026-05-03
> EPICS 8th Iteration — AquaGuard Flood Emergency Management Platform

---

## Mục lục

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Existing Solutions Gap](#3-existing-solutions-gap)
4. [V1 Achievements](#4-v1-achievements)
5. [V2 Architecture Upgrade](#5-v2-architecture-upgrade)
6. [Features Summary](#6-features-summary)
7. [Benefits & Impact](#7-benefits--impact)
8. [Business Model](#8-business-model)
9. [Future Plans](#9-future-plans)
10. [References](#10-references)

---

## 1. Executive Summary

AquaGuard V2 is a multi-platform flood emergency management system that connects **Citizens** (flood-affected residents), **Rescuers** (field rescue teams), and **Administrators** (system coordinators) in a real-time, coordinated response ecosystem. Unlike fragmented traditional emergency channels — phone hotlines, social media groups, and manual dispatch systems — AquaGuard provides a unified platform where SOS requests are submitted with GPS location, assigned to rescue teams instantly, and tracked end-to-end from submission to resolution.

The V2 iteration represents a major architectural upgrade from the V1 Firebase-based prototype to a production-ready stack: **Node.js + Express + PostgreSQL + WebSocket** on the backend, with **React + Vite** for web and **SwiftUI** for iOS. This report documents the problem, the solution, the quantified benefits, and the roadmap to pilot deployment.

---

## 2. Problem Statement

Vietnam is among the countries most affected by flooding in Southeast Asia. Annual monsoon seasons cause widespread disruption across the country, with the Central region (Thừa Thiên Huế, Đà Nẵng, Quảng Nam, Quảng Ngãi) being disproportionately impacted.

### 2.1 Scale of the Problem

| Metric | Value | Source |
|--------|-------|--------|
| Deaths and missing from natural disasters (2024) | Significant, trending ↑ | [7] General Statistics Office of Vietnam (GSO), 2025 |
| Economic losses from flooding | ~85.099 nghìn tỷ VND (~3.47 tỷ USD) | [8] VietnamNet & GSO, 2025 |
| Average households affected per major flood event | Tens of thousands | [6] MONRE, 2024 |
| Population in flood-prone areas | ~30% of Vietnam's 100M+ population | [2] World Bank, 2023 |

### 2.2 Root Causes

Three systemic failures make flood response inadequate in Vietnam:

1. **Fragmented communication channels** — Citizens have no unified emergency reporting channel. Phone hotlines (113/114/115) are overloaded. Zalo groups and Facebook are informal and unstructured. No GPS location is captured at source.

2. **Manual dispatch coordination** — Rescue dispatch relies on phone call chains from district officials to village heads to individual responders. No real-time visibility into which teams are available, en route, or already on-site.

3. **No structured data for post-event analysis** — Damage reports are collected via paper forms, WhatsApp photos, and phone summaries. It takes days to weeks to build a coherent picture of the disaster, delaying resource allocation for the next event.

---

## 3. Existing Solutions Gap

Current flood emergency solutions in Vietnam have critical limitations:

| Solution | Strengths | Critical Gaps |
|----------|-----------|---------------|
| **Government Emergency Hotlines (113/114/115)** | Widely known, universal access | Overloaded during disasters; no GPS; no real-time dispatch; no citizen tracking |
| **Zalo / Facebook Groups** | High adoption rate in Vietnam | Unstructured; no GPS; no tracking; information overload; no rescue coordination |
| **Viettel SOS / Military Apps** | Government-backed | Limited to registered users; no family safety tracking; no real-time GPS sharing |
| **Google Maps / Waze** | GPS navigation | No emergency reporting; no SOS features; no rescue coordination |
| **Traditional Loudspeaker Broadcasts** | Reaches offline phones | Requires power (fails during blackouts); slow relay; no confirmation of receipt |

**Common gaps across all existing solutions:**
- No GPS auto-capture at point of SOS
- No real-time rescue team tracking
- No structured data collection for post-event analytics
- No family safety visibility
- No multi-channel push alerts (app + SMS simultaneously)

AquaGuard V2 is designed to fill these specific gaps.

---

## 4. V1 Achievements

V1 (EPICS 8th Iteration, delivered) established the proof-of-concept with a Firebase-based web application:

### 4.1 What Was Built

- **RBAC System** — Three roles (Citizen, Rescuer, Admin) with distinct navigation and permissions
- **Live Flood Map** — Leaflet map with Firestore-connected markers displaying 5 sample flood zones in Đà Nẵng, using CartoDB tiles
- **SOS Request System** — Citizens can submit SOS with location, description, urgency level, and photo uploads (Cloudinary). Status tracked: pending → in_progress → resolved
- **Authentication** — Firebase Auth supporting Google OAuth and Phone OTP
- **Safety Protocols** — Emergency contact list and survival guides
- **Reports & News Feed** — Alert feed with danger/warning/info classification
- **Responsive Design** — Desktop sidebar + mobile bottom navigation

### 4.2 Research & Validation

| Metric | Value |
|--------|-------|
| Survey respondents | 500+ |
| On-site tested users | 100+ |
| Social media reach | 18,000+ |
| Test region | Đà Nẵng |

### 4.3 V1 Limitations

V1 was explicitly designed as a prototype with known constraints:
- Firebase-only backend (limited scalability, vendor lock-in)
- No real-time GPS tracking between rescuer and citizen
- No team hierarchy for rescuers
- No family safety network
- No comprehensive analytics dashboard

These limitations directly informed the V2 architecture.

---

## 5. V2 Architecture Upgrade

V2 is a ground-up architectural redesign, not an incremental improvement.

### 5.1 Architecture Comparison

| Component | V1 | V2 |
|-----------|----|----|
| **Frontend Web** | React 19 + Vite | React 19 + Vite (unchanged) |
| **Frontend Mobile** | None | SwiftUI (iOS MVP) |
| **Backend** | Firebase only | Node.js + Express |
| **Database** | Firestore (NoSQL) | PostgreSQL (relational) |
| **Auth** | Firebase Auth | JWT (phone + password) + Google OAuth |
| **Real-time** | Firestore listeners | WebSocket (Socket.io) |
| **Image Storage** | Cloudinary | Cloudinary (retained) |
| **Deployment** | Vercel | Docker Compose |

### 5.2 System Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Citizens (Web + iOS)              │
│  Citizens send SOS, track family, view flood map   │
└────────────────────────┬────────────────────────────┘
                         │ HTTPS + WebSocket
┌────────────────────────▼────────────────────────────┐
│              Node.js + Express API                   │
│  REST API (auth, sos, users, teams, analytics)      │
│  WebSocket Server (live GPS tracking)               │
└──────────┬──────────────────────────┬───────────────┘
           │                          │
┌──────────▼──────────┐  ┌────────────▼────────────────┐
│   PostgreSQL        │  │     Firebase (fallback)     │
│  (users, sos,      │  │   - Auth (Google OAuth)      │
│   teams, zones,    │  │   - Cloudinary (SOS photos)   │
│   analytics)       │  │                              │
└────────────────────┘  └──────────────────────────────┘
```

### 5.3 API Overview

| Endpoint | Method | Description | Role |
|----------|--------|-------------|------|
| `/api/auth/register` | POST | Register (phone + password) | Public |
| `/api/auth/login` | POST | Login → JWT | Public |
| `/api/auth/google` | POST | Google OAuth login | Public |
| `/api/sos` | POST | Create SOS request | Citizen |
| `/api/sos/my` | GET | My SOS requests | Citizen |
| `/api/sos/all` | GET | All SOS (overview) | All |
| `/api/sos/team` | GET | Team's missions | Rescuer |
| `/api/sos/:id/assign` | PUT | Assign SOS to team | Admin |
| `/api/sos/:id/accept` | PUT | Accept SOS mission | Rescuer |
| `/api/sos/:id/complete` | PUT | Complete mission | Rescuer |
| `/api/sos/:id/cancel` | PUT | Cancel mission | Rescuer |
| `/api/teams` | POST | Create rescue team | Rescuer |
| `/api/teams/:id/invite` | POST | Invite member | Leader/Co |
| `/api/teams/:id/promote` | PUT | Promote to Co-leader | Leader |
| `/api/teams/:id/kick` | DELETE | Remove member | Leader/Co |
| `/api/teams/:id/disband` | DELETE | Disband team | Leader |
| `/api/users` | GET | All users | Admin |
| `/api/users/:id/role` | PUT | Change user role | Admin |
| `/api/analytics` | GET | System analytics | Admin |
| `/api/zones` | CRUD | Flood zone management | Admin |

### 5.4 RBAC Permission Matrix

| Action | Citizen | Rescuer | Admin |
|--------|---------|---------|-------|
| Submit SOS | ✅ | ✅ | ✅ |
| View own SOS | ✅ | ✅ | ✅ |
| View all SOS | — | ✅ (queue) | ✅ |
| Accept SOS | — | ✅ (Leader/Co only) | ✅ (assign) |
| Complete/Cancel SOS | — | ✅ (Leader/Co only) | ✅ |
| Create/Manage team | — | ✅ | — |
| View analytics | — | — | ✅ |
| Manage flood zones | — | — | ✅ |
| Manage users | — | — | ✅ |

---

## 6. Features Summary

*(Detailed specification available in `features.md`)*

### 6.1 Citizen Features

- **Safety Status Toggle** — One-tap status: Safe ✅ / Danger ❌ / Injured 🩹. Updates in real-time for family members to see.
- **Family Safety Board** — Monitor safety status of all connected family members on a single dashboard.
- **SOS Request** — Submit emergency with GPS location (auto-detected), description, urgency level (low/medium/high/critical), and up to 5 photos uploaded to Cloudinary.
- **Live Rescuer Tracking** — Once a rescuer accepts, view their real-time GPS position on the map via WebSocket.
- **Interactive Flood Map** — Leaflet map with 4-level severity pins (Critical 🟣 / Severe 🔴 / Moderate 🟠 / Safe 🟢), zone details, and family member locations.
- **Safety Protocols** — Offline-accessible flood survival guides and emergency contact shortcuts (113/114/115).

### 6.2 Rescuer Features

- **Mission Queue** — View all pending SOS requests sorted by time, with full details (name, GPS, photos, urgency).
- **Team Missions** — Once a team accepts a SOS, all team members see the same mission data. Status: active → in_progress → completed.
- **Live GPS Sharing** — Share real-time GPS position with the citizen in need via WebSocket, enabling precise rendezvous.
- **Team Management** — Create teams, invite members by phone, promote members to Co-leader, kick members, and disband teams. Sub-roles: Leader / Co-leader / Member.
- **Flood Map (Rescuer View)** — Same as citizen map but with SOS markers highlighted and team member positions.

### 6.3 Admin Features

- **User Management** — Full user list with role reassignment (citizen ↔ rescuer ↔ admin).
- **SOS Assignment** — Assign pending SOS to any rescue group. The group's leader is automatically assigned as `assigned_to`.
- **Flood Map Editor** — Draw/edit/delete flood zones directly on the map interface. Zones propagate to all citizens and rescuers in real-time.
- **System Analytics** — Dashboards showing: user growth over time, SOS trends (by day/week/month), rescue performance metrics (avg response time, fastest, slowest), user distribution by role.

### 6.4 iOS App Features

- Native SwiftUI for iOS 26.0+
- MapKit integration with CoreLocation for GPS
- 5-tab navigation: Home, Map, Report, Rescue, Safety
- 4-level severity pins with tap-for-details
- One-tap emergency SMS (Viettel, Vinaphone, Mobifone data packages)
- Emergency direct-dial shortcuts (113, 114, 115)
- Offline safety guides

---

## 7. Benefits & Impact

*(Detailed methodology available in `benefit.md`)*

AquaGuard V2 achieves three quantified benefits, derived from industry benchmarks and validated through V1 user research:

### 7.1 Quantified Benefits

| Benefit | Improvement | Baseline | AquaGuard | Confidence |
|---------|-------------|----------|-----------|------------|
| Pre-flood citizen alert | **~97% faster** | 45–90 min (gov broadcast) | <2 min (GPS push) | Medium |
| Rescue response time | **~45% faster** (~20 min saved) | 35–55 min (traditional dispatch) | 15–25 min (GPS direct) | Medium-High |
| Post-flood analytics | **~85% faster** | 14 days (manual aggregation) | ~2 days (automated) | Medium-Low |

### 7.2 Social Impact

- **500+ surveyed** to understand user needs and pain points
- **100+ tested** in on-site pilot at Đà Nẵng
- **18,000+ social media reach** to build community awareness

### 7.3 Limitations

Benefits are contingent on: (1) sufficient user adoption in target regions, (2) integration with government emergency services, (3) internet/mobile connectivity during disasters. Pilot deployment is recommended to validate these estimates in real conditions.

*(Full methodology, confidence analysis, and citations: see `benefit.md`)*

---

## 8. Business Model

### 8.1 Revenue Streams

| Stream | Description | Timeline |
|--------|-------------|----------|
| **B2G (Government)** | Contracts with provincial governments (UBND) and national agencies (MARD, MONRE) for emergency management systems | Short-to-medium term |
| **Grants** | Disaster relief funding from international organizations (UNDP, World Bank DRF), social impact funds | Medium term |
| **Venture / Impact Investment** | Social enterprise investment focused on climate resilience | Long term |

### 8.2 Pricing Model

- **Pilot (0–12 months)**: Free for government partners in exchange for deployment data and case studies
- **Standard (1–3 years)**: Subscription model per province/region
- **Enterprise**: White-label deployment for national emergency management agencies

### 8.3 Competitive Positioning

| Competitor | Differentiation |
|------------|----------------|
| Zalo / Social media | Structured data, GPS, real-time tracking, RBAC |
| Government hotlines | Digital-first, app + SMS, real-time dispatch visibility |
| Commercial SOS apps | Team coordination, family safety network, open platform |
| Google Maps / Waze | Emergency-specific features, rescue team integration |

---

## 9. Future Plans

### 9.1 Short-term (2026)

- [ ] **Pilot Deployment** — Deploy AquaGuard V2 in 1–2 target provinces (Đà Nẵng, Quảng Nam) with local government partnership
- [ ] **Backend Integration for iOS** — Connect SwiftUI app to V2 backend (PostgreSQL + WebSocket)
- [ ] **User Testing** — Recruit 500+ users for pilot testing; measure actual response times
- [ ] **Government MOU** — Sign partnership agreements with provincial emergency management departments

### 9.2 Medium-term (2027)

- [ ] **National Emergency System Integration** — Integrate with existing government emergency infrastructure (113/114/115)
- [ ] **AI Flood Prediction** — Incorporate NASA/NOAA weather data + historical flood patterns for predictive alerts
- [ ] **Multi-province Expansion** — Scale to 5–10 provinces in Central Vietnam
- [ ] **Android App** — Develop native Android app for broader mobile coverage

### 9.3 Long-term (2028+)

- [ ] **SE Asia Expansion** — Adapt for Philippines, Indonesia, Thailand — countries with similar flood risk profiles
- [ ] **Satellite Connectivity** — Integrate satellite SMS fallback (e.g., Globalsat) for disaster scenarios where terrestrial networks fail
- [ ] **AI-powered Resource Optimization** — Predict rescue resource needs based on flood severity and population density

### 9.4 iOS App Roadmap

| Milestone | Status | Description |
|-----------|--------|-------------|
| MVP: UI/UX + MapKit | ✅ Complete | All screens implemented in SwiftUI |
| Backend Integration | 🔄 Pending | Connect to V2 PostgreSQL + WebSocket |
| Offline Mode | ⬜ Pending | Local SQLite database for reporting without internet |
| Push Notifications | ⬜ Pending | Alert users entering danger zones |
| AI Flood Prediction | ⬜ Pending | NASA/NOAA data integration for trend analysis |

---

## 10. References

[1] UNDRR. (2022). *Global Status of Multi-Hazard Warning Systems.* United Nations Office for Disaster Risk Reduction. https://www.undrr.org/

[2] World Bank. (2023). *Vietnam Disaster Risk Management Profile.* Washington, DC: World Bank Group.

[3] FEMA. (2021). *CERT Basic Training: Disaster Awareness.* Federal Emergency Management Agency. https://www.fema.gov/

[4] FEMA. (2022). *Community Emergency Response Team (CERT) Program Metrics.*

[5] Vietnam Ministry of Agriculture and Rural Development (MARD). (2023). *Báo cáo tổng kết công tác phòng chống thiên tai.* Hà Nội.

[6] Vietnam Ministry of Natural Resources and Environment (MONRE). (2024). *Báo cáo công tác phòng chống thiên tai và tìm kiếm cứu nạn.* Hà Nội.

[7] General Statistics Office of Vietnam (GSO). (2025). *Thiệt hại do thiên tai tại Việt Nam 2024.* Hà Nội. https://gso.gov.vn/

[8] VietnamNet & General Statistics Office. (2025). *Báo cáo thiệt hại kinh tế do lũ lụt.* Thiệt hại ước tính: 85.099 nghìn tỷ VND (~3.47 tỷ USD).

---

*Document prepared for EPICS 8th Iteration — V2 Submission*
*AquaGuard Team — 2026*
