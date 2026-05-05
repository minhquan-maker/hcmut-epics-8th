# AquaGuard EPICS V2 — Presentation Specification

> Phiên bản: 1.0.0
> Cập nhật: 2026-05-03
> Thời lượng: 5 phút (300 giây)

---

## Mục tiêu & Đối tượng

### Đối tượng khán giả
- Giám khảo EPICS 8th
- Giảng viên hướng dẫn
- Sinh viên các nhóm khác

### Mục tiêu presentation
1. Trình bày bài toán lũ lụt tại Việt Nam và khoảng trống giải pháp hiện tại
2. Giới thiệu AquaGuard V2 như giải pháp nâng cấp toàn diện so với V1
3. Demo/Tóm tắt các tính năng chính cho 3 nhóm người dùng
4. Chứng minh impact qua benefits methodology
5. Định hướng tương lai và kế hoạch triển khai pilot

### Phong cách trình bày
- Ngôn ngữ: Tiếng Anh (EN) — phù hợp với giám khảo quốc tế
- Tone: Chuyên nghiệp, data-driven, có emotional hook ở phần mở đầu
- Slide design: Clean, ít text, nhiều visual/diagram

---

## Cấu trúc Slide (22 slides)

### SECTION 1: Opening (Slides 1–3) — ~30s

#### Slide 1: Title Slide
- **Title:** AquaGuard V2 — Real-time Flood Emergency Management Platform
- **Subtitle:** EPICS 8th Iteration — 2026
- **Team names**
- **Visual:** Background flood imagery (ảnh lũ lụt thực tế)

#### Slide 2: Problem Hook
- **Visual:** 1 slide với 2-3 con số gây sốc về lũ lụt Việt Nam
  - Tử vong/mất tích: [NSO Vietnam data]
  - Thiệt hại kinh tế: 85.099T VND (~3.47B USD) [VietnamNet/VNSO 2025]
  - % dân số chịu ảnh hưởng
- **Message:** "Lũ lụt không chờ đợi — tại sao hệ thống cứu hộ vẫn đang gọi điện?"

#### Slide 3: The Gap
- **Visual:** So sánh nhanh 3 giải pháp hiện tại (Zalo, Viettel SOS, Gov platforms)
- **Message:** Thiếu real-time structured data + rescue visibility + family tracking

---

### SECTION 2: Our Solution (Slides 4–7) — ~60s

#### Slide 4: AquaGuard Overview
- **Visual:** Sơ đồ kiến trúc hệ thống V2
  - 3 user roles (Citizen / Rescuer / Admin)
  - Backend: Node.js + Express + PostgreSQL + WebSocket
  - Frontend: React + Vite + Leaflet + TailwindCSS
  - Mobile: SwiftUI (iOS)
- **Message:** "Một nền tảng kết nối tất cả bên liên quan trong thảm họa lũ lụt"

#### Slide 5: V1 vs V2 — What's New
- **Visual:** Table comparison V1 → V2
- **Key improvements:**
  - Backend: Firebase → Node.js + PostgreSQL
  - Auth: Firebase Auth → JWT + Google OAuth
  - Real-time: Firestore → WebSocket (live GPS tracking)
  - SOS Flow: Submit-only → Full E2E (assign/accept/track/complete/cancel)
  - Rescuer Teams: No hierarchy → Leader/Co-leader/Member
  - iOS App: None → SwiftUI MVP
- **Message:** "V1 là proof-of-concept. V2 là production-ready architecture."

#### Slide 6: System Architecture
- **Visual:** Full architecture diagram
  - Frontend (React Web + SwiftUI iOS)
  - API Gateway / Load Balancer
  - Node.js + Express REST API
  - PostgreSQL Database
  - WebSocket Server (Socket.io)
  - Firebase (Auth fallback + Cloudinary for SOS photos)
- **Message:** "Clean separation of concerns, ready for Docker deployment"

#### Slide 7: RBAC System
- **Visual:** Role hierarchy diagram
  - Admin (highest) → Rescuer → Citizen (lowest)
- **Key points:**
  - 3 roles, each with specific permissions
  - Role password protection for admin/rescuer registration
  - Frontend guards + backend middleware

---

### SECTION 3: Features (Slides 8–14) — ~90s

#### Slide 8: Features Overview
- **Visual:** 3-column layout giới thiệu 3 roles
- **Message:** "Mỗi người dùng có trải nghiệm được thiết kế riêng"

#### Slide 9: Citizen Features
- **Visual:** Screenshot/mockup của Citizen dashboard
- **Key features:**
  - Safety status toggle (Safe / Danger / Injured)
  - Family Safety Board (real-time status of relatives)
  - SOS Request (up to 5 photos, GPS, urgency levels)
  - Live tracking of rescuer approaching
  - Interactive flood map with severity zones
  - Safety protocols & emergency contacts

#### Slide 10: Rescuer Features
- **Visual:** Screenshot/mockup của Rescuer mission view
- **Key features:**
  - Mission queue (all pending SOS requests)
  - Team-based missions (Leader/Co-leader/Member hierarchy)
  - Accept/Complete/Cancel SOS
  - Live GPS sharing with citizens
  - Team management (invite, promote, kick, disband)
  - Live flood map with SOS markers

#### Slide 11: Admin Features
- **Visual:** Screenshot/mockup của Admin analytics dashboard
- **Key features:**
  - User management (role reassignment)
  - SOS assignment to rescue groups
  - System analytics (user growth, SOS trends, rescue performance)
  - Flood map editor (CRUD flood zones)
  - Full system visibility

#### Slide 12: End-to-End Rescue Flow
- **Visual:** Flowchart step-by-step
  1. Citizen sends SOS (location + description + photos)
  2. Admin assigns to rescue group OR Rescuer self-assigns
  3. Rescuer accepts → status: pending → in_progress
  4. WebSocket: live GPS tracking (rescuer → citizen)
  5. Rescuer completes → status: resolved
  6. Data saved to analytics
- **SOS States:** pending → assigned → in_progress → resolved / cancelled

#### Slide 13: iOS App Preview
- **Visual:** 5 screenshot grid (Home, Map, Report, Rescue, Safety)
- **Key points:**
  - Native SwiftUI for iOS 26.0+
  - MapKit + CoreLocation integration
  - 4-level severity pins (Critical/Severe/Moderate/Safe)
  - Offline safety guides
  - One-tap emergency SMS

#### Slide 14: Security & Scalability
- **Visual:** Security + deployment diagram
- **Key points:**
  - JWT authentication with refresh tokens
  - RBAC middleware on every protected route
  - WebSocket authentication
  - Docker Compose deployment
  - PostgreSQL for structured relational data

---

### SECTION 4: Benefits & Impact (Slides 15–17) — ~45s

#### Slide 15: Benefits Overview
- **Visual:** 3 big numbers highlighted
  - **97% faster** pre-flood alerts
  - **45% faster** rescue response (~20 min saved)
  - **85% faster** post-flood analytics

#### Slide 16: Methodology
- **Visual:** Methodology breakdown table
  | Benefit | Baseline | AquaGuard | Improvement |
  |---------|----------|-----------|-------------|
  | Pre-alert | 45-90 min (gov broadcast) | <2 min (GPS push) | ~97% faster |
  | Rescue response | 35-55 min (traditional) | 15-25 min (GPS direct) | ~45% faster |
  | Analytics | 14 days (manual) | 2 days (automated) | ~85% faster |
- **Sources:** UNDRR 2022, FEMA CERT studies, World Bank data

#### Slide 17: Research Validation
- **Visual:** Key statistics
  - 500+ người được khảo sát
  - 100+ người dùng thử nghiệm
  - 18K+ reach trên social media
- **Message:** "Our claims are backed by real user research"

---

### SECTION 5: Business & Future (Slides 18–20) — ~30s

#### Slide 18: Business Model
- **Visual:** 3 revenue streams
  1. **B2G:** Government contracts (Bộ Nông nghiệp, UBND các tỉnh)
  2. **Grants:** Disaster relief funding, international development funds
  3. **Venture:** Social impact investment

#### Slide 19: Future Plans
- **Visual:** Roadmap timeline
  - **Short-term:** Pilot deployment in 1-2 provinces (Đà Nẵng, Quảng Nam)
  - **Medium-term:** Integration with government emergency systems
  - **Long-term:** AI-powered flood prediction (NASA/NOAA data), expansion to SE Asia

#### Slide 20: iOS & Mobile Roadmap
- **Visual:** iOS app roadmap
  - ✅ MVP: UI/UX, MapKit, CoreLocation, Reporting
  - 🔄 Backend integration (Firebase/Supabase)
  - ⬜ Offline mode (local database)
  - ⬜ Push notifications (danger zone alerts)
  - ⬜ AI flood prediction

---

### SECTION 6: Closing (Slides 21–22) — ~15s

#### Slide 21: Demo Request
- **Visual:** QR code hoặc link demo (nếu có)
- **Message:** "Xem AquaGuard V2 hoạt động"

#### Slide 22: Thank You
- **Title:** Cảm ơn / Thank You
- **Team:** Tên thành viên + liên hệ
- **QR code:** GitHub repo / Demo link
- **Visual:** Closing visual — community resilience imagery

---

## Script Framework (5 phút)

### Phút 1: Opening & Problem (0:00 – 1:00)
- **[0:00–0:15]** Xin chào, giới thiệu team và project. Dẫn dắt bằng con số thảm họa lũ lụt.
- **[0:15–0:45]** Slide 2–3: Trình bày problem statement. Tại sao giải pháp hiện tại không đủ?
- **[0:45–1:00]** Chuyển: "Chúng tôi đã xây dựng AquaGuard để giải quyết vấn đề này."

### Phút 2: Solution Overview (1:00 – 2:00)
- **[1:00–1:30]** Slides 4–6: Giới thiệu AquaGuard V2 — architecture, V1 vs V2 comparison, tech stack.
- **[1:30–2:00]** Slide 7: RBAC system. Tại sao phân quyền quan trọng trong emergency management.

### Phút 3: Features (2:00 – 3:30)
- **[2:00–2:30]** Slide 9: Citizen features — đây là người dùng cuối chịu ảnh hưởng trực tiếp.
- **[2:30–3:00]** Slides 10–11: Rescuer và Admin — phối hợp cứu hộ hiệu quả.
- **[3:00–3:30]** Slide 12: End-to-end flow. Đây là điểm khác biệt chính so với V1.

### Phút 4: Impact & Business (3:30 – 4:30)
- **[3:30–4:00]** Slides 15–16: Benefits methodology. 3 con số chính. Tất cả có nguồn trích dẫn.
- **[4:00–4:30]** Slides 18–19: Business model và future plans. Định hướng triển khai thực tế.

### Phút 5: Closing (4:30 – 5:00)
- **[4:30–4:45]** Slide 20: iOS app. Demo available.
- **[4:45–5:00]** Slides 21–22: Cảm ơn, Q&A.

---

## Visual Assets Checklist

- [ ] Background: Flood imagery (thật, không stock photo quá generic)
- [ ] Architecture diagram (Slide 4, 6)
- [ ] Role hierarchy diagram (Slide 7)
- [ ] Screenshot/mockup: Citizen dashboard (Slide 9)
- [ ] Screenshot/mockup: Rescuer mission view (Slide 10)
- [ ] Screenshot/mockup: Admin analytics (Slide 11)
- [ ] Rescue flow flowchart (Slide 12)
- [ ] iOS 5-screenshot grid (Slide 13)
- [ ] Benefits comparison table visual (Slide 16)
- [ ] Research stats visual (Slide 17)
- [ ] Business model 3-column (Slide 18)
- [ ] Roadmap timeline (Slide 19)
- [ ] QR code cho demo link (Slide 21)
- [ ] Team photo / logos (Slide 22)

---

## Ghi chú khi thuyết trình

1. **Không đọc slide** — slide chỉ là visual anchor, nội dung chính ở script
2. **Transition rõ ràng** — mỗi section nên có 1 câu chuyển ("Now let me show you how it works...")
3. **Emotional hook ở phút 1** — người nghe cần thấy vấn đề là THỰC, không phải abstract
4. **Q&A preparation** — chuẩn bị cho các câu hỏi về: data privacy, scalability, deployment cost, government partnership
