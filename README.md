# Rom-gayray-web-update

ข้อมูลของเว็บ [เกเรแปลไทย](https://gayray.netlify.app/) แยกออกมาจากโค้ดเทมเพลต — repo นี้เก็บแค่
`games.json` + รูปปก (`covers/`) + แพตช์ (`patches/`) + บทสรุป (`Walkthrough/`) + รูป
QR PromptPay (`images/promptpay.png`)

เว็บ (โฮสต์บน Netlify, โค้ดอยู่ที่ `WEB/` ใน repo หลัก) ดึงไฟล์พวกนี้จาก repo นี้ตอน runtime ผ่าน
jsDelivr CDN (`WEB/index.html` ตัวแปร `DATA_BASE_URL`) — **repo นี้ต้องเป็น public** ไม่งั้น jsDelivr
ดึงไม่ได้

## วิธีอัปเดตข้อมูลเว็บ (ไม่ต้อง deploy Netlify เลย)

1. แก้ `games.json` / เพิ่มรูปใน `covers/` / เพิ่มแพตช์ใน `patches/` / เพิ่มบทสรุปใน `Walkthrough/`
2. `git add -A && git commit -m "..." && git push`
3. เสร็จ — เว็บจะเห็นข้อมูลใหม่เอง (jsDelivr cache ไฟล์ไว้ที่ edge ปกติไม่กี่ชั่วโมงถึง ~7 วัน
   ถ้าอยากให้เห็นผลทันทีให้สั่ง purge cache: เปิด
   `https://purge.jsdelivr.net/gh/<user>/Rom-gayray-web-update@main/games.json`
   ในเบราว์เซอร์ครั้งนึง — purge ทีละไฟล์ที่แก้)

เว็บ (`WEB/index.html`) จะ deploy ใหม่บน Netlify แค่ตอนแก้ดีไซน์/โครงสร้างหน้าเว็บเท่านั้น ไม่เกี่ยวกับ
การอัปเดตข้อมูลเกม/แพตช์/บทสรุปเลย

## การสร้าง repo นี้ครั้งแรก (ยังไม่ได้ทำ — เตรียมแค่โค้ด/ไฟล์ไว้ก่อนตามที่ผู้ใช้ขอ)

1. สร้าง repo ใหม่บน GitHub ชื่อ `Rom-gayray-web-update` ตั้งเป็น **Public**
2. ในโฟลเดอร์นี้:
   ```
   git remote add origin https://github.com/<user>/Rom-gayray-web-update.git
   git add -A
   git commit -m "init: games.json + covers + patches + walkthrough"
   git push -u origin main
   ```
3. เช็คว่า `WEB/index.html` ตัวแปร `DATA_BASE_URL` ชี้ไปที่ user/repo ถูกต้อง (ตอนนี้ตั้งเป็น
   `https://cdn.jsdelivr.net/gh/Artimis2nd/Rom-gayray-web-update@main` — ถ้า GitHub username ไม่ใช่
   `Artimis2nd` ต้องแก้ตรงนี้ด้วย)
4. Deploy `WEB/` ขึ้น Netlify อีกครั้งหนึ่ง (ครั้งสุดท้ายที่ต้องทำ deploy เพราะแก้โค้ดเทมเพลต) — หลังจากนี้
   ไม่ต้อง deploy อีกจนกว่าจะแก้ดีไซน์/โครงสร้างเว็บ
