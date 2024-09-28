# Get Hospital List
เรียกดูรายชื่อหน่วยบริการที่เข้าร่วมโครงการต่างๆ เพื่อนำเสนอหน่วยบริการให้แก่ผู้รับบริการ

```mermaid
flowchart TD
    s("getHospitalList(project_code)")
    v{"ตรวจสอบ (project_code)<br>ตอนนี้รับเฉพาะ P02"}
    p[["สร้างรายการตาม(project_code)"]]
    check_empty{"มีรายการหรือไม่"}
    success["statusDesc=SUCCESS"]
    r("Return HTTP 200 <br>ส่งคืนข้อมูล")

    s-->v-->|P02|p-->check_empty-->|"มี"|success-->r

    all["กำหนด project_code = P02"]
    v-->|ALL|all
    all-->p

    else("Return HTTP 404<br>statusCode:9999<br>statusDesc:projectCode not founnd")
    v------>|else|else

    empty["statusDesc=EMPTY"]
    check_empty-->|"ไม่มี"|empty-->r

```