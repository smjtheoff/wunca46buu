# 🔌 LibreNMS API - Node.js Examples

ตัวอย่าง code สำหรับใช้งาน LibreNMS API ด้วย Node.js

## 📋 ไฟล์ในโฟลเดอร์

| ไฟล์ | คำอธิบาย |
|------|----------|
| `librenms-api.md` | 📖 คู่มือ API ฉบับสมบูรณ์ |
| `.env.example` | 🔐 Template สำหรับ credentials |
| `package.json` | 📦 Node.js dependencies |
| `check-ether1.js` | ✅ ตรวจสอบสถานะพอร์ต ether1 |
| `list-devices.js` | 📋 แสดงรายการอุปกรณ์ทั้งหมด |
| `mikrotik-monitor.js` | 🖥️ Monitor MikroTik แบบเต็มรูปแบบ |
| `monitor-realtime.js` | 📊 Monitor แบบ real-time |

## 🚀 Quick Start

### 1. ติดตั้ง Dependencies

```bash
# เข้าไปยังโฟลเดอร์
cd librenms-api

# ติดตั้ง Node.js packages
npm install
```

### 2. ตั้งค่า Environment Variables

```bash
# Copy template
cp .env.example .env

# แก้ไขไฟล์ .env
nano .env
```

**แก้ไขค่าต่อไปนี้:**
```env
API_URL=http://localhost:8000/api/v0
API_TOKEN=your-actual-token-here
DEVICE_IP=192.168.56.10
```

### 3. รัน Scripts

```bash
# ตรวจสอบสถานะ ether1
npm run check

# แสดงรายการอุปกรณ์
npm run list

# Monitor MikroTik แบบเต็มรูปแบบ
npm run monitor

# Monitor แบบ real-time
npm run realtime
```

## 📝 การใช้งานแต่ละไฟล์

### check-ether1.js

ตรวจสอบสถานะพอร์ต ether1

```bash
node check-ether1.js
# หรือ
npm run check
```

**Output:**
```
==================================================
📡 MikroTik ether1 Status
==================================================
Interface:      ether1
Status:         UP ✅
Speed:          1000 Mbps
...
```

### list-devices.js

แสดงรายการอุปกรณ์ทั้งหมด

```bash
node list-devices.js
# หรือ
npm run list
```

**Output:**
```
====================================================================================================
📋 LibreNMS Devices List
====================================================================================================
ID    Hostname            Display Name             OS             Status    Uptime
----------------------------------------------------------------------------------------------------
1     192.168.56.10       MikroTik-Lab-Router      routeros       ✅ UP     2d 5h 30m
====================================================================================================
```

### mikrotik-monitor.js

Monitor MikroTik แบบครบถ้วน (Device Info + All Ports + ether1 Details)

```bash
node mikrotik-monitor.js
# หรือ
npm run monitor
```

### monitor-realtime.js

Monitor ether1 แบบ real-time (ทุกๆ 5 วินาที)

```bash
node monitor-realtime.js
# หรือ
npm run realtime
```

**Output:**
```
Time        Status    In (MB)        Out (MB)       Errors    Speed
--------------------------------------------------------------------------------
15:30:01    ✅ UP     123.45         67.89          0         1000 Mbps
15:30:06    ✅ UP     123.50         67.95          0         1000 Mbps
```

**กด `Ctrl+C` เพื่อหยุด**

## 🔧 Configuration

### Environment Variables

สร้างไฟล์ `.env` จาก `.env.example`:

```env
# LibreNMS API URL
API_URL=http://localhost:8000/api/v0

# API Token (from LibreNMS: Settings → API Settings)
API_TOKEN=a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6

# Device IP to monitor
DEVICE_IP=192.168.56.10
```

### ปรับแต่ง Monitor Interval

แก้ไขไฟล์ `monitor-realtime.js`:

```javascript
const INTERVAL = 5;  // วินาที ระหว่าง polls
const DURATION = 60; // วินาที (0 = ไม่จำกัด)
```

## 📚 คู่มือเพิ่มเติม

อ่านคู่มือ API ฉบับสมบูรณ์: [librenms-api.md](librenms-api.md)

- 🔑 วิธีสร้าง API Token
- 📡 API Endpoints reference
- 💻 ตัวอย่าง code เพิ่มเติม
- ⚠️ Error handling
- 🎯 Best practices

## 🔒 Security

**⚠️ สำคัญ:**
- ไฟล์ `.env` ถูก ignore โดย git แล้ว
- **ห้าม** commit API token เข้า repository
- เก็บ token ไว้เป็นความลับ

## 🐛 Troubleshooting

### Error: Missing environment variables

**แก้ไข:**
```bash
# ตรวจสอบว่ามีไฟล์ .env
ls -la .env

# ถ้าไม่มี copy จาก template
cp .env.example .env
```

### Error: Unauthenticated (401)

**แก้ไข:**
- ตรวจสอบ API_TOKEN ถูกต้อง
- สร้าง token ใหม่ใน LibreNMS

### Error: Device not found (404)

**แก้ไข:**
- ตรวจสอบ DEVICE_IP ถูกต้อง
- ตรวจสอบอุปกรณ์ถูกเพิ่มใน LibreNMS แล้ว

## 📦 Dependencies

- **axios:** HTTP client สำหรับเรียก API
- **dotenv:** โหลด environment variables จากไฟล์ .env

## 📝 License

MIT
