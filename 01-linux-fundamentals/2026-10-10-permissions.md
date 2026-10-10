# Linux File Permissions

Quick reference for POSIX file permissions, special bits, and directory traversal rules.

---

## 1. Permission Matrix

| Value | Octal | File Effect | Directory Effect |
| :---: | :---: | :--- | :--- |
| `r`   | 4     | Read content (`cat`, `less`) | List entries (`ls`) |
| `w`   | 2     | Modify/truncate content | **Create, rename, delete files inside** |
| `x`   | 1     | Execute binary/script | **Traverse directory** (`cd`, access files) |

> **Critical Rule:** File deletion depends entirely on **write permission on the parent directory**, NOT permissions on the target file itself.

---

## 2. Special Bits (SUID, SGID, Sticky)

| Bit | Octal | Symbol | Applied To | Effect | Common Use Case |
| :--- | :---: | :---: | :--- | :--- | :--- |
| **SUID** | `4000` | `s` (user) | Binary files | Runs process as file owner | `/usr/bin/passwd` |
| **SGID** | `2000` | `s` (group)| Directories | New files inherit parent dir's group | Shared team folders |
| **Sticky** | `1000` | `t` (other)| Directories | Only owner/root can delete files | `/tmp` |

*Note: Capital `S` or `T` indicates the special bit is set, but the underlying execution (`x`) bit is missing.*

---

## 3. Directory Traversal

* To access `/var/log/app/err.log`, the executing user **must have `x` on all parent directories**: `/`, `/var`, `/var/log`, `/var/log/app`.
* A directory with `chmod 111` allows opening known paths (`cat /dir/file`), but prevents listing (`ls /dir`).
* `+` at the end of `ls -l` permissions indicates POSIX ACLs are active (`getfacl <path>`).

---

## 4. Cheat Sheet Commands

```bash
# Set standard secure permissions
chmod 644 file.txt          # rw-r--r-- (standard non-executable)
chmod 755 script.sh         # rwxr-xr-x (standard executable/dir)

# Set SGID and Sticky bit on shared directory
chmod 2775 /shared/project  # Group inheritance
chmod +t /shared/project    # Sticky bit (prevents accidental deletion)

# Check and modify ACLs
getfacl /path/to/file
setfacl -m u:deploy:rw /path/to/file
setfacl -b /path/to/file    # Remove all ACLs
