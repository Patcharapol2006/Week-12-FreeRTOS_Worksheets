## 📋 Post-Lab Questions

1. **Watchdog Design**: เหตุใดต้องใช้ separate timer สำหรับ feeding watchdog?
- เหตุผลหลัก: เพื่อป้องกัน “false feeding” หรือการที่ระบบค้างแต่ watchdog ยังถูก feed อยู่
- รายละเอียด:
ถ้า feed watchdog โดยตรงจาก loop หลักหรือ task เดียวกันกับที่อาจค้างได้ → watchdog จะไม่สามารถตรวจจับความผิดปกติ
จึงต้องมี separate timer / independent task ที่ feed watchdog เฉพาะเมื่อระบบทำงานครบทุกส่วนตามเงื่อนไข เช่น
2. **Pattern Timing**: อธิบายการเลือก Timer Period สำหรับแต่ละ pattern
- เลือก timer period ตามความเร็วของงาน (fast = control, medium = sensor, slow = logging) เพื่อสมดุลระหว่าง response กับ CPU load
3. **Sensor Adaptation**: ประโยชน์ของ Adaptive Sampling Rate คืออะไร?
- คือการ ปรับอัตราการอ่าน sensor แบบอัตโนมัติ ตามสภาวะหรือความเปลี่ยนแปลงของข้อมูล
- ประหยัดพลังงาน – ลดการอ่านบ่อยเมื่อสัญญาณคงที่
- ลดปริมาณข้อมูล – ช่วยลดภาระในการประมวลผลและส่งข้อมูล
- เพิ่มความละเอียดช่วงเปลี่ยนแปลง – อ่านถี่ขึ้นเมื่อค่ากำลังเปลี่ยน เพื่อไม่พลาดเหตุการณ์สำคัญ
- เพิ่มอายุการใช้งานระบบ – โดยเฉพาะใน IoT ที่ใช้แบตเตอรี่
4. **System Health**: metrics ใดบ้างที่ควรติดตามในระบบจริง?
- ติดตาม CPU load, memory, comms error, power, sensor status, watchdog reset เพื่อตรวจเสถียรภาพระบบ