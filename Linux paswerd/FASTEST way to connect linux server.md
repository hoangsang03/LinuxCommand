Sure! Step 3 is about **configuring `~/.ssh/config`** on your local machine so that you don’t have to type all the connection options (port, username, key) every time you SSH. Let’s break it down in detail.

---

## 3️⃣ Save SSH connection settings in `~/.ssh/config`

### 3.1 Locate or create the config file

On your local machine (Windows using Git Bash, WSL, or Linux/macOS):

```bash
nano ~/.ssh/config
```

* If the file doesn’t exist, this command creates it.
* `~/.ssh/` is the folder where your private/public keys live.

---

### 3.2 Add a host entry

Example: your server uses port `11433`, username `moniesaver`, and your private key is `~/.ssh/id_ed25519`.

```text
Host myserver
    HostName 203.0.113.25
    User moniesaver
    Port 11433
    IdentityFile ~/.ssh/id_ed25519
```

**Explanation:**

| Directive       | Purpose                                                                                  |
| --------------- | ---------------------------------------------------------------------------------------- |
| `Host myserver` | This is a nickname for your server. You’ll use this in the SSH command (`ssh myserver`). |
| `HostName`      | Actual IP or domain of the server.                                                       |
| `User`          | The username on the server you want to log in as.                                        |
| `Port`          | The SSH port the server listens on.                                                      |
| `IdentityFile`  | Path to the private key to use for this server.                                          |

---

### 3.3 Save and set correct permissions

Make sure the config file has **correct permissions**:

```bash
chmod 600 ~/.ssh/config
```

* Only the owner should read/write the file.

---

### 3.4 Connect using the shortcut

Now you can connect without typing username, port, or key:

```bash
ssh myserver
```

* Behind the scenes, SSH uses all the settings from your `~/.ssh/config` entry.
* You can create multiple entries for different servers. For example:

```text
Host server1
    HostName 203.0.113.25
    User moniesaver
    Port 11433
    IdentityFile ~/.ssh/id_ed25519

Host server2
    HostName 198.51.100.10
    User admin
    Port 22
    IdentityFile ~/.ssh/id_rsa
```

Then you can simply do:

```bash
ssh server1
ssh server2
```

No need to type full commands every time.

---

✅ **Benefits of using `~/.ssh/config`:**

1. Saves typing the port and key every time.
2. Makes multiple server connections simple.
3. Prevents mistakes (like connecting to the wrong port).
4. Works with tools like `scp`, `rsync`, and VS Code Remote SSH automatically.

---

If you want, I can **update the full SSH setup doc** to include **custom port + key + password disabled + SSH config**, so it’s one complete guide ready to follow.

Do you want me to do that?
