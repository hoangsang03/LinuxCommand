Perfect 👍 since you’re on **Windows**, and you already run **SQL Server locally**, you should avoid binding the SSH tunnel to port **1433** (because it’s in use). Instead, we’ll use a custom local port like `11433`.

Here’s how you do it with **PuTTY** (the easiest way on Windows):

---

## 🛠 Setup SSH Tunnel with PuTTY (Windows)

### 1. Open PuTTY

* In **Host Name (or IP address)**, enter your Linux server’s public IP (e.g., `103.90.224.70`).
* Port: `22`
* Connection type: `SSH`

---

### 2. Configure the tunnel

1. In the left menu, go to:
   `Connection → SSH → Tunnels`
2. In **Source port**, enter:

   ```
   11433
   ```
3. In **Destination**, enter:

   ```
   localhost:1433
   ```

   (because SQL Server listens on `localhost` inside your Linux server)
4. Click **Add** → you’ll see it appear in the “Forwarded ports” box as:

   ```
   L11433  localhost:1433
   ```

---

### 3. Save session

* Go back to **Session** in the left menu.
* Type a name in “Saved Sessions” (e.g., `MySQLTunnel`).
* Click **Save**.

---

### 4. Start the tunnel

* Click **Open**.
* Login with your Linux user credentials.
* Keep this PuTTY window open → the tunnel stays active as long as this session is alive.

---

### 5. Connect in SQL Server Management Studio (SSMS) / .NET app

Use:

```
Server=localhost,11433;
User Id=sa;
Password=YourSAPassword;
```

---

✅ Result:

* Your local machine connects to port **11433**.
* PuTTY securely forwards it to your Linux server’s **localhost:1433**.
* No conflict with your local SQL Server on 1433.

---

👉 Do you want me to also show you how to make PuTTY **auto-launch the tunnel on Windows startup** (so you don’t need to open it manually each time)?
