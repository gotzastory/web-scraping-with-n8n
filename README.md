## ⚙️ วิธีการติดตั้งและรัน (How to Run)

### 1. สิ่งที่ต้องเตรียมเบื้องต้น (Prerequisites)
- ระบบ **n8n** (รันแบบ Local หรือ Cloud)
- บัญชี **Google Sheets** (สำหรับทำฐานข้อมูล)
- **LINE Messaging API** (Channel Access Token) 

---

### 2. การ Import Workflow
1. เปิดหน้า Canvas ของ n8n
2. ไปที่เมนูมุมขวาบน เลือก **Import from File...**
3. อัปโหลดไฟล์ `ข้อ 3.json` ที่อยู่ใน Repository นี้

---

### 3. การตั้งค่าก่อนรัน (Configuration & Credentials)
เพื่อให้กรรมการสามารถทดสอบ Workflow ได้สะดวกที่สุด รบกวนตั้งค่าใน Node ต่างๆ ดังนี้ครับ:

**3.1 การตั้งค่า AI (Ollama Chat Model)**
ทางผมได้เตรียม Server สำหรับ Ollama ไว้ให้เพื่อความสะดวกในการทดสอบแล้วครับ:
- **Base URL:** `http://72.62.71.35:32769/`
- **Model:** พิมพ์ชื่อ `minimax m2.7`
<img width="800" alt="Ollama Setup 1" src="https://github.com/user-attachments/assets/4fccd7f2-cbc4-4592-aaa7-5aa73d2c6eb8" />
<img width="400" alt="Ollama Setup 2" src="https://github.com/user-attachments/assets/d6ffce05-91e8-4404-889d-63577ee21621" />

**3.2 การตั้งค่าฐานข้อมูล (Google Sheets)**
- ท่านสามารถนำไฟล์ `livinginsider_dataset - listing.csv` ที่แนบมา นำไป Import ลงใน Google Sheets ของท่านเพื่อใช้เป็นโครงสร้างตารางได้เลย
- จากนั้นเชื่อมต่อ Credential บัญชี Google และเลือก Sheet ดังกล่าวใน Node (Get row และ Append row)
<img width="400" alt="Google Sheets Setup" src="https://github.com/user-attachments/assets/c5876ef6-670c-461b-80ba-7d0e54c88ecd" />

**3.3 การตั้งค่าแจ้งเตือน (LINE Messaging)**
- ใส่ Channel Access Token ของท่านใน Credentials
- **วิธีระบุเป้าหมายการส่ง:** หากต้องการส่งเข้าแชทส่วนตัว ท่านสามารถใช้ LINE Trigger Node รับข้อความเพื่อดึงค่า `userId` ของท่านออกมาก่อน จากนั้นนำ `userId` ที่ได้ ไปกรอกในช่อง Recipient ของ Node LINE Messaging (Action) ครับ
<img width="800" alt="LINE Setup 1" src="https://github.com/user-attachments/assets/39f811a3-c2df-4948-8ace-b7dbc12823de" />
<img width="800" alt="LINE Setup 2" src="https://github.com/user-attachments/assets/c0f10c6f-2eaf-49cc-8578-2eaea7c8cbb6" />

*(หมายเหตุ: สำหรับ Node HTTP Request ที่ดึงข้อมูลจาก Jina AI ไม่ต้องตั้งค่า Credential ใดๆ ครับ)*

---

### 4. การทดสอบการทำงาน (Execution)
- กดปุ่ม **Execute Workflow** ที่ด้านล่างของหน้าจอ
- ระบบจะจำลองการทำงานดึงหน้าเว็บ (Scrape via Jina) -> หั่นข้อความด้วย JavaScript -> ส่งให้ AI วิเคราะห์สกัด JSON -> ตรวจสอบข้อมูลซ้ำจาก Sheet -> บันทึกข้อมูลใหม่ และส่งแจ้งเตือนเข้า LINE
