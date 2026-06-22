## Task 1: Network Information
# Week 1 – Home Lab Audit

---

## Task 1: Network Information

### System Details

| Item                             | Value |
| :------------------------------- | :---- |
| Hostname                         | LAPTOP-41VHI60Q      |
| IPv4 Address                     |  172.30.42.99/20     |
| Subnet Mask                      | 255.255.240.0      |
| Default Gateway                  |   172.30.32.1    |
| DNS Server                       |   10.255.255.254    |
| MAC Address                      | 00:15:5d:XX:XX:XX       |
| Connection Type (Wi-Fi/Ethernet) |   Wi-Fi    |

### Analysis Questions

**1. What is your network IP range?**
Answer:172.30.32.0 – 172.30.47.255

**2. How many devices could potentially be on your network?**
Answer:A maximum of 4094 devices could potentially exist on this network.

**3. Is your DNS server your router or an external service?**
Answer:The DNS server appears to be an internal/gateway-based DNS service rather than a public external DNS service.

**4. Why does the MAC address matter?**
Answer:The MAC address is a unique identifier assigned to a network interface at Layer 2. It helps devices communicate within a local network and allows network hardware to correctly deliver data.

---

## Task 2: Open Ports and Processes

### Open Port Analysis

### Open Port Analysis

| Netid | State | Local Address | Port | Service |
|:---|:---:|:---|:---:|:---|
| UDP | UNCONN | 10.255.255.254 | 53 | DNS |
| UDP | UNCONN | 127.0.0.1 | 323 | Time Synchronization |
| UDP | UNCONN | ::1 | 323 | Time Synchronization |
| TCP | LISTEN | 10.255.255.254 | 53 | DNS |

### Analysis Questions

**1. How many ports are in LISTENING state?**
Answer:One TCP port was found in the LISTENING state. Additional UDP services were also active on the system.

**2. Are any common ports present?**
Answer:Yes. Port 53 was present and is commonly used for DNS (Domain Name System). Port 323 was also present and is used for time synchronization services.

**3. Is port 3389 (RDP) open? What does it mean for security?**
Answer:No, port 3389 was not found among active listening services. Since Remote Desktop Protocol is not active, the system has lower exposure to remote access attacks.

**4. Is port 23 (Telnet) open? Why is that a problem?**
Answer:No, port 23 was not found. Telnet is considered insecure because it transmits information without encryption, making credentials vulnerable to interception.

**5. Difference between `0.0.0.0:PORT` and `127.0.0.1:PORT`?**
Answer:0.0.0.0:PORT accepts connections from all available network interfaces, while 127.0.0.1:PORT accepts connections only from the local machine.
### Step 4: Running Processes

1. `/usr/lib/systemd/systemd-journald` (PID 40) - System logging service collecting system logs and kernel error dumps.
2. `/sbin/init` (PID 1) - The master process initialized during system startup to manage child processes.
3. `/usr/lib/systemd/systemd --user` (PID 277) - Systemd manager instance running scoped to user session operations.
4. `/usr/lib/systemd/systemd-udevd` (PID 49) - Device event manager that monitors kernel hardware shifts and hooks.
5. `/usr/lib/systemd/systemd-logind` (PID 94) - Manages active user sessions, logins, and seat allocations.
6. `(udev-worker)` (PID 3022) - Worker thread handling individual kernel device events on the machine.
7. `(udev-worker)` (PID 3023) - Duplicate worker thread sorting parallel hardware registration events.
8. `-bash` (PID 1967) - Interactive terminal user command shell instance (Session 1).
9. `-bash` (PID 292) - Interactive terminal user command shell instance (Session 2).
10. `/usr/bin/dbus-daemon` (PID 93) - Desktop message bus allowing processes to securely message each other
---

## Task 3: Network Scan

### Ping Sweep Results

| IP Address | Responds to ping? | Device type (if you know) |
| :--- | :---: | :--- |
| **172.30.32.1** | Yes | Default Gateway / Microsoft Virtual Router |
| **172.30.42.99** | Yes | Kali Linux Host Instance (Local Machine) |


### Analysis Questions

**1. How many devices responded to the ping sweep?**
Answer:** Exactly **2 devices** responded to the sweep: the default virtual gateway at `172.30.32.1` and the local machine itself at `172.30.42.99`.

**2. Did you find unexpected devices?**
Answer:**No.** The scan only discovered the default upstream network gateway and the local interface.

**3. If an attacker got onto your Wi-Fi, how many devices could they reach?**
Answer:An attacker inside this network block could theoretically target and probe all **4,094 host slots** mapping across the `/20` subnetwork architecture.

**4. What is the security risk of a flat network?**
Answer:A flat network offers no internal boundaries or access control lists (ACLs) to separate connected hosts. If a single device (such as an IoT smart plug or a guest device) is compromised, an attacker can effortlessly execute **lateral movement** to sniff local data packets, launch internal exploits, and pivot to high-value endpoints without any internal security blocks.

---

## Task 4: Home Network Security Assessment

### Network Overview

My network uses a large private space with up to 4,094 open spots. My computer's name is LAPTOP-41VHI60Q. The data shows this is a virtual network created inside Windows by Microsoft. When I did a network ping sweep using Nmap, it found only 2 devices online: the main gateway router and my own Kali Linux system. No outside or unknown devices were found during the scan.e.

### Open Ports & Services

My system has a small attack surface, which is good for security. The port scan shows only port 53 (DNS) is open to the network. Highly dangerous old ports like Telnet (port 23) and Remote Desktop (port 3389) are completely closed. The rest of the network tools on my machine are locked to the inside localhost address, so outsiders cannot reach them.

### Protocol Security

The network uses standard DNS traffic on port 53. This traffic travels in plain text, meaning an attacker could watch what websites I visit or try to trick my system. However, the system is mostly safe because dangerous, unencrypted tools like HTTP and Telnet are not running or open to the outside world.

### CIA Triad Analysis

**Confidentiality:** This is good because unencrypted remote tools like Telnet are turned off. However, since the network is flat and wide, clear text traffic could still be monitored by a hacker if they manage to get into the system.
* **Integrity:** The system's integrity depends heavily on DNS on port 53. If a hacker attacks this port, they could change my DNS records and trick my computer into visiting fake, dangerous websites.
* **Availability:** This is safe for now because I have very few open ports for hackers to attack. But since the network is completely flat, a major traffic flood from a broken device could still slow down my router.

### AAA Framework Analysis

**Authentication:** This is strong on my main login screen, but the open DNS service on port 53 answers requests from devices without asking for a password first.
* **Authorization:** My local account has standard limits so apps cannot run without permission. But on the network side, any device that connects to the Wi-Fi gets full authorization to scan all 4,094 IP addresses.
* **Accounting:** My system tracks local logs using a tool called systemd-journald. However, my home network does not have a central log server to record when other devices try to connect to my machine.

### Recommendations

| # | Improvement | Why it matters | How to do it |
| :-: | :--- | :--- | :--- |
| **1** | **Network Splitting** | Stops hackers from moving to your main PC. | Use a separate Guest Wi-Fi network. |
| **2** | **Encrypted DNS** | Stops people from seeing your web traffic. | Turn on DoH in your browser settings. |
| **3** | **Local Firewall** | Blocks bad traffic automatically. | Turn on the built-in Linux UFW firewall. |
| **4** | **Log Tracking** | Shows if someone is attacking you. | Check your system logs regularly. |
| **5** | **Strong Wi-Fi** | Keeps bad guys off your network. | Use a long password and WPA3 security. |

## Task 5: Protocol Journal

| Time | Action | Protocol | Port | Encrypted? | Data Exposed / Notes |
| :--- | :--- | :--- | :---: | :---: | :--- |
| **08:15** | Checked my email in Gmail | HTTPS | 443 | Yes | None, mail content and login tokens are fully hidden. |
| **08:45** | Opened a simple test blog | HTTP | 80 | No | Website URL and full page contents travel in clear text. |
| **09:12** | Typed google.com into browser | DNS | 53 | No | The domain name requested can be seen by network sniffers. |
| **10:00** | Logged into a secure web portal | HTTPS | 443 | Yes | Login credentials and cookies are securely scrambled. |
| **11:30** | Pulled a code repository from GitHub | SSH | 22 | Yes | Code files and connection keys are safe from eavesdroppers. |
| **12:05** | Streamed a video file on YouTube | HTTPS | 443 | Yes | Video data data is encrypted; sniffers only see YouTube's IP. |
| **13:10** | System updated its internal clock | NTP | 123 | No | Basic time data packets can be seen on the local network. |
| **14:22** | Downloaded an application update | HTTPS | 443 | Yes | Download source and file components are safely encrypted. |
| **15:40** | Logged into an old router forum | HTTP | 80 | No | Warning: My clear text password and user session can be stolen. |
| **16:00** | Sent a message via web chat app | HTTPS | 443 | Yes | Message contents and user details are securely encrypted. |
