Almost 👍 — let’s clarify carefully:

### ✅ Key point

* **One login** (`ReadUser`) can be mapped to **one user per database**.
* But each database can have **many users** (some mapped to different logins, some to the same login under different names).

---

### Example

#### Server level:

```sql
CREATE LOGIN ReadUser WITH PASSWORD = 'yourpass';
CREATE LOGIN AnotherUser WITH PASSWORD = 'otherpass';
```

#### Database1:

```sql
USE Database1;
CREATE USER ReadUser FOR LOGIN ReadUser;         -- User for login ReadUser
CREATE USER AnotherUser FOR LOGIN AnotherUser;   -- User for login AnotherUser
```

#### Database2:

```sql
USE Database2;
CREATE USER ReadUser FOR LOGIN ReadUser;         -- Again, but for this DB
CREATE USER ReportingUser FOR LOGIN ReadUser;    -- Same login, different user name!
```

---

### 🔎 What this means

* In **Database1**, the login `ReadUser` is mapped to a database user `ReadUser`.
* In **Database2**, the same login can be mapped to either:

  * a user also called `ReadUser`, or
  * a user with a different name (e.g., `ReportingUser`).

So, **a database can have multiple users** (for different logins).
But **a single login can only map to one user inside a specific database**.

---

Ahh good catch 👌 — let me clarify what I meant, because that part is subtle.

---

### 🔑 The actual rule

* **Inside a single database**:
  One **login** (server-level) can only map to **one user** (database-level).

So if you already did:

```sql
CREATE USER ReadUser FOR LOGIN ReadUser;
```

you **cannot** do:

```sql
CREATE USER ReportingUser FOR LOGIN ReadUser;
```

in the same database — SQL Server won’t allow it.

---

### 💡 Where "same login under different names" is possible

That happens when:

* You map the **same login** to a **different user name in another database**.

Example:

```sql
USE Database1;
CREATE USER ReadUser FOR LOGIN ReadUser;   -- in DB1, name = ReadUser

USE Database2;
CREATE USER ReportingUser FOR LOGIN ReadUser; -- in DB2, name = ReportingUser
```

Here, the **login is the same (`ReadUser`)**,
but the **database user names differ** (`ReadUser` in DB1, `ReportingUser` in DB2).

---

### ✅ Corrected statement

So more precisely:

* Each **database** can have many users.
* Each **login** can only appear **once per database**, but in different databases it can appear under **different user names**.

---

⚡So my earlier phrase “same login under different names” only applies **across multiple databases**, not inside the same one.

---

👉 Do you want me to show you with an actual demo script that proves what happens if you try to create **two users for one login in the same DB** (the error you get)?

