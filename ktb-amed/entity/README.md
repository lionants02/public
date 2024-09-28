# Basic ER Diagram
```mermaid
erDiagram
    hospital one to one business_hours: "one to one"
    hospital one to many business_project_code :"one to many"
    hospital one to many appointment :"one to many"
    appointment one to many appointment_possible_diagnosist:"one to many"
    appointment one to one appointment_response_status:"one to one"
    appointment one to many appointment_toke:"one to many"

    hospital["ข้อมูลโรงพยาบาล"]{
        varchar(20) hospital_code PK
        text hospital_name
        double latitude
        double longitude
        text address
        text tel_no "โทรศัพท์"
    }

    business_project_code["โครงการที่เข้าร่วม"]{
        varchar(20) hospital_code PK,FK
        varchar(10) project_code PK
    }

    business_hours["เวลาทำการ"]{
        varchar(20) hospital_code PK,FK
        text hour_mon_from "เวลาเปิดทำการ วันจันทร์ 8:00"
        text hour_mon_to "เวลาปิดทำการ วันจันทร์ 12:00"
        text hour_tue_from
        text hour_tue_to
        text hour_wed_from
        text hour_wed_to
        text hour_thu_from
        text hour_thu_to
        text hour_fri_from
        text hour_fri_to
        text hour_sat_from
        text hour_sat_to
        text hour_sun_from
        text hour_sun_to
        boolean holiday "หยุดนักขัตฤกษ์ T:หยุด F:ไม่หยุด"
    }

    appointment["การนัดหมาย"]{
        varchar(20) hospital_code PK,FK
        varchar(50) appointment_ref_id PK "รหัสอ้างอิง"
        timestamp booking_date_time
        varchar(10) project_code "โครงการที่ผู้ป่วยต้องการเข้ารับบริการ"
        text project_name "ชื่อโครงการที่ผู้ป่วยต้องการเข้ารับบริการ"
        varchar(20) citizen_id
        varchar(5) weight
        varchar(5) height
        varchar(10) platform "จองจาก platform อะไร"
        varchar(5) flag_code "สีของอาการ (Color Flag)
"
        array[text] general_advice "คำแนะนำเบื้องต้นสำหรับคนไข้ (Lifestyle Medicine - General Advice)"
        array[text] suggestion "คำแนะนำ (Suggestion)"
        array[text] transportation "คำแนะนำการเดินทาง (Transportation)"
        text urgency "ความเร่งด่วนในการเข้ารักษาพยาบาล (Urgency)"
    }

    appointment_possible_diagnosist["วินิจฉัยเบื้องต้น"]{
        varchar(50) appointment_ref_id FK
        varchar(15) symptom_code
        text symptom_result
        text symptom_url
        varchar(15) assessment_code
        text assessment_name

        array[text] assessment_practitioner
        array[text] medication
    }

    appointment_response_status["สถานนะการจอง"]{
        varchar(50) appointment_ref_id PK,FK "รหัสอ้างอิง"
        text status "CANCEL:ยกเลิก CONFIRM:ยืนยันการเข้ารับบริการ"
        varchar(10) http_status "ค่าได้หลังจากส่งข้อมูล 200"
        varchar(10) status_code "ค่าได้หลังจากส่งข้อมูล"
        text status_desc "ค่าได้หลังจากส่งข้อมูล Status description"
    }

    appointment_toke["โทเคนการจอง"]{
        timestamp time "วันเวลาที่สร้าง token"
        varchar(30) token PK "โทเคน"
        varchar(50) appointment_ref_id FK
        varchar(20) citizen_id "มีไว้เพื่อ debug"
    }
```
---
project_code โครงการที่เข้าร่วม
- P01 : โครงการรับยา 16 อาการ
- P02 : โครงการรับยา 32 อาการ
- P03 : โครงการเจ็บป่วยเล็กน้อย 42 อาการ (TeleMedicine)
---
flag_code flag_code
- C10 : สีขาว ผู้ป่วยทั่วไป (สามารถนัดเพื่อไปรักษาวันอื่นได้ หรือพักผ่อนให้เพียงพอเพื่อดูอาการ)
- C20 : สีเขียว เจ็บป่วยเล็กน้อย 
- C30 : สีเหลือง เจ็บป่วยปานกลาง
- C40 : สีส้ม เจ็บป่วยรุนแรง
- C50 : สีแดง ผู้ป่วยวิกฤต
---
appointment_status_code (statusCode)
- 0000 = SUCCESS
- 1002 = Invalid Request format
- 2000 = Status doesn't match
- 5000 = No data found
- 9999 = General Error 

---
ใช้สำหรับบันทึกข้อมูลที่จะส่งกลับไปยังระบบที่ทำการจองมา โดยจะมีอีก กระบวนการ รับช่วงต่อตรงนี้
```mermaid
erDiagram
    queue_appointment_confirm_status["คิวงานสำหรับส่งสถานนะการจองกลับไปยังต้นทาง"]{
        timestamp time "วันเวลาที่สร้าง"
        varchar(50) appointment_ref_id
        varchar(20) citizen_id
        varchar(20) status
        varchar(10) platform "จาก platform อะไร"
    }
```
---
ชุดข้อมูลสำหรับบันทึกกิจกรรม
```mermaid
erDiagram
    activity_log["บันทึกกิจกรรม"]{
        timestamp time
        text action
        varchar(50) ref_id
        text description
        int http_response_code
        varchar(10) tag

    }
```