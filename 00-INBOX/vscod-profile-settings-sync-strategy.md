---
type: inbox
status: active
created: 2026-10-07
tags:
  - vscode
  - github
  - settings-sync
  - dev-environment
  - company-laptop
---

# VS Code Profile & Settings Sync Strategy

## Goal

ทำให้ VS Code environment ของ Personal และ Company คล้ายกันในส่วน Base

แต่ยังแยก:

- GitHub identity
- Company-specific configuration
- Machine-specific configuration
- Repo-specific configuration

ออกจากกันอย่างชัดเจน

---

## Mental Model

VS Code Profile คือกล่อง configuration

แต่ละ Profile สามารถมี:

- Settings
- Extensions
- Keyboard shortcuts
- Snippets
- Tasks

Configuration แบ่งเป็น 4 scope หลัก

```text
1. Base / Personal Usage
   └─ สิ่งที่ต้องการใช้เกือบทุก project

2. Toolset / Profile
   ├─ Platform
   ├─ Java
   └─ profile อื่นตามลักษณะงาน

3. Machine-specific
   ├─ macOS
   ├─ Windows
   ├─ WSL
   ├─ terminal
   └─ local paths

4. Repo / Workspace
   └─ repo/.vscode/*
```

---

## Identity Model

Personal GitHub และ Company GitHub เป็นคนละ identity

### Personal

```text
Personal GitHub Identity
        │
        ▼
VS Code Settings Sync
        │
        ├─ Default / Base Profile
        ├─ Platform Profile
        ├─ Java Profile
        └─ ...
        │
        ├─ MacBook VS Code
        └─ Personal Codespaces
```

### Company

```text
Company GitHub Identity
        │
        ▼
VS Code Settings Sync
        │
        ├─ Default / Base Profile
        ├─ Platform Profile
        ├─ Java Profile
        └─ ...
        │
        ├─ Company Windows VS Code
        └─ Company Codespaces
```

Personal และ Company Settings Sync เป็นคนละชุดกัน

เป้าหมายคือทำให้ Base ใกล้เคียงกัน:

```text
Personal Base
      │
      │ sanitize / copy common behavior
      ▼
Company Base
```

ไม่ clone ตรง ๆ

---

## What Should Be Similar

Personal Base และ Company Base ควรคล้ายกันในเรื่อง:

- Editor behavior
- Theme / UI preference
- Formatter behavior
- Git behavior
- Keyboard shortcuts
- Snippets
- Common extensions
- Generic extension configuration

---

## What Should Stay Separate

ไม่ควร copy ตรง ๆ ระหว่าง Personal และ Company:

```text
Account-specific configuration
Company-specific configuration
Mac / Windows paths
WSL configuration
Terminal profiles
Credentials
Old project configuration
Old Jira / Atlassian site IDs
Extension-version-specific paths
Policy-sensitive settings
```

---

## Repo Scope

`repo/.vscode/*` ไม่ถือเป็นส่วนของ Personal หรือ Company Settings Sync model นี้

```text
repo/
└─ .vscode/
   ├─ settings.json
   ├─ extensions.json
   ├─ tasks.json
   └─ launch.json
```

Repo configuration เป็น project-specific และสามารถ override User/Profile settings ได้

---

## VS Code Settings Sync

Settings Sync สามารถ sync ข้อมูลเช่น:

- Profiles
- Settings
- Extensions
- Keyboard shortcuts
- Snippets
- Tasks

GitHub account ในกรณีนี้ทำหน้าที่เป็น identity สำหรับ VS Code Settings Sync

ไม่ได้หมายความว่า settings ถูกเก็บเป็น GitHub repository ปกติ

---

## Local vs Codespaces

ถ้า Local VS Code และ Codespace ใช้ Settings Sync identity เดียวกัน:

```text
VS Code Sync Identity
        │
        ├─ Local VS Code
        └─ Codespace
```

ทั้งสอง environment จะได้ Base/Profile ใกล้เคียงกัน

แต่ effective configuration อาจต่างกันจาก:

- Operating system
- Remote environment
- Machine-specific settings
- `.devcontainer`
- `repo/.vscode/*`

---

## Current Migration

### Source

Personal MacBook VS Code

Current configuration มีหลาย scope ปนอยู่ เช่น:

- Base settings
- Extension settings
- Platform tooling
- Windows terminal configuration
- Old Atlassian / Jira configuration
- Mac absolute paths
- Copilot configuration

### Target

Company Windows VS Code

แนวทาง:

1. Extract Personal Base
2. Remove personal/account-specific configuration
3. Remove old project configuration
4. Remove machine-specific paths
5. Review extensions
6. Create Company Base
7. Create Platform / Java Profiles ตามต้องการ
8. Configure Company Settings Sync
9. Test Company Codespaces

---

## Current Decision

```text
Personal Base ≈ Company Base
```

Personal Base เป็น template

Company Base เป็น sanitized version ที่เหมาะกับ company environment

---

## Next Actions

- [ ] Clean Personal Default/Base settings
- [ ] Review Personal Profiles
- [ ] Run `code --list-extensions` on Mac
- [ ] Categorize extensions
  - [ ] Base
  - [ ] Platform
  - [ ] Java
  - [ ] Markdown
  - [ ] Git
  - [ ] Old / unused
- [ ] Create Company Base settings
- [ ] Check Windows / WSL availability
- [ ] Create Company Profiles
- [ ] Configure Company Settings Sync
- [ ] Test Company Codespace

---

## Promotion

ตอนนี้เก็บไว้ที่:

```text
00-INBOX/
└─ vscod-profile-settings-sync-strategy.md
```

เมื่อทดลองใช้งานจริงและ design stable แล้ว ค่อย promote ไป:

```text
docs/
└─ vscode-environment-strategy.md
```

ถ้าภายหลังมีผลลัพธ์ที่ใช้เป็น career/performance evidence ได้
ค่อยสรุป artifact แยกเข้า `EIJ/`
