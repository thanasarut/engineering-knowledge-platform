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
- [x] Create Company Base settings
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

---

## Implementation Notes — 2026-10-07

### Common Base: current state

Company `Default` profile has been initialized as the Common Base.

Current decisions:

- Common settings live in the Default profile.
- Settings that must be shared by every profile are listed in `workbench.settings.applyToAllProfiles`.
- Common extensions are handled separately from settings.
- Common extensions such as VSCodeVim and PlantUML can be marked **Apply Extension to all Profiles**.
- Platform / Java / Node.js profiles have not been created yet.

### Settings vs Extensions: two separate mechanisms

Common settings and common extensions are not controlled by the same configuration.

```text
Common settings
└─ workbench.settings.applyToAllProfiles

Common extensions
└─ Extensions UI
   └─ Apply Extension to all Profiles
```

This distinction is important because a setting can be common without its extension being common, and vice versa.

### Profile relationship

`Platform`, `Java`, and `Node.js` should be treated as sibling profiles at the same scope.

```text
                    Common
          Apply to All Profiles
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
      Platform        Java        Node.js
```

Profiles do not behave as a permanent parent-child inheritance tree.

Creating a new profile from Default can copy configuration as a starting point, but later Default changes are not automatically inherited unless the relevant setting or extension is explicitly applied to all profiles.

### Suggested profile boundaries

```text
Default / Common
├─ editor behavior
├─ theme / UI
├─ Git behavior
├─ Vim
├─ PlantUML
└─ other tools used regardless of application stack

Platform
├─ YAML
├─ Terraform
├─ Ansible
├─ Kubernetes
├─ Docker
└─ Helm

Java
├─ Java
├─ Spring
├─ Maven / Gradle
└─ OpenAPI tooling when relevant

Node.js
├─ JavaScript / TypeScript tooling
├─ ESLint
├─ package-manager tooling
└─ OpenAPI tooling when relevant
```

The exact extension list should be added incrementally when there is a real use case rather than designing every profile up front.

### Dev Container boundary

A Dev Container is part of the project development environment, not the personal VS Code identity.

Do not depend on Settings Sync to make a Dev Container reproducible.

Project-required extensions should be declared in `.devcontainer/devcontainer.json`, for example:

```json
{
  "customizations": {
    "vscode": {
      "extensions": [
        "redhat.vscode-yaml",
        "hashicorp.terraform"
      ]
    }
  }
}
```

Repository recommendations can also live in:

```text
.vscode/extensions.json
```

Project-specific VS Code behavior belongs in:

```text
.vscode/settings.json
```

Mental model:

```text
Settings Sync
└─ who I am / how I use the editor

Repository + Dev Container
└─ what this project needs to run and develop
```

### CLI tool boundary

Command-line tools such as `jq`, `yq`, `kubectl`, `helm`, Terraform CLI, and similar tools are not VS Code profile configuration.

Treat them as environment/toolchain dependencies.

```text
VS Code profile
└─ editor extensions and editor behavior

Host OS
└─ general-purpose CLI tools used interactively

Dev Container
└─ project-specific CLI tools and required versions
```

For Windows administration and remote Windows hosts, PowerShell remains an important native tool even when Unix-style tools such as `jq` are preferred for portable workflows.

### Current checkpoint

Completed:

- [x] Create Company Default/Common Base
- [x] Configure common settings to apply across profiles
- [x] Decide that common extensions use **Apply Extension to all Profiles**
- [x] Establish Dev Container vs Settings Sync boundary
- [x] Establish CLI/toolchain vs VS Code profile boundary

Deferred:

- [ ] Create Platform profile
- [ ] Add Platform-specific extensions
- [ ] Create Java profile when needed
- [ ] Create Node.js profile when needed
- [ ] Review Personal Mac Default/Base against the new Common Base
- [ ] Configure Settings Sync after the profile model is stable
- [ ] Validate the same identity/profile model in Codespaces

