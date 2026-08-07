# Git Workflow

## Purpose

Git is used as version control and synchronization layer for EKMP.

Source of truth:

```
GitHub Repository
        |
        v
Obsidian Vault
```

---

## Daily Workflow

### 1. Start working

Pull latest changes:

```bash
git pull
```

---

### 2. Write knowledge

Update:

```
00-INBOX/
EIJ/
Concepts/
```

---

### 3. Review changes

```bash
git status
```

Example:

```
modified:
00-INBOX/openbci.md
```

---

### 4. Commit

Commit message format:

```
<type>: <description>
```

Examples:

```
docs: add OpenBCI investigation note

docs: update OpenVPN experiment

chore: update Obsidian config
```

---

### 5. Push

```bash
git push
```

---

## Android Obsidian Git

Current setup:

```
Obsidian Android
        |
        | HTTPS + PAT
        |
GitHub
```

Reason:

SSH authentication works with Termux git,
but Obsidian Git plugin has compatibility issue with SSH remote.

---

## Troubleshooting

### Git repository corrupted

Symptoms:

```
fatal: bad object HEAD
```

Recovery:

1. Keep markdown files
2. Clone repository again
3. Copy markdown content
4. Commit new changes

Do not copy `.git` folder.