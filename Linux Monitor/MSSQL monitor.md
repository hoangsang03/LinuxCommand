Let’s break down your `ps aux | grep sqlservr` output line by line:

```
mssql     490154  0.0  0.4  67676 19456 ?        Ssl  Sep27   0:03 /opt/mssql/bin/sqlservr
mssql     490159  1.9 22.7 5509216 910836 ?      Sl   Sep27  18:29 /opt/mssql/bin/sqlservr
root      520742  0.0  0.0   7080  2176 pts/0    S+   14:53   0:00 grep --color=auto sqlservr
```

---

### **Columns explained**

`ps aux` columns are:

```
USER       PID  %CPU %MEM    VSZ    RSS TTY STAT START   TIME COMMAND
```

1. **USER** → which user runs the process
2. **PID** → process ID
3. **%CPU** → percentage of CPU used
4. **%MEM** → percentage of RAM used
5. **VSZ** → virtual memory size in KB (total memory mapped)
6. **RSS** → resident memory in KB (actual RAM used)
7. **TTY** → terminal associated (`?` = no terminal)
8. **STAT** → process state (`Ssl`, `Sl`)
9. **START** → process start time/date
10. **TIME** → CPU time used by process
11. **COMMAND** → command that started the process

---

### **Line 1**

```
mssql     490154  0.0  0.4  67676 19456 ?        Ssl  Sep27   0:03 /opt/mssql/bin/sqlservr
```

* A **helper/background process** of SQL Server
* Uses very little CPU (`0.0%`) and RAM (`0.4%`)
* RSS = 19,456 KB (~19 MB)

### **Line 2**

```
mssql     490159  1.9 22.7 5509216 910836 ?      Sl   Sep27  18:29 /opt/mssql/bin/sqlservr
```

* Main **SQL Server engine process**
* Uses **1.9% CPU** currently
* Uses **22.7% of total RAM**
* VSZ = 5,509,216 KB (~5.25 GB virtual memory)
* RSS = 910,836 KB (~888 MB actual RAM)
* State `Sl` → Sleeping, multithreaded

### **Line 3**

```
root      520742  0.0  0.0   7080  2176 pts/0    S+   14:53   0:00 grep --color=auto sqlservr
```

* This is just the **`grep` command you ran**, ignore it

---

### **Summary**

* SQL Server is running normally.
* **Main process is using ~22.7% of RAM**, which on a 3.8 GiB server is ~870–880 MB.
* CPU usage is low (`1.9%`).
* No obvious memory problem, so SQL Server is **not starving your server**.

---

If you want, I can show you a **full command set to monitor MSSQL memory + MoneyManager API + Nginx + React in real time**, so you can see which process is using how much RAM/CPU, all in one view.

Do you want me to do that?
