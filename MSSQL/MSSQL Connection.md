
Absolutely! Let’s go **step by step** to secure your **MSSQL login** on Linux (Step 4), focusing on **strong authentication** and avoiding default/weak accounts.

---

## **Step 1: Connect to your MSSQL server**

1. On your Linux server, log in as the `mssql` user (or a user with sudo privileges).
2. Open the **MSSQL command-line tool**:

```bash
sqlcmd -S localhost -U sa -P 'mnsvdbpw@2025Y'
```

> Replace `'YourCurrentSaPassword'` with your current `sa` password.

---

## **Step 2: Check existing logins**

Once inside `sqlcmd`, run:

```sql
SELECT name, type_desc, is_disabled FROM sys.sql_logins;
GO
```

* `sa` is the default admin account.
* Check if there are other users and if any are **disabled**.

---

## **Step 3: Change or disable the `sa` account**

### **Option A: Rename `sa` (recommended)**

```sql
ALTER LOGIN sa WITH NAME = SecureAdmin;
GO
```

* `SecureAdmin` is your new admin name.

### **Option B: Disable `sa` (if you have another admin)**

```sql
ALTER LOGIN sa DISABLE;
GO
```

---

## **Step 4: Create a new admin login with strong password**

```sql
CREATE LOGIN AdminUser WITH PASSWORD = 'Str0ngP@ssw0rd123!';
GO
```

* Make sure password:

  * At least **12 characters**
  * Includes **uppercase, lowercase, numbers, symbols**
  * Not dictionary words or simple sequences

---

## **Step 5: Grant admin privileges to the new user**

```sql
ALTER SERVER ROLE sysadmin ADD MEMBER AdminUser;
GO
```

---

## **Step 6: Create additional restricted users**

* Avoid using admin for regular queries.
* Example: read-only user:

```sql
CREATE LOGIN ReadUser WITH PASSWORD = 'mnsvR3ad0nly!Pass';
GO

USE MoneyManagerDB;
CREATE USER ReadUser FOR LOGIN ReadUser;
ALTER ROLE db_datareader ADD MEMBER ReadUser;
GO
```

* `db_datareader` can only **read data**, cannot modify.

---

## **Step 7: Test new accounts**

* Exit `sqlcmd`:

```sql
QUIT
```

* Test login with new user:

```bash
sqlcmd -S localhost -U MNReadUser -P 'mnsvR3ad0nly!Pass'
```

* Make sure it works before disabling `sa` permanently.

---

## **Step 8: Optional security settings**

* Enforce **password policy**:

```sql
ALTER LOGIN AdminUser WITH CHECK_POLICY = ON, CHECK_EXPIRATION = ON;
GO
```

* This ensures:

  * Password expiration
  * Complexity enforced

---

✅ **Summary**

1. Avoid using `sa` → rename or disable it.
2. Create a strong admin account.
3. Create separate users with limited permissions for day-to-day tasks.
4. Enable password policies for all logins.

---

If you want, I can make a **full ready-to-run script** that you can paste into `sqlcmd` to secure your MSSQL server completely, including disabling `sa` safely.

Do you want me to create that script?

-- Connect as sysadmin (like sa)
CREATE LOGIN ReadWriteUser WITH PASSWORD = 'MNusjghDbcch6UPl5yeT!';

USE MyDB;  -- switch to your database

CREATE USER ReadWriteUser FOR LOGIN ReadWriteUser;

-- db_datareader = can read all tables
-- db_datawriter = can insert, update, delete

ALTER ROLE db_datareader ADD MEMBER ReadWriteUser;
ALTER ROLE db_datawriter ADD MEMBER ReadWriteUser;

