Sure! Let’s dive deeper into **step 4: using a firewall to protect your SSH server**. On Ubuntu, the default firewall tool is **UFW (Uncomplicated Firewall)**, which is simple to configure and effective.

---

## **1. Check if UFW is installed**

```bash
sudo ufw status
```

* If it says `Status: inactive`, it’s installed but not enabled.
* If not installed:

```bash
sudo apt update
sudo apt install ufw
```

---

## **2. Basic SSH protection**

If you’re using the default SSH port (22):

```bash
sudo ufw allow 22/tcp
```

Or if you changed SSH to a custom port, e.g., 2222:

```bash
sudo ufw allow 2222/tcp
```

This allows only SSH connections through that port.

---

## **3. Limit SSH access to your IP (optional but more secure)**

If your public IP is, for example, `123.45.67.89`:

```bash
sudo ufw allow from 123.45.67.89 to any port 2222 proto tcp
```

* This blocks **all other IPs** from connecting to SSH.
* If you have dynamic IP, you may skip this, but it’s safest for static IPs.

---

## **4. Enable UFW**

```bash
sudo ufw enable
```

* Once enabled, all ports not explicitly allowed are blocked.
* You can check rules with:

```bash
sudo ufw status verbose
```

---

## **5. Allow other services (if needed)**

If your server runs a web app (HTTP/HTTPS):

```bash
sudo ufw allow 80/tcp   # HTTP
sudo ufw allow 443/tcp  # HTTPS
```

---

## **6. Advanced: deny all by default, allow only needed**

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

* Only ports you explicitly allow are open.
* Recommended for a secure server.

---

### ✅ **Why this helps**

* Blocks brute-force attacks on SSH from unknown IPs.
* Even if passwords are enabled, attackers cannot reach SSH unless their IP is allowed.
* Works well with **Fail2Ban** — blocked IPs are dropped by the firewall automatically.

---

If you want, I can write a **ready-to-run UFW setup** that:

1. Allows SSH only on a custom port
2. Allows web traffic (HTTP/HTTPS)
3. Denies everything else
4. Is safe to run without locking yourself out

Do you want me to do that?
