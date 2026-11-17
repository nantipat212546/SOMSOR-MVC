# SOMSOR — ระบบแจ้งซ่อมภายในมหาวิทยาลัย

เว็บแอปภาษาไทยตามเอกสารออกแบบ: Node.js + Express + SQLite + Vanilla JavaScript

## เริ่มต้นใช้งาน

ต้องมี Node.js 20 ขึ้นไป (แนะนำ Node.js 22 LTS)

```bash
npm install
```

PowerShell (Windows) สำหรับข้อมูลทดลอง:

```powershell
$env:SEED_DEMO="true"
$env:JWT_SECRET="replace-with-a-long-random-secret"
npm start
```

Command Prompt (Windows):

```bat
set SEED_DEMO=true
set JWT_SECRET=replace-with-a-long-random-secret
npm start
```

เปิด http://localhost:3000

บัญชีทดลอง (เฉพาะเมื่อ SEED_DEMO=true ตอนเริ่มครั้งแรก):

| Role | Email | Password |
|---|---|---|
| Admin | admin@somsor.demo | Admin12345! |
| Student | student@somsor.demo | Student12345! |
| Technician | tech@somsor.demo | Tech12345! |

**สำคัญ:** บัญชีตัวอย่างมีรหัสผ่านที่เผยแพร่ไว้สำหรับทดสอบเท่านั้น ห้ามเปิด SEED_DEMO บนระบบจริง ควรกำหนด JWT_SECRET ผ่าน environment และใช้ HTTPS ก่อนนำขึ้น production

## ตารางฐานข้อมูล

- `users` นักศึกษา ช่าง และผู้ดูแล (password เก็บเป็น bcrypt hash)
- `categories` ประเภทปัญหา
- `technicians` ข้อมูลและสถานะช่าง
- `requests` รายการแจ้งซ่อม
- `assignments` ประวัติมอบหมายงาน

ฐานข้อมูลสร้างอัตโนมัติที่ `somsor.db` (SQLite) เพื่อเริ่มได้โดยไม่ต้องติดตั้ง MySQL; โครงสร้าง 5 ตารางอิงจากแบบ MySQL ใน PDF

## API

| Method | Path | Role | Success |
|---|---|---|---|
| POST | /api/v1/auth/register | Public | 201 |
| POST | /api/v1/auth/login | Public | 200 |
| GET | /api/v1/auth/me | Authenticated | 200 |
| GET | /api/v1/dashboard | Authenticated | 200 |
| GET | /api/v1/categories | Authenticated | 200 |
| POST | /api/v1/categories | Admin | 201 |
| PUT | /api/v1/categories/:id | Admin | 200 |
| GET | /api/v1/requests | All authenticated, scoped by role | 200 |
| GET | /api/v1/requests/me | Student | 200 |
| GET | /api/v1/requests/:id | Authorized owner/assigned technician/admin | 200 |
| POST | /api/v1/requests | Student | 201 |
| POST | /api/v1/requests/:id/assign | Admin | 200 |
| PATCH | /api/v1/requests/:id/status | Assigned Technician/Admin | 200 |
| DELETE | /api/v1/requests/:id | Owner/Admin, pending only (soft cancel) | 204 |
| GET | /api/v1/technicians | Admin | 200 |

Authorization: `Bearer <JWT>` (8 ชั่วโมง)

## แนวทางที่ปรับปรุง

- จำกัดสิทธิ์ตามบทบาทและความเป็นเจ้าของรายการใน backend
- ตรวจสอบ input, hash รหัสผ่าน, rate limit ที่ auth endpoints
- ป้องกัน SQL injection ด้วย parameterized queries
- ใช้ soft cancellation เพื่อเก็บประวัติรายการ
- ป้องกันการปิดงานก่อนเริ่มและมอบหมายงานให้ช่างที่ busy
- แสดงผล responsive สำหรับมือถือ

## ขอบเขตปัจจุบัน

เป็นโปรเจกต์ต้นแบบสำหรับเรียนและสาธิต ยังไม่มีระบบอัปโหลดรูป, แจ้งเตือนอีเมล, การเพิ่มช่างจากหน้า Admin, audit log, automated tests และระบบจัดการ session แบบ production. สถานะช่างเป็น available/busy อย่างง่าย; หากต้องรองรับช่างหลายงานพร้อมกัน ควรเปลี่ยนเป็นระบบ capacity และประวัติการมอบหมายแบบละเอียด
