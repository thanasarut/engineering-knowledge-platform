# Obsidian Workflow

## Purpose

Obsidian is used as the writing interface for EKMP.

The repository structure is managed by Git:

```
GitHub Repository
        |
        v
Obsidian Vault
        |
        v
Markdown files
```

Obsidian is used for:

- Capturing ideas
- Writing Engineering Investigation Journal (EIJ)
- Maintaining reusable concepts
- Managing knowledge through Markdown

---

# Template Engine

EKMP uses:

**Obsidian Templater plugin**

Do not use Obsidian Core Templates.

Template files are stored in:

```
templates/
```

Example:

```
templates/
└── inbox-template.md
```

Templater syntax example:

```markdown
<% tp.file.title %>

<% tp.date.now("YYYY-MM-DD HH:mm:ss") %>
```

---

# Create New Note Workflow

## 1. Create new note

Create a new Markdown file:

```
Ctrl + N
```

---

## 2. Rename file first

Before inserting template, rename the file.

Example:

```
20260730-obsidian-git-android-storage-issue.md
```

Reason:

Templater evaluates:

```markdown
<% tp.file.title %>
```

at execution time.

If the file is still:

```
Untitled
```

the generated title will become:

```
Untitled
```

Renaming afterward will not rerun Templater.

---

## 3. Insert template

Open command palette:

```
Ctrl + P
```

Select:

```
Templater: Open Insert Template modal
```

Select required template:

Example:

```
templates/inbox-template.md
```

---

## 4. Template execution

Templater will generate:

- Metadata
- Created timestamp
- Default sections
- Initial structure

Example:

```yaml
---
created: 2026-07-30 14:32:30 +07:00
type: inbox
status: captured
tags:
  - inbox
---
```

---

# Inbox Workflow

New ideas should start from:

```
00-INBOX
```

Flow:

```
Idea / Observation
        |
        v
     00-INBOX
        |
        v
 Weekly Review
        |
        +--> Jira Task
        |
        +--> EIJ
        |
        +--> Concept
        |
        +--> Archive
```

---

# Writing Guideline

## Why / Origin Story

Capture:

- Why this topic appeared
- What triggered the curiosity
- Initial motivation

Write naturally first.

---

## Context

Capture background information needed to understand the topic.

Examples:

- Environment
- Existing systems
- Previous experience
- Constraints

---

## Notes

Capture observations and discoveries.

No need to organize perfectly.

---

## Questions

Capture unknowns.

Questions can later become:

- Investigation topics
- Experiments
- Research items

---

## Possible Next Step

Capture possible future directions.

This is not a task list.

Execution tasks should move to Jira during review.

---

# Git Sync

Before synchronization:

```
git status
```

Review changed files.

Then:

```
git add .
git commit -m "docs: update knowledge"
git push
```

---

# Current Android Setup

Android Obsidian Git uses:

```
Obsidian Git
      |
      HTTPS + Personal Access Token
      |
GitHub
```

Reason:

SSH authentication works with Termux Git,
but Obsidian Git on Android has compatibility issues with SSH remote handling.

---

# Important Rules

- Do not manually copy `.git` directory between devices.
- Do not store workspace state in Git.
- Markdown files are the source content.
- Git history is the version history of knowledge.