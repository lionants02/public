# Update Appointment Status
เมื่อผู้ป่วยเข้ารับบริการตามนัดหมาย ให้ partner ทำการส่งข้อมูลการเข้ารับบริการสำเร็จที่กลับมาที่ระบบที่สร้างนัดหมาย  
โดยจะมีรายการประมวลผลเข้ามาต่อเมื่อ มีการเรียกดูข้อมูลผ่าน [Get Information](getInformation.md)

## โครงสร้างข้อมูล
ใช้แนวคิดจาก Message Queue(MQ) ฉะนันโฟลที่ออกแบบสามารถปรับเปลี่ยนรองรับได้ทั้ง แบบ database และ MQ

### Consumer feed
ตัวที่รับข้อมูลมาจาก Queue
```mermaid
flowchart LR
    feed("ดึงข้อมูลรายการ [appointment_ref_id, platform]")
    process_platform[["ประมวลผลแยกตาม platform"]]

    feed-->|streaming data|process_platform
```

### platform K
```mermaid
flowchart TD
    process_platform[["platform K"]]
    get_detail["ดึงข้อมูลประกอบการประมวลผล"]
    send["ส่งข้อมูลไปยังระบบปลายทาง"]
    timeout{api timeout}
    monitor[[monitor]]

    p1(( ))

    process_platform-->get_detail-->p1-->send-->timeout-->|ไม่|monitor

    timeout_over{"ทำซ้ำครบ 5 ครั้งแล้วหรือไม่"}
    timeout-->|ใช่|timeout_over-->|ไม่|p1

    re["นำรายการนี้ไปเข้าคิวรอประมวลผลใหม่"]
    timeout_over-->|ใช่|re

    re--->monitor

```
