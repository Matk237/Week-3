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

Results:

| port | State | Service           |
|------|-------|-------------------|
| 80   | open  | HTTP (web server) |

