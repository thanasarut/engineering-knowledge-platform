---
id: eij-obsidian-git-android-app-storage
title: Obsidian Git on Android — App Storage Workflow
type: eij
status: validated
visibility: public-safe

created: 2026-08-21
last_reviewed: 2026-08-21

platforms:
  - Android
  - macOS
  - GitHub

devices:
  - Huawei TGR-W09

technologies:
  - Obsidian
  - Obsidian Git
  - Git
  - GitHub
  - Termux
  - Android App Storage
  - GitHub PAT

engineering_concepts:
  - application-sandboxing
  - uid-isolation
  - filesystem-ownership
  - git-repository-ownership
  - app-private-storage
  - shared-storage
  - secret-management
  - synchronization-architecture

source_notes:
  - 00-INBOX/obsidian-git-android-storage-issue.md

publish_candidate:
  - medium

tags:
  - eij
  - obsidian
  - git
  - github
  - android
  - termux
  - mobile-workflow
  - troubleshooting
---

# Obsidian Git on Android — App Storage Workflow

## Why

I wanted to use the same Git-backed Obsidian knowledge repository across:

- macOS
    
- Android tablet
    
- GitHub
    

The goal was to have a practical mobile workflow where Markdown files remain normal Git-controlled files and GitHub remains the source of synchronization and version history.

The first Android setup used an Obsidian vault in Android shared storage and attempted to access the same Git working tree from both:

- Obsidian Git
    
- Termux Git CLI
    

That design eventually exposed permission problems inside `.git`.

A cleaner workflow was found and tested successfully on a Huawei TGR-W09.

---

# Initial Problem

The original setup looked roughly like:

```text
Android Shared Storage
└── Obsidian Vault / Git Repository
        ↑
        ├── Obsidian Git
        └── Termux Git
```

This appeared convenient because both applications could work with the same repository.

However, Android isolates applications using different application UIDs.

On the tested TGR-W09:

```text
Obsidian → u0_a127
Termux  → u0_a201
```

Some files under `.git`, including Git objects created through Obsidian, were owned by the Obsidian application identity.

When Termux later attempted to inspect the repository, Git reported errors such as:

```text
unable to open loose object ... Permission denied
fatal: bad object HEAD
```

The Git repository was therefore visible from shared storage, but this did not mean every Git object was safely accessible by both applications.

---

# Key Realization

The problem was not primarily Git itself.

The problematic architecture was:

```text
Obsidian
    ↘
     Same .git repository
    ↗
Termux
```

Two sandboxed Android applications were trying to manage the same Git metadata.

The better design is:

```text
Obsidian App Storage
└── Vault
    └── .git

Owner:
Obsidian Git
```

Let **Obsidian Git be the sole Git implementation managing the Obsidian vault on Android**.

If Git CLI is needed in Termux, use a completely separate clone inside Termux private storage.

---

# Validated Android Setup

## 1. Create a New Obsidian Vault

On Android:

```text
Obsidian
→ Create new vault
→ Select App Storage
```

Use a fresh/empty vault.

Using App Storage avoids the previous design where the same repository was exposed through Android shared storage to both Obsidian and Termux.

---

# 2. Install Only Obsidian Git First

Inside the new empty vault:

```text
Settings
→ Community Plugins
→ Browse
→ Git
```

Install and enable:

**Obsidian Git by Vinzent03**

For the initial repository restore, avoid spending time configuring the rest of the vault first.

The remote repository may already contain Obsidian configuration that will be restored during the clone.

---

# 3. Configure GitHub Authentication

GitHub authentication on Obsidian Git for Android uses HTTPS with a Personal Access Token.

Configure:

```text
Username:
<GitHub username>

Password / Personal Access Token:
<GitHub PAT>
```

For a fine-grained GitHub PAT, the minimal useful repository permissions for normal pull/commit/push operation are:

```text
Metadata
→ Read

Contents
→ Read and Write

Commit statuses
→ Read and Write
```

Restrict the token to only the required repository when possible.

Do not embed the PAT inside the repository URL.

---

# 4. Clone the Existing Repository

Open:

```text
Command Palette
→ Git: Clone existing remote repo
```

Use the Git HTTPS clone URL:

```text
https://github.com/<username>/<repository>.git
```

Example structure:

```text
https://github.com/example/knowledge-repository.git
```

Then follow the plugin prompts for:

- destination;
    
- existing `.obsidian` configuration;
    
- clone operation;
    
- Obsidian restart.
    

Wait until the clone completes and Obsidian asks to restart.

---

# 5. Result

After restart:

```text
GitHub Repository
        ↓
Obsidian Git
        ↓
Android App Storage Vault
```

The existing repository content becomes available directly inside Obsidian.

The Android device can then use normal Obsidian Git commands such as:

```text
Git: Pull
Git: Commit
Git: Push
Git: Commit-and-sync
```

No Termux Git operation is required for the Obsidian vault.

---

# Tested Working Workflow

The practical procedure that worked on the TGR-W09 was:

```text
1. Create a new Obsidian vault using App Storage

2. Install and enable only
   Obsidian Git by Vinzent03

3. Configure GitHub authentication
   - GitHub username
   - Personal Access Token

4. Command Palette
   → Git: Clone existing remote repo

5. Enter HTTPS repository URL ending in .git

6. Follow the clone prompts

7. Restart Obsidian

8. Repository is now available as the Android vault
```

Result:

**Ta-da — an existing GitHub-backed Obsidian vault running directly on Android.**

---

# What Not to Do

Avoid managing the same Android Obsidian Git repository using Termux Git when the vault is owned and managed by Obsidian.

In particular, avoid treating the Android shared-storage vault like an ordinary Linux working tree and running operations such as:

```text
git reset --hard
git gc
git fsck
```

from Termux against the same `.git` repository.

Even if the directory itself appears accessible, individual Git objects may have different application ownership/permissions.

---

# If Termux Git Is Still Needed

Use a separate repository clone:

```text
Termux Private Storage

~/repos/
└── engineering-knowledge-platform/
    └── .git
```

Architecture:

```text
                 GitHub
                /      \
               /        \
      Obsidian Vault   Termux Clone
       App Storage     Termux $HOME
```

This deliberately creates two Git working trees.

That costs a small amount of additional storage but avoids application-UID ownership conflicts.

Synchronization happens through GitHub rather than through a shared `.git` directory.

---

# Android-Specific Lesson

Android applications run under separate application identities.

Therefore:

```text
Shared filesystem access
≠
shared Unix ownership of every file
```

A directory being visible from both Obsidian and Termux does not guarantee that both applications can safely manipulate all Git metadata inside that directory.

For Git repositories, especially `.git/objects`, this distinction matters.

---

# Obsidian Git Mobile Limitation

Obsidian Git uses a JavaScript Git implementation on mobile rather than native Git.

The upstream project currently documents several mobile limitations, including:

- SSH authentication is not supported on mobile;
    
- GitHub authentication uses HTTPS/PAT;
    
- mobile repositories are subject to memory limits;
    
- some native Git functionality is unavailable.
    

Therefore the Android workflow should be designed around the plugin rather than assuming it behaves exactly like desktop Git.

---

# Decision

For EKMP on Android:

```text
Obsidian vault
→ App Storage

Git implementation
→ Obsidian Git

Remote authentication
→ HTTPS + GitHub PAT

Termux access to Obsidian .git
→ Avoid

Termux Git CLI
→ Separate clone if required

Synchronization source
→ GitHub
```

---

# Why This Is Interesting

The useful lesson is larger than just “how to install Obsidian Git.”

The failed design initially looked cleaner:

```text
One repository
+ two tools
= less duplication
```

But Android's application isolation made that apparent simplicity fragile.

The more reliable architecture is actually:

```text
Two isolated working trees
+ one shared remote
= clearer ownership
```

This is a practical example of preferring **clear ownership boundaries over filesystem-level sharing**.

---

# Potential Medium Article

Possible angle:

**Running a Real Git-Backed Obsidian Vault on Android — What Finally Worked**

Alternative angle:

**Why Sharing One Git Repo Between Obsidian and Termux Broke on Android**

Possible article structure:

1. What I wanted
    
2. The obvious architecture
    
3. Why it failed
    
4. Android UID isolation
    
5. The App Storage insight
    
6. The working setup
    
7. PAT configuration
    
8. Final architecture
    
9. Lessons learned
    

The failure story should be retained because it explains _why_ the final procedure works rather than presenting another unexplained setup tutorial.

---

# References

- Original investigation: `00-INBOX/obsidian-git-android-storage-issue.md`
    
- Obsidian Git upstream mobile setup documentation
    
- Android application sandbox / UID model