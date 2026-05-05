# AquaGuard EPICS V2 — Project Memory

> Cập nhật: 2026-05-04
> Trạng thái: ✅ Hoàn thành

---

## Trạng thái các file

| File | Trạng thái | Ghi chú |
|------|-----------|---------|
| features.md | ✅ Hoàn chỉnh | Spec tính năng đầy đủ (347 dòng) |
| spec.md | ✅ Hoàn thành | Presentation specification (22 slides, script framework) |
| benefit.md | ✅ Hoàn thành | Benefits methodology với đầy đủ citations |
| overallreport.md | ✅ Hoàn thành | Final consolidated report đầy đủ |
| memory.md | ✅ Hoàn thành | File tracking này |

---

## Quyết định quan trọng

- **features.md vs spec.md**: features.md là technical spec (mô tả tính năng hệ thống chi tiết), spec.md là presentation specification (đề cương slide cho buổi thuyết trình EPICS V2).
- **V2 scope**: Nâng cấp từ Firebase-only (V1) lên Node.js + Express + PostgreSQL + WebSocket. V2 giữ nguyên React frontend nhưng thay backend hoàn toàn.
- **Benefit claims từ V1**: 3 claims chính — 97% faster pre-alert, 45% faster rescue response, 85% faster post-flood analytics. Giữ nguyên methodology từ slide19_report.md, bổ sung chi tiết calculations và citations.
- **Format style**: Theo V1 (`AquaGuard_Epics8th_SlideReport.md`) — markdown headers, bảng biểu, citation footnotes [1]–[8], limitations section, dual-language awareness.
- **Không tạo PPTX**: Chỉ tạo documentation files (spec.md, benefit.md, overallreport.md). File PowerPoint không nằm trong scope V2 documentation.
- **business_report_web.html**: Business model page mới với white design, bổ sung Service Lifecycle (Pre/In/Post-Flood 3-phase), full responsive (900px, 640px, 380px breakpoints). File cũ `business-model.html` giữ lại với dark theme.

---

## V1 vs V2 So sánh

| Khía cạnh | V1 | V2 |
|-----------|----|----|
| Backend | Firebase only | Node.js + Express + PostgreSQL |
| Auth | Firebase Auth (Google/OTP) | JWT (phone+password) + Google OAuth |
| Real-time | Firestore listeners | WebSocket (live GPS tracking) |
| SOS Flow | Citizen submits, Rescuer views | Full E2E: assign → accept → track → complete → cancel |
| Family Safety | Basic list | Full family network + location sharing on map |
| Rescuer Teams | No sub-roles | Leader / Co-leader / Member hierarchy |
| Role Verification | None | Role passwords for admin/rescuer registration |
| Deployment | Vercel | Docker Compose |
| iOS App | Không có | SwiftUI MVP (AquaGuard-iOS-main/) |

---

## V1 Achievements (tham khảo)

- 500+ người được khảo sát
- 100+ người dùng thử nghiệm
- 18K+ reach trên social media
- Firebase MVP hoạt động với 3 role (Citizen, Rescuer, Admin)
- Live Flood Map với 5 sample locations (Đà Nẵng)

---

## Tiến độ — HOÀN THÀNH

### Bước 1: spec.md — Presentation Specification ✅
- [x] Đề cương slide-by-slide (22 slides, 6 sections)
- [x] Key messages per section
- [x] 5-minute script framework
- [x] Visual assets checklist
- [x] Ghi chú khi thuyết trình

### Bước 2: benefit.md — Benefits Methodology ✅
- [x] 3 benefits claims với methodology chi tiết (baseline, approach, calculation)
- [x] Derivation chains cho từng claim
- [x] Bracketed ranges và confidence levels
- [x] Citations đầy đủ (UNDRR, FEMA CERT, World Bank, NSO Vietnam, MONRE, MARD)
- [x] Limitations & assumptions
- [x] References section (8 citations)

### Bước 3: overallreport.md — Final Consolidated Report ✅
- [x] Problem statement & statistics (85.099T VND economic loss)
- [x] Existing solutions gap analysis
- [x] V1 achievements (500 surveyed, 100+ tested, 18K reach)
- [x] V2 architecture upgrade (comparison table, system diagram, API overview)
- [x] RBAC permission matrix
- [x] Features summary (Citizen, Rescuer, Admin, iOS)
- [x] Benefits & impact (linked từ benefit.md)
- [x] Business model (B2G + grants + venture)
- [x] Future plans (short/medium/long term + iOS roadmap)
- [x] References (8 citations)

### Bước 4: Cập nhật memory.md ✅
- [x] Đánh dấu tất cả files hoàn thành
- [x] Ghi nhận tất cả quyết định quan trọng

---

## Nguồn tham khảo chính

| File | Mô tả |
|------|-------|
| `features.md` | V2 feature specification đầy đủ |
| `AquaGuard_Documentation.md` | Web app technical documentation |
| `AquaGuard-iOS-main/README.md` | iOS app overview |
| `AquaGuard 2025/Epics 8th/V1/AquaGuard_Epics8th_SlideReport.md` | V1 format reference |
| `AquaGuard 2025/Epics 8th/V1/slide19_report.md` | V1 benefits verification chi tiết |
| `AquaGuard 2025/Epics 8th/V1/memory.md` | V1 execution memory |

---

## Các file đã tạo trong V2/

| File | Lines | Mô tả |
|------|-------|-------|
| `features.md` | ~347 | Technical feature specification (pre-existing) |
| `spec.md` | ~220 | Presentation specification (22 slides, script) |
| `benefit.md` | ~300 | Benefits methodology + citations |
| `overallreport.md` | ~380 | Final consolidated report |
| `memory.md` | ~130 | Project memory & tracking |
| `CLAUDE.md` | ~65 | Claude Code guidance (tạo riêng) |
| `business-model.html` | ~455 | Business model visual (dark theme, pre-existing) |
| `business_report_web.html` | ~1075 | Business model page (white design, Service Lifecycle, full responsive) |
| `business.txt` | ~40 | Business model source text |
| `survey-report.html` | ~700 | Interactive survey report (91 respondents, 24 questions, Chart.js) |
| `survey-report-simulated.html` | ~700 | Simulated demo version (adjusted regional distribution) |
| `surveydata.xlsx` | 91 rows | Source survey data (24 questions, EN/VI bilingual) |

---

## Thứ tự thực hiện

1. ✅ Tạo memory.md (2026-05-03)
2. ✅ Tạo spec.md (2026-05-03)
3. ✅ Tạo benefit.md (2026-05-03)
4. ✅ Tạo overallreport.md (2026-05-03)
5. ✅ Cập nhật memory.md final (2026-05-03)
