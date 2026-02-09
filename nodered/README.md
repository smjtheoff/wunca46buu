# 🔄 Node-RED + MQTT Integration

Flow-based programming เพื่อดึงข้อมูลจาก LibreNMS API และส่งต่อผ่าน MQTT

## 📁 ไฟล์ในโฟลเดอร์

| ไฟล์ | คำอธิบาย |
|------|----------|
| `nodered.md` | 📖 คู่มือฉบับสมบูรณ์ |
| `docker-compose.yml` | 🐳 Docker Compose configuration |
| `flow-example.json` | 🔄 ตัวอย่าง Node-RED flow |
| `mosquitto/config/` | ⚙️ Mosquitto configuration |

## 🚀 Quick Start

### 1. Setup

```bash
# เข้าโฟลเดอร์
cd nodered

# สร้าง directories
mkdir -p mosquitto/config mosquitto/data mosquitto/log nodered_data

# สร้าง Mosquitto config
cat > mosquitto/config/mosquitto.conf << 'EOF'
listener 1883
allow_anonymous true
listener 9001
protocol websockets
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
EOF

# สร้าง Docker network (ถ้ายังไม่มี)
docker network create monitoring_network

# Connect LibreNMS to network
docker network connect monitoring_network librenms
```

### 2. Start Services

```bash
docker-compose up -d
```

### 3. เข้าใช้งาน

- **Node-RED:** http://localhost:1880
- **MQTT:** `localhost:1883`

### 4. Import Flow

1. เปิด Node-RED
2. Menu (≡) → Import
3. เลือกไฟล์ `flow-example.json`
4. แก้ไข API Token ใน HTTP Request node
5. Deploy

## 📊 ทดสอบ MQTT

```bash
# Subscribe to messages
mosquitto_sub -h localhost -t "mikrotik/ether1/status" -v

# ใน Node-RED คลิกปุ่มทดสอบที่ Inject node
# ควรเห็นข้อมูลใน terminal
```

## 📚 คู่มือเพิ่มเติม

อ่านคู่มือฉบับสมบูรณ์: [nodered.md](nodered.md)

- 🏗️ Architecture overview
- 🔧 ตั้งค่า Flow แบบละเอียด
- 📡 MQTT topics และ payload format
- 🐛 Troubleshooting
- 🎯 Advanced flows

## 🛑 Stop Services

```bash
docker-compose down
```

## 🔧 Useful Commands

```bash
# View logs
docker-compose logs -f nodered
docker-compose logs -f mosquitto

# Restart services
docker-compose restart

# Enter container shell
docker exec -it nodered bash
docker exec -it mosquitto sh
```
