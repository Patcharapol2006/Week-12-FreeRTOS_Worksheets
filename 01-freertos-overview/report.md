# Lab 1: Task Priority และ Scheduling
## คำถามสำหรับวิเคราะห์
1. Priority ไหนทำงานมากที่สุด? เพราะอะไร?
- Task ที่มี Priority สูงสุด (ตัวเลข Priority สูงสุด) จะได้ทำงานมากที่สุด (หรือได้ทำงานก่อน) เพราะ FreeRTOS Scheduler เป็นแบบ Preemptive ซึ่งจะหยุด Task ที่กำลังรันอยู่
2. เกิด Priority Inversion หรือไม่? จะแก้ไขได้อย่างไร?
- เกิดขึ้นได้ เมื่อ Task สูง ต้องรอทรัพยากร (เช่น Mutex) ที่ Task ต่ำถืออยู่ แก้ได้โดยใช้ Mutex ที่มีคุณสมบัติ Priority Inheritance (การสืบทอด Priority)
- แก้ด้วย Priority Inheritance
3. Tasks ที่มี priority เดียวกันทำงานอย่างไร?
- สลับกันทำงาน (แบ่งเวลา) แบบ Round-Robin เมื่อหมด Time Slice (ครบ Tick) หรือเมื่อ Task นั้นๆ Block ตัวเอง (เช่น vTaskDelay)
4. การเปลี่ยน Priority แบบ dynamic ส่งผลอย่างไร?
- Scheduler จะจัดลำดับการทำงานใหม่ทันที Task ที่ถูกปรับให้สูงขึ้นอาจได้รันเลย ส่วน Task ที่ถูกปรับลดอาจต้องหยุดรอ
5. CPU utilization ของแต่ละ priority เป็นอย่างไร?
- Priority สูงสุดจะได้ใช้ CPU มากที่สุด (High utilization) Priority ต่ำสุดจะได้ใช้ CPU ก็ต่อเมื่อ Task ที่สูงกว่าทั้งหมดกำลัง Block (Low utilization)

# Lab 2: Task States Demonstration
## คำถามสำหรับวิเคราะห์
1. Task อยู่ใน Running state เมื่อไหร่บ้าง?
- เมื่อ Scheduler เลือกให้มันได้ใช้ CPU โดยมันต้องมี Priority สูงสุดที่พร้อมทำงาน (Ready)(Ready)
2. ความแตกต่างระหว่าง Ready และ Blocked state คืออะไร?
- Ready = พร้อมรันแต่รอคิว CPU / Blocked = ไม่พร้อมรันเพราะกำลังรอเหตุการณ์ (เช่น vTaskDelay)กำลังรอเหตุการณ์บางอย่าง เช่น การหน่วงเวลา (vTaskDelay()), รอ semaphore, หรือรอ queue
3. การใช้ vTaskDelay() ทำให้ task อยู่ใน state ใด?
- ทำให้ Task ย้ายไปสถานะ Blocked (หยุดรอ) จนกว่าจะครบเวลาที่กำหนด
4. การ Suspend task ต่างจาก Block อย่างไร?
- Block คือการหยุดรอแบบมีเงื่อนไข (เช่น รอเวลา) แต่ Suspend คือการแช่แข็งจนกว่าจะถูกสั่งปลุก
5. Task ที่ถูก Delete จะกลับมาได้หรือไม่?
- ไม่ได้ครับ เมื่อถูกลบ (Delete) ทรัพยากรจะถูกคืน ต้องสร้างใหม่ด้วย xTaskCreate() เท่านั้น
# Lab 3: Stack Monitoring และ Debugging
## คำถามสำหรับวิเคราะห์
1. Task ไหนใช้ stack มากที่สุด? เพราะอะไร?
- Task ที่เรียกฟังก์ชันซ้อนกันลึกๆ หรือใช้ตัวแปร Local ขนาดใหญ่ (เพราะ Stack ใช้เก็บข้อมูลเหล่านี้)
2. การใช้ heap แทน stack มีข้อดีอย่างไร?
- Heap ช่วยให้จองหน่วยความจำขนาดใหญ่ตอนรันไทม์ได้ ช่วยลดการใช้ Stack และป้องกัน Stack Overflow
3. Stack overflow เกิดขึ้นเมื่อไหร่และทำอย่างไรป้องกัน?
- เกิดเมื่อใช้ Stack เกินที่จองไว้ / ป้องกันโดย: เพิ่มขนาด Stack, เลี่ยง Recursion, หรือใช้ Heap แทน
4. การตั้งค่า stack size ควรพิจารณาจากอะไร?
- พิจารณาจากความลึกของการเรียกฟังก์ชัน (Call Depth) และขนาดของตัวแปร Local ทั้งหมดที่ใช้
5. Recursion ส่งผลต่อ stack usage อย่างไร?
- Recursion สร้าง Stack Frame ใหม่ซ้อนทับไปเรื่อยๆ ทำให้ Stack เต็มเร็วและเสี่ยง Overflow สูง
