# The Ghost in the Blocks — Cytech CTF Write-Up

| Challenge | Category | Flag |
| :--- | :--- | :--- |
| **The Ghost in the Blocks** | OSINT / Git Forensics | `CTF{cryptix}` |

---

## 📌 Challenge Overview

Cyber intelligence intercepted chatter regarding a threat actor named **"Herobrine Steve"** who resurfaced in blocky gaming realms. The actor boasted about publishing a script aimed at **"killing villagers"** and attempted to scrub an older, classified project from public sight.

**Objective:** Investigate the digital footprint of "Herobrine Steve", navigate his Git repositories, and recover the hidden artifact / flag.

---

## 🔍 Investigation Walkthrough

### 1. OSINT Reconnaissance & Finding the GitHub

Using open-source search operators for the username and recent activity:
* Search Queries:
  ```text
  "Herobrine Steve"
  "herobrine-steve kill villagers"
  ```
* Found Target Profile: [https://github.com/herobrine-steve](https://github.com/herobrine-steve)

Reviewing the profile revealed two public repositories:
1. `Killing-software` — The villager-killing script mentioned in chatter.
2. `nether-core-legacy` — The older project targeted for scrubbing.

---

### 2. Inspecting `Killing-software` Git History

Navigating to the commit logs of the first repository:
* URL: `https://github.com/herobrine-steve/Killing-software/commits/main/`

Reviewing the commit history revealed that a file named `notes.txt` was added and subsequently deleted. Looking at the commit diff of `notes.txt` revealed developer notes:

```text
The old 'nether-core-legacy' project has some leftover artifacts in the logs.
Need to scrub that repository completely before anyone starts digging.
```

This confirmed our next target: the `nether-core-legacy` repository.

---

### 3. Digging into `nether-core-legacy` Commit History

Navigating to the legacy repository:
* Repository: `https://github.com/herobrine-steve/nether-core-legacy`
* Commits: `https://github.com/herobrine-steve/nether-core-legacy/commits/main/`

Looking for scrubbed/deleted files or cleanup commits led to commit:
* **Commit Hash:** `f27fa3dbd43fe5791bb14b2e1e9c01bfcd0ee5e5`
* **Commit URL:** [https://github.com/herobrine-steve/nether-core-legacy/commit/f27fa3dbd43fe5791bb14b2e1e9c01bfcd0ee5e5](https://github.com/herobrine-steve/nether-core-legacy/commit/f27fa3dbd43fe5791bb14b2e1e9c01bfcd0ee5e5)

Inspecting the diff revealed that a deleted JavaScript config block `SYSTEM_OVERRIDES` contained:
```javascript
ARTIFACT_DATA : "Q1RGe2NyeXB0aXh9"
```

---

### 4. Decoding the Artifact

The artifact string `Q1RGe2NyeXB0aXh9` is Base64 encoded.

```bash
echo "Q1RGe2NyeXB0aXh9" | base64 -d
```

**Output:**
```text
CTF{cryptix}
```

---

## 🚩 Flag

```text
CTF{cryptix}
```

---

## 💡 Summary & Key Takeaways

1. **Git Forensics Rule:** Deleting a file or code snippet in a subsequent commit does **not** delete it from Git history.
2. Unless Git history is actively rewritten (e.g., `git filter-repo` or BFG Repo-Cleaner) and force-pushed, all previous versions, commit diffs, and deleted sensitive keys remain permanently accessible.
3. Attack path:
   ```text
   Herobrine Steve
   └── Killing-software (Commit log)
       └── Deleted notes.txt (Pivot clue)
           └── nether-core-legacy (Commit log)
               └── Scrub commit (f27fa3d...)
                   └── SYSTEM_OVERRIDES Base64
                       └── CTF{cryptix}
   ```
