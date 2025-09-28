Let’s break down your `df -h` output line by line. This command shows **disk usage (storage)** on all mounted filesystems in a human-readable format (`-h` = human-readable, e.g., GiB/MB).

---

```
Filesystem      Size  Used Avail Use% Mounted on
```

* **Filesystem** → the partition or virtual filesystem
* **Size** → total size of that filesystem
* **Used** → space already used
* **Avail** → free space available
* **Use%** → percentage of space used
* **Mounted on** → the path where it is mounted

---

### **Line-by-line analysis**

1. **`tmpfs           392M   38M  355M  10% /run`**

* `tmpfs` = temporary filesystem stored in RAM
* Size 392 MB, 38 MB used, 355 MB free
* Mounted at `/run` for runtime files like PID files or sockets
* Uses **RAM**, not disk

2. **`/dev/sda1        71G   11G   61G  15% /`**

* `/dev/sda1` = main disk partition
* Total 71 GB, 11 GB used, 61 GB free → **plenty of free space**
* Mounted at `/` = root filesystem, holds your OS, apps, database, etc.
* Only 15% used → not close to full

3. **`tmpfs           2.0G     0  2.0G   0% /dev/shm`**

* `tmpfs` in RAM for **shared memory** used by processes
* Size 2 GB, currently unused

4. **`tmpfs           5.0M     0  5.0M   0% /run/lock`**

* `tmpfs` in RAM for **lock files** (used to prevent simultaneous access to resources)
* Very small, currently unused

5. **`/dev/sda15      105M  6.2M   99M   6% /boot/efi`**

* EFI System Partition (ESP) for **bootloader**
* 105 MB total, only 6.2 MB used

6. **`tmpfs           392M   12K  392M   1% /run/user/0`**

* `tmpfs` for **root user runtime files**
* Essentially empty, in RAM

---

### **Summary**

* Your main disk `/dev/sda1` has **71 GB total, 11 GB used, 61 GB free** → very healthy
* All other `tmpfs` are in **RAM**, small and mostly empty
* No storage issues — **disk space is not causing your server flapping**

---

If you want, I can explain **how to monitor both RAM, disk, and your MoneyManager API process at the same time** so you can detect why it seems online/offline.

Do you want me to do that?
