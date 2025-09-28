Perfect — let’s walk through it step by step 🚀

You already have SQL Server running on your **Linux server**, and you want to connect from your **own computer (Windows or another Linux)**.

---

## 🔹 Step 1. Make sure SQL Server is listening on TCP

On the **Linux server**:

1. Check if SQL Server is listening on TCP (usually port **1433**):

   ```bash
   sudo ss -tulpn | grep 1433
   ```

   You should see `sqlservr` bound to `0.0.0.0:1433` (all IPs) or the server’s private IP.

2. If not, open `/var/opt/mssql/mssql.conf` and set:

   ```ini
   [network]
   tcpport=1433
   ```

   Then restart:

   ```bash
   sudo systemctl restart mssql-server
   ```

---

## 🔹 Step 2. Allow your computer’s IP in firewall

On the **Linux server** (if using UFW):

```bash
sudo ufw allow from <your-computer-ip> to any port 1433
```

* Replace `<your-computer-ip>` with the **IPv4** address of your computer (you can get it from [whatismyipaddress.com](https://whatismyipaddress.com) if you’re connecting over the internet).
* If you only want local network access, use your local LAN IP.

Check rules:

```bash
sudo ufw status
```

---

## 🔹 Step 3. Verify SQL authentication is enabled

Log into SQL Server on the Linux machine:

```bash
/opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P 'yourSAPassword'
```

Run:

```sql
EXEC sp_configure 'contained database authentication', 1;
RECONFIGURE;
```

Also, make sure your login (`ReadUser`) exists:

```sql
SELECT name, type_desc FROM sys.server_principals WHERE name = 'ReadUser';
```

---

## 🔹 Step 4. Connect from your computer

### Option A. From Windows (SSMS)

* **Server name**: `your_server_ip,1433`
* **Authentication**: SQL Server Authentication
* **Login**: `ReadUser`
* **Password**: `yourpass`

---

### Option B. From command line (Windows or Linux)

```bash
sqlcmd -S your_server_ip,1433 -U ReadUser -P yourpass -d MoneyManagerDB
```

---

### Option C. From an app (example .NET connection string)

```csharp
string connString = "Server=your_server_ip,1433;Database=MoneyManagerDB;User Id=ReadUser;Password=yourpass;Encrypt=True;TrustServerCertificate=True;";
```

---

## 🔹 Step 5. Test connectivity

From your computer, test if the port is open:

```bash
telnet your_server_ip 1433
```

or

```bash
nc -zv your_server_ip 1433
```

If it connects, SQL Server is reachable.

