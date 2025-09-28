Got it 👍 — error **18456** means *login failed*.
This usually happens because of one of these reasons: wrong password, SQL Authentication not enabled, or the login isn’t mapped correctly to the database.

Let’s troubleshoot step by step:

---

### 1. Make sure **SQL Authentication is enabled**

By default, SQL Server (especially on Linux) may only allow **Windows Authentication**.
Run this on your **source server**:

```sql
EXEC xp_instance_regread
    'HKEY_LOCAL_MACHINE',
    'Software\Microsoft\MSSQLServer\MSSQLServer',
    'LoginMode';
```

* `1` → Windows Authentication only
* `2` → SQL Authentication only
* `3` → Mixed Mode (both SQL + Windows) ✅

If it’s not `3`, you need to enable **Mixed Mode**:

* On Linux, edit `/var/opt/mssql/mssql.conf`
* Add / update:

```ini
[sqlagent]
enabled = true

[sqlservr]
authentication = mixed
```

Then restart SQL Server:

```bash
sudo systemctl restart mssql-server
```

---

### 2. Verify the login exists

Run on the **source server**:

```sql
SELECT name, type_desc, is_disabled 
FROM sys.server_principals 
WHERE name = 'DataReaderUser';
```

* Must show `SQL_LOGIN` and `is_disabled = 0`.

If it’s disabled:

```sql
ALTER LOGIN DataReaderUser ENABLE;
```

---

### 3. Double-check password

Try changing it just to be safe:

```sql
ALTER LOGIN DataReaderUser WITH PASSWORD = 'NewStrongPassword123!';
```

---

### 4. Check database user mapping

Make sure the login maps correctly into your database:

```sql
USE MySourceDB;
GO
EXEC sp_change_users_login 'Auto_Fix', 'DataReaderUser';
```

This fixes any orphaned user-login mapping.

---

### 5. Try to connect again

From your local machine:

```bash
sqlcmd -S 192.168.1.100 -U DataReaderUser -P NewStrongPassword123! -d MySourceDB
```

---

⚡ Quick question: when you connected with your **Windows Authentication**, was it using a **domain account** (like `DOMAIN\User`) or just your **local Linux SQL admin setup**? That affects whether Mixed Mode is already enabled.

Do you want me to give you the exact steps to **turn on Mixed Mode authentication in SQL Server on Linux** (since I think that might be the blocker here)?
