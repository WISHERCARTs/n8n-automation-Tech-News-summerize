# Tech News Sentiment Dashboard

> Workflow สำหรับดึงข่าว Tech มาวิเคราะห์ Sentiment ด้วย AI แล้วบันทึกลง Google Sheets

![n8n](https://img.shields.io/badge/n8n-Workflow-orange)
![Gemini AI](https://img.shields.io/badge/Gemini-AI-blue)
![Google Sheets](https://img.shields.io/badge/Google-Sheets-green)

---

## Workflow ทำอะไร?

```
TechCrunch RSS → จำกัด 3 ข่าว → Gemini AI วิเคราะห์ → บันทึก Google Sheets
```

**Output ที่ได้:**
| Headline | Summary (TH) | Score | Category | Keywords | Date |
|----------|--------------|-------|----------|----------|------|
| ข่าว... | สรุปภาษาไทย | 1-10 | AI/Crypto/etc | คำสำคัญ | วันที่ |

---

## สิ่งที่ต้องเตรียม

1.  **n8n** (self-hosted หรือ cloud)
2.  **Gemini API Key** - [รับ API Key ที่นี่](https://aistudio.google.com/apikey)
3.  **Google Service Account** - สำหรับเขียน Google Sheets
4.  **Google Sheets** - สร้าง sheet พร้อม columns

---

## ขั้นตอนการติดตั้ง

### Step 1: ตั้งค่า Environment Variable

ใน n8n ให้ตั้งค่า Environment Variable:

```bash
# สำหรับ Docker
docker run -e GEMINI_API_KEY="your-api-key-here" n8nio/n8n

# หรือใส่ใน .env file
GEMINI_API_KEY=your-api-key-here
```

> **อย่าใส่ API Key โดยตรงใน workflow file!**

---

### Step 2: สร้าง Google Sheets

1. สร้าง Google Sheets ใหม่
2. ตั้งชื่อ columns ใน Row แรก:

| Date | Headline | Summary | Score | Category | Keywords |
| ---- | -------- | ------- | ----- | -------- | -------- |

3. คัดลอก URL ของ Sheet ไว้

---

### Step 3: ตั้งค่า Google Service Account

1. ไปที่ [Google Cloud Console](https://console.cloud.google.com/)
2. สร้าง Project ใหม่ หรือใช้ Project ที่มี
3. เปิดใช้งาน **Google Sheets API**
4. สร้าง **Service Account**:
   - ไปที่ IAM & Admin → Service Accounts
   - Create Service Account
   - ดาวน์โหลด JSON key file
5. **Share Google Sheet** ให้กับ email ของ Service Account (มีสิทธิ์ Editor)

---

### Step 4: Import Workflow

1. เปิด n8n
2. ไปที่ **Settings → Credentials**
3. เพิ่ม **Google Service Account** credential:
   - ใส่ข้อมูลจาก JSON key file
4. ไปที่ **Workflows → Import from File**
5. Import ไฟล์ `Tech News Sentiment Dashboard.json`

---

### Step 5: แก้ไข Settings ใน Workflow

เปิด workflow แล้วแก้ไข:

#### Node: Gemini AI Analysis

- ตรวจสอบว่า URL ใช้ `{{$env.GEMINI_API_KEY}}` แล้ว
- หรือเปลี่ยนเป็น API Key ของคุณ (ไม่แนะนำ)

#### Node: Tech_News_Analysis (Google Sheets)

- คลิกที่ node
- เลือก **Credential** ที่สร้างไว้
- ใส่ **URL ของ Google Sheet** ที่คุณสร้าง
- เลือก **Sheet name** ที่ต้องการ

---

## ทดสอบ Workflow

1. คลิก **Execute Workflow**
2. รอสักครู่ให้ทำงานเสร็จ
3. เปิด Google Sheets ดูผลลัพธ์

---

## โครงสร้างไฟล์

```
n8n project/
├── Tech News Sentiment Dashboard.json  # Workflow file
└── README.md                           # ไฟล์นี้
```

---

## Customization

### เปลี่ยน RSS Feed

แก้ไข Node **TechCrunch RSS** → เปลี่ยน URL เป็น feed อื่น:

- The Verge: `https://www.theverge.com/rss/index.xml`
- Ars Technica: `https://feeds.arstechnica.com/arstechnica/index`
- Wired: `https://www.wired.com/feed/rss`

### เปลี่ยนจำนวนข่าว

แก้ไข Node **Limit 3 Items** → เปลี่ยน `maxItems`

### เปลี่ยน AI Model

แก้ไข URL ใน Node **Gemini AI Analysis**:

- `gemini-2.5-flash` (เร็ว, ประหยัด)
- `gemini-2.5-pro` (แม่นยำกว่า)

---

## 🐛 Troubleshooting

| ปัญหา                             | วิธีแก้                               |
| --------------------------------- | ------------------------------------- |
| `API Key invalid`                 | ตรวจสอบ GEMINI_API_KEY ใน environment |
| `Google Sheets permission denied` | Share sheet ให้ Service Account email |
| `JSON parse error`                | AI อาจตอบผิดรูปแบบ ลองรันใหม่         |

---

## 👤 Author

สร้างโดย: Wish Nakthong (wishercarts)

---

## 📄 License

MIT License - ใช้ได้ฟรี แก้ไขได้ตามสบาย
