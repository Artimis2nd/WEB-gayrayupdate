# Rom-gayray-web-update

ข้อมูล **สาธารณะ** ของเว็บ [เกเรแปลไทย](https://gayray.netlify.app/) แยกออกมาจากโค้ดเทมเพลต — repo นี้
(public) เก็บแค่สิ่งที่ยอมให้คนอื่นเห็น/ดาวน์โหลดได้ และไม่มีปัญหาเรื่อง Content-Type (ดูหัวข้อล่างสุด):

- `games.json` — ตั้งค่าเว็บ + รายชื่อเกม + checksum ROM
- `covers/` — รูปปกเกม
- `images/promptpay.png` — รูป QR PromptPay

เว็บ (โฮสต์บน Netlify, โค้ดอยู่ที่ `WEB/` ใน repo หลัก) ดึงไฟล์พวกนี้จาก repo นี้ตอน runtime ผ่าน
jsDelivr CDN (`WEB/index.html` ตัวแปร `DATA_BASE_URL`) — repo นี้ต้องเป็น **public** ไม่งั้น jsDelivr
ดึงไม่ได้

**ไม่มี** ในนี้ (ตั้งใจเก็บแยกไว้ที่อื่น):
- `.bps` (แพตช์จริง) — อยู่ที่ `WEB/patches/` ใน repo หลัก (private) — เว็บ fetch same-origin
- `.txt` (แม่แบบดราฟท์บทสรุป มีเครดิตงานอ้างอิงอยู่) — อยู่ที่ `Walkthrough-Drafts/` **ในโฟลเดอร์นี้เอง**
  แต่ถูกกันด้วย `.gitignore` ไม่ให้หลุดขึ้น GitHub เด็ดขาด — **ห้ามลบ/แก้ `.gitignore` บรรทัด
  `Walkthrough-Drafts/` โดยไม่ตั้งใจ**
- `Walkthrough/*.html` + รูปประกอบ (หน้าบทสรุป) — อยู่ที่ `WEB/Walkthrough/` ใน repo หลักแทน (ดูเหตุผล
  หัวข้อล่างสุด — **ไม่ใช่** เพราะเป็นความลับ แค่ทาง technical ทำผ่าน jsDelivr ไม่ได้)

## วิธีอัปเดตข้อมูลเว็บ (ไม่ต้อง deploy Netlify เลย)

1. แก้ `games.json` / เพิ่มรูปใน `covers/`
   - เพิ่มแพตช์ `.bps` ใหม่ → วางที่ `WEB/patches/` แทน แล้ว deploy `WEB/` ขึ้น Netlify ใหม่
   - เพิ่ม/แก้หน้าบทสรุป → แก้ที่ `WEB/Walkthrough/` แทน แล้ว deploy `WEB/` ขึ้น Netlify ใหม่
2. `git add -A && git commit -m "..." && git push`
3. เสร็จ — เว็บจะเห็นข้อมูลใหม่เอง (jsDelivr cache ไฟล์ไว้ที่ edge ปกติไม่กี่ชั่วโมงถึง ~7 วัน
   ถ้าอยากให้เห็นผลทันทีให้สั่ง purge cache: เปิด
   `https://purge.jsdelivr.net/gh/Artimis2nd/Rom-gayray-web-update@main/games.json`
   ในเบราว์เซอร์ครั้งนึง — purge ทีละไฟล์ที่แก้)

## ทำไม Walkthrough/*.html ถึงย้ายกลับไป WEB/ (2026-09-17)

ลองแล้วพบว่า jsDelivr (และ raw.githubusercontent.com) **บังคับส่งไฟล์ `.html` เป็น
`Content-Type: text/plain`เสมอ** (กันไม่ให้เอา CDN ไปโฮสต์หน้าเว็บปลอม/phishing) — พอเปิดลิงก์บทสรุป
ตรงๆ เบราว์เซอร์เลยโชว์ source code แทนที่จะ render หน้าเว็บ แก้ที่ config ฝั่ง jsDelivr ไม่ได้ (ไม่มี
media type override ให้ตั้ง) ต้องย้ายไฟล์ `.html` (บทสรุป) กลับไปให้ Netlify serve เอง (ตั้ง
Content-Type ตามนามสกุลไฟล์ถูกต้องเสมอ) — รูปภาพ/`games.json`/`.bps` ไม่มีปัญหานี้ (เบราว์เซอร์ไม่สนใจ
Content-Type ของรูป และ fetch().json()/.arrayBuffer() ก็ไม่สนใจ header เหมือนกัน)

ผลคือ: เพิ่ม/แก้ **เกม** (games.json/covers) ไม่ต้อง deploy Netlify เหมือนเดิม แต่เพิ่ม/แก้
**หน้าบทสรุป** ต้อง deploy Netlify ใหม่ (ยอมรับได้เพราะบทสรุปเปลี่ยนไม่บ่อยเท่าข้อมูลเกม)

## ประวัติ repo นี้ (สำคัญถ้าเพิ่ง clone ใหม่)

Commit แรก (`f5c5cb7`) ที่เคย push ขึ้น GitHub มี `.bps`/`.txt` ติดไปด้วยโดยไม่ตั้งใจ — repo เลยถูก
**ลบทิ้งแล้วสร้างใหม่** ด้วยประวัติสะอาด (orphan commit) ไม่มี `.bps`/`.txt` หลงเหลืออยู่ใน git
history เลย ถ้า `git log` เจอ commit เก่าที่มี `.bps`/`patches/` แปลว่ากำลังดูของเก่าที่ถูกแทนที่แล้ว
