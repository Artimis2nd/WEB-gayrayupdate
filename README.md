# Rom-gayray-web-update

ข้อมูล **สาธารณะ** ของเว็บ [เกเรแปลไทย](https://gayray.netlify.app/) แยกออกมาจากโค้ดเทมเพลต — repo นี้
(public) เก็บแค่สิ่งที่ยอมให้คนอื่นเห็น/ดาวน์โหลดได้:

- `games.json` — ตั้งค่าเว็บ + รายชื่อเกม + checksum ROM
- `covers/` — รูปปกเกม
- `images/promptpay.png` — รูป QR PromptPay
- `Walkthrough/` — หน้าบทสรุป (`.html`) + รูปปก/รูปประกอบ

**ไม่มี** `.bps` (แพตช์จริง) และ **ไม่มี** `.txt` (แม่แบบดราฟท์บทสรุป มีเครดิตงานอ้างอิงอยู่) —
สองอย่างนี้ตั้งใจเก็บแยกเป็นความลับ ไม่ขึ้น GitHub สาธารณะ:
- แพตช์ `.bps` อยู่ที่ `WEB/patches/` ใน repo หลัก (private) — เว็บ fetch same-origin จากตัวเว็บเอง
- แม่แบบ `.txt` อยู่ที่ `Walkthrough-Drafts/` **ในโฟลเดอร์นี้เอง** แต่ถูกกันด้วย `.gitignore` ไม่ให้
  หลุดขึ้น GitHub เด็ดขาด — **ห้ามลบ/แก้ `.gitignore` บรรทัด `Walkthrough-Drafts/` โดยไม่ตั้งใจ**

เว็บ (โฮสต์บน Netlify, โค้ดอยู่ที่ `WEB/` ใน repo หลัก) ดึง `games.json`/`covers/`/`Walkthrough/`/
`images/promptpay.png` จาก repo นี้ตอน runtime ผ่าน jsDelivr CDN (`WEB/index.html` ตัวแปร
`DATA_BASE_URL`) — repo นี้ต้องเป็น **public** ไม่งั้น jsDelivr ดึงไม่ได้ ส่วน `.bps` ไม่ผ่าน jsDelivr
เลย (ดู comment ใน `WEB/index.html` ตรง fetch g.patch)

## วิธีอัปเดตข้อมูลเว็บ (ไม่ต้อง deploy Netlify เลย)

1. แก้ `games.json` / เพิ่มรูปใน `covers/` / เพิ่มบทสรุปใน `Walkthrough/`
   (ถ้าเป็นแพตช์ `.bps` ใหม่ ให้วางที่ `WEB/patches/` แทน แล้ว deploy `WEB/` ขึ้น Netlify ใหม่)
2. `git add -A && git commit -m "..." && git push`
3. เสร็จ — เว็บจะเห็นข้อมูลใหม่เอง (jsDelivr cache ไฟล์ไว้ที่ edge ปกติไม่กี่ชั่วโมงถึง ~7 วัน
   ถ้าอยากให้เห็นผลทันทีให้สั่ง purge cache: เปิด
   `https://purge.jsdelivr.net/gh/Artimis2nd/Rom-gayray-web-update@main/games.json`
   ในเบราว์เซอร์ครั้งนึง — purge ทีละไฟล์ที่แก้)

เว็บ (`WEB/index.html`) จะ deploy ใหม่บน Netlify แค่ตอนแก้ดีไซน์/โครงสร้างหน้าเว็บ หรือเพิ่ม/แก้
`.bps` เท่านั้น ไม่เกี่ยวกับการอัปเดตเกม/บทสรุปที่นี่เลย

## ประวัติ repo นี้ (สำคัญถ้าเพิ่ง clone ใหม่)

Commit แรก (`f5c5cb7`) ที่เคย push ขึ้น GitHub มี `.bps`/`.txt` ติดไปด้วยโดยไม่ตั้งใจ — repo เลยถูก
**ลบทิ้งแล้วสร้างใหม่** ด้วยประวัติสะอาด (orphan commit) ไม่มี `.bps`/`.txt` หลงเหลืออยู่ใน git
history เลย ถ้า `git log` เจอ commit เก่าที่มี `.bps`/`patches/` แปลว่ากำลังดูของเก่าที่ถูกแทนที่แล้ว
