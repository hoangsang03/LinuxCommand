On Linux, to **view a log file in real-time**, you can use `tail -f`. For your auth log:

```bash
sudo tail -f /var/log/auth.log
```

### Explanation:

* `tail` → shows the end of the file
* `-f` → **follow**, meaning it will keep the terminal open and print new lines as they are added

---

### Tips:

1. **Filter for SSH events only**:

```bash
sudo tail -f /var/log/auth.log | grep sshd
```

2. **Stop watching**: Press `Ctrl+C`

3. **Alternative**: Use `less` with follow mode:

```bash
sudo less +F /var/log/auth.log
```

* Press `Ctrl+C` to stop following
* Press `F` to resume following

---

This is useful to **watch login attempts in real-time**, e.g., when testing your SSH configuration.

Do you want me to show a **real-time command that only shows failed logins**?
