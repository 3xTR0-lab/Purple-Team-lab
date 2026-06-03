# Remote File Inclusion (RFI) — Offense

For this attack I exploited DVWA's file upload vulnerability to upload a PHP webshell, then used it to execute commands remotely on the Windows 10 target. Unlike SQL injection which targets the database, RFI gives direct OS-level command execution — making it one of the more impactful web vulnerabilities to demonstrate.

**MITRE ATT&CK:** T1190 — Exploit Public-Facing Application | T1505.003 — Web Shell | T1059 — Command and Scripting Interpreter

---

## Environment

| | IP | OS |
|---|---|---|
| Attacker (Kali) | 192.168.56.102 | Kali Linux |
| Target (DVWA) | 192.168.56.105 | Windows 10 (Sysmon installed) |

---

## Attack Chain

### Step 1 — Upload the Webshell

I uploaded `RFI_bash.py` (served as `RFI_bash.php`) through DVWA's file upload page at `/dvwa/vulnerabilities/upload/`. DVWA on low security performs no file type validation, so the PHP webshell was accepted without restriction.

The upload was confirmed when the server responded at:
```
http://192.168.56.105/dvwa/hackable/uploads/RFI_bash.php
```

> 📸 `screenshots/upload-success.png`

---

### Step 2 — Access the Webshell

After uploading, I navigated to the file directly in the browser to confirm it was executing on the server:

```
http://192.168.56.105/dvwa/hackable/uploads/RFI_bash.php
```

A `200 OK` response with `10,879 bytes` returned — the webshell interface loaded successfully on the Windows target.

> 📸 `screenshots/webshell-access.png`

---

### Step 3 — Remote Command Execution

With the webshell running on the Windows machine, I executed four post-exploitation enumeration commands through it:

```
whoami
ipconfig
systeminfo
net user
```

These commands confirmed I had code execution on the target OS, revealed the network configuration, dumped detailed system information, and enumerated local user accounts — all from a browser-based shell over HTTP.

> 📸 `screenshots/rce-commands.png`

---

## Attack Timeline

| Time | Action |
|------|--------|
| 13:36:49 | Browsed to uploads directory |
| 13:37:00 | Logged into DVWA |
| 13:38:19 | Set security level to Low |
| 13:40:07 | Navigated to file upload page |
| 13:40:17 | Uploaded `RFI_bash.php` |
| 13:40:26 | First access attempt — 404 (typo in filename) |
| 13:40:31 | Webshell accessed — 200, 10,879 bytes |
| 13:40:37 | Executed `whoami` |
| 13:40:42 | Executed `ipconfig` |
| 13:40:53 | Executed `systeminfo` |
| 13:41:06 | Executed `net user` |

Total time from login to command execution: under 5 minutes.

---

## 📹 Video Demonstration

🎥 [Watch](#) *(add link)*
