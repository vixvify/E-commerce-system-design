##1. Problem Statement

สร้างระบบเว็บไซต์ขายสินค้าออนไลน์ ที่ให้ผู้ใช้สามารถ
- สมัครสมาชิก / ล็อกอิน
- เลือกดูสินค้า / ค้นหา
- ใส่สินค้าในตะกร้า / ชำระเงิน
- ฝั่งแอดมินสามารถเพิ่ม ลบ แก้ไขสินค้าได้

##2. Requirements Gathering	

Manager
Front-end
UI, product, cart, checkout, user profile

API Gateway / Load Balancer: กระจายคำขอไปยัง backend

Backend Services
แยก services แต่ละประเภท

- Catalog Service — จัดการสินค้า, category

Customer
- Search Service — full-text / keyword search
- Comment Service - reviews, comment
- Cart Service — cart, pricing, discounts
- Order Service — create order, order lifecycle
- Payment Service — payment gateway 
- User / Auth Service — user profile, auth, token
- Notification Service — email/SMS

Admin
- Admin Service — dashboard, product management

##3. Architecture Overview

- Frontend -> React/Next.js
- Backend -> Node.js (Express.js)
- Database -> MongoDB Atlas
- Cache -> Redis
- Authentication -> JWT
- Cloud -> AWS 

##4. System Flow

เข้าสู่ระบบด้วย Account ที่สมัคร
1. ลูกค้าเข้าสู่เว็บไซต์ E-commerce
2. ลูกค้าสร้างบัญชีใหม่ (POST/createUser)
3. ลูกค้าเข้าสู่ระบบด้วย Account ที่สร้าง (POST/login) -> ไปข้อที่ 8

เข้าสู่ระบบด้วย Email OTP
4. ลูกค้าเข้าสู่เว็บไซต์ E-commerce
5. ลูกค้ากรอก Email เพื่อยืนยันตัวตน
6. ระบบส่ง OTP ผ่าน Email
7. ลูกค้าเข้าสู่ระบบด้วย OTP ที่ส่งมาทาง Email -> ไปข้อที่ 8

8. ลูกค้าเรียกดูสินค้าผ่านหน้าแรก ดูตามหมวดหมู่สินค้า หรือการค้นหาด้วยการใส่คำค้น (GET/getData or GET/getData/:keyword)
9. ระบบแสดงรายการสินค้าพร้อมข้อมูล เช่น ชื่อ ราคา รูปภาพ จำนวนที่ขายได้ ชื่อร้านค้า ส่วนลด คะแนนรีวิว
10. ลูกค้าเลือกดูรายละเอียดสินค้าที่สนใจ (GET/getSingleData)
11. ลูกค้าเลือกจำนวนสินค้า
12. ลูกค้าเพิ่มสินค้าลงตะกร้า (local storage)
13. ระบบแสดงโปรโมชั่นที่สามารถใช้ได้
14. ลูกค้าใส่รหัสคูปอง
15. ลูกค้าเลือกใช้คะแนนสะสม (ถ้ามี)
16. ลูกค้าเลือกวิธีการชำระเงิน
17. ลูกค้ากรอกข้อมูลที่อยู่จัดส่งและเลือกวิธีการจัดส่ง
18. ระบบคำนวณค่าจัดส่งและแสดงผลรวมราคาทั้งหมด
19. ลูกค้ากรอกข้อมูลการชำระเงิน (JWT for token checked and POST/createOrder)
20. ระบบประสานงานกับระบบชำระเงินภายนอกเพื่อดำเนินการชำระเงิน (Payment Gateway)
21. ระบบแจ้งผลการชำระเงินให้ลูกค้าทราบ
22. ระบบส่งอีเมลยืนยันคำสั่งซื้อให้ลูกค้า
23. ระบบอัพเดตสถานะคำสั่งซื้อเป็น "ชำระเงินแล้ว" (PUT/updateStatus)
24. เมื่อได้รับการยืนยันการชำระเงิน, ระบบจะส่งคำสั่งซื้อไปยังคลังสินค้าเพื่อเตรียมจัดส่ง
25. ระบบจัดส่งสินค้าและอัพเดตสถานะคำสั่งซื้อเป็น "กำลังจัดส่ง" (PUT/updateStatus)
26. ลูกค้าสามารถติดตามสถานะการจัดส่งผ่านเว็บไซต์ (GET/getStatus)
27. ลูกค้าได้รับสินค้า
28. ระบบอัพเดตสถานะคำสั่งซื้อเป็น "จัดส่งสำเร็จ" (PUT/updateStatus)
29. ลูกค้าเขียนรีวิวและให้คะแนนสินค้า (POST/createReview)

##5. Database Design
##6. Component Design

Auth Service

- ลงทะเบียน / login / ออก token (JWT)
- Middleware ตรวจสอบ token ทุกครั้งที่เรียก API

Cart Service

- เก็บสินค้าชั่วคราวใน localStorage 
- sync กับ server เมื่อผู้ใช้กด checkout

Order Service

- ตรวจสอบสต็อก → คำนวณยอดรวม → เชื่อม Payment Gateway

##7. Scalability & Reliability

- Load Balancer: กระจายโหลดระหว่างหลาย server
- Cache (Redis): เก็บ product ที่ถูกเรียกบ่อยเพื่อลด query DB
- CDN: ให้ static assets โหลดเร็วทั่วโลก

##8. Security

- ใช้ HTTPS 
- รหัสผ่านเก็บด้วย bcrypt hashing
- ใช้ JWT สำหรับ Auth token
- ตั้งค่า Rate Limiting และ Input Validation
- ใช้ HttpOnly + Secure Cookie สำหรับ session 

##9. Trade-offs
