# แมพโครงสร้างกับ 13 แฟ้ม

- [แมพโครงสร้างกับ 13 แฟ้ม](#แมพโครงสร้างกับ-13-แฟ้ม)
  - [แฟ้มที่ใช้](#แฟ้มที่ใช้)
    - [อธิบาย](#อธิบาย)
    - [แฟ้มที่ 1 NHSO Patient](#แฟ้มที่-1-nhso-patient)
    - [แฟ้มที่ 2 NHSO Provider](#แฟ้มที่-2-nhso-provider)
    - [แฟ้มที่ 4 NHSO OPD](#แฟ้มที่-4-nhso-opd)
    - [แฟ้มที่ 5 NHSO Diagnosis](#แฟ้มที่-5-nhso-diagnosis)
    - [แฟ้มที่ 7 NHSO CHAD](#แฟ้มที่-7-nhso-chad)
- [ออกแบบลำดับการประมวลผล](#ออกแบบลำดับการประมวลผล)
  - [er diagram](#er-diagram)
  - [ภาพรวม](#ภาพรวม)
    - [โฟล เชื่อมโยงข้อมูลประมวลผล](#โฟล-เชื่อมโยงข้อมูลประมวลผล)
    - [โฟล Data Markings](#โฟล-data-markings)
  - [กระบวนการ Cleansing](#กระบวนการ-cleansing)
- [Note อื่นๆ](#note-อื่นๆ)

## แฟ้มที่ใช้
- แฟ้มที่ 1 NHSO Patient ข้อมูลทั่วไปของผู้เข้ารับบริการ
- แฟ้มที่ 2 NHSO Provider ข้อมูลสถานพยาบาลที่ให้บริการผู้เข้ารับบริการ
- แฟ้มที่ 4 NHSO OPD ข้อมูลการมารับบริการผู้ป่วยนอก (OPD) ของผู้เข้ารับบริการ
- แฟ้มที่ 5 NHSO Diagnosis ข้อมูลวินิจฉัยโรคของผู้เข้ารับบริการ
- แฟ้มที่ 7 NHSO CHAD ข้อมูลรายละเอียดค่าใช้จ่ายรายรายการของผู้เข้ารับบริการตามรหัสมาตรฐาน

### อธิบาย
1. ไฟล์รูปแบบ `csv` ใช้ตัวคั่นเป็น `;`  
2. จะมีการเพิ่ม field พิเศษตามความต้องการของ user โดยจะขึ้นต้นด้วย `CUSTOM_` ซึ่งเป็น field นอกมาตฐาน 13 แฟ้ม ไม่แนะนำให้ใช้สำหรับการประมวลผล
3. `field` ที่ไม่มีการส่งออกข้อมูล คือ ที่มีคำอธิบายลงท้ายว่าว่า **ไม่ต้องใส่**
4. คำอธิบายที่ลงท้ายด้วย **marking** คือ มีข้อมูล แต่ส่งออกขั้นสุดท้ายโดนปิดไว้ไม่ส่งออก เพราะเนื่องจาก อาจมีข้อมูลบุคคล
5. ไฟล์ที่ส่งออกจะมี 2 ชุดฃ
   1. orginal. ไฟล์ก่อนทำการ marking ไว้สำหรับตรวจสอบความถูกต้อง หรือ ไว้สำหรับประมวลผลต่อ
   2. marking. ไฟล์ที่ผ่านการ marking ลบพวกเลขบัตรแล้ว
### แฟ้มที่ 1 NHSO Patient
- SEQ admits._id
- TYPE fix CID = บัตรประชาชน
- CID permissions_user.username **marking**
- PPN null **ไม่ต้องใส่**
- PWD null **ไม่ต้องใส่**
- NAME.GIVEN permissions_user.name **ไม่ต้องใส่**
- NAME.FAMILY permissions_user.surname **ไม่ต้องใส่**
- BIRTHDATE permissions_user.birth รูปแบบตาม 13 แฟ้ม `yyyy-MM-dd` `2021-12-31`
- GENDER permissions_user.gender แปลงเป็นเลข 1=ชาย, 2=หญิง
- ADDRESS.LINE null **ไม่ต้องใส่**
- ADDRESS.CITY null **ไม่ต้องใส่**
- ADDRESS.DISTRICT null **ไม่ต้องใส่**
- ADDRESS.STATE null **ไม่ต้องใส่**
- ADDRESS.POSTALCODE null **ไม่ต้องใส่**
- NATIONALITY permissions_user.nationality **ไม่ต้องใส่**
- RACE permissions_user.nationality **ไม่ต้องใส่**
- HN admits.patientDetail.hn **ไม่ต้องใส่**
- AN admits.patientDetail.an **ไม่ต้องใส่**
- **CUSTOM_AGE อายุ**

### แฟ้มที่ 2 NHSO Provider
- SEQ admits._id
- HCODE hospitals.prefix
- HCODE_NAME hospitals.name
- HCODE_SEND hospitals.prefix **ไม่ต้องใส่**
- HCODE_SEND_NAME hospitals.name **ไม่ต้องใส่**
- HMAIN null **ไม่ต้องใส่**
- HMAIN_NAME  null **ไม่ต้องใส่**
- AN admits.patientDetail.an **ไม่ต้องใส่**
- **CUSTOM_HCODE_TYPE** ประเภทสถานพยาบาลในระบบ amed

### แฟ้มที่ 4 NHSO OPD
ติดไว้ก่อนยังไม่ต้องทำพบ PERMITNO หลายตัวยัง งงๆ อยู่
- SEQ admits._id
- DATEOPD admits.createdAt วันที่เข้ารับบริการ รูปแบบตาม 13 แฟ้ม `yyyy-MM-dd'T'HH:mm:ss` `2021-12-31T13:15:30`
- INSCL null
- PERMITNO รหัส Claim Code **ยังไม่แน่ใจ** **marking**
  - lookup users-permissions_user.opself ไปหา components_user_opselves.history*.claimCode
    ```
    ใน components_user_opselves.history* จะมี hcode อยู่ต้องเอามาแมพกลับว่าเป็นของ user ไหน
    ตัวอย่าง id 6678f84cfc37f586a49b72f1
    ```
  - lookup users-permissions_user.sclaim*.ref ไปหา components_user_sclaims.authenCode
  - admit.patientDetail.opself.ccode
  - เหมือน 2 อันบนจะมีเลขที่ไม่ซ้ำกัน
  - PERMITNO 2 วันยังงมอยู่ เอาเท่าที่ได้ เหมือนข้อมูล api ตัวใหม่จะเป็นโครงสร้าง database อีกแบบเดี๊ยวค่อยตาม **TODO**
- HTYPE null **ไม่ต้องใส่**
- UUC null **ไม่ต้องใส่**
- CHIEFCOMP null **ไม่ต้องใส่**
- BTEMP null **ไม่ต้องใส่**
- SBP null **ไม่ต้องใส่**
- DBP null **ไม่ต้องใส่**
- PR null **ไม่ต้องใส่**
- RR null **ไม่ต้องใส่**
- WAISTLINE null **ไม่ต้องใส่**
- WEIGHT null **ไม่ต้องใส่**
- HEIGHT null **ไม่ต้องใส่**
- HEADCIRCUM null **ไม่ต้องใส่**
- CLINIC null **ไม่ต้องใส่**

### แฟ้มที่ 5 NHSO Diagnosis
- SEQ admits._id
- DATEDX diagnoses.createdAt รูปแบบตาม 13 แฟ้ม `yyyy-MM-dd'T'HH:mm:ss` `2021-12-31T13:15:30`
- DIAG diagnoses.diseases ต้องเอาไป lookup หา icd10 
- DIAGTYPE null **ไม่ต้องใส่**
- PROFESSION_ID diagnoses.doctor ดูเลขที่ใบอนุญาติต้องเอาไป lookup username **ให้ใช้เป็น data masking**
- CLINIC null **ไม่ต้องใส่**
- AN admits.patientDetail.an **ไม่ต้องใส่**

### แฟ้มที่ 7 NHSO CHAD 
- SEQ admits._id
- STDCODE null **ไม่ต้องใส่**
- INVOICE_NO drug._id **ไม่ต้องใส่**
- SERVDATE drugs.createdAt รูปแบบตาม 13 แฟ้ม `yyyy-MM-dd'T'HH:mm:ss` `2021-12-31T13:15:30`
- LOCALCODE tpu
- DESCRIPT ต้องเอา drug ไป lookup "ชื่อยาname unit"
- QTY components_drug_medicines.qty ต้องเอา drug.medicines มา lookup
- UNITPRICE null **ไม่ต้องใส่**
- CHARGEAMT null **ไม่ต้องใส่**
- CODESYS fix "TMT"
- LAB_RESULT null **ไม่ต้องใส่**
- UNIT medicines.unit
- REIMBPRICE null **ไม่ต้องใส่**
- XRAY_RESUT null **ไม่ต้องใส่**
- PATHO_RESULT null **ไม่ต้องใส่**
- AN admits.patientDetail.an **ไม่ต้องใส่**
- PROJCODE null **ไม่ต้องใส่**
- SP_ITEM null **ไม่ต้องใส่**
- DRUGREMARK null **ไม่ต้องใส่**
- PA_NO null **ไม่ต้องใส่**
- USE_STATUS null **ไม่ต้องใส่**
- CAGCODE null **ไม่ต้องใส่**
- SERIALNO null **ไม่ต้องใส่**
- SIGCODE null รหัสวิธีใช้ยา **ไม่ต้องใส่**
- SIGTEXT null วิธีใช้ยา **ไม่ต้องใส่**
- **CUSTOM_TPUID**
- **CUSTOM_TPUFSN**
- **CUSTOM_GPUID**
- **CUSTOM_GPUFSN**
- **CUSTOM_CONTENT** อาจจะไม่ต้องใส่ เพราะเป็น free text ผู้ใช้กรอกอะไรมาก็ได้ อาจมีข้อมูลส่วนบุคคล **marking**

# ออกแบบลำดับการประมวลผล

## er diagram
```mermaid
erDiagram
  person["ข้อมูลคน"]
  admit_discharge["ข้อมูลการ admit และ discharge"]
  icd10["ข้อมูลโรค"]
  drug["ข้อมูลยา"]
  
  person one to many admit_discharge : "1 คนมีได้หลาย admit"
  admit_discharge one to many icd10 : "1 admit มีได้หลาย โรค"
  admit_discharge one to many drug : "1 admit มีได้หลาย ยา"
```

## ภาพรวม
ออกแบบกระบวนการประมวลผล มีการเพิ่ม **Data Markings** เข้าไปเพื่อไม่ให้สืบค้นกลับหาต้นของข้อมูลได้
```mermaid
flowchart TD
  s("START")
  s-->collect[["รวบรวมข้อมูลที่ต้องใช้จากฐาน amed-care"]]
  collect-->process[["เชื่อมโยงข้อมูลประมวลผล"]]
  process-->cleansing[["ทำการ Cleansing ข้อมูล"]]
  cleansing-..->export1["ส่งออกข้อมูล orginal.csv"]
  cleansing-->marking[["Data Markings\nเปลี่ยนข้อมูล ID ทั้งหมด ไม่ให้สืบค้นกลับคืน"]]
  marking-->export2[[ส่งออกข้อมูลที่ผ่านการ Data Markings.csv]]
  export2-->e("END")
```

### โฟล เชื่อมโยงข้อมูลประมวลผล
```mermaid
flowchart TD
  s(start)
  s-->p5["แฟ้ม 5 ส่งออก SEQ, Patient, hospital"]
  p5-->p7["แฟ้ม 7 ส่งออก SEQ, Patient, hospital"]
  p7-->p1_p2["แฟ้ม 1,2 ประมวลผลด้วยกัน \nนำเข้า SEQ, Patient, hospital"]
  p1_p2-->p4["แฟ้ม 4 นำเข้า SEQ"]
```

### โฟล Data Markings
```mermaid
flowchart TD
  s(Start)
  s-->import["นำเช้าข้อมูล"]
  import-->marking["ซ่อนข้อมูลระบุตัวตนออกไป \nเลขบัตร, free text, เลข ว."]
  marking-->export_r["ส่งออกข้อมูลที่ผ่านการ Data Markings.csv \nสำหรับงานวิจัย"]
  export_r-->e(End)
```

## กระบวนการ Cleansing
เนื่องจากแฟ้มที่ 1 กับ 2  เกิดจากข้อมูลจากทุกแฟ้ม จะใช้ตัวนี้เป็นตัวตั้งต้น โดยทุกแฟ้มต้องมี SEQ ในแฟ้ม 1 และ 2  
เช่น มีข้อมูล แต่อาจจะไม่มีข้อมูลในแฟ้ม 1 และ 2 ก็นำข้อมูลส่วนนั้นออก


# Note อื่นๆ
วันเวลาที่ Diag อาจเกิดทีหลังการจ่ายยาได้ เพราะว่า ระบบไม่ได้บังคัง สามารถมาใส่เพิ่มตอนหลังได้
```
DIAGTYPE
1 = PRINCIPLE DX (การวินิจฉัยโรคหลัก) 
2 = CO-MORBIDITY(การวินิจฉัยโรคร่วม) 
3 = COMPLICATION(การวินิจฉัยโรคแทรก)
4 = OTHER (อื่น ๆ) 
5 = EXTERNAL CAUSE(สาเหตุภายนอก) 
6 = Additional Code (รหัสเสริม) 
7 = Morphology Code (รหัสเกี่ยวกับเนื้องอก)
```

