# เปิดใช้งาน Neon บน Vercel

รุ่นนี้เปลี่ยนฐานข้อมูลและระบบล็อกอินทั้งหมดเป็น Neon + Vercel API แล้ว จึงไม่ต้องตั้ง Firebase สำหรับโค้ดรุ่นนี้ แต่ยังไม่มีการแก้ไขเว็บจริงหรือย้ายข้อมูลจริงให้โดยอัตโนมัติ

## 1. อัปเดต GitHub

สำรอง repository และข้อมูลเดิมก่อน จากนั้นแทน source เดิมด้วยไฟล์ภายในโฟลเดอร์ tickblawealth ของ ZIP นี้ รวม api, server, database, src, scripts, public และ package-lock.json ห้ามอัปโหลดเพียง ZIP หรือ dist

ลบไฟล์เก่าที่รุ่นนี้เลิกใช้: firebase.json, firestore.rules, firestore.indexes.json, vitest.rules.config.ts, tests/firestore.rules.test.ts, src/lib/firebase.ts และ scripts/*.mjs รุ่น Firebase ปรับ .github/workflows/ci.yml ให้ตรงรุ่นนี้

## 2. สร้างฐานข้อมูล Neon และใส่ตัวแปร

ใน Neon สร้างโปรเจกต์/ฐานข้อมูล แล้วนำ Connection string ที่เปิด SSL มาใส่ใน Vercel > Project > Settings > Environment Variables

| ชื่อ | ค่า |
| --- | --- |
| DATABASE_URL | Connection string ของ Neon สำหรับ server เช่น postgresql://…?sslmode=require |
| APP_ORIGIN | https://tickblawealth.vercel.app (ไม่มี / ท้าย) หรือ origin จริงที่ใช้ |
| VITE_DEMO_MODE | false |

DATABASE_URL ต้องเป็น server environment เท่านั้น ห้ามเปลี่ยนชื่อเป็น VITE_DATABASE_URL และห้ามใส่ใน GitHub ไม่ต้องส่ง connection string ในแชต

หากใช้ Custom Domain ให้เปลี่ยน APP_ORIGIN ให้ตรงโดเมนที่ลูกค้าเปิดใช้งาน โดเมนของ deployment ที่ Vercel ใส่ใน VERCEL_URL ได้รับอนุญาตเพิ่มโดยอัตโนมัติ Preview และ Production ควรใช้คนละฐานข้อมูล

## 3. สร้างตารางและบัญชี admin ครั้งแรก

นำเนื้อหา `database/001-schema.sql` ไปรันใน Neon SQL Editor ของฐานข้อมูลเป้าหมาย หรือรัน `npm run db:migrate` จาก terminal ที่ตั้ง DATABASE_URL แล้ว

จาก terminal ภายในโฟลเดอร์โปรเจกต์:

```bash
npm ci
npm run admin:create -- admin
```

ตั้ง DATABASE_URL ใน environment ของ terminal ก่อน สคริปต์จะขอรหัสผ่านแบบซ่อนตัวอักษร ให้กรอกรหัสผ่านที่ติ๊กกำหนดในแชต จากนั้นจะเก็บเฉพาะ salted password hash ใน Neon ไม่มีรหัสผ่านเริ่มต้นที่เปิดให้ใช้ทั่วทุก deployment และไม่มี public setup endpoint

สคริปต์จะไม่ทับรหัสผ่านของบัญชีที่มีอยู่แล้ว ค่าเริ่มต้นรองรับ 7 ตัวอักษรตามคำขอเดิม เมื่อเปลี่ยนรหัสผ่านผ่านหลังบ้านต้องมีอย่างน้อย 8 ตัวอักษร

หลังเข้าสู่ /login ด้วยชื่อ admin เปลี่ยนรหัสผ่านได้ที่ /admin/account ต้องกรอกรหัสผ่านเดิม และจะออกจากระบบทุกอุปกรณ์หลังเปลี่ยนสำเร็จ

## 4. Deploy

Root Directory คือโฟลเดอร์ที่มี package.json, api และ vercel.json อยู่ด้วยกัน Framework เป็น Vite, Build คือ npm run build, Output คือ dist โฟลเดอร์ api ต้องอยู่ใน source เพื่อให้ Vercel สร้าง Node Functions

Redeploy หลังเพิ่ม environment variables แล้วตรวจ /api/auth ได้ JSON `{ "user": null }` เมื่อยังไม่ล็อกอิน จากนั้นตรวจ /irr.html, /login และส่งแบบสำรวจทดสอบที่ไม่ใช่ข้อมูลลูกค้าจริง ตรวจว่ารายการปรากฏในหลังบ้านและหลังออกจากระบบไม่สามารถอ่านได้

## เพิ่มลิงก์ / หน้าใหม่

1. เปิด /admin/links
2. ชื่อลิงก์: คำนวณ IRR
3. URL: /irr.html
4. เว้นเนื้อหาว่าง เพราะหน้า IRR มีอยู่แล้ว
5. เลือกแสดงและบันทึก ลิงก์ปรากฏในเมนูลิงก์เพิ่มเติม

ตัวอย่างหน้าข้อความใหม่: URL /pension.html กรอกชื่อและเนื้อหา แล้วบันทึก จะเปิดหน้าข้อความได้ทันทีโดยไม่ต้องสร้างไฟล์ .html จริง

หากต้องการเครื่องคำนวณชนิดใหม่ ต้องพัฒนา logic และหน้า React เพิ่ม การเพิ่มลิงก์อย่างเดียวไม่สร้างเครื่องคำนวณให้เอง เนื้อหาหน้าใหม่รองรับข้อความธรรมดาและขึ้นบรรทัด ไม่รองรับการรัน JavaScript/HTML

## ข้อมูลเดิมใน Firebase

อย่าลบโปรเจกต์ Firebase ก่อนสำรองและตรวจจำนวนข้อมูลที่ย้าย ข้อมูลเดิมยังอยู่ใน Firebase จนกว่าจะส่งออกและนำเข้าเอง รุ่นนี้มีตัวนำเข้า JSON ที่ผ่าน validation:

```bash
npm run db:import -- /private/path/export.json
npm run db:import -- /private/path/export.json --apply
```

ไฟล์ JSON เป็น object ที่ชื่อ key คือ insurance_products, leads, financial_surveys, site_content, site_links และแต่ละ key เป็น array ของเอกสาร `{ "id": "...", ...fields }` เวลา createdAt/updatedAt/preferredContactAt ต้องเป็น ISO UTC เช่น 2026-11-05T00:00:00.000Z ไม่ใช่ Firestore Timestamp object; ถ้าไม่มี createdAt/updatedAt จะใช้เวลานำเข้า

สคริปต์นี้ไม่ใช่ตัวอ่าน Firestore managed export และไม่ส่งออก Firebase ให้เอง ต้องเตรียม JSON ตาม schema ก่อน ทดสอบ dry-run ก่อน --apply, ตรวจ IDs/จำนวนข้อมูลหลังนำเข้า ID ที่มีอยู่แล้วจะไม่ถูกเขียนทับ บัญชี Firebase Auth จะไม่ย้ายรหัสผ่าน ต้องสร้างบัญชี Neon admin ใหม่

## ผลตรวจสอบ

47 tests ผ่าน (IRR/validation 37 และ API+Postgres 10), strict TypeScript build ผ่าน, ตรวจหน้า IRR ใน browser แล้ว ไม่มี Neon production credentials จึงยังไม่ได้ตรวจการเชื่อมต่อ Neon จริง, Deploy Vercel หรือสร้างบัญชีจริงให้

## แบนเนอร์ติ๊ก รุ่นแก้ไขล่าสุด
- ไปที่ `/admin/site` → ภาพแบนเนอร์ด้านบน → เลือกภาพจากเครื่อง → ตรวจตัวอย่าง → บันทึกข้อมูลเว็บไซต์
- รองรับ JPG, PNG, WebP ไม่เกิน 10 MB ภาพแนวนอนกว้างอย่างน้อย 600 px แนะนำ 2160 × 720 px
- ระบบลดขนาดและแปลงภาพเป็น JPEG เก็บใน `site_content/public` ของ Neon (ไม่ต้องสร้าง bucket หรือ Deploy ใหม่) รูปแบนเนอร์เป็นข้อมูลสาธารณะ
- ข้อความชื่อ ตำแหน่ง และคำโปรยแก้แยกได้ ตัวอักษรไทยบนเว็บเป็น HTML ไม่ใช่ข้อความที่ AI วาด
- ถ้าภาพใหม่มีข้อความในตัว ให้ปิด “แสดงข้อความและปุ่มบนแบนเนอร์”; ปุ่มติดต่อจะย้ายมาใต้ภาพ
- ปุ่มคืนค่าแบนเนอร์ติ๊กคืนภาพและคำโปรยเริ่มต้น แล้วต้องกดบันทึกอีกครั้ง
- รุ่นนี้ใช้ภาพประกอบใหม่อ้างอิงภาพติ๊กที่แนบ ต้นฉบับภาพบุคคลอยู่ใน `public/assets/tick-portrait.jpeg`; แบนเนอร์ที่สร้างผ่านเครื่องมือสร้างภาพอยู่ใน `public/assets/tick-hero.png`
- แนวคำสั่งภาพ: แทนผู้หญิงในแบนเนอร์ด้วยบุคคลจากภาพที่แนบ รักษาใบหน้าและชุดดำ พื้นกรมท่า/ทอง ครอบครัวและเมืองยามเย็น ไม่มีข้อความในไฟล์ภาพ
- ค่าข้อความตั้งต้นตรงตามที่ให้: ติ๊ก / ที่ปรึกษาการเงิน / ดูแลคุณ…เหมือนคนในครอบครัว / ความมั่นคงวันนี้ คือรอยยิ้มของคนที่คุณรักในวันข้างหน้า / ชีวิตที่มั่นคง เริ่มได้…วันนี้ / ให้ชีวิตที่ดีกว่า เป็นไปได้
- ข้อมูลเว็บไซต์เก่าที่ไม่มีฟิลด์แบนเนอร์จะได้ค่าเริ่มต้นอัตโนมัติ ไม่ต้องแก้ schema SQL
