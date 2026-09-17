# Rom-gayray-web-update

ข้อมูล **สาธารณะ** ของเว็บ [เกเรแปลไทย](https://gayray.netlify.app/) แยกออกมาจากโค้ดเทมเพลต — repo นี้
(public) เก็บแค่สิ่งที่ยอมให้คนอื่นเห็น/ดาวน์โหลดได้:

- `games.json` — ตั้งค่าเว็บ + รายชื่อเกม + checksum ROM
- `covers/` — รูปปกเกม
- `images/promptpay.png` — รูป QR PromptPay
- `Walkthrough/` — หน้าบทสรุป (`.html`) + รูปปก/รูปประกอบ

เว็บ (โฮสต์บน Netlify, โค้ดอยู่ที่ `WEB/` ใน repo หลัก) ดึงไฟล์พวกนี้จาก repo นี้ตอน runtime — แต่ใช้
**2 ทาง คนละงาน** (ดูตัวแปร `DATA_BASE_URL`/`PAGES_BASE_URL` ใน `WEB/index.html`):

- `games.json`/`covers/`/`images/promptpay.png` → ผ่าน **jsDelivr CDN**
- `Walkthrough/*.html` (+ รูปประกอบ) → ผ่าน **GitHub Pages** ของ repo นี้เอง:
  https://artimis2nd.github.io/Rom-gayray-web-update/

repo นี้ต้องเป็น **public** ไม่งั้นทั้ง jsDelivr และ GitHub Pages ใช้ไม่ได้

**ไม่มี** ในนี้ (ตั้งใจเก็บแยกไว้ที่อื่น):
- `.bps` (แพตช์จริง) — อยู่ที่ `WEB/patches/` ใน repo หลัก (private) — เว็บ fetch same-origin
- `.txt` (แม่แบบดราฟท์บทสรุป มีเครดิตงานอ้างอิงอยู่) — อยู่ที่ `Walkthrough-Drafts/` **ในโฟลเดอร์นี้เอง**
  แต่ถูกกันด้วย `.gitignore` ไม่ให้หลุดขึ้น GitHub เด็ดขาด — **ห้ามลบ/แก้ `.gitignore` บรรทัด
  `Walkthrough-Drafts/` โดยไม่ตั้งใจ**

## วิธีอัปเดตข้อมูลเว็บ (ไม่ต้อง deploy Netlify เลย — ทั้งเกมและบทสรุป)

1. แก้ `games.json` / เพิ่มรูปใน `covers/` / เพิ่ม-แก้บทสรุปใน `Walkthrough/`
   (ถ้าเป็นแพตช์ `.bps` ใหม่ ให้วางที่ `WEB/patches/` แทน แล้ว deploy `WEB/` ขึ้น Netlify ใหม่)
2. `git add -A && git commit -m "..." && git push`
3. เสร็จ:
   - `games.json`/`covers/` — jsDelivr cache ไฟล์ไว้ที่ edge ปกติไม่กี่ชั่วโมงถึง ~7 วัน อยากให้เห็นผล
     ทันทีให้เปิด `https://purge.jsdelivr.net/gh/Artimis2nd/Rom-gayray-web-update@main/games.json`
     ในเบราว์เซอร์ครั้งนึง (purge ทีละไฟล์ที่แก้)
   - `Walkthrough/` — GitHub Pages rebuild เองอัตโนมัติหลัง push ปกติไม่ถึงนาที เช็คสถานะได้ที่แท็บ
     Actions ของ repo นี้

เว็บ (`WEB/index.html`) จะ deploy ใหม่บน Netlify แค่ตอนแก้ดีไซน์/โครงสร้างหน้าเว็บ หรือเพิ่ม/แก้
`.bps` เท่านั้น ไม่เกี่ยวกับการอัปเดตเกม/บทสรุปที่นี่เลย

## ทำไม Walkthrough/*.html ต้องผ่าน GitHub Pages ไม่ใช่ jsDelivr (2026-09-17)

ลองแล้วพบว่า jsDelivr (และ raw.githubusercontent.com) **บังคับส่งไฟล์ `.html` เป็น
`Content-Type: text/plain` เสมอ** (กันไม่ให้เอา CDN ไปโฮสต์หน้าเว็บปลอม/phishing) — พอเปิดลิงก์บทสรุป
ตรงๆ เบราว์เซอร์เลยโชว์ source code แทนที่จะ render หน้าเว็บ แก้ที่ config ฝั่ง jsDelivr ไม่ได้ (ไม่มี
media type override ให้ตั้ง)

แก้โดยเปิด **GitHub Pages** ของ repo นี้ (static host จริง ตั้ง Content-Type ตามนามสกุลไฟล์ถูกต้อง
เสมอ) แล้วให้ `WEB/index.html` ลิงก์บทสรุปไปที่ `https://artimis2nd.github.io/Rom-gayray-web-update/...`
แทน — รูปภาพ/`games.json`/`.bps` ไม่มีปัญหานี้ (เบราว์เซอร์ไม่สนใจ Content-Type ของรูป และ
`fetch().json()`/`.arrayBuffer()` ก็ไม่สนใจ header เหมือนกัน) เลยยังใช้ jsDelivr ได้ปกติ

ผลคือ: เพิ่ม/แก้ **เกมและบทสรุป** ไม่ต้อง deploy Netlify เลยทั้งคู่ (ต่างจากรอบแรกที่ต้องย้าย
Walkthrough กลับไป WEB/ ชั่วคราว — ตอนนี้แก้ด้วย GitHub Pages แทนแล้ว)

## ประวัติ repo นี้ (สำคัญถ้าเพิ่ง clone ใหม่)

Commit แรก (`f5c5cb7`) ที่เคย push ขึ้น GitHub มี `.bps`/`.txt` ติดไปด้วยโดยไม่ตั้งใจ — repo เลยถูก
**ลบทิ้งแล้วสร้างใหม่** ด้วยประวัติสะอาด (orphan commit) ไม่มี `.bps`/`.txt` หลงเหลืออยู่ใน git
history เลย ถ้า `git log` เจอ commit เก่าที่มี `.bps`/`patches/` แปลว่ากำลังดูของเก่าที่ถูกแทนที่แล้ว
