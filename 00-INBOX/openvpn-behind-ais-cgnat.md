---
title: OpenVPN behind AIS Fibre CGNAT
created: 2026-07-14
status: captured
tags:
  - networking
  - openvpn
  - cgnat
  - thddns
  - home-lab
---

# OpenVPN behind AIS Fibre CGNAT

## Why I am capturing this

ตอนแรก ว่าจะเอา router tp-link ax10 ไปติดตั้งที่บ้านพ่อ เพื่อจะได้ขยาย wifi range ให้ที่บ้านพ่อ เพราะ เค้าไม่มีคนดูแล network แต่อยากเปลี่ยนจากกล้อง coaixcial เป็น กล้อง ip 
ผมเลยคิดว่าจะเอา tp-link ax10 ที่ไม่ได้ใช้ที่บ้านไปให้พ่อใช้ เป็น network หลัง router หลัก ais เพราะทั้งบ้านแนะนำให้เค้าซื้อกล้อง tp-link C245D กับ tp-link nvr16ch ไปแล้ว
หลังจากนั้นพอเอา tp-link ax10 มาเสียบว่าจะตั้งค่า กลับกลายเป็นไฟไม่ยอมเข้า adapter มันเสีย เลยไปส่งเคลมกลับมา หลังจากผ่านไปได้เกือบ 3 สัปดาห์ ได้ tp-link ax10 กล่องใหม่กลับมา แต่คิดว่าน่าจะได้ version ล่าสุดมา เพราะ มาเจอว่า firmware มามันรองรับ openvpn service + openvpn client ด้วย ทั้งที่ๆ จำได้ว่าตัวก่อนหน้าที่เคยซื้อมานานแล้ว มันไม่มี feature นี้ (เดาว่าน่าจะเป็น version เก่า ที่ไม่รองรับ firmware ที่มี feature นี้มาก่อน)
เลยได้โอกาสลองเล่น ที่บ้านตัวเองก่อนเลย เพราะ เดิมที ที่บ้านใช้ fiber-lan ของ ais มี ont ตัวหลักของ ais ยี่ห้อ huawei และ มันสามารถต่อ fiber optic lan ไปห้องอื่นๆได้ด้วย ที่บ้านตัวเอง คือ มี tp-link deco M5 อยู่ 2 ตัว ต่อหลัง ont ตัวนี้อยู่แล้ว เพื่อแยก network IOT ทั้งบ้านเอาไว้อยู่แล้ว แต่ก่อน ตัดสินใจอยู่ว่าจะใช้ ecosystem deco or tp-link mesh แต่ตอนนั้นมีแต่ ax10 ตัวแรก มันทำ vpn อะไรไม่ได้ เราเลยตัดสินใจว่าในบ้านเราจะใช้ deco network แทน
โอกาสดีเลยได้ลอง openVPN บน ax10 ในบ้านเลย 

เคยมี project เล่นๆ เพราะ ที่บ้านใช้ DNS-320 ตั้งแต่แรกๆ สมัย NAS ในบ้านยังใหม่ๆ บวกกับที่บ้านเพิ่งจะชุบชีวิต DNS-320 ให้ลง fonz fun-plug ได้ (เนื่องจากเก็บ artifact ไฟล์ source ไว้ทั้งหมด) ตอนนี้จะหา download ก็หาไม่ได้แล้ว แต่ผมยังมีไฟล์ที่ใช้ได้อยู่ เลยเพิ่งจะชุบมันกลับมาใช้ได้ ตอนแรกคิดว่าจะเอา DNS-320 squeez (busy box) มาทำเป็น internal jumphost ภายในบ้าน เลยคิดว่าต้องทำให้ public internet สามารถ remote ssh เข้ามาถึงได้

รวมถึงมันโยงกับ tp-link nvr16ch ซึ่งมันเป็นเครื่องบันทึกกล้องวงจรปิดภายในบ้าน ถ้าเป็นไปได้ก็ไม่อยากให้มัน อัพโหลด video stream ขึ้น tp-link cloud เพราะ การซื้อ cloud storage tp-link มองว่าเป็น subscription ที่สิ้นเปลือง และ ไม่อยากให้ขึ้น cloud ด้วย 

หลังจากที่บ้านเคยทำ tp-link nvr16ch การจะเข้าหน้า admin ของ nvr และสามารถ full management ได้ต้องอยู่ใน local network นั้นก็คือ concept เดียวกันกับการ vpn เข้ามาจาก public internet ให้ได้ด้วย

เข้าใจดีว่าการ pull management nvr ถ้าเคยทำแล้วมันใช้ได้ ปกติก็คงไม่ต้องเข้าบ่อยๆ แต่ เนื่องจาก การที่เราสามารถเข้ามาจาก internet ได้มันทำให้เรา maintainance ได้ดีกว่า เกิด ip กล้อง ชน หรือ internet down หรือ config รวน เราก็สามารถ remote แก้ไขได้ โดยไม่ต้อง on-site ใดๆ

ต้องการเก็บประสบการณ์การเปิด Remote Access เข้าระบบ Home Network ผ่าน OpenVPN
โดยอินเทอร์เน็ต AIS Fibre อยู่หลัง CGNAT และไม่ได้ซื้อ Public IPv4 เพิ่ม

เรื่องนี้ควรเก็บไว้ เพราะระหว่างแก้ปัญหามีทั้งสมมติฐานที่ผิด การทดลอง IPv6
การตรวจสอบ Port Forwarding และจุดเปลี่ยนสำคัญจาก AIS THDDNS

## Initial Goal

ต้องการใช้โทรศัพท์ที่เชื่อมต่อผ่านเครือข่ายมือถือ 5G
เชื่อมต่อ OpenVPN กลับเข้ามาที่บ้าน และเข้าถึงอุปกรณ์ภายใน เช่น

- TP-Link AX10
- Huawei AIS ONT
- DNS-320 NAS
- อุปกรณ์อื่นใน Home Network

โดยไม่ต้องซื้อ Public IPv4 รายเดือน

## Starting Environment

### Internet and network

- ISP: AIS Fibre
- AIS ONT: Huawei V163-AIS
- Router: TP-Link Archer AX10
- AX10 LAN: `192.168.2.0/24`
- AIS ONT LAN: `192.168.1.0/24`
- OpenVPN network: `10.8.0.0/24`
- OpenVPN server: TP-Link AX10
- OpenVPN protocol: UDP
- OpenVPN internal port: `1194`

### Test client

- Device: OPPO Find N3
- Connection used for external testing: Mobile 5G
- The client was not connected to the home Wi-Fi during the successful test

## Original Assumption

ตอนแรกคาดว่า TP-Link DDNS จะสามารถใช้เชื่อมต่อกลับมาที่ AX10 ได้โดยตรง

จึงแก้ไฟล์ OpenVPN client configuration ให้ใช้ DDNS hostname แทน Local IP

ตัวอย่างแนวคิด:

```text
remote <tp-link-ddns-hostname> 1194

## Investigation Notes

### 1. ทดสอบ OpenVPN ภายในเครือข่ายก่อน

เริ่มจากตรวจสอบว่า OpenVPN Server บน TP-Link AX10 ทำงานภายในเครือข่ายหรือไม่

ค่าที่ใช้ตอนนั้น:

- OpenVPN Server: TP-Link AX10
- Protocol: UDP
- Internal Port: `1194`
- AX10 WAN-side IP: `192.168.1.165`

ทดสอบส่ง UDP ไปที่ Port `1194`

```bash
nc -uv 192.168.1.165 1194
```

ผลการทดสอบทำให้เชื่อว่า OpenVPN Service ภายในเครือข่ายทำงานอยู่ และปัญหาน่าจะอยู่ที่การเชื่อมต่อจากภายนอกเข้ามา

### 2. ทดลองใช้ TP-Link DDNS

แก้ไฟล์ OpenVPN Client Configuration จาก Local IP ให้ใช้ TP-Link DDNS hostname

```text
remote <tp-link-ddns-hostname> 1194
```

จากนั้นใช้ OPPO Find N3 เชื่อมต่อผ่านเครือข่ายมือถือ 5G เพื่อให้แน่ใจว่าเป็นการทดสอบจากภายนอกบ้านจริง

ผลคือ OpenVPN Client เชื่อมต่อไม่ได้ และขึ้น:

```text
CONNECTION_TIMEOUT
```

### 3. ตรวจสอบ DNS และ Port จากภายนอก

ทดลองตรวจสอบ DDNS hostname ด้วยคำสั่ง:

```bash
nslookup <tp-link-ddns-hostname>
dig <tp-link-ddns-hostname>
ping <tp-link-ddns-hostname>
```

บางคำสั่งให้ผลไม่ตรงกัน จึงสงสัยว่า DNS อาจเป็นสาเหตุ

ทดลองตรวจสอบ Port จากภายนอกเพิ่มเติม:

```bash
nc -uv <tp-link-ddns-hostname> 1194
```

และตรวจสอบด้วย `nmap`

```bash
nmap -p 1194 <tp-link-ddns-hostname>
```

ผลที่พบในช่วงหนึ่งคือ:

```text
1194/tcp filtered
```

ภายหลังพบว่า DNS ไม่ใช่สาเหตุหลักของปัญหา

### 4. ตรวจสอบ Public IP และพบว่าอยู่หลัง CGNAT

นำ IP ที่แสดงบน AIS ONT มาเปรียบเทียบกับ IP ที่ TP-Link DDNS resolve ได้

พบว่า IP ไม่ตรงกัน

จากหลักฐานนี้จึงสรุปว่า AIS Fibre Connection อยู่หลัง Carrier Grade NAT หรือ CGNAT

ข้อสรุปในตอนนั้นคือ Port Forwarding ที่ตั้งไว้บน Router ภายในบ้านไม่สามารถรับ Connection จาก Public Internet ได้โดยตรง เพราะยังมี NAT ของ AIS อยู่ข้างหน้าอีกชั้นหนึ่ง

## First Conclusion

ในตอนแรกคิดว่าการเปิด OpenVPN Server จากบ้านออกสู่ Internet อาจทำไม่ได้ หากไม่ซื้อ Public IPv4 เพิ่มจาก AIS

แนวทางที่นึกถึงในตอนนั้น ได้แก่:

- ขอ Public IPv4 จาก AIS
- ใช้ IPv6
- ใช้ Tailscale หรือ Overlay Network
- ใช้ Server ภายนอกเป็นตัวกลาง

## IPv6 Investigation

ทดลองตรวจสอบว่า IPv6 สามารถใช้แทน Public IPv4 ได้หรือไม่

ประเด็นที่ตรวจสอบ ได้แก่:

- เปิด IPv6 บน AIS ONT
- DHCPv6 Prefix Delegation
- IPv6 Prefix ที่ AIS แจกให้
- ความสามารถด้าน IPv6 ของ TP-Link AX10
- ความสามารถของ OpenVPN Server บน AX10

ภายหลังพบว่า OpenVPN Server ใน Firmware ของ TP-Link AX10 ไม่รองรับการรับ OpenVPN Connection ผ่าน IPv6 ในรูปแบบที่ต้องการ

ใช้เวลาตรวจสอบ IPv6 ประมาณ 2 ชั่วโมง แต่ยังไม่สามารถแก้ปัญหา Remote Access ได้

## Turning Point

จุดเปลี่ยนเกิดจากการกลับไปตรวจสอบหน้า AIS THDDNS อย่างละเอียด

พบว่า AIS THDDNS ไม่ได้มีเพียงการสร้าง Domain Name แต่มีช่องสำหรับกำหนด External Port ด้วย

จากจุดนี้จึงตั้งสมมติฐานใหม่ว่า AIS อาจมีระบบรับ Connection ที่ Public Endpoint ของ AIS แล้ว Forward ผ่าน CGNAT เข้ามายัง AIS ONT ของลูกค้า

## Final Configuration

### AIS THDDNS

สร้าง THDDNS hostname และกำหนด External Port เป็น:

```text
4140
```

ใน Public Repository ให้ใช้ placeholder แทน hostname จริง:

```text
<ais-thddns-hostname>
```

### Huawei AIS ONT Port Mapping

สร้าง User-defined Port Mapping ด้วยค่าดังนี้:

```text
Protocol: UDP
External Port: 4140
Internal Port: 1194
Internal Host: 192.168.1.165
```

โดย `192.168.1.165` คือ IP ฝั่ง WAN ของ TP-Link AX10 ที่เชื่อมต่ออยู่หลัง AIS ONT

### TP-Link AX10 OpenVPN Server

ตั้งค่า OpenVPN Server:

```text
Protocol: UDP
Port: 1194
```

### OpenVPN Client Configuration

แก้ไฟล์ `.ovpn` ให้เชื่อมต่อผ่าน AIS THDDNS และ External Port

```text
remote <ais-thddns-hostname> 4140
```

## Result

ทดสอบจาก OPPO Find N3 ผ่านเครือข่ายมือถือ 5G

OpenVPN เชื่อมต่อสำเร็จและได้รับ VPN IP:

```text
10.8.0.3
```

สามารถเข้าถึง TP-Link AX10 ได้ที่:

```text
192.168.2.1
```

และสามารถเข้าถึงอุปกรณ์ในเครือข่ายฝั่ง AIS ONT ได้ด้วย เช่น:

```text
192.168.1.1
192.168.1.6
```

โดย:

- `192.168.1.1` คือ AIS ONT
- `192.168.1.6` คือ DNS-320

## Unexpected Observation

ก่อนทดสอบคาดว่า VPN Client อาจเข้าถึงได้เฉพาะเครือข่ายหลัง AX10:

```text
192.168.2.0/24
```

แต่ผลจริงคือสามารถเข้าถึงเครือข่ายฝั่ง WAN ของ AX10 ได้ด้วย:

```text
192.168.1.0/24
```

และไม่ได้เพิ่ม Static Route ด้วยตนเอง

สมมติฐานเบื้องต้นคือ AX10 ทำ Routing หรือ NAT ให้ Traffic จาก VPN Client สามารถออกไปยัง Network ฝั่ง WAN ได้

ประเด็นนี้ยังต้องตรวจสอบเพิ่มเติมก่อนสรุปกลไกที่แน่นอน

## Security Notes

OpenVPN Configuration ที่ AX10 สร้างให้มี Certificate และ Key สำหรับใช้เชื่อมต่อ

ข้อมูลต่อไปนี้ห้าม Commit ลง Public Repository:

- AIS THDDNS hostname จริง
- Public IP
- Private Key
- Certificate ที่ใช้เชื่อมต่อจริง
- Username หรือ Password
- Device Serial Number
- QR Code หรือ Configuration ที่มี Credential
- Network Information ที่ไม่ต้องการเปิดเผย

ความเสี่ยงที่ควรติดตาม:

- OpenVPN Configuration รั่วไหล
- Router Firmware มีช่องโหว่
- OpenVPN หรือ OpenSSL Version ล้าสมัย
- AIS THDDNS External Port ถูก Scan
- Router ไม่มี Firmware Update ในอนาคต

## Questions for Follow-up

- AX10 ใช้ Routing หรือ NAT แบบใดระหว่าง `10.8.0.0/24`, `192.168.2.0/24` และ `192.168.1.0/24`
- AIS THDDNS Forwarding ทำงานในระดับใดของ AIS Network
- External Port ที่กำหนดมีวันหมดอายุหรือข้อจำกัดหรือไม่
- สามารถจำกัด VPN Client ให้เข้าถึงเฉพาะบาง Subnet หรือบาง Service ได้หรือไม่
- ควรเปรียบเทียบ OpenVPN กับ Tailscale ในด้าน Security และ Maintenance หรือไม่
- ควรตรวจสอบ Firmware Version และรอบการอัปเดตของ AX10 หรือไม่

## Evidence Available

หลักฐานที่มีหรือควรนำมาใช้ตอนเขียน EIJ:

- Screenshot หน้า AIS THDDNS ที่มีช่อง External Port
- Screenshot Huawei ONT Port Mapping
- Screenshot TP-Link AX10 OpenVPN Server Configuration
- Screenshot OpenVPN Client เชื่อมต่อสำเร็จ
- VPN IP `10.8.0.3`
- ผลทดสอบเข้าถึง `192.168.2.1`
- ผลทดสอบเข้าถึง `192.168.1.1`
- ผลทดสอบเข้าถึง `192.168.1.6`
- Command output จาก `nslookup`, `dig`, `ping`, `nc` และ `nmap`

## Potential Outputs

- [ ] เขียน EIJ เรื่อง OpenVPN behind AIS CGNAT
- [ ] สร้าง Network Architecture Diagram
- [ ] สร้าง Concept Note เรื่อง CGNAT
- [ ] สร้าง Concept Note เรื่อง AIS THDDNS
- [ ] สร้าง Security Review
- [ ] พิจารณาเรียบเรียงเป็น Medium Article

## Current Status

OpenVPN Remote Access ผ่าน AIS THDDNS และ CGNAT ใช้งานได้สำเร็จแล้ว

ข้อมูลในไฟล์นี้ยังเป็น Raw Investigation Note สำหรับเก็บความทรงจำและหลักฐาน ก่อนนำไปเรียบเรียงเป็น EIJ ฉบับสมบูรณ์