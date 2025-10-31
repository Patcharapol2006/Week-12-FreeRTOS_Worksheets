# Lab 1: Binary Semaphores (45 นาที)
### คำถามสำหรับการทดลоง
1. เมื่อ give semaphore หลายครั้งติดต่อกัน จะเกิดอะไรขึ้น?
- สำหรับ Binary Semaphore, การ 'give' ซ้ำ (เมื่อค่ายังเป็น 1) จะไม่มีผลอะไร ค่าจะยังคงเป็น 1
- แต่สำหรับ Counting Semaphore, การ 'give' ซ้ำจะเพิ่มค่า (increment) ตัวนับไปเรื่อยๆ จนถึงค่าสูงสุดที่กำหนดไว้
2. ISR สามารถใช้ `xSemaphoreGive` หรือต้องใช้ `xSemaphoreGiveFromISR`?
- ISR (Interrupt Service Routine) ต้องใช้ `xSemaphoreGiveFromISR` เสมอ
- เพราะเป็นฟังก์ชันเวอร์ชันที่ปลอดภัยสำหรับใช้ใน Interrupt, ซึ่งจะไม่พยายาม Block และจัดการการสลับ Task (Context Switch) ได้อย่างถูกต้อง
3. Binary Semaphore แตกต่างจาก Queue อย่างไร?
- Binary Semaphore ใช้สำหรับ "การแจ้งเตือน" (Signaling) หรือ "การล็อก" (Synchronization) เท่านั้น มันไม่สามารถเก็บข้อมูลใดๆ ได้ (เหมือนธงสัญญาณ)
- Queue ใช้สำหรับ "การส่งข้อมูล" (Data Transfer) จาก Task หนึ่งไปยังอีก Task หนึ่ง มันสามารถเก็บ "Payload" (ตัวข้อมูล) ไว้ใน Buffer ได้

# Lab 2: Mutex and Critical Sections (45 นาที)
### คำถามสำหรับการทดลอง
1. เมื่อไม่ใช้ Mutex จะเกิด data corruption หรือไม่?
- เกิดขึ้นได้แน่นอน หากมี Task มากกว่าหนึ่งพยายาม "เขียน" หรือ "อ่าน-แก้ไข-เขียน" ข้อมูลตัวเดียวกัน (Shared Resource) พร้อมๆ กัน
- สิ่งนี้เรียกว่า Race Condition ซึ่ง Mutex ถูกออกแบบมาเพื่อป้องกัน โดยอนุญาตให้เพียง Task เดียวเข้าถึงทรัพยากรนั้น ณ เวลาใดเวลาหนึ่ง
2. Priority Inheritance ทำงานอย่างไร?
- เมื่อ Task Priority สูง (H) พยายามยึด Mutex ที่ Task Priority ต่ำ (L) ถืออยู่, ระบบจะ "ยก Priority" ของ Task L ชั่วคราว ให้เท่ากับ Task H
- นี่เป็นการป้องกันไม่ให้ Task Priority ปานกลาง (M) เข้ามาแทรก Task L, ทำให้ Task L ทำงานจนเสร็จและปล่อย Mutex ได้เร็วขึ้น (แก้ปัญหา Priority Inversion)
3. Task priority มีผลต่อการเข้าถึง shared resource อย่างไร?
- หากไม่มี Mutex, Task Priority สูงสามารถขัดจังหวะ (Preempt) Task ต่ำ "ขณะ" ที่มันกำลังแก้ไข Resource, ทำให้ข้อมูลพัง (Corrupted)
- หากมี Mutex, Priority จะกำหนดว่าใครจะได้ Mutex "เมื่อมันว่าง" และ Priority Inheritance จะทำงาน "เมื่อมันถูกยึดไปแล้ว"

# Lab 3: Counting Semaphores (30 นาที)
### คำถามสำหรับการทดลอง
1. เมื่อ Producers มากกว่า Resources จะเกิดอะไรขึ้น?
- เมื่อ Semaphore ถูก 'take' (ยึด) จนค่านับเป็น 0 (หมายถึง Resource ถูกใช้หมดแล้ว)
- Task (Producer) ใดๆ ที่พยายาม 'take' อีก จะถูก Block (เข้าคิวรอ) จนกว่า Task อื่นจะ 'give' (คืน) Resource กลับมา
2. Load Generator มีผลต่อ Success Rate อย่างไร?
- Load Generator ที่สูงขึ้น (พยายาม 'take' บ่อยขึ้น) จะทำให้ Semaphore มีโอกาสเป็น 0 บ่อยขึ้น
- หาก Task พยายาม 'take' โดยตั้ง Timeout เป็น 0 (ไม่รอ), อัตราการ 'ล้มเหลว' (Failure Rate) จะสูงขึ้น, ทำให้ Success Rate ต่ำลง
3. Counting Semaphore จัดการ Resource Pool อย่างไร?
- มันทำหน้าที่เป็น "ตัวนับ" จำนวน Resource ที่ว่างอยู่ (เช่น ตั้งค่าเริ่มต้นเท่ากับ 5 สำหรับ Pool ขนาด 5)
- Task ต้อง 'take' (ลบ 1) ก่อนใช้ Resource และ 'give' (บวก 1) เมื่อใช้เสร็จ ถ้าตัวนับเป็น 0 Task ที่มา 'take' ต้องรอ