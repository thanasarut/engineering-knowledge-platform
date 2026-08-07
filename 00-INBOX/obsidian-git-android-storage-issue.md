---
title: Obsidian Git Android Storage Issue
created: 2026-08-08 02:03:00 +07:00
type: inbox
status: captured
tags:
  - inbox
  - git
  - obsidian
  - android
---

# Obsidian Git Android Storage Issue

## Why

เกิดจากการทดลองใช้งาน EKMP บน Huawei TGR-W09 โดยต้องการใช้ Obsidian เป็น knowledge editor และใช้ GitHub เป็น version control

ต้องการให้สามารถเขียน Markdown ได้จากหลาย environment เช่น MBP และ Android tablet โดยไม่ต้องเปิดเครื่องหลัก

ระหว่าง setup พบปัญหาเมื่อใช้ Obsidian Git plugin ร่วมกับ Git repository บน Android shared storage

---

## Context

- EKMP repository ใช้ Markdown + GitHub เป็น knowledge repository
- ใช้ Obsidian เป็น editor
- ใช้ Obsidian Git plugin สำหรับ commit/sync
- ใช้ Termux สำหรับ Git CLI และ SSH authentication
- Repository ถูก clone ไว้ใน Android shared storage (`Documents`)
- ต้องการ workflow:
  - MBP → GitHub
  - Android tablet → Obsidian → GitHub

---

## Notes

- Termux Git สามารถใช้ SSH key (`ed25519`) push ไป GitHub ได้
- Obsidian Git plugin บน Android มีปัญหากับ SSH remote
- ทดลองเปลี่ยน authentication approach เป็น HTTPS + Personal Access Token
- พบว่า Git metadata (`.git`) บน Android shared storage มีปัญหา permission/object access
- `git fsck --full` พบ invalid HEAD และ missing objects
- ต้อง clone repository ใหม่ และนำ Markdown files กลับเข้า repo

---

## Questions

- Android shared storage เหมาะกับการเก็บ Git repository หรือไม่?
- ควรวาง Git working tree บน Android filesystem แบบไหน?
- Obsidian Git บน Android ควรใช้ HTTPS + PAT เป็นมาตรฐานหรือไม่?
- ควรแยก Obsidian vault กับ Git repository หรือไม่?
- Multi-device workflow สำหรับ EKMP ควรออกแบบอย่างไร?

---

## Possible Next Step

- ทดสอบ Obsidian Git ด้วย HTTPS + PAT
- กำหนด workflow สำหรับ MBP และ Android tablet
- สรุปข้อจำกัดของ Android shared storage กับ Git repository
- บันทึก decision หลังทดลองเสร็จ