<div align="center">
  <img src="picture/wunca46.jpg" alt="WUNCA46 Banner" width="100%" style="max-width: 1200px; border-radius: 10px;">
</div>

<br>

# 🧪 Network Monitoring Lab: LibreNMS + MikroTik RouterOS

> 📚 Workshop: สร้างระบบ Network Monitoring ด้วย LibreNMS และ MikroTik RouterOS

---

## 🎯 Lab Overview

ในแล็ปนี้คุณจะได้เรียนรู้การสร้างระบบ Network Monitoring แบบ end-to-end:

1. **ติดตั้ง LibreNMS** บน Docker สำหรับเป็น Monitoring System
2. **สร้าง MikroTik RouterOS VM** บน VirtualBox พร้อม configuration
3. **Monitor MikroTik** ด้วย LibreNMS ผ่าน SNMP protocol

### 🎓 สิ่งที่จะได้เรียนรู้

- ✅ Docker Compose orchestration
- ✅ Network Monitoring concepts (SNMP, Polling, Metrics)
- ✅ VirtualBox networking (Host-Only, NAT, Internal)
- ✅ MikroTik RouterOS configuration
- ✅ SNMP v2c setup และ troubleshooting
- ✅ Real-time monitoring และ alerting

### ⏱️ ระยะเวลา

- **Lab 1:** 15-20 นาที (Setup LibreNMS)
- **Lab 2:** 20-30 นาที (Setup MikroTik)
- **Lab 3:** 10-15 นาที (Integration & Monitoring)
- **รวม:** ~45-65 นาที

### 📋 Prerequisites

**Software:**
- [Docker Desktop](https://docs.docker.com/get-docker/) 20.10+ (Windows/macOS/Linux)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 6.0+
- Terminal/PowerShell access
- Web Browser (Chrome, Firefox, Edge)

**Hardware:**
- CPU: 4 cores (recommended)
- RAM: 6 GB available
- Disk: 20 GB free space
- Internet connection

**Knowledge:**
- Basic Linux commands
- Basic networking concepts (IP, subnet, gateway)
- Docker basics (optional but helpful)

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Host Machine                              │
│                                                                  │
│  ┌──────────────────────┐       ┌──────────────────────────┐   │
│  │  LibreNMS (Docker)   │       │  MikroTik RouterOS (VM)  │   │
│  │  ==================   │       │  ====================    │   │
│  │  • Port: 8000        │◄─────►│  • ether1: 192.168.56.10│   │
│  │  • MariaDB           │ SNMP  │  • ether2: NAT          │   │
│  │  • Redis             │ Poll  │  • ether3: Internal     │   │
│  │  • Dispatcher        │       │  • ether4: Internal     │   │
│  └──────────────────────┘       └──────────────────────────┘   │
│           │                               │                     │
│           └───────────────┬───────────────┘                     │
│                           │                                     │
│                   Host-Only Network                             │
│                   192.168.56.0/24                               │
└─────────────────────────────────────────────────────────────────┘
```

**Network Design:**
- **Host-Only Network (192.168.56.0/24):** สำหรับ management และ monitoring
- **LibreNMS:** Host เข้าถึงผ่าน `localhost:8000`
- **MikroTik:** IP `192.168.56.10` บน ether1
- **SNMP:** Port 161/UDP สำหรับ polling data

---

## 🧪 LAB 1: ติดตั้งและรัน LibreNMS

**🎯 วัตถุประสงค์:** ติดตั้ง LibreNMS Monitoring System ด้วย Docker Compose

**⏱️ เวลา:** 15-20 นาที

### Step 1.1: เตรียม Environment

```bash
# Clone repository
git clone <repository-url>
cd wunca46buu

# ตรวจสอบ Docker
docker --version
docker-compose --version
```

**Expected Output:**
```
Docker version 20.10.x or higher
Docker Compose version v2.x.x or higher
```

### Step 1.2: เข้าไปยัง LibreNMS Directory

```bash
cd librenms
ls -la
```

**ไฟล์ที่ควรเห็น:**
- `docker-compose.yml` - Configuration file
- `librenms.md` - คู่มือฉบับเต็ม

### Step 1.3: Start LibreNMS Containers

```bash
# Start ทุก services (MariaDB, Redis, LibreNMS, Dispatcher)
docker-compose up -d

# ตรวจสอบสถานะ
docker-compose ps
```

**Expected Output:**
```
NAME                   STATUS    PORTS
librenms_db            Up        3306/tcp
librenms_redis         Up        6379/tcp
librenms               Up        0.0.0.0:8000->8000/tcp
librenms_dispatcher    Up        -
```

✅ **All 4 containers ต้องมีสถานะ "Up"**

### Step 1.4: ดู Logs (Optional)

```bash
# ดู logs เพื่อตรวจสอบการทำงาน
docker-compose logs -f librenms
```

**รอจนเห็น:**
```
[OK] Database migrations completed
[OK] LibreNMS is ready
```

**กด `Ctrl+C` เพื่อออกจากโหมดดู logs**

### Step 1.5: สร้าง Admin User

```bash
# สร้าง admin user สำหรับ login
docker exec librenms lnms user:add admin -p Admin@123 --role admin
```

**Expected Output:**
```
User admin added successfully
```

💡 **จดข้อมูล Login:**
- Username: `admin`
- Password: `Admin@123`

### Step 1.6: เข้าถึง LibreNMS Web Interface

1. เปิด Web Browser
2. เข้าไปที่: **http://localhost:8000**
3. Login ด้วย username และ password ที่สร้างไว้

**✅ Success Criteria:**
- [ ] เห็นหน้า Login page
- [ ] Login สำเร็จ
- [ ] เห็น Dashboard (ยังไม่มีอุปกรณ์)

### 📸 Screenshot Lab 1

Dashboard ควรแสดง:
```
Devices: 0
Ports: 0
Services: 0
```

### 🎉 LAB 1 Complete!

คุณได้ติดตั้ง LibreNMS สำเร็จแล้ว ตอนนี้พร้อมที่จะเพิ่มอุปกรณ์เข้ามา monitor!

---

## 🧪 LAB 2: ติดตั้งและรัน MikroTik RouterOS

**🎯 วัตถุประสงค์:** สร้าง MikroTik RouterOS VM และตั้งค่า network interfaces

**⏱️ เวลา:** 20-30 นาที

**📖 คู่มือฉบับเต็ม:** [mikrotik/mikrotik.md](mikrotik/mikrotik.md) (มีรายละเอียดทุกขั้นตอน)

### Step 2.1: ดาวน์โหลด RouterOS

1. ไปที่: https://mikrotik.com/download
2. เลือก **Cloud Hosted Router (CHR)**
3. เลือก **OVA** image
4. ดาวน์โหลดไฟล์ `.ova`

### Step 2.2: Import OVA ใน VirtualBox

1. เปิด **VirtualBox**
2. **File** → **Import Appliance...**
3. เลือกไฟล์ `.ova` ที่ดาวน์โหลด
4. ปรับแต่ง settings:
   - **Name:** `MikroTik-Router`
   - **RAM:** `256 MB` (แนะนำ)
   - **CPU:** `2 cores` (optional)
5. คลิก **Import**

### Step 2.3: ตั้งค่า Network Interfaces

**สร้าง Host-Only Network (ถ้ายังไม่มี):**

1. **File** → **Host Network Manager**
2. คลิก **Create**
3. ตั้งค่า:
   - IPv4 Address: `192.168.56.1`
   - IPv4 Network Mask: `255.255.255.0`

**ตั้งค่า VM Adapters:**

1. คลิกขวาที่ VM → **Settings** → **Network**
2. ตั้งค่าแต่ละ adapter:

| Adapter | Attached to | Name/Network | Purpose |
|---------|-------------|--------------|---------|
| **Adapter 1** | Host-only Adapter | vboxnet0 | Management (LibreNMS) |
| **Adapter 2** | NAT | - | Internet Access |
| **Adapter 3** | Internal Network | intnet1 | LAN 1 |
| **Adapter 4** | Internal Network | intnet2 | LAN 2 |

3. คลิก **OK**

### Step 2.4: Start VM และ Login

1. Start VM: **Start** → **Normal Start**
2. รอจนเห็น login prompt (~30 วินาที)

```
MikroTik RouterOS 7.x
Login: admin
Password: [กด Enter - ไม่มีรหัสผ่าน]
```

3. Login สำเร็จ → เห็น prompt:
```
[admin@MikroTik] >
```

### Step 2.5: ตั้งค่า IP Address สำหรับ Management

```bash
# ตั้ง IP สำหรับ ether1 (Host-Only)
[admin@MikroTik] > /ip address add address=192.168.56.10/24 interface=ether1

# ตรวจสอบ
[admin@MikroTik] > /ip address print
```

**Expected Output:**
```
0   192.168.56.10/24   192.168.56.0    ether1
```

### Step 2.6: ทดสอบ Connectivity

จากเครื่อง host (Terminal/PowerShell):

```bash
ping 192.168.56.10
```

**Expected Output:**
```
Reply from 192.168.56.10: bytes=32 time<1ms TTL=64
```

✅ **ถ้า ping ได้ = network configuration ถูกต้อง!**

### Step 2.7: เปิด SNMP Service

```bash
# เปิด SNMP
[admin@MikroTik] > /snmp set enabled=yes

# ตั้งค่า contact และ location
[admin@MikroTik] > /snmp set contact="admin@lab.local" location="Lab Network"

# ตรวจสอบ
[admin@MikroTik] > /snmp print
```

**Expected Output:**
```
enabled: yes
contact: admin@lab.local
location: Lab Network
...
```

### Step 2.8: ตรวจสอบ SNMP Community

```bash
# ดู community (default มี "public" อยู่แล้ว)
[admin@MikroTik] > /snmp community print
```

**Expected Output:**
```
0   name="public" addresses=0.0.0.0/0 security=none read-access=yes
    write-access=no
```

💡 **Community "public" พร้อมใช้งาน ไม่ต้องสร้างเพิ่ม**

### Step 2.9: ทดสอบ SNMP

จากเครื่อง host หรือ LibreNMS container:

```bash
# จาก LibreNMS container
docker exec librenms snmpwalk -v 2c -c public 192.168.56.10 system
```

**Expected Output:**
```
SNMPv2-MIB::sysDescr.0 = STRING: RouterOS CHR
SNMPv2-MIB::sysObjectID.0 = OID: SNMPv2-SMI::enterprises.14988.1
SNMPv2-MIB::sysUpTime.0 = Timeticks: (123456) 0:20:34.56
SNMPv2-MIB::sysContact.0 = STRING: admin@lab.local
SNMPv2-MIB::sysName.0 = STRING: MikroTik
SNMPv2-MIB::sysLocation.0 = STRING: Lab Network
```

✅ **ถ้าเห็น output เหล่านี้ = SNMP ทำงานถูกต้อง!**

### 🎉 LAB 2 Complete!

MikroTik RouterOS พร้อมให้ LibreNMS เข้ามา monitor แล้ว!

**Summary:**
- ✅ VM running
- ✅ IP: 192.168.56.10
- ✅ Ping ได้จาก host
- ✅ SNMP enabled
- ✅ Community: public

---

## 🧪 LAB 3: Monitor MikroTik บน LibreNMS

**🎯 วัตถุประสงค์:** เพิ่ม MikroTik เข้าสู่ LibreNMS และดูข้อมูล monitoring

**⏱️ เวลา:** 10-15 นาที

### Step 3.1: เพิ่มอุปกรณ์ผ่าน Web Interface

1. เปิด LibreNMS: **http://localhost:8000**
2. Login ด้วย `admin` / `Admin@123`
3. ไปที่ **Devices** → **Add Device** (มุมบนขวา)

### Step 3.2: กรอกข้อมูลอุปกรณ์

**Device Information:**
```
Hostname or IP:     192.168.56.10
SNMP Version:       v2c
Community:          public
Port:               161
```

**Optional Settings:**
```
Display Name:       MikroTik-Lab-Router
Hardware:           [ปล่อยว่าง - auto-detect]
OS:                 [ปล่อยว่าง - auto-detect]
```

**Other Options:**
- ✅ เปิด **ICMP Ping**
- ❌ ไม่ต้องเลือก **Force add**

### Step 3.3: Add Device

1. คลิก **Add Device**
2. รอ LibreNMS ทำการ:
   - ✅ ตรวจสอบ SNMP connectivity
   - ✅ Detect device type และ OS
   - ✅ Discovery interfaces และ sensors
   - ✅ สร้าง RRD files

**Expected Result:**
```
✅ Device added successfully!
   Device: MikroTik-Lab-Router (192.168.56.10)
   OS: RouterOS
   Hardware: CHR
```

3. คลิก **View Device** เพื่อดูรายละเอียด

### Step 3.4: ดูข้อมูลอุปกรณ์

คุณจะเห็น:

**Overview Tab:**
- Device Status: **Up** (สีเขียว)
- Uptime: เวลาที่ RouterOS ทำงาน
- Location: Lab Network
- Contact: admin@lab.local
- Hardware: CHR
- Version: RouterOS 7.x

**Health Tab:**
- CPU Usage graph
- Memory Usage graph
- Disk Usage
- System Uptime

**Ports Tab:**
- ether1 (Host-Only) - Status: Up
- ether2 (NAT) - Status: Up
- ether3 (Internal) - Status: Up
- ether4 (Internal) - Status: Up

**Graphs Tab:**
- Traffic In/Out graphs (5 min, 1 hour, 1 day, 1 week)
- Packet rates
- Error rates

### Step 3.5: รอให้ Poller เก็บข้อมูล

```bash
# ตรวจสอบ polling status
docker exec librenms lnms device:poll 192.168.56.10 -vvv
```

**Poller จะทำงานทุก 5 นาที (default)** เพื่อเก็บ metrics

### Step 3.6: ดู Dashboard

1. กลับไปที่ **Dashboard** (หน้าหลัก)
2. ตอนนี้จะเห็น:

```
Devices: 1 (Up: 1, Down: 0)
Ports: 4 (Up: 4, Down: 0)
```

3. ดู widgets ต่างๆ:
   - **Device Summary**
   - **Recent Events**
   - **Alert Summary**
   - **Port Usage**

### Step 3.7: สร้าง Traffic บน MikroTik (Optional)

เพื่อดูกราฟที่มีข้อมูล:

```bash
# SSH เข้า MikroTik
ssh admin@192.168.56.10

# Ping ไปที่ Internet (generate traffic)
[admin@MikroTik] > /ping 8.8.8.8 count=100

# หรือ
[admin@MikroTik] > /tool fetch url="http://speedtest.tele2.net/10MB.zip"
```

### Step 3.8: ตรวจสอบกราฟ

1. กลับไปที่ LibreNMS
2. **Devices** → **MikroTik-Lab-Router** → **Graphs**
3. เลือก **ether2** (NAT interface)
4. ดูกราฟ Traffic In/Out

**✅ ควรเห็นกราฟมี spike ขึ้นมา!**

### 🎉 LAB 3 Complete!

ยินดีด้วย! คุณได้สร้างระบบ Network Monitoring แบบสมบูรณ์แล้ว!

**Achievement Unlocked:**
- ✅ LibreNMS monitoring system
- ✅ MikroTik RouterOS as monitored device
- ✅ SNMP polling active
- ✅ Real-time graphs และ metrics
- ✅ Full network visibility

---

## 🎓 Lab Summary

### สิ่งที่คุณได้ทำใน Lab นี้

1. **LAB 1:** ติดตั้ง LibreNMS monitoring system ด้วย Docker Compose (4 containers)
2. **LAB 2:** สร้าง MikroTik RouterOS VM พร้อม 4 network interfaces และเปิด SNMP
3. **LAB 3:** เชื่อมต่อและ monitor MikroTik ผ่าน LibreNMS

### Metrics ที่ Monitor ได้

- ✅ Device uptime และ availability
- ✅ Interface status (Up/Down)
- ✅ Traffic In/Out (bandwidth usage)
- ✅ Packet rates (pps)
- ✅ Error และ discard counters
- ✅ CPU และ memory usage
- ✅ System information

### Network Protocols ที่ใช้

- **SNMP v2c:** สำหรับ polling metrics (UDP port 161)
- **ICMP:** สำหรับ ping monitoring
- **HTTP:** สำหรับ web interface (TCP port 8000)
- **SSH:** สำหรับ management (TCP port 22)

---

## 📚 คู่มือเพิ่มเติม

### 📖 [LibreNMS - คู่มือฉบับสมบูรณ์](librenms/librenms.md)

รายละเอียดครบถ้วนเกี่ยวกับ LibreNMS:
- 🏗️ Architecture diagrams (Mermaid)
- ⚙️ Configuration reference
- 🔧 Advanced commands
- 💾 Backup & restore
- 🔍 Troubleshooting (10+ ปัญหา)
- 🎯 Best practices

### 🔧 [MikroTik RouterOS - คู่มือฉบับสมบูรณ์](mikrotik/mikrotik.md)

รายละเอียดครบถ้วนเกี่ยวกับ MikroTik:
- 📦 Import & setup VirtualBox
- 🌐 Network configuration details
- 🔊 SNMP configuration (CLI + GUI)
- ➕ Device management
- 🔍 Troubleshooting (8+ ปัญหา)
- 🎯 Security best practices

---

## 🔧 คำสั่งที่ใช้บ่อย

### LibreNMS (Docker)

```bash
# Start/Stop services
docker-compose up -d
docker-compose stop
docker-compose restart

# ดู logs
docker-compose logs -f librenms

# เข้า container shell
docker exec -it librenms bash

# เพิ่มอุปกรณ์
docker exec librenms lnms device:add <ip> -c <community>

# Poll ทันที
docker exec librenms lnms device:poll <ip> -vvv

# Validate configuration
docker exec librenms lnms validate
```

### MikroTik RouterOS

```bash
# SSH login
ssh admin@192.168.56.10

# ดู interfaces
/interface print

# ดู IP addresses
/ip address print

# ตรวจสอบ SNMP
/snmp print
/snmp community print

# ดู system resources
/system resource print

# Backup config
/export file=backup
```

### VirtualBox

```bash
# Start VM (headless)
VBoxManage startvm "MikroTik-Router" --type headless

# Stop VM
VBoxManage controlvm "MikroTik-Router" poweroff

# List VMs
VBoxManage list vms

# Show VM info
VBoxManage showvminfo "MikroTik-Router"
```

---

## 🆘 Troubleshooting

### ปัญหาที่พบบ่อย

#### 1. LibreNMS ไม่สามารถ poll MikroTik

**ตรวจสอบ:**
```bash
# Test ping
docker exec librenms ping -c 3 192.168.56.10

# Test SNMP
docker exec librenms snmpwalk -v 2c -c public 192.168.56.10 system

# ดู poller logs
docker-compose logs -f dispatcher
```

**แก้ไข:**
- ตรวจสอบว่า MikroTik SNMP enabled: `/snmp print`
- ตรวจสอบ community string: `/snmp community print`
- ตรวจสอบ network connectivity

#### 2. MikroTik ไม่ได้รับ IP Address

**แก้ไข:**
```bash
# SSH เข้า MikroTik (ผ่าน VirtualBox console)
[admin@MikroTik] > /ip address add address=192.168.56.10/24 interface=ether1
[admin@MikroTik] > /ip address print
```

#### 3. Graphs ไม่แสดงข้อมูล

**รอ 5-10 นาที** เพื่อให้ poller เก็บข้อมูลพอ

```bash
# Force poll ทันที
docker exec librenms lnms device:poll 192.168.56.10 -vvv
```

**ดูคู่มือ Troubleshooting แบบละเอียดที่:**
- [LibreNMS Troubleshooting](librenms/librenms.md#การแก้ไขปัญหา)
- [MikroTik Troubleshooting](mikrotik/mikrotik.md#ตรวจสอบและแก้ไขปัญหา)

---

## 🎯 Next Steps

หลังจากจบ Lab แล้ว คุณสามารถ:

### 1. เพิ่มอุปกรณ์เพิ่มเติม
- สร้าง MikroTik VM ตัวที่ 2
- เพิ่ม Linux server
- เพิ่ม Windows machine (ถ้ามี SNMP)

### 2. ตั้งค่า Alerts
- Alert เมื่อ device down
- Alert เมื่อ bandwidth สูง
- Alert เมื่อ CPU/Memory สูง

### 3. สร้าง Custom Dashboards
- Dashboard สำหรับ overview
- Dashboard ต่อ team/location
- Widget ต่างๆ ตามต้องการ

### 4. Advanced Configuration
- ตั้งค่า SNMP v3 (secure)
- API integration
- External alerting (Email, Slack, Discord)
- Backup automation

---

## 📞 Resources

### Official Documentation
- [LibreNMS Docs](https://docs.librenms.org/)
- [MikroTik Wiki](https://wiki.mikrotik.com/)
- [Docker Docs](https://docs.docker.com/)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)

### Community
- [LibreNMS Community](https://community.librenms.org/)
- [LibreNMS Discord](https://discord.gg/librenms)
- [MikroTik Forum](https://forum.mikrotik.com/)

---

## 📝 License

โปรเจคนี้ใช้งานสำหรับการศึกษาและพัฒนา

- LibreNMS: GPL v3.0
- MikroTik RouterOS: Commercial License

---

<div align="center">

## 🎉 Congratulations!

**คุณได้เรียนรู้การสร้างระบบ Network Monitoring แบบ Professional แล้ว!**

*ดูเอกสารเพิ่มเติม:*
- [📖 LibreNMS Full Guide](librenms/librenms.md)
- [🔧 MikroTik Full Guide](mikrotik/mikrotik.md)

**Happy Monitoring! 🚀**

</div>
