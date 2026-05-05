# AquaGuard — Feature Documentation

> Tài liệu mô tả chi tiết tính năng hệ thống AquaGuard — Flood Emergency Management Platform.
> Cập nhật: 2026-05-03

---

## Mục lục

1. [Tổng quan hệ thống](#1-tổng-quan-hệ-thống)
2. [Tính năng chung (Shared)](#2-tính-năng-chung-shared)
3. [Citizen — Công dân](#3-citizen--công-dân)
4. [Rescuer — Đội cứu hộ](#4-rescuer--đội-cứu-hộ)
5. [Admin — Quản trị viên](#5-admin--quản-trị-viên)
6. [Quy trình cứu hộ End-to-End](#6-quy-trình-cứu-hộ-end-to-end)

---

## 1. Tổng quan hệ thống

AquaGuard là nền tảng quản lý khẩn cấp lũ lụt, kết nối **người dân (Citizen)**, **đội cứu hộ (Rescuer)** và **quản trị viên (Admin)** thông qua một hệ thống web app real-time.

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React + Vite, Leaflet (map), WebSocket |
| Backend | Node.js + Express, PostgreSQL |
| Auth | JWT (phone + password), Google OAuth |
| Storage | Cloudinary (ảnh SOS) |
| Deploy | Docker Compose |

### Kiến trúc phân quyền (RBAC)

Hệ thống sử dụng **3 role chính**, mỗi role có navigation riêng biệt:

```
Admin (cao nhất)
  └── Rescuer
        └── Citizen (thấp nhất)
```

- **Middleware backend**: `authMiddleware` (JWT verify), `requireAdmin`, `requireRoles([...])`.
- **Frontend guard**: `canAccessPage(role, page)` — redirect nếu không có quyền.

---

## 2. Tính năng chung (Shared)

Các tính năng sau khả dụng cho **tất cả role**:

### 2.1 Authentication & Registration

| Tính năng | Mô tả |
|-----------|-------|
| **Đăng nhập bằng SĐT + mật khẩu** | Nhập số điện thoại VN (format +84), mật khẩu → nhận JWT token |
| **Đăng nhập bằng Google** | OAuth qua Firebase → auto-create tài khoản nếu chưa có |
| **Đăng ký tài khoản** | Gồm: họ tên, SĐT, mật khẩu, chọn role (citizen/rescuer/admin), giới tính, ngày sinh |
| **Chọn role khi đăng nhập Google lần đầu** | Modal `RoleSelectionModal` hiện ra nếu user chưa có role |
| **Quên mật khẩu** | Flow: nhập SĐT → nhận OTP → xác thực → đặt mật khẩu mới |

> ⚠️ Đăng ký role `admin` hoặc `rescuer` yêu cầu nhập **mã xác thực role** riêng (role password).

### 2.2 Profile Settings

| Tính năng | Mô tả |
|-----------|-------|
| **Chỉnh sửa thông tin cá nhân** | Tên, SĐT, địa chỉ, giới tính, ngày sinh |
| **Upload avatar** | Ảnh đại diện cá nhân |
| **Đổi mật khẩu** | Nhập mật khẩu cũ + mới |

### 2.3 Appearance & Language

| Tính năng | Mô tả |
|-----------|-------|
| **Chế độ giao diện** | Light / Dark / System (auto detect) |
| **Ngôn ngữ** | Tiếng Anh (EN) / Tiếng Việt (VI), lưu persistent |

### 2.4 News & Alerts

| Tính năng | Mô tả |
|-----------|-------|
| **Tin tức lũ lụt** | Feed tin tức cảnh báo, phân loại theo mức độ (danger, warning, info) |

### 2.5 About Us

| Tính năng | Mô tả |
|-----------|-------|
| **Giới thiệu hệ thống** | Thông tin về AquaGuard, team, mục tiêu |

### 2.6 AI ChatBot

| Tính năng | Mô tả |
|-----------|-------|
| **Trợ lý AI** | Chatbot hỗ trợ người dùng, nằm ở góc dưới bên phải, có thể toggle mở/đóng |

---

## 3. Citizen — Công dân

> **Mô tả**: Người dùng cuối cùng trong hệ thống. Citizen là người gửi yêu cầu cứu hộ khi gặp lũ lụt, theo dõi gia đình và xem bản đồ lũ.

### 3.1 Dashboard (Trang chính)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Welcome header** | Hiển thị tên user + tên thành phố (trích xuất từ địa chỉ trong database) |
| **Safety status toggle** | 3 nút nhanh: Safe ✅ / Danger ❌ / Injured 🩹 — cập nhật trạng thái an toàn real-time cho gia đình thấy |
| **Active SOS Banner** | Nếu có SOS đang active (pending/in_progress), hiển thị banner nổi bật với trạng thái, nút xem chi tiết |
| **Quick Actions** | Các nút hành động nhanh: Gửi SOS, Xem bản đồ, Kiểm tra gia đình, Xem tin tức |
| **Family Safety Board** | Widget hiển thị danh sách người thân + trạng thái an toàn của họ (safe/danger/injured/unknown) |
| **Pending Family Invites** | Thông báo lời mời kết nối gia đình đang chờ xử lý |
| **SOS Request History** | Lịch sử các yêu cầu SOS đã gửi, xem trạng thái từng request |

### 3.2 Live Flood Map (Bản đồ lũ lụt)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Bản đồ real-time** | Leaflet map hiển thị vùng lũ, mực nước, điểm nguy hiểm |
| **SOS markers** | Các điểm SOS đang active trên bản đồ |
| **Family on map** | Xem vị trí người thân trên bản đồ (nếu họ đã chia sẻ location) |
| **Right panel — Citizen** | Panel bên phải hiển thị: Family Check (danh sách + trạng thái gia đình), Report Issue form |
| **Search** | Tìm kiếm khu vực, điểm cứu hộ trên bản đồ |
| **Map legend** | Chú giải các biểu tượng trên bản đồ |

### 3.3 SOS Request (Yêu cầu cứu hộ)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Tạo SOS mới** | Form gồm: vị trí (auto-detect GPS hoặc nhập), mô tả tình huống, mức độ khẩn cấp (low/medium/high/critical), upload tối đa 5 ảnh |
| **Upload ảnh** | Ảnh được upload lên Cloudinary, lưu URL vào database |
| **Mức độ khẩn cấp** | 4 level: Low (thấp), Medium (trung bình), High (cao), Critical (nguy kịch) |
| **Xem SOS đã gửi** | Danh sách SOS của mình, filter theo trạng thái |
| **Live tracking** | Khi SOS được rescuer accept → citizen có thể xem vị trí rescuer trên map real-time qua WebSocket |
| **Trạng thái SOS** | `pending` → `in_progress` → `resolved` (hoặc `cancelled`) |

### 3.4 Family Safety (An toàn gia đình)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Tìm & kết nối người thân** | Tìm kiếm theo SĐT, gửi lời mời kết nối |
| **Chấp nhận/Từ chối lời mời** | Quản lý các lời mời kết nối gia đình |
| **Cập nhật trạng thái an toàn** | Chọn: Safe / Danger / Injured / Unknown + ghi chú sức khỏe |
| **Xem trạng thái gia đình** | Danh sách người thân + trạng thái an toàn, SĐT, địa chỉ |
| **Xem gia đình trên map** | Hiển thị vị trí người thân trên Live Flood Map |
| **Xóa kết nối** | Hủy liên kết với người thân |

### 3.5 Safety Protocols (Quy trình an toàn)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Hướng dẫn an toàn lũ lụt** | Các bước cần làm trước/trong/sau lũ |
| **Liên hệ khẩn cấp** | Danh sách SĐT cứu hộ, bệnh viện, cảnh sát |

---

## 4. Rescuer — Đội cứu hộ

> **Mô tả**: Người cứu hộ hoạt động theo **nhóm (team)**. Để nhận nhiệm vụ, rescuer phải thuộc một nhóm và giữ vai trò Leader hoặc Co-leader.

### 4.1 Rescue Requests (Hàng đợi SOS)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Xem tất cả SOS** | Danh sách toàn bộ SOS requests từ citizens, sắp xếp theo thời gian (mới nhất trước) |
| **Chi tiết SOS** | Click vào request để xem: tên, SĐT, địa chỉ, mô tả, mức khẩn cấp, ảnh đính kèm, vị trí GPS |
| **Accept SOS** | Nhận nhiệm vụ cho team — **chỉ Leader/Co-leader** mới có nút Accept |
| **Đánh dấu đã xem** | Hệ thống track request nào là "mới" (chưa xem, trong 30 phút gần nhất) |
| **Filter tabs** | Active SOS / Team Missions / Completed — filter theo trạng thái |

> ⚠️ **Điều kiện Accept**: Rescuer PHẢI (1) có nhóm cứu hộ active, (2) là Leader hoặc Co-leader trong nhóm đó. Member thường chỉ xem, không thao tác.

### 4.2 Team Missions (Nhiệm vụ nhóm)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **No-Team Prompt** | Nếu rescuer chưa có nhóm → hiển thị full-page prompt "Tham gia hoặc tạo nhóm cứu hộ" |
| **Not-Leader Banner** | Nếu là member (không phải leader/co-leader) → banner cảnh báo "Bạn không thể nhận nhiệm vụ" |
| **Stats overview** | 3 thẻ thống kê: Active SOS, Team Missions, Completed |
| **Tab navigation** | Active (SOS chờ) / Team Missions (đang thực hiện) / Completed (đã xong) |
| **Xem nhiệm vụ nhóm** | Tất cả thành viên trong nhóm thấy **cùng dữ liệu** (fetch từ `GET /api/sos/team`) |
| **Complete mission** | Đánh dấu nhiệm vụ hoàn thành — chỉ Leader/Co-leader |
| **Cancel mission** | Hủy/trả lại nhiệm vụ — chỉ Leader/Co-leader |
| **Live tracking** | Xem vị trí trên map khi đang thực hiện nhiệm vụ, chia sẻ GPS real-time |

### 4.3 Live Flood Map (Bản đồ — Rescuer view)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Bản đồ real-time** | Giống citizen nhưng **không có** Family features |
| **SOS markers** | Xem các điểm SOS active trên bản đồ |
| **Right panel — Rescuer** | Panel bên phải hiển thị: Team info, Rescue status, Report Issue |

### 4.4 Rescuer Team (Quản lý đội)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Tạo nhóm mới** | Nhập tên nhóm, mô tả → người tạo tự động trở thành **Leader** |
| **Mời thành viên** | Tìm rescuer theo SĐT → gửi lời mời (chỉ Leader/Co-leader) |
| **Chấp nhận/Từ chối lời mời** | Rescuer nhận được invite → accept hoặc decline |
| **Xem danh sách thành viên** | Tên, SĐT, vai trò (Leader/Co-leader/Member), trạng thái |
| **Promote thành viên** | Nâng cấp Member → Co-leader (chỉ Leader) |
| **Xóa thành viên** | Kick member ra khỏi nhóm (Leader/Co-leader) |
| **Rời nhóm** | Member tự rời nhóm (Leader không thể rời, phải disband) |
| **Giải tán nhóm** | Xóa toàn bộ nhóm (chỉ Leader) |
| **Chỉnh sửa thông tin nhóm** | Đổi tên, mô tả nhóm (Leader/Co-leader) |
| **Thống kê nhóm** | Tổng thành viên, nhiệm vụ đã hoàn thành, v.v. |

#### Sub-roles trong nhóm:

| Sub-role | Quyền hạn |
|----------|----------|
| **Leader** | Toàn quyền: accept SOS, complete, cancel, invite, promote, kick, edit group, disband |
| **Co-leader** | Accept SOS, complete, cancel, invite, kick (không thể promote hoặc disband) |
| **Member** | Chỉ xem danh sách nhiệm vụ và thông tin nhóm, không thể thao tác SOS |

### 4.5 Settings (Rescuer)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Profile** | Chỉnh sửa thông tin cá nhân (giống shared) |
| **Appearance** | Light/Dark/System |
| **Language** | EN/VI |
| ~~Family tab~~ | **Không có** — bị ẩn đối với role rescuer |

---

## 5. Admin — Quản trị viên

> **Mô tả**: Quản trị viên có quyền cao nhất trong hệ thống. Quản lý user, phân công SOS cho nhóm cứu hộ, xem analytics toàn hệ thống.

### 5.1 Admin Dashboard (Bảng điều khiển)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Overview stats** | 4 thẻ thống kê: Total Users, Rescuers, Pending SOS, Active SOS |
| **Quick SOS summary** | Bảng tóm tắt SOS: active, pending, resolved counts |
| **Tab navigation** | Overview / User Management / Rescue Teams / Flood Map |

### 5.2 User Management (Quản lý người dùng)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Danh sách tất cả users** | Bảng hiển thị: tên, SĐT, role, ngày tạo |
| **Phân loại theo role** | Tab Citizens / Rescuers / Admins |
| **Thay đổi role** | Dropdown đổi role user: citizen ↔ rescuer ↔ admin |
| **Xem chi tiết user** | Click để xem thông tin đầy đủ |

### 5.3 SOS Management (Quản lý yêu cầu cứu hộ)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Xem tất cả SOS** | Danh sách toàn bộ SOS requests, filter theo status/urgency |
| **Chi tiết SOS** | Xem đầy đủ thông tin: citizen info, vị trí, mô tả, ảnh, timeline trạng thái |
| **Assign SOS cho nhóm** | Chọn rescue group → leader tự động trở thành `assigned_to` |
| **Theo dõi tiến trình** | Xem trạng thái mission đang được xử lý |

### 5.4 Rescue Teams Management

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Xem tất cả nhóm cứu hộ** | Danh sách groups, thành viên, leader, trạng thái |
| **Xem chi tiết nhóm** | Danh sách members, thống kê mission |

### 5.5 Flood Map Editor

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Admin Flood Map** | Bản đồ với quyền chỉnh sửa vùng lũ |
| **Quản lý flood zones** | Thêm/sửa/xóa vùng ngập lụt trên map |

### 5.6 System Analytics

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **User growth** | Biểu đồ tăng trưởng người dùng theo thời gian |
| **SOS trends** | Biểu đồ xu hướng SOS requests (theo ngày/tuần/tháng) |
| **Rescue performance** | Thống kê hiệu suất cứu hộ: thời gian phản hồi trung bình, nhanh nhất, chậm nhất |
| **User analytics** | Phân bổ user theo role, trạng thái hoạt động |

### 5.7 Settings (Admin)

| Tính năng | Mô tả chi tiết |
|-----------|----------------|
| **Profile** | Chỉnh sửa thông tin cá nhân |
| **Appearance** | Light/Dark/System |
| **Language** | EN/VI |
| ~~Family tab~~ | **Không có** — bị ẩn đối với role admin |

---

## 6. Quy trình cứu hộ End-to-End

### Flow chính:

```
🌊 Lũ lụt xảy ra
    │
    ▼
🆘 Citizen gửi SOS
    │  (location + description + urgency + photos)
    │
    ▼
📋 Admin assign cho Rescue Group   ←── hoặc ──→  ✋ Rescuer tự accept từ queue
    │  (chọn group → leader auto-assigned)              (Leader/Co-leader only)
    │
    ▼
✋ Rescuer Leader/Co-leader nhận nhiệm vụ
    │  (status: pending → in_progress)
    │
    ▼
🗺️ Live Tracking bắt đầu
    │  (WebSocket: rescuer share GPS → citizen xem real-time)
    │
    ▼
✅ Rescuer đánh dấu hoàn thành
    │  (status: in_progress → resolved)
    │
    ▼
📊 Data lưu vào analytics
```

### Trạng thái SOS:

| Status | Mô tả | Ai thay đổi |
|--------|-------|-------------|
| `pending` | Mới tạo, chờ tiếp nhận | Citizen (tạo SOS) |
| `assigned` | Admin đã phân công cho group | Admin (assign) |
| `in_progress` | Rescuer đã accept và đang xử lý | Rescuer (accept) |
| `resolved` | Đã cứu hộ thành công | Rescuer (complete) |
| `cancelled` | Bị hủy/trả lại | Rescuer (cancel) |

### API Endpoints theo flow:

| Step | Method | Endpoint | Role |
|------|--------|----------|------|
| Citizen tạo SOS | `POST` | `/api/sos` | Citizen |
| Xem SOS của mình | `GET` | `/api/sos/my` | Citizen |
| Xem tất cả SOS | `GET` | `/api/sos/all` | All |
| Xem SOS của team | `GET` | `/api/sos/team` | Rescuer |
| Admin assign group | `PUT` | `/api/sos/:id/assign` | Admin |
| Rescuer accept | `PUT` | `/api/sos/:id/accept` | Rescuer |
| Rescuer complete | `PUT` | `/api/sos/:id/complete` | Rescuer |
| Rescuer cancel | `PUT` | `/api/sos/:id/cancel` | Rescuer |

---

> 📄 **File liên quan**: `docs/rbac-diagram.html` — Sơ đồ trực quan RBAC, flow diagrams, permission matrix.
