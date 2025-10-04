Sure! Let’s break your log snippet line by line. This is from `/var/log/auth.log` on Ubuntu, so it mainly logs **authentication events** like `sudo` and `ssh`.

---

### 1. `sudo` session

```
2025-09-27T11:23:24.114222+07:00 monievps-uuyq-bjpn sudo:     root : TTY=pts/0 ; PWD=/root ; USER=root ; COMMAND=/usr/bin/tail -f /var/log/auth.log
2025-09-27T11:23:24.115353+07:00 monievps-uuyq-bjpn sudo: pam_unix(sudo:session): session opened for user root(uid=0) by root(uid=0)
```

* Root ran a `sudo` command (in this case `tail -f /var/log/auth.log`)
* `TTY=pts/0` → pseudo-terminal used
* `session opened` → sudo session started successfully

✅ This is **legitimate activity**.

---

### 2. SSH failed login attempts

```
2025-09-27T11:23:25.233566+07:00 monievps-uuyq-bjpn sshd[464215]: Received disconnect from 103.26.136.173 port 56278:11: Bye Bye [preauth]
2025-09-27T11:23:25.234508+07:00 monievps-uuyq-bjpn sshd[464215]: Disconnected from invalid user user 103.26.136.173 port 56278 [preauth]
```

* Someone tried to SSH as user `user` from `103.26.136.173`
* `[preauth]` → this was **before authentication succeeded**
* Connection was closed (`Bye Bye`) → **failed login attempt**

---

```
2025-09-27T11:24:00.030525+07:00 monievps-uuyq-bjpn sshd[464245]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=85.190.243.208  user=root
2025-09-27T11:24:01.952170+07:00 monievps-uuyq-bjpn sshd[464245]: Failed password for root from 85.190.243.208 port 34692 ssh2
2025-09-27T11:24:02.979492+07:00 monievps-uuyq-bjpn sshd[464245]: Connection closed by authenticating user root 85.190.243.208 port 34692 [preauth]
```

* IP `85.190.243.208` tried to log in as **root**
* `authentication failure` and `Failed password` → login **did not succeed**
* Connection was closed

---

### 3. SSH attempts for invalid users

```
2025-09-27T11:24:26.710123+07:00 monievps-uuyq-bjpn sshd[464251]: Invalid user imran from 190.12.108.68 port 37976
2025-09-27T11:24:26.713237+07:00 monievps-uuyq-bjpn sshd[464251]: pam_unix(sshd:auth): check pass; user unknown
2025-09-27T11:24:26.713335+07:00 monievps-uuyq-bjpn sshd[464251]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=190.12.108.68
2025-09-27T11:24:28.204010+07:00 monievps-uuyq-bjpn sshd[464251]: Failed password for invalid user imran from 190.12.108.68 port 37976 ssh2
2025-09-27T11:24:28.566978+07:00 monievps-uuyq-bjpn sshd[464251]: Received disconnect from 190.12.108.68 port 37976:11: Bye Bye [preauth]
```

* Someone tried to log in as **non-existent user `imran`** from IP `190.12.108.68`
* SSH logs it as `Invalid user` → this is a typical **brute-force attempt**

---

### 4. Another attempt for invalid user

```
2025-09-27T11:24:36.311568+07:00 monievps-uuyq-bjpn sshd[464254]: Invalid user guest from 103.26.136.173 port 52386
2025-09-27T11:24:36.314786+07:00 monievps-uuyq-bjpn sshd[464254]: pam_unix(sshd:auth): check pass; user unknown
2025-09-27T11:24:36.314976+07:00 monievps-uuyq-bjpn sshd[464254]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=
```

* Same idea: someone tried logging in as `guest`
* IP `103.26.136.173` → failed attempt

---

### ✅ Summary

1. Your **sudo session** (tailing the log) is normal.
2. The rest are **SSH login attempts**, mostly:

   * Invalid users (`user`, `imran`, `guest`) → likely automated brute-force scans
   * Root login attempts from unknown IPs → dangerous, especially if `PasswordAuthentication yes` is enabled
3. None of these attempts succeeded (so far in this snippet).

---

If you want, I can give a **command to watch `/var/log/auth.log` in real-time and **highlight failed root or invalid user attempts** — very useful for spotting attacks as they happen.

Do you want me to do that?
