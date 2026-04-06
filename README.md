# 🏠 Real Estate Data Pipeline & AI Alert System

โปรเจกต์นี้คือระบบ Data Pipeline อัตโนมัติที่สร้างขึ้นบน **n8n** เพื่อดึงข้อมูลประกาศขายอสังหาริมทรัพย์ล่าสุดจากเว็บไซต์ โดยประยุกต์ใช้ **Jina AI (Reader)** ในการทำ Web Scraping อย่างมีประสิทธิภาพ จากนั้นนำมาประมวลผลด้วย **AI Agent**, ตรวจสอบข้อมูลซ้ำ และแจ้งเตือนผ่าน **LINE** (Assignment Test: ข้อ 3)

## ✨ ฟีเจอร์หลัก (Key Features)
- **Smart Web Scraping:** ดึงข้อมูลจากหน้าเว็บไซต์อสังหาริมทรัพย์ (เช่น LivingInsider) ผ่าน HTTP Request โดยใช้ `r.jina.ai` ช่วยแปลงโครงสร้าง HTML ที่ซับซ้อนให้กลายเป็น Markdown ที่คลีนและอ่านง่าย ซึ่งเป็นมิตรต่อการนำไปประมวลผลต่อด้วย LLM รวมทั้งช่วยหลีกเลี่ยงการถูกบล็อก (Anti-bot)
- **AI-Driven Data Cleansing:** ใช้ **Ollama (AI Agent)** ในการอ่านและวิเคราะห์ข้อความ Markdown เพื่อ:
  - จัดหมวดหมู่ประเภทอสังหาฯ (บ้าน / คอนโด / ที่ดิน / ฯลฯ)
  - สกัดราคาขายและแปลงเป็นตัวเลขจำนวนเต็ม (Integer)
  - วิเคราะห์และจับคีย์เวิร์ดเพื่อตรวจสอบสถานะ "เจ้าของขายเอง"
- **Structured Output Parser:** บังคับให้ AI ส่งข้อมูลกลับมาในรูปแบบ JSON Format ที่มีโครงสร้างสมบูรณ์ 100%
- **Smart Deduplication:** ป้องกันการบันทึกและแจ้งเตือนซ้ำซ้อน ด้วยการเปรียบเทียบข้อมูลใหม่กับฐานข้อมูลเดิมใน Google Sheets (Compare Datasets)
- **Instant Alert:** แจ้งเตือนประกาศใหม่เข้า LINE Messaging แบบเรียลไทม์ พร้อมสรุปข้อมูลสำคัญให้อ่านง่าย

## 🛠️ เครื่องมือและเทคโนโลยี (Tech Stack)
- **n8n:** Workflow Automation Platform
- **Jina AI (Reader API):** เครื่องมือสำหรับดึงหน้าเว็บและแปลงเป็น Markdown (Scraping Proxy)
- **Ollama:** รัน Local LLM Model สำหรับการทำ AI Agent (Data Extraction & Cleansing)
- **Google Sheets:** ระบบฐานข้อมูลสำหรับจัดเก็บ (Database) และตรวจสอบข้อมูลซ้ำ
- **LINE Messaging API:** สำหรับส่งข้อความแจ้งเตือน (Alert)
- **JavaScript:** สำหรับจัดการ Array และหั่นข้อมูล (Data Transformation) ใน Node Code

---

## ⚙️ วิธีการติดตั้งและรัน (How to Run)

### 1. สิ่งที่ต้องเตรียมเบื้องต้น (Prerequisites)
- ระบบ **n8n** (รันแบบ Local หรือ Cloud)
- **Ollama** พร้อมติดตั้ง Model (เช่น llama3) บนเครื่อง Local หรือ Server 
- **Google Sheets** ที่สร้างคอลัมน์เตรียมไว้ (เช่น `listing_id`, `title`, `province`, `district`, `url`, `fetched_at`, `property_category`, `price_thb`, `is_owner_sale`)
- **LINE Messaging API** (Channel Access Token) สำหรับส่งข้อความแจ้งเตือน

### 2. การ Import Workflow
1. เปิดหน้า Canvas ของ n8n
2. ไปที่เมนูมุมขวาบน เลือก **Import from File...**
3. อัปโหลดไฟล์ `ข้อ 3.json` ที่อยู่ใน Repository นี้

### 3. การตั้งค่า Credentials (สำคัญ)
เนื่องจากระบบรักษาความปลอดภัยของ n8n จะไม่แนบข้อมูล Credential มาพร้อมไฟล์ Export รบกวนตั้งค่าการเชื่อมต่อใน Node ต่างๆ ดังนี้:
- **HTTP Request (Jina AI):** *ไม่ต้องตั้งค่า Credential* ระบบใช้การยิง GET Request ไปที่ `https://r.jina.ai/<target_url>` ได้ทันที
- **Ollama Chat Model:** คลิกที่ Node แล้วกรอก Base URL ของ Ollama (เช่น `http://localhost:11434`)
- **Google Sheets (Get row & Append row):** เชื่อมต่อบัญชี Google ของคุณ และเลือกไฟล์ Sheet ที่สร้างเตรียมไว้
- **LINE Messaging:** ใส่ Channel Access Token จาก LINE Developers Console

### 4. การทดสอบการทำงาน (Execution)
- กดปุ่ม **Execute Workflow** ที่ด้านล่างของหน้าจอ
- ระบบจะจำลองการทำงานดึงหน้าเว็บ (Scrape via Jina) -> หั่นข้อความด้วย JavaScript -> ส่งให้ AI วิเคราะห์สกัด JSON -> ตรวจสอบข้อมูลซ้ำจาก Sheet -> บันทึกข้อมูลใหม่ และส่งแจ้งเตือนเข้า LINE

---

## 📁 ไฟล์ส่งมอบ (Deliverables)
- `ข้อ 3.json`: ไฟล์โครงสร้าง n8n Workflow (สามารถ Import นำไปใช้ได้ทันที)
- `ddproperty_dataset.csv`: ตัวอย่างชุดข้อมูลผลลัพธ์ที่ผ่านกระบวนการ Cleansing และกรองข้อมูลซ้ำแล้ว
- `README.md`: เอกสารอธิบายโครงสร้างและวิธีการรันระบบ
