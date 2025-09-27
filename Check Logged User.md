Yes, on Linux you can **force all users to log out**, including SSH sessions, but you have to be careful not to kick yourself out unintentionally. Here’s how:

---

### 1. **List all logged-in users**

```bash
who
```

or

```bash
w
```

* Shows username, terminal, IP, login time, etc.

---

### 2. **Kill all user sessions**

You can use `pkill` to terminate processes belonging to a user:

```bash
sudo pkill -KILL -u username
```

* Replace `username` with the user you want to log out.
* For **all users except yourself**, you can do:

```bash
for user in $(who | awk '{print $1}' | sort | uniq); do
  if [ "$user" != "$USER" ]; then
    sudo pkill -KILL -u $user
  fi
done
```

This will **kill all processes of every user except your current session**.

---

### 3. **Logout SSH sessions immediately**

You can also restart SSH after setting `PasswordAuthentication no` to prevent new logins:

```bash
sudo systemctl restart ssh
```

* Existing sessions might be dropped depending on configuration, but new logins **will require SSH key**.

---

### ⚠ Warning

* Killing all user sessions will **terminate any running processes for those users**, which may cause service interruption.
* Always keep at least one admin session open until you confirm everything works.

---

If you want, I can give a **single command to log out all root sessions from unauthorized IPs** without affecting yourself. Do you want me to do that?
