# LOG-FILE-ARCHIVING
# Linux Log File Archiving Lab
> **Platform:** Cisco Networking Academy / NDG Linux Essentials  
> **Topic:** File Archiving, Tar Utility, Directory Navigation  
---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Case Scenario](#case-scenario)
3. [Lab Objectives](#lab-objectives)
4. [Prerequisites](#prerequisites)
5. [Lab Walkthrough](#lab-walkthrough)
   - [Step 1 – Create the Archive Directory](#step-1--create-the-archive-directory)
   - [Step 2 – Create the Archive](#step-2--create-the-archive)
   - [Step 3 – List the Archive Contents](#step-3--list-the-archive-contents)
   - [Step 4 – Create the Backup Directory](#step-4--create-the-backup-directory)
   - [Step 5 – Extract Files to Backup Directory](#step-5--extract-files-to-backup-directory)
   - [Step 6 – Verify the Backup Directory](#step-6--verify-the-backup-directory)
6. [Verification & Deliverables](#verification--deliverables)
7. [Expected Output](#expected-output)
8. [Summary of Commands Used](#summary-of-commands-used)
9. [Key Concepts](#key-concepts)

---

## Overview

This lab simulates a real-world incident response scenario. Suspicious activity has been detected on the system, and the administrator must **preserve log files** by archiving them before they can be altered or lost. The lab covers creating a `tar` archive of all `.log` files from `/var/log`, inspecting the archive contents, and extracting them to a backup location.

---

## Case Scenario

Suspicious activity has been detected on the Linux system. To preserve evidence, all files in `/var/log` ending with the `.log` extension must be archived immediately. The archive must be:

- Named `log.tar`
- Stored in `~/archive`
- Extracted as a backup copy to `~/backup`

---

## Lab Objectives

- [x] Create the `~/archive` directory in the home directory
- [x] Create a `tar` archive named `log.tar` stored in `~/archive`
- [x] Remove path names from the archived files (strip leading `/var/log/`)
- [x] Produce verbose output during archiving
- [x] List the contents of the archive without extracting
- [x] Create the `~/backup` directory in the home directory
- [x] Extract the archived files into `~/backup`
- [x] Verify the backup directory contents match the expected output

---

## Prerequisites

- Access to a Linux system (NDG Linux Essentials sandbox or equivalent)
- A user account with access to `/var/log`
- Familiarity with basic Linux terminal navigation

---

## Lab Walkthrough

---

### Step 1 – Create the Archive Directory

Before creating the archive, the destination directory must exist. Create the `archive` directory inside the home directory (`~`):

```bash
mkdir ~/archive
```

Verify it was created:

```bash
ls ~
```
### Step 2 – Create the Archive

Use the `tar` command to archive all `.log` files from `/var/log` into `~/archive/log.tar`.

```bash
tar -cvf ~/archive/log.tar -C /var/log $(ls /var/log/*.log | xargs -n1 basename)
```

**Breaking down the flags:**

| Flag | Meaning |
|------|---------|
| `-c` | **Create** a new archive |
| `-v` | **Verbose** — print each file name as it is archived |
| `-f ~/archive/log.tar` | **File** — specifies the archive file name and destination path |
| `-C /var/log` | **Change** to `/var/log` before adding files — removes the path prefix |
| `$(ls /var/log/*.log \| xargs -n1 basename)` | Dynamically lists only `.log` files by filename only |

> **Why `-C`?** Using `-C /var/log` changes the working directory to `/var/log` before archiving. This means files are stored in the archive **without** the full `/var/log/` path — just the bare filename (e.g., `auth.log` instead of `var/log/auth.log`).

**Alternative single-command approach:**

```bash
tar -cvf ~/archive/log.tar --strip-components=1 /var/log/*.log
```

> **Why `--strip-components=1`?** This strips the first directory component (`/var/log/`) from the file paths when storing them in the archive, achieving the same path-removal effect.

During execution you will see verbose output listing each file as it is added:

```
alternatives.log
auth.log
bootstrap.log
cron.log
dpkg.log
kern.log
mail.log
```
<img width="516" height="346" alt="image" src="https://github.com/user-attachments/assets/ef7765dd-cf9c-43ee-9d41-c01ce5de65fd" />
---

### Step 3 – List the Archive Contents

Inspect the contents of the archive **without extracting** to confirm all files were captured correctly:

```bash
tar -tf ~/archive/log.tar
```

**Flag breakdown:**

| Flag | Meaning |
|------|---------|
| `-t` | **List** the contents of the archive |
| `-f ~/archive/log.tar` | **File** — specifies the archive to inspect |

Expected output:

```
alternatives.log
auth.log
bootstrap.log
cron.log
dpkg.log
kern.log
mail.log
```

### Step 4 – Create the Backup Directory

Create the `backup` directory in the home directory to serve as the extraction destination:

```bash
mkdir ~/backup
```

Verify it was created:

```bash
ls ~
```

You should now see both `archive` and `backup` in your home directory.

> 📸 **[SCREENSHOT HERE]** — Terminal output of `ls ~` showing both the `archive` and `backup` directories present in the home directory.

---

### Step 5 – Extract Files to Backup Directory

Extract the contents of `log.tar` into the `~/backup` directory:

```bash
tar -xvf ~/archive/log.tar -C ~/backup
```

**Flag breakdown:**

| Flag | Meaning |
|------|---------|
| `-x` | **Extract** files from the archive |
| `-v` | **Verbose** — print each file name as it is extracted |
| `-f ~/archive/log.tar` | **File** — specifies the archive to extract from |
| `-C ~/backup` | **Change** to `~/backup` before extracting — sets the output destination |

During extraction, you will see each file printed as it is restored:

```
alternatives.log
auth.log
bootstrap.log
cron.log
dpkg.log
kern.log
mail.log
```

### Step 6 – Verify the Backup Directory

Confirm all files were extracted successfully by listing the contents of `~/backup`:

```bash
ls ~/backup
```

Expected output:

```
alternatives.log  bootstrap.log  dpkg.log  mail.log
auth.log          cron.log       kern.log
```
<img width="504" height="195" alt="image" src="https://github.com/user-attachments/assets/646f1f22-e9ff-47a6-961e-5197a46dc381" />

## Verification & Deliverables

The table below summarizes all required verification checks:

| Deliverable | Command | Expected Result |
|-------------|---------|-----------------|
| Archive directory exists | `ls ~` | `archive` directory visible |
| Archive was created | `ls -lh ~/archive/` | `log.tar` file present |
| Archive contents are correct | `tar -tf ~/archive/log.tar` | 7 `.log` files, no path prefix |
| Backup directory exists | `ls ~` | `backup` directory visible |
| Files extracted correctly | `ls ~/backup` | 7 `.log` files present |

### Full Verification Block

```bash
echo "=== Home Directory ==="
ls ~

echo ""
echo "=== Archive File ==="
ls -lh ~/archive/

echo ""
echo "=== Archive Contents ==="
tar -tf ~/archive/log.tar

echo ""
echo "=== Backup Directory Contents ==="
ls ~/backup
```
<img width="495" height="368" alt="image" src="https://github.com/user-attachments/assets/96960076-c8fa-4fb7-9818-a675a4ba7f47" />

## Expected Output

### Archive Listing (`tar -tf ~/archive/log.tar`)

```
alternatives.log
auth.log
bootstrap.log
cron.log
dpkg.log
kern.log
mail.log
```

### Backup Directory Listing (`ls ~/backup`)

```
alternatives.log  bootstrap.log  dpkg.log  mail.log
auth.log          cron.log       kern.log
```

## Summary of Commands Used

| Command | Purpose |
|---------|---------|
| `mkdir ~/archive` | Creates the archive destination directory |
| `mkdir ~/backup` | Creates the backup extraction directory |
| `tar -cvf <archive> -C <dir> <files>` | Creates a verbose archive, stripping paths with `-C` |
| `tar --strip-components=1` | Alternative method to remove leading path components |
| `tar -tf <archive>` | Lists archive contents without extracting |
| `tar -xvf <archive> -C <dir>` | Extracts archive verbosely into a specified directory |
| `ls ~` | Verifies home directory contents |
| `ls -lh ~/archive/` | Verifies archive file exists with human-readable size |
| `ls ~/backup` | Verifies extracted files in the backup directory |

---

## Key Concepts

### The `tar` Command

`tar` (Tape Archive) is the standard Linux utility for bundling multiple files into a single archive file. It does **not** compress by default — it only bundles. Compression can be added with additional flags (`-z` for gzip, `-j` for bzip2).

### Common `tar` Flag Reference

| Flag | Long Form | Purpose |
|------|-----------|---------|
| `-c` | `--create` | Create a new archive |
| `-x` | `--extract` | Extract files from archive |
| `-t` | `--list` | List archive contents |
| `-v` | `--verbose` | Show files being processed |
| `-f` | `--file` | Specify the archive filename |
| `-C` | `--directory` | Change to directory before operation |
| `--strip-components=N` | | Strip N leading path components |

### Path Stripping — Why It Matters

Without path stripping, files would be archived with their full path:

```
# Without -C or --strip-components (bad)
var/log/auth.log
var/log/kern.log

# With -C /var/log or --strip-components=1 (correct)
auth.log
kern.log
```

Storing files without full paths makes the archive portable — when extracted, files land directly in the target directory instead of recreating the full `/var/log/` folder structure.

### Wildcard Globbing

```bash
/var/log/*.log
```

The `*` wildcard matches **any sequence of characters**. `/var/log/*.log` expands to every file in `/var/log` that ends with `.log`, ensuring only log files are captured — not subdirectories or other file types.

*Lab completed as part of the NDG Linux Essentials course on Cisco Networking Academy.*
