# แก้ไข source code ด้วยตนเอง

Source code ทั้งหมดแก้ไขได้ ไฟล์อยู่ภายในโฟลเดอร์ tickblawealth ใน ZIP นำไฟล์ภายในโฟลเดอร์นี้ไปวางที่ root ของ repository เดิม (ระดับเดียวกับ package.json)

## ตัวเลขและลูกน้ำ

- `src/components/MoneyInput.tsx`: ช่องกรอกเงินแบบมีลูกน้ำระหว่างพิมพ์ รองรับวางตัวเลขที่มีลูกน้ำ เคอร์เซอร์ และทศนิยม ส่งค่าที่ไม่มีลูกน้ำกลับไปคำนวณ
- `src/lib/numberFormat.ts`: ฟังก์ชันจัดกลุ่มตัวเลขและอ่านข้อมูลจากช่องกรอก
- `src/lib/calculator.ts`: ฟังก์ชัน `money()` จัดรูปแบบผลลัพธ์เป็นลูกน้ำและทศนิยมไม่เกิน 2 ตำแหน่ง
- `src/pages/PlanningTools.tsx`: หน้าวางแผนการศึกษา เกษียณ ลดหย่อนภาษี และเป้าหมายการเงิน ค่าตั้งต้นอยู่ใน `useState` ตัวแปร `v` เช่น `cost: 1000000` หมายถึงค่าเรียนตั้งต้นหนึ่งล้านบาท
- `src/pages/Calculator.tsx`: หน้าคำนวณเบี้ย ช่องทุนประกัน
- `src/pages/Irr.tsx`: ช่องกระแสเงินสดรายปี รองรับค่าติดลบ
- `src/lib/planning.ts`: สูตรวางแผนการเงิน
- `src/lib/irr.ts`: สูตร IRR

ตัวอย่างช่องกรอกใน React:

```tsx
import { MoneyInput } from '../components/MoneyInput';
<MoneyInput
  value={amount}
  onValueChange={raw => setAmount(raw === '' ? NaN : Number(raw))}
/>
```

ตัวอย่างแสดงผล:

```tsx
import { money } from '../lib/calculator';
<strong>{money(1000000)} บาท</strong>
```

อย่าใส่ลูกน้ำในค่าตัวเลขใน source code เช่น `cost: 1000000` ถูกต้อง ไม่ใช่ `cost: 1,000,000` และอย่าบันทึก `"1,000,000"` เป็นจำนวนเงินลงฐานข้อมูล ควรบันทึกเป็นเลข `1000000` ให้ component เป็นผู้จัดหน้าจอ

`input type="number"` แสดงค่าที่มีลูกน้ำไม่ได้ ช่องเงินจึงใช้ `type="text"` พร้อม `inputMode="decimal"`; อายุ จำนวนปี และเปอร์เซ็นต์ยังใช้ช่องตัวเลขเดิม สำหรับ IRR ใช้แป้นข้อความเพื่อให้พิมพ์เครื่องหมายลบบนมือถือได้

## หน้าตาและเนื้อหา

| สิ่งที่ต้องการแก้ | ไฟล์ |
| --- | --- |
| หน้าแรก | src/pages/Home.tsx |
| แบนเนอร์และตำแหน่งข้อความ | src/components/HeroBanner.tsx |
| ค่าแบนเนอร์/คำโปรยเริ่มต้น | src/lib/siteContent.ts |
| สีและรูปแบบ | src/styles.css |
| รูปแบบมือถือ/แท็บเล็ตและแบนเนอร์ | src/responsive.css |
| หน้าจอแก้ไขเว็บไซต์ของแอดมิน | src/pages/SiteEditor.tsx |

ถ้าบันทึกข้อมูลจากแอดมินไว้แล้ว ค่าใน Neon จะใช้แทนค่าเริ่มต้นใน source code ให้แก้ผ่านแอดมิน หรือใช้ปุ่มคืนค่าแบนเนอร์แล้วบันทึก

ก่อนนำขึ้นเว็บ รัน `npm run build` และ `npm test` จากนั้น Commit/Push ไป GitHub สาขาที่เชื่อม Vercel; ถ้าเปิด Automatic Deploy ไว้ Vercel จะ Build รุ่นใหม่ให้ ตรวจว่า deployment สำเร็จก่อนเปิดเว็บใหม่
