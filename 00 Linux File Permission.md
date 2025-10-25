# **Comprehensive Linux File Permissions Notes**

---

# 🧠 **Linux File Permissions — Complete Notes**

---

## 📘 1. What Are File Permissions?

Every file and directory in Linux has **three types of access permissions** and **three categories of users**.

### **Access types (for each user class):**

| Symbol | Meaning | Description                       |
| :----: | ------- | --------------------------------- |
|   `r`  | Read    | Can view file contents / list dir |
|   `w`  | Write   | Can modify file / create/delete   |
|   `x`  | Execute | Can run file / enter directory    |

---

## 👥 2. User Categories

| Category | Symbol | Description                         |
| -------- | ------ | ----------------------------------- |
| Owner    | `u`    | User who owns the file              |
| Group    | `g`    | Users belonging to the file’s group |
| Others   | `o`    | Everyone else                       |
| All      | `a`    | All of the above (u + g + o)        |

---

## 🧾 3. Viewing Permissions

```bash
ls -l
```

**Example:**

```
-rwxr-xr--  1  ubuntu  devteam  1234  Oct 23  09:10  script.sh
```

### Breakdown:

```
-   rwx   r-x   r--
|   |     |     |
|   |     |     └── Others (read)
|   |     └──────── Group (read + execute)
|   └────────────── Owner (read + write + execute)
└────────────────── File type (- = file, d = directory)
```

---

## 📂 4. File Type Indicators (first character)

| Symbol | Type             |
| :----: | :--------------- |
|   `-`  | Regular file     |
|   `d`  | Directory        |
|   `l`  | Symbolic link    |
|   `c`  | Character device |
|   `b`  | Block device     |
|   `s`  | Socket           |
|   `p`  | Named pipe       |

---

## 🔢 5. Numeric (Octal) Representation

Each permission bit has a numeric value:

| Permission    | Value |
| ------------- | ----- |
| `r` (read)    | 4     |
| `w` (write)   | 2     |
| `x` (execute) | 1     |

So, combine them:

| Mode  | Binary | Value | Meaning        |
| ----- | ------ | ----- | -------------- |
| `rwx` | 111    | 7     | Full access    |
| `rw-` | 110    | 6     | Read + write   |
| `r-x` | 101    | 5     | Read + execute |
| `r--` | 100    | 4     | Read only      |
| `---` | 000    | 0     | No access      |

---

### Example:

```
chmod 754 file.txt
```

| User   | Permissions | Value |
| ------ | ----------- | ----- |
| Owner  | rwx         | 7     |
| Group  | r-x         | 5     |
| Others | r--         | 4     |

✅ **Result:** Owner can read/write/execute, group can read/execute, others can read.

---

## 🧰 6. Changing Permissions

### Symbolic method:

```bash
chmod u+x file.txt        # Add execute for owner
chmod g-w file.txt        # Remove write from group
chmod o=r file.txt        # Set others to read-only
chmod a+r file.txt        # Everyone can read
```

### Numeric method:

```bash
chmod 700 script.sh       # rwx for owner only
chmod 644 file.txt        # rw for owner, r for others
chmod 755 /var/www/html   # rwx for owner, rx for group/others
```

---

## 👑 7. Ownership Management

Each file has **two owners:**

* **User (owner)**
* **Group**

### Check ownership:

```bash
ls -l
```

### Change ownership:

```bash
sudo chown user file.txt
sudo chown user:group file.txt
sudo chgrp group file.txt
```

### Example:

```bash
sudo chown ubuntu:devteam script.sh
```

---

## 🧩 8. Directory Permissions — Special Notes

| Permission | Effect on Directory            |
| ---------- | ------------------------------ |
| `r`        | Can list contents (`ls`)       |
| `w`        | Can create/delete/rename files |
| `x`        | Can **enter** directory (`cd`) |

✅ To **open** a file inside a directory, you need **execute permission** on the directory itself.

---

## ⚙️ 9. Special Permissions (Advanced)

| Special Bit    | Symbol          | Octal | Description                                         |
| -------------- | --------------- | ----- | --------------------------------------------------- |
| **SetUID**     | `s` (on owner)  | 4xxx  | Run file with owner’s privileges                    |
| **SetGID**     | `s` (on group)  | 2xxx  | Run with group privileges / new files inherit group |
| **Sticky Bit** | `t` (on others) | 1xxx  | Only owner can delete files (used in `/tmp`)        |

### Examples:

```bash
chmod 4755 /usr/bin/customapp   # SetUID
chmod 2755 /var/www/html        # SetGID
chmod 1777 /tmp                 # Sticky bit
```

**Symbolic equivalents:**

```bash
chmod u+s file
chmod g+s dir
chmod +t /tmp
```

**Example of sticky bit dir:**

```
drwxrwxrwt  10 root  root  4096  Oct 23  /tmp
```

---

## 🧮 10. Default Permissions & umask

**umask** = *permissions mask applied when a file is created.*

```bash
umask
```

Default `umask` = 0022 → new files = 644, directories = 755.

**Formula:**

```
New permission = 666 (file) - umask
New directory  = 777 - umask
```

Example:

```bash
umask 002
# Files: 664, Directories: 775
```

---

## 🔍 11. Permission Commands Summary

| Command              | Description                              |
| -------------------- | ---------------------------------------- |
| `ls -l`              | Show permissions                         |
| `chmod`              | Change permissions                       |
| `chown`              | Change owner                             |
| `chgrp`              | Change group                             |
| `umask`              | Show/change default mask                 |
| `getfacl`, `setfacl` | View/set ACLs (fine-grained permissions) |

---

## 🧠 12. Advanced — Access Control Lists (ACLs)

Used when you need **per-user or per-group permissions** beyond standard `ugo`.

```bash
# Enable ACL support (if needed)
sudo apt install acl

# Set ACL
setfacl -m u:alice:rwx file.txt

# Remove ACL
setfacl -x u:alice file.txt

# View ACL
getfacl file.txt
```

---

## 🧱 13. Permission Troubleshooting

| Problem                            | Likely Cause             | Fix                          |
| ---------------------------------- | ------------------------ | ---------------------------- |
| “Permission denied”                | Missing `x` on directory | `chmod +x dir`               |
| File not executable                | Missing execute bit      | `chmod +x script.sh`         |
| Can’t delete file in shared folder | No `w` on dir            | `chmod +w dir`               |
| Others can delete my files         | Missing sticky bit       | `chmod +t dir`               |
| File owner wrong                   | Ownership mismatch       | `sudo chown user:group file` |

---

## 🧩 14. Visual — Permission Structure

```mermaid
graph TD
  A[File] --> B[Owner Permissions (u)]
  A --> C[Group Permissions (g)]
  A --> D[Other Permissions (o)]
  B --> E[Read / Write / Execute]
  C --> F[Read / Write / Execute]
  D --> G[Read / Write / Execute]
```

---

## ⚡ 15. Interview Quick Answers

> **Q:** What does `chmod 755 file` mean?
> **A:** Owner: rwx (7), Group: r-x (5), Others: r-x (5).

> **Q:** Difference between `chmod 777` and `chmod 775`?
> **A:** `777` gives everyone full access; `775` restricts write to owner & group only.

> **Q:** What’s the sticky bit?
> **A:** Protects files in shared dirs like `/tmp`; only the owner can delete their own files.

> **Q:** How to give execute to everyone but remove write for group?
> **A:** `chmod a+x,g-w file`

> **Q:** What’s `umask`?
> **A:** Default permission mask used when new files or directories are created.

---